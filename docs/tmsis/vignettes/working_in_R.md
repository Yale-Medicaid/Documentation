# Working in R

The examples below are in Python. Similar logic applies when working in R, with a small caveat. The `arrow` package in R opens a connection to the dataset without reading it into memory. [Core functions](https://arrow.apache.org/docs/r/reference/acero.html) from `dplyr` and some other packages are translated into Arrow, then [executed in C++](https://arrow.apache.org/docs/cpp/acero/overview.html) to [cut runtime](https://r4ds.hadley.nz/arrow.html) by as much as 100x.

`arrow` also works nicely with `duckdb`, a database language that can work with even larger datasets.
