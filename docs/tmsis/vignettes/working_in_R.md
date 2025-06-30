# Working in R

The `arrow` package in R opens a connection to a dataset without reading it into memory. [Core functions](https://arrow.apache.org/docs/r/reference/acero.html) from `dplyr` and some other packages are translated into Arrow, then [executed in C++](https://arrow.apache.org/docs/cpp/acero/overview.html) to [cut runtime](https://r4ds.hadley.nz/arrow.html) by as much as 100x.

`arrow` also works nicely with `duckdb`, a database language that can work with even larger datasets. I occassionally send data to `duckdb` (using `arrow::to_duckdb()`) to run filters on large datasets, then bring it back with `arrow::to_arrow()` — no need to actually create a database.

In general, you should do as much processing as possible before pulling data into R. Be creative! Some functions you ordinarily use might not be translatable into Arrow — you might have to re-implement it with simple `dplyr` commands.

## Opening Datasets with Arrow

A "dataset" is a collection of parquet files. To open a single-file dataset stored at `path_to_data/my_data.parquet`, run `arrow::open_dataset("path_to_data")`.

This generalizes to a [hive-partitioned dataset](https://r4ds.hadley.nz/arrow.html#partitioning), like our T-MSIS TAF data. If you want to read every state's 2016 inpatient header, you can run `arrow::open_dataset("/gpfs/milgram/pi/medicaid_lab/data/cms/ingested/TMSIS_taf/taf_inpatient_header/year=2016/")`. Arrow will tell you that you opened a dataset with 52 files. Also notice that Arrow creates a column called `state` to reflect the partitioning. If you read every year and every state (with `arrow::open_dataset("/gpfs/milgram/pi/medicaid_lab/data/cms/ingested/TMSIS_taf/taf_inpatient_header/")`), Arrow would also create a `year` column.

Sometimes opening a hive-partitioned dataset can cause some headaches. Suppose you have a character-type `year` column in each dataset already. If you run `arrow::open_dataset("/gpfs/milgram/pi/medicaid_lab/data/cms/ingested/TMSIS_taf/taf_inpatient_header/")`, Arrow will try to also add an integer `year` column, and you'll hit an error. If this happens, you can instead pass a vector of parquet files to `arrow::open_dataset()`:

```r
list.files("/gpfs/milgram/pi/medicaid_lab/data/cms/ingested/TMSIS_taf/taf_inpatient_header/",
           pattern = "*.parquet",
           full.names = TRUE,
           recursive = TRUE) |>
  arrow::open_dataset()
```

## 'Pivoting' in Arrow

`tidyr::pivot_longer()` is not yet available in Arrow. This can be disruptive, but you should be able to work around it. If your dataset isn't too large, do all the processing you can, pull the data into R, and pivot. However, if your dataset is still huge, you can manually pivot in Arrow.

Think about what a pivot does — it stacks multiple columns into one dataset. Suppose your dataset has columns ID, A, B, and C. If you want to stack columns A, B, and C, you can save three intermediate datasets with columns ID, name, and value, where name is "A" and value is the value from column A (and the same for B and C). Then, read those three files into a new dataset.

## Anonymous Functions in Arrow

`dplyr` has a fantastic `dplyr::across()` function to let you run functions on multiple columns. This is implemented in Arrow, but only for named functions, i.e., you cannot pass an [anonymous function](http://adv-r.had.co.nz/Functional-programming.html#anonymous-functions). The workaround is easy — simply name your function!

```r
# DOESN'T WORK

df |>
  dplyr::mutate(dplyr::across(some_columns, \(x) !is.na(x)))

# WORKS

not_is.na <- function(x) {
  !is.na(x)
}

df |>
  dplyr::mutate(dplyr::across(some_columns, not_is.na))
```

It seems a bit silly, but that's what works right now.

## Other Tips and Tricks

- Arrow enforces separate data types for `int32` and `int64`. If your joins don't work, this could be why!
- You can join a tibble into an Arrow dataset, but not the other way around.
- You can't run `dplyr::bind_rows()` in Arrow. Instead, save each piece as a parquet file and open them together.
