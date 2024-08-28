# T-MSIS TAF RIF

CMS has Transformed Medicaid Statistical Information System (T-MSIS) Analytic Files (TAF) from which they create Reasearch Identifiiable Files (RIF) to meet the needs of researchers. We're just going to coloquially refer to this data as T-MSIS data (though it's technically the TAF RIF). CMS has provided substantial documentation about the files [here](https://www.medicaid.gov/medicaid/data-systems/macbis/medicaid-chip-research-files/transformed-medicaid-statistical-information-system-t-msis-analytic-files-taf/index.html)

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
	- Special Requests: request access to the `medicaid` group.

### Data Organization

T-MSIS data initially come as ~TB files broken up into several parts. We have partioned the files by year and by state to allow for more manageable chunks of data to be used for initial exploration. The clusters can then scale any analyses to a national level. 

The starting point for most projects will be the year-by-state partitioned files, located at `/gpfs/milgram/pi/medicaid_lab/data/cms/ingested/TMSIS_TAF/`. Within this directory there is a folder for each specific file provided by CMS:

```
TMSIS_taf/
├── taf_demog_elig_base/
├── taf_demog_elig_dates/
├── taf_demog_elig_disability/
├── taf_demog_elig_hh_spo/
├── taf_demog_elig_mngd_care/
├── taf_demog_elig_mny_flw_prsn/
├── taf_demog_elig_waiver/
├── taf_inpatient_header/
├── taf_inpatient_line/
├── taf_inpatient_occurrence/
├── taf_long_term_header/
├── taf_long_term_line/
├── taf_long_term_occurrence/
├── taf_other_services_header/
├── taf_other_services_line/
├── taf_other_services_occurrence/
├── taf_rx_header/
└── taf_rx_line/
```

Within each specific file folder the organization is state, then year:

```
taf_demog_elig_base/
├── year=2016/
│   ├── state=AK/
│   │   └── data.parquet
│   ├── state=AL/
│   │   └── data.parquet
│   └── ...
├── year=2017/
│   ├── state=AK/
│   │   └── data.parquet
│   ├── state=AL/
│   │   └── data.parquet
│   └── ...
└── ...
```

### ResDAC Documentation

ResDAC provides extensive documentation for all of the files. Because of this we have maintained the original column names and datatypes, so all the documentation presented on this website applies to the standardized files as well as the raw files.

* [Documentation for demographic and eligibility files](https://resdac.org/cms-data/files/taf-de)
* [Documentation for inpatient files](https://resdac.org/cms-data/files/taf-ip)
* [Documentation for pharmacy files](https://resdac.org/cms-data/files/taf-rx)
* [Documentation for other services files](https://resdac.org/cms-data/files/taf-ot)
* [Documentation for long term care files](https://resdac.org/cms-data/files/taf-lt)
