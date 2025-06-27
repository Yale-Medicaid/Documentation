
## Overview

Parallelization is helpful when you need to run a time/memory intensive script many times or over many states.  Parallelization helps run multiple states at the same time to make code run quicker and more efficiently.  In this document, I will show how to convert a typical python script that loops over states into something that can be run in parallel.

Note: Parallelization is, of course, helpful for more things than just running a bunch of states at once.  See [this resource](https://docs.ycrc.yale.edu/clusters-at-yale/job-scheduling/dsq/) to learn more about dSQ and its many capabilities.

## Basic Example

When you write a python script use for parallelization, you structure your code slightly differently.  For example, a typical python script that loops over states might look something like this.

```python
def my_function(state):
	# Your processing code here
	print(f"Processing state: {state}")

states = ["AL", "AK", "AZ", "AR", "..."] # etc.

for state in states:
	my_function(state)

```

The same script written in a parallel-ready format would look like this:
```python
def my_function(state):
    # Your processing code here
    print(f"Processing state: {state}")

if __name__ == "__main__":
    # The state is now passed as a command-line argument
    state_to_process = sys.argv[1]
    my_function(state_to_process)
```

This needs to be used in conjunction with a `joblist.txt` file that looks like this (assuming your parallel-ready .py file is called my_script.py)

```text
python my_script.py AL
python my_script.py AK
python my_script.py AZ
python my_script.py AR
# ... and so on for all your states
```

In our context, you'd want to load miniconda and your environment too, so it might look something like this

```text
module load miniconda; conda activate my_env; python my_script.py AL
module load miniconda; conda activate my_env; python my_script.py AK
# ... etc.
```

Next, you want to go to your command line connected to the YCRC clusters and load dSQ onto your path with `module load dSQ`.  Then we want to tell dSQ to make our bash script.  For example, we might run
`dsq --job-file joblist.txt --mem-per-cpu 4g -t 20:00 --mail-type ALL`
to request 4 gigs and 20 minutes *per job*.  See more on the settings you can specify [here](https://docs.ycrc.yale.edu/clusters-at-yale/job-scheduling/dsq/).  This will create a script called `dsq_joblist-yyyy-mm-dd.sh` where yyyy-mm-dd is today's date.  The script will look something like this

```bash
#!/bin/bash
#SBATCH --array 0-999
#SBATCH --output dsq-joblist-%A_%3a-%N.out
#SBATCH --job-name dsq-joblist
#SBATCH --mem-per-cpu 4g -t 20:00 --mail-type ALL

# DO NOT EDIT LINE BELOW
/path/to/dSQBatch.py --job-file /path/to/joblist.txt --status-dir /path/to/here
```
You can edit this file as desired.  If you get rid of the `-%A_%3a-%N` in the `.out` file, it will put all the output in one file instead of creating a separate file for each job.

Finally, make sure all of these files (`my_script.py`, `joblist.txt`, and `dsq_joblist-yyyy-mm-dd.sh`) are in your desired folder and submit your batch script using `sbatch dsq-joblist-yyyy-mm-dd.sh`.

If you need more customization or run into errors, [YCRC](https://docs.ycrc.yale.edu/clusters-at-yale/job-scheduling/dsq/) has some great suggestions of how to start and diagnose.

Note: Unfortunately, there is not a way to customize how much memory each job gets, meaning if you run all states in one script you'd have to assign CA and HI the same amount of memory.  One potential workaround is to create multiple scripts - a "big state" and "small state" script for example.

## Another Example
Below is the group of files I made to run a very simple function that imports the demographic file and reports the number of unique `BENE_ID`s.  This runs for 5 states but obviously could be run for more.
`parallel_test.py`
```python
import pyarrow.parquet as pq
import pyarrow.compute as pc
import pyarrow as pa
import sys

p = '/home/as4765/medicaid_lab/data/cms/ingested/TMSIS_taf/taf_demog_elig_base/'
years = range(2016,2022)

def function(state):
    print(f'Processing state: {state}')
    for year in years:
        schema = pq.read_schema(f'{p}year={year}/state={state}/data.parquet')
        new_schema = schema.set(schema.get_field_index('year'), pa.field('year', pa.int64()))

        table = pq.read_table(f'{p}/year={year}/state={state}/data.parquet',
                              columns = ['BENE_ID'],
                              schema = new_schema)
        unique_ids = pc.unique(table['BENE_ID'])
        print(f'Year: {year}, State: {state}, Rows: {len(unique_ids)}')

if __name__ == "__main__":
    state_to_process = sys.argv[1]
    function(state_to_process)
```

`joblist_parallel_test.txt`
```text
module load miniconda; conda activate test_environ; python3 ./parallel_test.py AZ
module load miniconda; conda activate test_environ; python3 ./parallel_test.py HI
module load miniconda; conda activate test_environ; python3 ./parallel_test.py MO
module load miniconda; conda activate test_environ; python3 ./parallel_test.py NH
module load miniconda; conda activate test_environ; python3 ./parallel_test.py WA
```
The following batch script is made using the following prompt:
`dsq --job-file joblist_parallel_test.txt --mem-per-cpu 20g -t 20:00 --mail-type ALL`

`dsq-joblist_parallel_test-2025-06-26.sh`
```bash
#!/bin/bash
#SBATCH --output dsq-joblist_parallel_test.out
#SBATCH --array 0-4
#SBATCH --job-name dsq-joblist_parallel_test
#SBATCH --mem-per-cpu 20g -t 20:00 --mail-type ALL

# DO NOT EDIT LINE BELOW
/gpfs/milgram/apps/hpc.rhel7/software/dSQ/1.05/dSQBatch.py --job-file /gpfs/milgram/project/ndumele/as4765/misc/joblist_parallel_test.txt --status-dir /gpfs/milgram/project/ndumele/as4765/misc
```
