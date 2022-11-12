---
title: Install and patch st 
date: 2022-11-12T18:14:20+05:00
---

# How to easily host a Hugo website on Github Pages

Documentation:
* [Hugo documentation](https://gohugo.io/documentation/)
* [Github Pages](https://docs.github.com/en/rest/pages)

## Prerequisites

* Steps from [Quick Start](https://gohugo.io/getting-started/quick-start/)
* Have some content in `.md` in `/content` folder 
* Theme installed ([themes list](https://hugothemesfree.com/))
* Separate Github repository for the project created 

## Action

*If you have cloned repositories in the `/themes` folder, remove `.git` directories in them. Otherwise, it will cause an error when building remotely.*

1. Build your project into `/public` folder:

```shell
hugo
```

2. Add all the files from the project root folder to a commit and push it to the repo.

```shell
git add . && git commit -m "Initial commit" && git push -u
```

3. Open your repository on Github and click "Settings". Open "Pages" and in "Branch" subsection choose "/docs" folder. Click "Save". 

You may use Github Actions, but the easier way is to deploy from "/docs" folder directly.

Now, your project must be built and available by the URL. If not, open "Actions" in the navbar and see the last build attempt. 

