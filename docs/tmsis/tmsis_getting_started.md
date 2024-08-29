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

Once you've obtained access, sign into [Milgram OnDemand](ood-milgram.ycrc.yale.edu). You may need to be connected to the Yale VPN even if you're on the Yale Secure Wi-Fi.

There are three ways you'll regularly interact with Milgram.

### File Explorer

First, you'll want to peruse files. Open OnDemand, then click on Files -> Home Directory in the navbar. That should take you to `/home/your_net_id/`. To see the Medicaid lab data and code, click "change directory" and enter `/gpfs/milgram/pi/medicaid_lab/`.



### Coding
You should do most of your own work in the `project` subfolder

### Terminal

- terminal: The special requests are not always honored; once you obtain access to Milgram, check your permissions by typing `groups` (or `groups [your_net_id]`) in the terminal. If you see `medicaid`, you're good to go. If not, let Anthony know.; also mention alias for medicaid_lab

## T-MSIS Tech Stack

## Getting Help
