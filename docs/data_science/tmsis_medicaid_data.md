# T-MSIS TAF RIF

CMS has Transformed Medicaid Statistical Information System (T-MSIS) Analytic Files from which they create Reasearch Identifiiable Files (RIF) to meet the needs of researchers. We're just going to coloquially refer to this data as T-MSIS data (though it's technically the TAF RIF). CMS has provided substantial documentation about the files [here](https://www.medicaid.gov/medicaid/data-systems/macbis/medicaid-chip-research-files/transformed-medicaid-statistical-information-system-t-msis-analytic-files-taf/index.html)

## Overview

We have Medicaid data for the following time periods

| Year  |   Purchased  | Partitioned  | Files       |
| :---- | :----------: | :----------: | :---------- |
| 2016  |  Y           | Y            | Demo, Out, In, Oth, Rx, LTC, Plan, Prov |
| 2017  |  Y           | Y            | Demo, Out, In, Oth, Rx, LTC, Plan, Prov |
| 2018  |  Y           | Y            | Demo, Out, In, Oth, Rx, LTC, Plan, Prov |
| 2019  |  Y           | Y            | Demo, Out, In, Oth, Rx, LTC, Plan, Prov |
| 2020  |  Y           | Y            | Demo, Out, In, Oth, Rx, LTC, Plan, Prov |
| 2021  |  Y           | N            | |

### Obtaining Access to T-MSIS Data
Due to the size of the national data set, T-MSIS data are housed in the HIPAA aligned Milgram cluster at Yale Center for Research Computing. The first step is to [request an account](https://research.computing.yale.edu/support/hpc/account-request)

1. Make sure Anthony is aware you are requesting an account he will need to approve the request
2. Fill out the form
	- Department or School: Yale School of Public Health
	- Are you a Principal Investigator: No
	- Principal Investigator: Chima Ndumele
	- Clusters: Milgram
	- Special Requests: request access to the medicaid_lab group.

### Data Organization

T-MSIS data initially come as ~TB files broken up into several parts. We have partioned the files by state and by year to allow for more manageable chunks of data to be used for initial exploration. The clusters can then scale any analyses to a national level. The data organization is explianed in the [T-MSIS Data Ingestion Documentation](https://yale-medicaid.github.io/TMSIS_data_documentation/)
