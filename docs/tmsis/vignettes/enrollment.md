# Identifying Medicaid & Chip Enrollment

For nearly every research project, we need to identify which beneficiaries are enrolled in Medicaid and what that coverage entails. Enrollment can be determined solely within the base demographic and eligibility data. While the file contains fields related to the "days enrolled each month" we **use only the restricted benefits codes** to determine eligibility. Taken from DQ Atlas:

>In many states, once an individual is determined eligible, coverage begins on the first day of the month of the application and generally ends on the last day of the month. Therefore, gaps of less than a month may represent administrative errors or other data quality issues rather than true disenrollment and reenrollment in Medicaid or CHIP in these states.

> Analyses conducted on the 2016 TAF data found that using the restricted benefits code alone is the most reliable approach for counting the number of Medicaid and CHIP beneficiaries with comprehensive benefits. 

### Replicating DQ Atlas Monthly Medicaid & Chip Enrollment

The demographic and eligibility file have columns for each month (01-12) with suffix `_XX`. From DQ Atlas, the values for `RSTRCTD_BNFTS_CD` that determine full Medicaid eligibility are as follows:

>Indicates the scope of Medicaid or CHIP benefits to which a beneficiary is entitled during the month. Can be used to distinguish between individuals not eligible for Medicaid or CHIP benefits during the month (value of 0); those enrolled with full or comprehensive benefits (values of 1, 4, 5, 7, A, B, or D); a and those enrolled with limited benefits (values of 2, 3, 6, C, E, or F). Beneficiaries with a restricted benefits code of 4 (restricted benefits for pregnancy-related services) have benefits that meet the Minimum Essential
Coverage (MEC) requirements in all states except Arkansas, Idaho, and South Dakota. Beneficiaries with a restricted benefits code of 4 in those three states have limited benefits.

There is further clarification that code `'5'` should only be considered in recent years:

> As of 2020, the restricted benefits code value of 5 (the individual is eligible for Medicaid or Medicaid-Expansion CHIP but, for reasons other than alien, dual-eligibility, or pregnancy-related status, is entitled to restricted benefits only) should be used only if the coverage meets the MEC standard and a new valid value of E should be used if the coverage does not meet the MEC standard. For years prior to 2020, we did not include the code 5 group for any state because it represented a more heterogenous mix of beneficiaries (some of whom had limited benefits in some states).


