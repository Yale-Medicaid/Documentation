# Identifying Providers and Facilities

It's not immediately clear how to identify providers and facilities in the T-MSIS data. Though imperfect, we use the servicing and billing provider NPIs in our work.

## NPI Dataset

The Centers for Medicare and Medicaid Services publish information about each provider through the [National Plan and Provider Enumeration System (NPPES) Downloadable File](https://www.cms.gov/medicare/regulations-guidance/administrative-simplification/data-dissemination), which is updated monthly. You can learn more about a single NPI using the CMS [NPI registry](https://npiregistry.cms.hhs.gov/search).

A [repository](https://github.com/Yale-Medicaid/NPPES_NPI) in the Yale Medicaid GitHub organization cleans the dataset and converts the output into a parquet file. The cleaned dataset is located at `/gpfs/milgram/pi/medicaid_lab/data/public/nppes/ingested/` on Milgram.

The cleaned dataset contains the provider NPI, credential, gender, taxnomoy information, and an "entity type code" that distinguishes individuals from organizations. (The entity type code can be `"individual"`, `"organization"`, or missing.)

## Providers

The `SRVC_PRVDR_NPI` column is available in the IP, LT, and OT line files. The servicing provider NPI gives "The National Provider Identifier (NPI) of the health care professional who delivers or completes a particular medical service or non-surgical procedure," according to the documentation.

In our research on emergency departments, we join the NPPES NPI dataset into our claims dataset using the `SRVC_PRVDR_NPI` column. Then, we filter the entity type code to `"individual"`. Unfortunately, less than half of our claims have an `"individual"` assigned at the servicing provider, so we cannot identify an actual doctor for the remaining majority of the claims. We've also noticed that whenever a servicing provider has entity type `"organization"`, the NPI is identical to the billing NPI.

## Facilities
