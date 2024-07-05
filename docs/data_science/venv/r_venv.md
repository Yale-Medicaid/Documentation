# Virtual Environments in R

The most common way to use a virtual environment in R is through the `renv` package, developed by Posit.  The best place to learn about using renv is the package's introductory vignette [here](https://rstudio.github.io/renv/articles/renv.html), which provides a brief overview of the package and everything you need to get started.

You can also see examples of `renv` being used in practice in many of our R codebases on github, including:

1. All of the R examples in the [TMSIS documentation](https://github.com/Yale-Medicaid/TMSIS_data_documentation/tree/main/R), which uses `renv` so new users can quickly install the R package stack needed to run queries against our TMSIS data.
2. The [physician to voter codebase](https://github.com/Yale-Medicaid/physician_to_voter), which uses `renv` to ensure reporoducibility and make sure that it will still run even if a server user updates their package libraries.
3. The [TMSIS ingestion pipeline](https://github.com/Yale-Medicaid/TMSIS_ingestion), which uses `renv` to help ensure that all maintainers are using the same version of the dependency packages.