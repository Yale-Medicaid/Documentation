# Getting Started with T-MSIS

## Obtaining Access to T-MSIS Data
Due to the size and nature of the national data set, T-MSIS data are housed in the HIPAA-aligned [Milgram cluster](https://docs.ycrc.yale.edu/clusters/milgram/) at Yale Center for Research Computing. The first step is to [request an account](https://research.computing.yale.edu/support/hpc/account-request).

1. Make sure Anthony is aware you are requesting an account he will need to approve the request.
2. Fill out the form
	- Department or School: Yale School of Public Health
	- Are you a Principal Investigator: No
	- Principal Investigator: Chima Ndumele
	- Clusters: Milgram
	- Special Requests: request access to the `medicaid` group.

## Using Milgram

Once you've obtained access, sign into [Milgram OnDemand](https://ood-milgram.ycrc.yale.edu/). You may need to be connected to the Yale VPN even if you're on the Yale Secure Wi-Fi.

There are three ways you'll regularly interact with Milgram.

### Terminal

You'll use the terminal to run larger jobs and perhaps to explore files. You can open the terminal in a Remote Desktop on Milgram OnDemand, or you can [set up an SSH connection](https://docs.ycrc.yale.edu/clusters-at-yale/access/ssh/) from your machine. We highly recommend the latter.

Open the terminal (however you choose) and type `groups`. You should see `medicaid`; if you don't try typing `groups your_net_id`. If you still don't see `medicaid`, your request for access was not honored and you should contact Anthony. You won't have access to the Medicaid data until your permissions are updated.

To make your life easier, you may want to add a symlink at your home directory to the `medicaid_lab` folder. You can do this by typing `ln -s /gpfs/milgram/pi/medicaid_lab /home/your_net_id`. This isn't necessary but may be helpful.

### File Explorer

You'll probably want to peruse the Medicaid lab files. Open OnDemand, then click on Files -> Home Directory in the navbar. That should take you to `/home/your_net_id/`. If you set up the symlink correctly, you should be able to click on `medicaid_lab`. If not, click "change directory" and enter `/gpfs/milgram/pi/medicaid_lab/`.

!!! warning "Don't download files"

    It's easy—too easy—to accidentally download a file in the explorer. T-MSIS data are only allowed to live on Milgram, so do not download them. Furthermore, don't download any files with information that could possibly recreate individual-level claims data.

To make it a bit harder to download files, you can use the Remote Desktop app. The interface isn't as nice, but it's good to be careful.

### Coding

Start your first project as a subfolder in `/home/your_net_id/project/`. Keep in mind that this is actually a symlink to `/gpfs/milgram/project/ndumele/your_net_id/`.

Then, choose your preferred OnDemand app and start coding! Most work at the lab is conducted in R (use RStudio Server) or Python (use Jupyter).

For most T-MSIS tasks, the interactive session has enough computing power to work on one year of data in a smaller state, e.g., South Dakota in 2018. When starting your session, you should be able to request six hours of one CPU core with 30 GB of RAM. When you want to run larger analyses, you'll need to [run a job with SLURM](https://docs.ycrc.yale.edu/clusters-at-yale/job-scheduling/).

## T-MSIS Tech Stack

Begin by reading the [ResDAC documentation](tmsis_medicaid_data.md#resdac-documentation). Then, you'll want to familiarize yourself with the tech stack the Medicaid lab uses.

We store all our data in parquet files. R programmers can use the `arrow` package to connect to these files, and Python programmers can use `pyarrow`.

If you haven't used Arrow or the parquet format before, first read [Hadley Wickham's introduction](https://r4ds.hadley.nz/arrow). The exercises are quite helpful. You may find the following material helpful additional reading:

- [Function reference](https://arrow.apache.org/docs/r/reference/acero.html)
- [A workshop notebook](https://arrow-user2022.netlify.app/hello-arrow.html)
- [An online book by Apache](https://arrow.apache.org/cookbook/r/)
- [The R `arrow` package page](https://arrow.apache.org/docs/r/)

## Getting Help

It takes some time to get started, but there are many resources to help. Anthony and other folks at the lab can assist with most questions, which you can ask individually or through the Medicaid lab's T-MSIS Slack channel. Issues with Milgram—including access, permissions, package installation errors, and code execution issues—should be directed to the [YCRC support desk](https://research.computing.yale.edu/support).
