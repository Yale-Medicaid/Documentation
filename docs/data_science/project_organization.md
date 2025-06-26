# Project Organization

Research projects are typically lengthy, require several revisions, and often outlast the tenure of the initial primary researcher. By maintaining consistency across projects, other researchers can readily contribute to revisions or new analyses for a project they did not initially work on. The following should act as general guidelines which have proven useful in the past and can be adapted.

## User Directories
Within the group's directory, each researcher should create their own folder to have a dedicated place to work.

- Yale Server: `D:/Groups/YSPH-HPM-Ndumele/Networks/`
- Milgram: `/gpfs/milgram/project/ndumele/` 

```
Networks/
├── Anthony/
├── Beniamino/
├── Hannah/
├── Dohyun/
...
├── Jacob/
```

Within your own folder, you can create a separate folder for each distinct project. Choose a short, descriptive name so another researcher would easily know what research project the folder relates to.

```
Networks/
├── Anthony/
    ├── Network_Overlap/
    ├── LA_Plan_Effects/
    ├── TN_Plan_Effects/
    ├── MI_enrollment/
```

Within each project it helps to organize the code in one spot and the analytic products in another. The code is the most important aspect, as you want another researcher to readily know which code is responsible for all of the analyses. For instance, within the `MI_enrollment` folder the overall organization would look something like:

```
MI_enrollment/
├── README.txt
├── venv/
├── code/
    ├── main.py
    ├── analysis.py
    ├── enrollment_counts.py
    ├── sensitivity_analysis.py
    ├── project_paths.py
├── trunk/
    ├── raw/
        ├── grab_MI_claims/
            ├── claims_2010.pkl
            ├── claims_2011.pkl
            ...
            ├── diagnosis_2016.pkl
    ├── derived/
        ├── create_enrollment_table/
            ├── output/
                ├── enrollment_table.pkl
        ├── create_pregnancy_list/
            ├── output/
                ├── pregnancy.pkl
    ├── analysis/
        ├── hazard_plot/
            ├── output/
                ├── enrollment_distribution.png
                ├── hazard_by_age_monthly.png
        ├── stability_DiD_estimates/
            ├── temp/
            ├── output/
                ├── coeffs.csv
├── jupyter/ (python-specific)
    ├── sandbox.ipynb
    ├── enrollment_check.ipynb
├── .gitattributes
├── .gitignore
```

- `README.txt`: The first file someone should read, it contains any particular project-specific information or organization that is important to know and documents how to reproduce the analyses fully from start to finish.
- `venv/`: The virtual environment for the project to ensure that we don't run into library versioning issues across projects.
- `code/`: Contains all code related to the project.
    - `main.py`: A single script which can re-run all of the analyses from start to finish. For instance, in python this script might import other .py files and would have a single function that, if run, would reproduce all analyses from start to finish.
    - `other_files.py`: Individual code files which are responsible for specific analyses. For instance, it may make sense to have a different file for pulling copies of the raw data, one for the creation of the analytic table from the raw data, and another for sensitivy analyses.
- `trunk/`: This folder contains all data, derived tables and analytic extracts for this project.
    - `raw/`: An optional folder. Would contain copies of Medicaid data which are pulled into this specific project. The raw data can be accessed directly from the storage at yale drive: YSPH_HPM-CC0940-MEDSPH, or on Milgram: `/gpfs/milgram/pi/medicaid_lab/data/`. This is useful if you need a targeted or smaller subset of data to work from.
    - `derived/`: This folder contains any data which are created specifically for this project. Within `derived/`, each folder represents some atomic manipuation or derivation, which may be used in other processes or to create analytic products. The outputs stored in these directories are almost always data files (.pkl, .parquet).
        - In python, the method responsible for running the analysis should share the same name as the folder. For instance, the method `create_enrollment_table()` would be responsible for generating `create_enrollment_table/output/enrollment_table.pkl`, making it easy for anyone to identify the code responsble.
    - `analysis/`: This folder contains the analytic endpoints, typically from analyses of some data produced in `derived/`. The outputs stored in these directories are typically figures, tables, or smaller data files containing coefficients or data that would create a manuscript table.
- `jupyter/`: A potential sandbox to test and develop code. (In an R project, this could be called `qmd`, `rmd`, `notebooks`, or something similar.) Should be treated as temporary.
- `.gitfiles`: If the folder is version controlled with git. DATA SHOULD NEVER BE PUSHED. (Also avoid pushing any information that could recreate raw data.) Because the code exists in one directory, you can ignore `trunk/` and only maintain `code/`.

## Project Templates

We recommend starting your R projects from the [template repository](https://github.com/Yale-Medicaid/R_project_template/) on the Yale Medicaid Lab GitHub page. Follow the instructions in the read-me file in that repository.
