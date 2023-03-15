The documentation stored in this repository is built and hosted by Read the Docs.

# Previewing the site locally

MkDocs is a static website generator which is used by Read the Docs to build the website. Individual pages are markdown files, and the website configuration is managed with a YAML file.

MkDocs can be used locally to build and view the website. It can be installed using the ```pip``` or ```conda``` package managers.

```bash
$ conda install -c conda-forge mkdocs
```

MkDocs also includes a server allowing you to view the website in real time as you work on it by running the following command from the directory containing the mkdocs YAML configuration file.

```bash
$ mkdocs serve
```

By default the website is served over port 8000 and can be accessed from your browser via ```localhost:8000``` (```http://127.0.0.1:8000```).
