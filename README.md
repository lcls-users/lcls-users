The documentation stored in this repository is built and hosted by Read the Docs.

# Contributing

1. Find / create an [Open Issue](https://github.com/lcls-users/lcls-users/issues) you'd like to work on.

2. [Fork this repository](https://github.com/lcls-users/lcls-users/fork) and clone a version locally (`git clone ssh://git@github.com/<user>/lcls-users.git`). Setup `lcls-users` as the upstream: `git remote add upstream https://github.com/lcls-users/lcls-users.git`.

3. Create a new feature branch to do work (`git checkout -b <new-branch-name>`).

4. Run the steps below to preview locally and make edits.  Then commit your changes (`git commit`).

5. Push your changes (`git push -u origin <new-branch-name>`) and go to your lcls-users repo on github to create a pull request.

6. Sync your main branch from upstream:

```
    git checkout main
    git pull upstream main
```

# Previewing The Site Locally

MkDocs is a static website generator which is used by Read the Docs to build the website. Individual pages are markdown files, and the website configuration is managed with a YAML file.

MkDocs can be used locally to build and view the website. It can be installed using the ```pip``` or ```conda``` package managers.

```bash
$ pip3 install mkdocs # for pip
$ conda install -c conda-forge mkdocs # for conda
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
