# Project Links

This page holds links to the documentation sites and code for individual
projects that our team works on.

## TMSIS Data Ingestion ([link](https://yale-medicaid.github.io/TMSIS_data_documentation/))([code](https://github.com/Yale-Medicaid/TMSIS_ingestion))

The TMSIS ingestion pipeline is responsible for taking raw TMSIS data purhased
from CMS and transforming it into a set of standardized [parquet
datasets](https://parquet.apache.org/). This standardization and formatting
makes the data signficantly (> 100x) faster to query against and easier to work
with. The pipeline also ensures that all data is kept up-to-date as the logic
required to process it changes, ensuring the integrity of the final analytic
files.

## Physician-to-Voter linkage ([link](https://yale-medicaid.github.io/physician_to_voter/))([code](https://github.com/Yale-Medicaid/physician_to_voter))

The physician-to-voter pipeline is responsible for merging physician data onto
L2 voter file using Locality Sensitive Hashing and Random Forests. The result
is a fast, credible linkage of physician data to information including home
address, party registration, predicted income and more.
