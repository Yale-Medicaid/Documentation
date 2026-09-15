# State Medicaid Data

## Overview

We have Medicaid data extracts from the following states:

| State          |     Years    | SNAP  |
| :------------- | :----------: | :---: |
| Tennessee      |  2010-2017   | N     |
| Lousiana       |  2010-2016   | N     |
| Connecticut    |  2018-2024   | Y     |
| Michigan       |  2010-2020   | N     |
| Kansas         |  2013-2018   | N     |
| Washington     |  2010-2016   | N     |

### Obtaining Access to State Data
State data are accessible on Milgram and are located in `gpfs\milgram\pi\medicaid_lab\data\state_medicaid`. Follow the instructions for [Obtaining Access to T-MSIS Data](../tmsis/tmsis_getting_started.md) to create an account and access Milgram. 

### Data Organization
Data for each state are stored in `YSPH_HPM-CC0940-MEDSPH2\Private Data Pipeline\Healthcare_Data\`.

For all states (except CT) the data are organized as follows:

```
State_Abbreviation/
├── Docs/
├── Raw/
	├── Original/
		├── compressed/
├── Standard/
├── Intermediate/
├── Gold/
├── Analytic_Tables/
├── Logs/
├── Junk/
```

- `Docs/`: Contains any data dictionaries or relevant state-specific information.
- `Raw/`: Contains minimally processed files obtained directly from the state. The only processing is generally to rename files and ensure files are saved in an easy to read format (e.g., .pkl or .parquet).
- `Standard/`: Contains files that have been initially processed. Typically, data have been typecast and variabled names are mapped to match the standard schema.
- `Intermediate/`: Contains intermediate files that haven't been fully processed
- `Gold/`: Contains final files that have been fully processed and match our desired schema. These are generally the starting files used in any research project.
- `Analytic_Tables/`: Contains any analytic extracts which are derived from the `Gold/` files. For instance, this folder could contain member-level risk adjustors for each calendar year, annual measures of high- and low-value care, and a basic member-month analytic skeleton.
- `Logs/`: Contains any log files produced during the processing.
- `Junk/`: Scrap folder containing any temporary outputs during development.
