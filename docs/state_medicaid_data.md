## Overview

We have Medicaid data for the following states

| State          |     Years    | SNAP  |
| :------------- | :----------: | :---: |
| Tennessee      |  2010-2017   | N     |
| Lousiana       |  2010-2016   | N     |
| Connecticut    |  2015-2021   | Y     |
| Michigan       |  2010-2020   | N     |
| Kansas         |  2013-2018   | N     |

### Obtaining Access to State Data
Once you have obtained access to `YSPH_HPM-CC0940-MEDSPH2` you will need to log into the server and do the following:

1. open File Explorer (the one with the yellow folder icon)
2. Click on This PC on the left side
3. At the top toolbar click on Computer – Map network drive – Map Network Drive
4. Keep the default drive letter `Y:`
5. In folder use:  `\\storage.yale.edu\home\YSPH_HPM-CC0940-MEDSPH2`
6. Make sure the checkbox “Reconnect at sign-in” is checked
7. Click on finish

### Data Organization
Data for each state are stored in `YSPH_HPM-CC0940-MEDSPH2\Private Data Pipeline\Healthcare_Data\`

For all states (except CT) the data are organized:

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

- `Docs/`: contains any data dictionaries or relevant state specific information. 
- `Raw/`: contains minimally processed files obtained directly from the state. The only processing is generally to rename files and ensure files are saved in an easy to read format (e.g., .pkl or .parquet)
- `Standard/`: contains files that have been initially processed, typically data have been typecast and variabled names are mapped to match the standard schema
- `Intermediate/`: contains intermediate files that haven't been fully processed
- `Gold/`: contains final files that have been fully processed and match our desired schema. These are generally the starting files used in any research project
- `Analytic_tables`: contain any analytic extracts which are derived from the gold files. For instance, contains member-level risk adjustors for each calendar year, annual measures of high- and low-value care, and a basic member-month analytic skeleton.
- `Logs\`: contains any log files produced during the processing
- `Junk\`: scrap folder containing any temporary outputs during development. 