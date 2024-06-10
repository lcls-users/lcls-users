The documentation stored in this repository is built and hosted by Read the Docs.

# Contributing

1. Find / create an [Open Issue](https://github.com/lcls-users/lcls-users/issues) you'd like to work on.

2. [Fork this repository](https://github.com/lcls-users/lcls-users/fork) and clone a version locally (`git clone ssh://git@github.com/<user>/lcls-users.git`). Setup `lcls-users` as the upstream: `git remote add upstream https://github.com/lcls-users/lcls-users.git`.

3. Create a new feature branch to do work

   - Option 1: In the right sidebar under "Development", click Create a branch. If the issue already has a linked branch or pull request, select and click Create a branch. [more info](https://docs.github.com/en/issues/tracking-your-work-with-issues/creating-a-branch-for-an-issue)  There should be a drop-down menu letting you create the branch on your own fork of the repository.

   - Option 2: Manually `git checkout -b <new-branch-name>` and be sure to reference the issue number when writing the pull-request (e.g. [closes #NN](https://docs.github.com/en/issues/tracking-your-work-with-issues/linking-a-pull-request-to-an-issue#linking-a-pull-request-to-an-issue-using-a-keyword)).  Note that you can create a pull request right away and [mark it as a draft](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/changing-the-stage-of-a-pull-request) using the right side-bar under "Reviewers".

4. Run the steps below to preview locally and make edits.  Then commit your changes (`git commit`).

5. Push your changes (`git push -u origin <new-branch-name>`) and go to your lcls-users repo on github to create/update your pull request.

   - Depending on tests/comments, etc.  You may want to continue committing
     changes to your feature branch and then pushing them.  Changes you push
     will be reflected in the pull-request and visible to your reviewers.

     Once the pull request is merged, however, these updates no longer have
     any effects.

6. Sync your main branch from upstream.  This fetches a local copy of all the changes that have merged to main.  It's generally useful any time you want to start work on a new feature branch.

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
