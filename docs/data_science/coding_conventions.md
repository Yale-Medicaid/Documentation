# Coding Conventions

These are some general guidelines that help maintain consistency across projects.

## Naming Conventions

- Do not include the following characters in filenames, function names or variable names: spaces, periods, most other special characters (underscore and dash are okay)
- Keep names short, but descriptive.
- Keep naming conventions consistent within a project
    - Lowercase, separate words by an underscore.
        - `enrollment_table.csv`
        - `claims_2012.parquet`
    - CamelCase, words denoted by capilization
        - `EnrollmentTable.csv`
        - `Claims2012.parquet`

### Python-Specific Naming Conventions

Python often has it's own general style guidlines which are similar to the above, but vary depending upon what is being named. The general naming convention rules still apply with the following modifications

- functions: lowercase with words separated by an underscore
    - `def grab_raw_claims()`
- variables: lowercase with words separated by an underscore
    - `num_days = 31`
    - constants can be all caps with words separated by underscores: `DAYS_IN_WEEK = 7`
- classes: CamelCase
    - `pd.DataFrame`
    - `class MedicaidEnrollee()`
    - Instances of a class: lowercase, same as variables
        - `df = pd.DataFrame()`
