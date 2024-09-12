# Identifying Providers and Facilities

It's not immediately clear how to identify providers and facilities in the T-MSIS data. Though imperfect, we use the servicing and billing provider NPIs in our work.

This vignette is inspired by our work on the T-MSIS Other Services file. YMMV on the other T-MSIS datasets.

## NPI Dataset

The Centers for Medicare and Medicaid Services publish information about each provider through the [National Plan and Provider Enumeration System (NPPES) Downloadable File](https://www.cms.gov/medicare/regulations-guidance/administrative-simplification/data-dissemination), which is updated monthly. You can learn more about a single NPI using the CMS [NPI registry](https://npiregistry.cms.hhs.gov/search).

A [repository](https://github.com/Yale-Medicaid/NPPES_NPI) in the Yale Medicaid GitHub organization cleans the dataset and converts the output into a parquet file. The cleaned dataset is located at `/gpfs/milgram/pi/medicaid_lab/data/public/nppes/ingested/` on Milgram.

The cleaned dataset contains the provider NPI, credential, gender, taxonomy information (though we suggest using taxonomy values from NUCC, located at `/gpfs/milgram/pi/medicaid_lab/data/public/nucc_taxonomy/ingested/nucc_taxomony/` on Milgram), and an "entity type code" that distinguishes individuals from organizations. The entity type code can be `"individual"`, `"organization"`, or missing.

## Providers

The `SRVC_PRVDR_NPI` column is available in the IP, LT, and OT line files. The servicing provider NPI gives "The National Provider Identifier (NPI) of the health care professional who delivers or completes a particular medical service or non-surgical procedure," according to the documentation.

In our research on emergency departments, we join the NPPES NPI dataset into our claims dataset using the `SRVC_PRVDR_NPI` column. Then, we filter the entity type code to `"individual"`. Unfortunately, less than half of our claims in the T-MSIS Other Services file have an `"individual"` assigned at the servicing provider, so we cannot identify an actual doctor for the remaining majority of the claims. We've noticed that whenever a servicing provider has entity type `"organization"`, the servicing NPI is identical to the billing NPI; that is, the facility overall is the "health care professional" who provides service.

Though the servicing provider is assigned at each line on a claim, we've found that a single claim often has the same `SRVC_PRVDR_NPI` value on each line. This isn't universal, though, so you should take care if selecting a "primary" `SRVC_PRVDR_NPI`.

## Facilities

It's easier to identify a facility, at least in the Other Services file. The `BLG_PRVDR_NPI` column is the "The National Provider ID (NPI) of the billing entity responsible for billing a patient for healthcare services. The billing provider can also be servicing, referring, or prescribing provider. Can be admitting provider except for Long Term Care," according to the documentation. The `BLG_PRVDR_NPI` column appears in the IP, LT, OT, and RX header files.

We've found the vast majority (98-99%) of `BLG_PRVDR_NPI` entity type codes are `"organization"`, which is what we want. Again, this may not be true outside the Other Services file.

Also keep in mind that some facilities may have multiple NPI values. This can provide greater granularity to identify parts of a hospital, but it may affect any facility-level aggregations.
