# Working with File Sizes

T-MSIS TAF data are large and at the start of the project it can be overwhelming and technically challenging to manipulate the data. The data are already organized into more manageable state x year paritions, but larger states (CA, NY, TX) and larger files (Other Services) still exceed hundreds of GB, if not TB, when read into memory — the `.parquet` files are highly compressed and can be 1-2 orders of magitude off of the true in memory footprint. 

Below we provide some tips and worked examples that will help get a project started before data are subset to a more manageable size. 

### Reading Data

#### DataFrames
For small states and small files it's reasonable to read the entire dataset into memory. Below we read the entire California Inpatient Header file for 2020 into a pandas DataFrame, which requires ~7 GB of memory.

```python
import pandas as pd

data_p = '/gpfs/milgram/pi/medicaid_lab/data/cms/ingested/TMSIS_taf/'
file = 'taf_inpatient_header/year=2020/state=CA/data.parquet'

df = pd.read_parquet(file)

print(df.shape)
#(1348818, 158)

df.info(memory_usage='deep')
#memory usage: 6.4 GB
```

While this can be helpful for initial exploratory analyses once you have a better sense of the types of analyses and relevant data you should use the `columns` argument to read in only the relevant data. Because `.parquet` files are column-organized this can be done efficiently. Below we'll read in the standard inportant claims information (Date, ID, Member) as well as the diagnosis information (admitting diagnosis + 12 potential diagnosis columns). The result is a much smaller DataFrame that only takes up 1/4 of the previous memory requirement. 

```python
columns = (['BENE_ID', 'CLM_ID', 'SRVC_END_DT', 
            'ADMTG_DGNS_CD', 'ADMTG_DGNS_VRSN_CD']
            + [f'DGNS_CD_{x}' for x in range(1,13)]
            + [f'DGNS_VRSN_CD_{x}' for x in range(1,13)])

df = pd.read_parquet(data_p+file, columns=columns)

print(df.shape)
#(1348818, 29)

df.info(memory_usage='deep')
#memory usage: 1.6 GB
```

#### Arrow Tables
Once the files become large enough the memory footprint of DataFrames can become problematic. Arrow tables have a much smaller footprint and can handle basic filtering and merging operations. This allows you to read in a larger amounts of data (comared with DataFrames) and perform merge or filtering operations to a point where it's practical to use `pandas` or `dyplr` for more sophisticated manipulations.

While the pandas DataFrame above had a 6.4 GB memory footprint, the comparable arrow table only takes up 1.3 GB.


```python
import pyarrow.parquet as pq

data_p = '/gpfs/milgram/pi/medicaid_lab/data/cms/ingested/TMSIS_taf/'
file = 'taf_inpatient_header/year=2020/state=CA/data.parquet'

tbl = pq.read_table(data_p+file)

print(f'memory usage: {tbl.nbytes/10**9:.1f} GB')
#memory usage: 1.3 GB
```

Arrow also support a `columns` argument and now the table subset to diagnosis information barely takes up 300 MB:

```python
tbl = pq.read_table(data_p+file, columns=columns)

print(f'memory usage: {tbl.nbytes/10**9:.1f} GB')
#memory usage: 0.3 GB
```

At any time a pyarrow table can be converted to a pandas DataFrame using the `.to_pandas()` method

```python
df = tbl.to_pandas()

# Though now we're back to the same, larger footprint above
df.info(memory_usage='deep')
#memory usage: 1.6 GB
```

### Filtering Data

#### Background
Often we only need a subset of the data, which can be easily identified from some field(s) within the data. In these cases `filters` can be provided to the read operations which can greatly reduce the overall memory footprint. 

For instance, reading in just 3 fields from the New York Other Services Header file for 2020 runs out of memory (even with 180 GB allocated) for the arrow table! It simply is too large to work with...

```python
import pyarrow.parquet as pq

data_p = '/gpfs/milgram/pi/medicaid_lab/data/cms/ingested/TMSIS_taf/'
file = 'taf_other_services_header/year=2020/state=NY/data.parquet'

columns = ['BENE_ID', 'CLM_ID', 'POS_CD']
tbl = pq.read_table(data_p+file)
#memory usage: > 180 GB (OOM event kills it)
```

However if we needed a specific subset of data — only emergency departments, defined by `POS_CD == '23'` — then we can add this to the read to greatly reduce the data. 

```python
tbl = pq.read_table(data_p+file,
                    filters=[('POS_CD', '=', '23')])

print(f'memory usage: {tbl.nbytes/10**9:.1f} GB')
#memory usage: 2.6 GB
```

Now the data are small enough that we can also bring in more columns and easily manipulate it. 

