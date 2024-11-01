# SCALE Medicaid Onboarding and Data Science Documentation

All Material is available on the **[Website](https://yale-medicaid.github.io/Documentation/)**.

## Contributing
If you'd like to contribute to the group's documentation you can install `mkdocs` which allows you to locally test changes before pushing and deploying the markdown and .yml files to GitHub Pages.

From the terminal can use [miniconda](https://docs.anaconda.com/miniconda/) to create a separate minimal environment named "doc_env":

```
conda create -n doc_env
```

and install the required mkdocs and mkdocs-materials here:

```
conda install -n doc_env conda-forge::mkdocs
conda install -n doc_env mkdocs-material
```

Activate this local environment:

```
conda activate doc_env
```

Navigate to the local directory where the Documentation repository is saved and deploy locally:

```
mkdocs serve
```

This will provide output indicating any warnings or issues. The last line shows where the website is locally served, which can be opened in any web browser.

```
INFO    -  [14:38:18] Serving on http://127.0.0.1:8000/
```

If the site is deploying successfully, push the changes to GitHub. 