# Identifying Providers and Facilities

It's not immediately clear how to identify providers and facilities in the T-MSIS data. Though imperfect, we use the servicing and billing provider NPIs in our work.

## NPI Dataset

The Centers for Medicare and Medicaid Services produce a file with information about each provider through the [National Plan and Provider Enumeration System (NPPES) Downloadable File](https://www.cms.gov/medicare/regulations-guidance/administrative-simplification/data-dissemination), which is updated roughly monthly. A [repository](https://github.com/Yale-Medicaid/NPPES_NPI) in the Yale Medicaid GitHub organization cleans the dataset and converts the output into a parquet file. The cleaned dataset is located at `/gpfs/milgram/pi/medicaid_lab/data/public/nppes/ingested/` on Milgram.

The cleaned dataset contains the provider NPI, credential, gender, taxnomoy information, and an "entity type code" that distinguishes individuals from organizations. The entity type code can be `"individual"`, `"organization"`, or missing.

## Providers

The `SRVC_PRVDR_NPI` column is available in the IP, LT, and OT line files. The servicing provider NPI gives "The National Provider Identifier (NPI) of the health care professional who delivers or completes a particular medical service or non-surgical procedure," according to the documentation.

However, not every servicing provider NPI can be used to identify an actual person. In our research on emergency departments, we require the servicing provider entity type code to be "individual."