#### Simple Examples
Both `pandas.read_parquet` and `pyarrow.read_table` support the same filtering. Filtering based on the following comparisons: `[==, =, >, >=, <, <=, !=, in, not in]` is achieved by providing a list of tuples which each tuple being `(column_name, comparison, value)`. 

Below we filter to inpatient hosptial admissions for a delivery (diagnosis code Z3800)

```python
import pandas as pd
import pyarrow.parquet as pq

data_p = '/gpfs/milgram/pi/medicaid_lab/data/cms/ingested/TMSIS_taf/'
file = 'taf_inpatient_header/year=2020/state=CA/data.parquet'

tbl = pq.read_table(data_p+file,
                    filters=[('ADMTG_DGNS_CD', '==', 'Z3800')]
                    )
```

Additional conditions can be joined with `AND` logic by providing additional tuples within this list. The following filters to deliveries that occur within January of 2020.

```python
tbl = pq.read_table(data_p+file,
                    filters=[('ADMTG_DGNS_CD', '==', 'Z3800'),
                             ('SRVC_END_DT', '<=', pd.to_datetime('2020-01-31')),
                             ('SRVC_END_DT', '>=', pd.to_datetime('2020-01-01'))]
                   )
```

`OR` logic works by embedding separate lists within the `filters` list. For instance to include either deliveries (Z3800) or cesarean deliveries (Z3801) we would:

```python
tbl = pq.read_table(data_p+file,
                    filters=[
                             [('ADMTG_DGNS_CD', '==', 'Z3800')], # Delivery
                             [('ADMTG_DGNS_CD', '==', 'Z3801')]  # Or C-Section
                            ])
                            
# Equivalently, you would `in` for this.
tbl = pq.read_table(data_p+file,
                    filters=[('ADMTG_DGNS_CD', 'in', ['Z3800', 'Z3801'])]
                    )                                           
```

Naturally these can be combined. If you want logic that identifies (Deliveries AND January) OR (C-sections AND April):

```python
tbl = pq.read_table(data_p+file,
                    filters=[
                             [('ADMTG_DGNS_CD', '==', 'Z3800'),
                              ('SRVC_END_DT', '<=', pd.to_datetime('2020-01-31')),
                              ('SRVC_END_DT', '>=', pd.to_datetime('2020-01-01'))], # Delivery & January
                                                                                    # OR
                             [('ADMTG_DGNS_CD', '==', 'Z3801'),
                              ('SRVC_END_DT', '<=', pd.to_datetime('2020-04-30')),
                              ('SRVC_END_DT', '>=', pd.to_datetime('2020-04-01'))] # C-Section & April
                            ])

# Simple verification
df = tbl.to_pandas()
print(df.groupby('ADMTG_DGNS_CD').SRVC_END_DT.agg(['min', 'max']))                      
#                      min         max
#ADMTG_DGNS_CD                        
#Z3800          2020-01-01  2020-01-31
#Z3801          2020-04-01  2020-04-30
```

#### Advanced Filtering

TODO

### Partition Strategy for Large Files

#### Background
In certain cases none of the above options work. For instance, if you need to create medical per-member-per-month spending by category in a state like California you'll need to read in the entire Other Services file and many of individual columns.

In these cases we can impose further partitioning based on the last digit(s) of the scrambled `BENE_ID` column. This is quite a powerful "trick" because the following are true:

- `BENE_ID` is already present and (almost) entirely non-missing in all files
- `BENE_ID` is scambled so this evenly divides the data. 
- Almost every analysis will link data _within_ a person not _across_ people. 

To expand on that last point, a typical scenario is merging line files with their corresponding header file with a join on `CLM_ID`. If headers and lines are randomly partitioned then we still need to compare each header partition with every line partition since we don't know where the matches will occur. However, because claims are unique to a single person, if we partition the header files (based on the last character of `BENE_ID`) then we know we only need to compare the header partition that ends with `'A'` to the line partition that ends with `'A'` (claims for different people by definition have different `CLM_ID` values).

!!! note "There are other options" 
    
    In the above example, you might be thinking "why partition on `BENE_ID` when you could partition on `CLM_ID`", which is the merge key and _guarantees_ the joins happen within partition. That would also be a perfectly fine strategy, though in practice we often perform multiple merges or have more complicated join conditions. If in addition to merging header and line files, you'd also like to bring over demographic and eligibility data, then `BENE_ID` becomes an obvious choice for a partition which guarantees the within-partition relationship between data. 
    
    Still, in other cases `BENE_ID` may not be the right choice. If analyses happen across beneficiaries, say identifying members linked through a case (`MSIS_CASE_NUM`), then you could partition based on this field. As this field is not readily available in every file you'd need to map `BENE_ID` in each file to `MSIS_CASE_NUM` to respect that partitioning strategy.
    
#### Code
 
    
    