The following code identifies the months of full Medicaid and Chip enrollment and reproduces the "Avg Mthly Medicaid & CHIP Enrollment in TAF" column within the [Enrollment Benchmarking Table](https://www.medicaid.gov/dq-atlas/landing/topics/single/table?topic=g1m7&tafVersionId=18) provided by DQ Atlas.

On the server, we have created a simple `HelperTools` directory with some useful variables (like lists of states) so those can be more readily accessed. The example below shows you how to work with that in python.


```python
import pandas as pd
import numpy as np
import pyarrow as pa
import pyarrow.parquet as pq
import pyarrow.compute as pc
import sys
sys.path.append('/gpfs/milgram/pi/medicaid_lab/code/HelperTools/')
from DataVariables import StateVars, TMSISPath

file = 'taf_demog_elig_base'
year = 2018

# Generally useful beneficiary columns
bene_cols = ['BENE_ID', 'STATE_CD', 'AGE', 
             'SEX_CD', 'BIRTH_DT', 'MISG_ELGBLTY_DATA_IND']
# Columns used to determine eligibility
elig_cols = [f'RSTRCTD_BNFTS_CD_{str(x).zfill(2)}' for x in range(1,13)]


# Loop over all states
d = {}
for state in StateVars().abbrevs:
    # Read in Data with only required columns
    table = pq.read_table(TMSISPath().data_path+f'{file}/year={year}/state={state}/data.parquet', 
                          columns=bene_cols+elig_cols)
    
    # Remove rows for BENE_IDs with claims, but no eligibility info. 
    conds = pc.equal(table['MISG_ELGBLTY_DATA_IND'], 0)
    table = table.filter(conds)
    
    # Logic across states and years to define Full Benefit Coverage
    if state in ['AR', 'ID', 'SD']:
        codes = ['1', '7', 'A', 'B', 'D']
    else: 
        codes = ['1', '4', '7', 'A', 'B', 'D']
    if year >= 2020:
        codes.append('5')

    # DQ Atlas then calculates the number of cells with a valid code divided by 12
    # for avg. monthly enrollment. We can do the same with a sum of Booleans:
    valid_enroll = [pc.is_in(table[col], pa.array(codes)) for col in elig_cols]

    # Save average in the dictionary, round and format numbers.
    d[StateVars().abbrev_to_name[state]] = f'{np.round(np.sum(valid_enroll)/12, 0):,.0f}'

pd.Series(d).sort_index()

```


```python
Output:
Alabama                    947,419
Alaska                     216,843
Arizona                  1,776,912
Arkansas                   862,029
California              12,289,909
...
Virginia                 1,059,721
Washington               1,763,954
West Virginia              461,666
Wisconsin                1,043,046
Wyoming                     62,478
dtype: object
```

This takes ~3 minutes to run for a single year and has a minimal memory footprint (<20 GB).

### De-duplicating Multiple Records for the Same Beneficiary

`BENE_ID` is the unique identifier which tracks an individual across states and across years. However ~3% of records in the base demographic and eligibility file are duplicated with respect to BENE_ID. 

For demographic information (`AGE`, `BIRTH_DT`, `SEX_CD`) there are very rarely discrepancies across rows for the same `BENE_ID`. In instances where there is mismatch it largely appears to be typos or small 1-digit transposition issues (e.g., birthdate on one row is '1965-02-18' and '1967-02-18' on another). This is a very small problem (<0.1%) and transposition issues of few days or months are unlikely to meaningfully impact any research. 

Simple solutions are to groupby the `BENE_ID` and take the first, last, minimum, maximum, or most commonly reported value. If we want a more nuanced solution for `BIRTH_DT`, we can exclude only records where the disagreement between the birthdate is larger than some threshold, and then decide which of the similar values to keep.

The example below removes any beneficiaries who have a discrepancy in `BIRTH_DT` of more than 1 year and who have conflicting `SEX_CD` information. We then determine demographic values as the minimum `BIRTH_DT` and the singular `SEX_CD` for that `BENE_ID`. 

```python
# Example using Pennsylvania 2018
state = 'PA'
year = 2018
file = 'taf_demog_elig_base'
bene_cols = ['BENE_ID', 'STATE_CD', 'AGE', 
             'SEX_CD', 'BIRTH_DT', 'MISG_ELGBLTY_DATA_IND']

# Read in data and filter to valid rows
df = pd.read_parquet(TMSISPath().data_path+f'{file}/year={year}/state={state}/data.parquet', 
                     columns=bene_cols,
                     filters=[('MISG_ELGBLTY_DATA_IND', '=', 0)])
                     
# Remove rows with missing beneficiary identifier
df = df[df['BENE_ID'].notnull()]

# Begin reconciling differences
df['BIRTH_DT'] = pd.to_datetime(df['BIRTH_DT'], errors='coerce')

# Calculate difference in days, exclude above threshold
s = df.groupby('BENE_ID')['BIRTH_DT'].agg(['min', 'max'])
s = (s['max'] - s['min']).dt.days
THRESHOLD = 365
to_exclude = s[s.ge(THRESHOLD)].index
df = df[~df.BENE_ID.isin(to_exclude)]

# Age, remove if there are multiple unique values
s = df.groupby('BENE_ID')['SEX_CD'].nunique()
to_exclude = s[s.gt(1)].index
df = df[~df.BENE_ID.isin(to_exclude)]

# Obtain resolved beneficiary information
df_bene = df.groupby('BENE_ID').agg(BIRTH_DT=('BIRTH_DT', 'min'),
                                    SEX_CD=('SEX_CD', 'first'))
```