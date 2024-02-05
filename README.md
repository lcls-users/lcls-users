The documentation stored in this repository is built and hosted by Read the Docs.

# Previewing The Site Locally

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

# A note on relative links
Due to how the site is built and documents are linked, only true relative links can be used when linking between internal pages. I.e. if you want to link to another page on the website, use a relative path and include the extension of the file you would like to link to. This is instead of using the intended output destination as you may be accustomed to. For example, if you are working on a page that resides in the `before` directory, and would like to link to another page, `other_page.md`, you can do so like:
- `[other page link](other_page.md)`, if the other page is also in the `before` directory. 
- Or, `[other page link](../during/other_page.md)` if it is in another directory, such as `during`. 

This will allow the links to properly resolve. Links of this format: `[other page link](/before/other_page/)` will NOT work properly when the website is published.
