Setting up a virtual environment for each project is highly recommended. This ensures the code remains reproducible even if packages required by other projects are modified. The following shows one way to set up virtual environments using GitBash, other options are available using the cmd prompt if you're knowledgeable of that. 

The [Python Packaging User Guide](https://packaging.python.org/en/latest/guides/installing-using-pip-and-virtual-environments/) contains step-by-step instructions for creating and using a virtual environment, the steps below are explicit to one way to do so on our server.  

## Python Virtual Environments
### Creating a New Virtual Environment

To set up a virtual environment for a project you can navigate to the project folder (for instance `Anthony/NewProject/`) and activate GitBash. (For example, if using the file explorer, right click and select `GitBash Here`). This will open GitBash and you should see the following:

```
your_netID@ysph-hpm MINGW64 /d/Groups/YSPH-HPM-Ndumele/Networks/Anthony/NewProject
$
```

Create a new virtual environment by typing:
```
python -m venv my_venv
```

This will create a virtual environment in the folder named `my_venv`, so if you would like it named something different you can change the name of the last argument. 

### Activating & Adding Packages to the Virtual Environment
Now that a virtual environment has been created, we can activate the virtual environment (named `my_venv`) with GitBash by typing:

```
source my_venv/Scripts/activate
```

After this is done you will know it's successful because GitBash will indicate the environment being used within `()`
```
(my_venv)
al686@ysph-hpm MINGW64 /d/Groups/YSPH-HPM-Ndumele/Networks/Anthony/NewProject
$
```

Packages can then be installed into this virtual environment with pip, so to install the latest version of numpy we would just type `pip3 install numpy`. The packages will then be saved to `.../my_venv/Lib/site_packages/`

### Setting up Jupyter Notebooks
Jupyter notebooks are great for exploratory data analysis or code development. In order to run jupyter notebooks we will need to install `ipykernel` and pass the virtual environment to the name. This is accomplished with the following two commands:

```
pip3 install ipykernel

python -m ipykernel install --user --name=my_venv
```

Then, you can open up a juptyer notebook within GitBash with `jupyter notebook`. Now when you create a new notebook you will be able to select this virtual environment.


### Deactivating the virtual environment
Typing `deactivate` will deactivate the currently activated virtual environment. 