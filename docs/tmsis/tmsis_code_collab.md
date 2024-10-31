# Coding Collaboration

We will use `git` to collaboratively develop analyses. The most up to date, stable, version of the libraries will be found in `/gpfs/milgram/pi/medicaid_lab/code/`, while researcher specific development branches can be stored in your own project directory. 

## Set up 
### Quickstart for Git on Milgram

<span style="color:#4781BE">**SSH Key**</span>

This saves you from having to type your password or provide a token every time. YCRC provides a [thorough guide](https://docs.ycrc.yale.edu/clusters-at-yale/access/ssh/) on using and generating SSH keys, the main steps are outlined here. If you've already generated SSH keys (say to SSH into Milgram through the terminal) you can skip the generation steps. 

Within a terminal in Milgram type:

```
ssh-keygen
```

Press enter to save them in the devault location `/home/yourusername/.ssh/id_rsa` and create some passphrase as an extra layer of protection. The **public key**, saved in `~/.ssh/id_rsa.pub` is what we share, **NEVER SHARE THE PRIVATE KEY**, which is stored in the similarly named file *without* the `.pub` suffix. 

You can display the contents of the public key with 

```
cat ~/.ssh/id_rsa.pub 
```

which will show a very long string beginning with something like `ssh-rsa `. Copy the entire outut (including the ssh-ra part). 

Now you need to add that public key to your GitHub Account: [instructions are provided by GitHUb](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account#adding-a-new-ssh-key-to-your-account).

1. Click on your account icon
2. Click on Settings
3. Click "SSH and GPG keys" under "Access" on left hand menu
4. Click "New SSH Key"
5. Give it some descriptive name, paste the output from `cat ~/.ssh/id_rsa.pub` into the Key box
6. Click "Add SSH Key"

Now when you clone a repository you can use the SSH remote URL.



### Existing Projects
Navigate to the repository within Yale-Medicaid on Github. Click on the green `<> Code` button in the to right and copy the SSH url to your clipboard. 

![SSH clone](../images/tmsis_ssh.png){: style="height: 100px;width: 200px;display: block; margin: 0 auto"}
    
Within Milgram use the terminal to navigate to the directory where you'd like to clone this repository. This is very likely `/home/NETID/project/`. From there type

```
git clone URL_YOU_COPIED_ABOVE
```

which should begin cloning the directory with messages like:

```
Cloning into 'QualityMetrics'...
remote: Enumerating objects: 14, done.
remote: Counting objects: 100% (14/14), done.
remote: Compressing objects: 100% (11/11), done.
remote: Total 14 (delta 3), reused 13 (delta 2), pack-reused 0 (from 0)
Receiving objects: 100% (14/14), done.
Resolving deltas: 100% (3/3), done.
```

Navigate into that directory. 

You're likely on the main branch (since you cloned from there), which you can see with `git branch`. Since you don't want to develop on the main branch create a new branch for development and switch to it. 

```
git checkout -b NEW_BRANCH_NAME
```

and you should get a message `Switched to a new branch 'NEW_BRANCH_NAME'`. Try to make the name descriptive if the branch is for a specific task, otherwise I used something general like `anthony-dev`. 

