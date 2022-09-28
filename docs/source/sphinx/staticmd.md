# Markdown pages 

## 1. Goal and Process
### 1.1 Installing the requirements
#### 1.1.1 Python virtual environment for docs
- Assume that we already create project directory, I would like to create documenting 
directory `docs/` inside the project in a virtually independent way, so resulting file tree
will look like this.  

```
── project/  
    ├── docs/
```

- I prefer independent documenting environment installed sphinx and its dependencies. First, 
 create virtual environment, say 'sphinx'. You know that `$` means just a prompt, right?  
 And You can always use `pip` command instead of `conda`. 
 Don' forget update `pip`.

```
$ conda create -n sphinx python=3.9
$ conda activate sphinx
$ python -m pip install --upgrade pip
``` 

- If I were not an anaconda fan, I would create (.venv) instead `sphinx` inside my nested project `docs/`.

```
$ mkdir docs
$ cd docs
docs$ python -m venv .venv
docs$ source ./.venv/bin/activate
(.venv)docs$ python -m pip install --upgrade pip
``` 
- Note that this virtual environment is not for the main project but only for the `docs` project.  

#### 1.1.2 Installing Sphinx and others
- Next, install `sphinx`, in our virtual environment `(sphinx)`.

```
(sphinx)$ conda install sphinx
```

- Also, may need an awesome theme, like `sphinx-rtd-theme`.

```
(sphinx)$ conda install sphinx-rtd-theme`
```

- [To render Markdown](https://www.sphinx-doc.org/en/master/usage/markdown.html), we also need the Markdown parser `myst-parser`.

```
(sphinx)$ pip install --upgrade myst-parser
```

#### 1.1.3 Filing `docs/` directory with sphinx tools
>
> Before doing that, it is good idea to create a new orphan branch `gh-pages` as github suggested, to isolate documenting process from the main project and to publish your site from the branch.  
> Go to the `docs/` and create branch `gh-pages` there.
> ```
> (sphinx)$ cd docs
> (sphinx)docs$ git checkout --orphan gh-pages
> (sphinx)docs$ git rm -rf
> ```
>  Removes the contents from your default branch from the working directory

- Now, we are ready to execute Sphinx quickstart command.
```
(sphinx)docs$ sphinx-quickstart
```

Or if you did not create `docs/` directory yet, you can command like this.

```
(sphinx)$ sphinx-quickstart docs
```

- Then you will be asked preferences like this.

```
Selected root path: .

You have two options for placing the build directory for Sphinx output.
Either, you use a directory "_build" within the root path, or you separate
"source" and "build" directories within the root path.
> Separate source and build directories (y/n) [n]: 
```

- `y` will create sepatate directory `build/`  

```
The project name will occur in several places in the built documentation.
> Project name: 
> Author name(s): 
> Project release []:
```
- After answering them, final question,
```
For a list of supported codes, see
https://www.sphinx-doc.org/en/master/usage/configuration.html#confval-language.
> Project language [en]:
```
- If you prefer, you can ignore it and type `Enter` key.
Then, it generates documenting frameworks inside `docs/` directory like 
[this](https://www.sphinx-doc.org/en/master/tutorial/getting-started.html#setting-up-your-project-and-development-environment).

```
docs/
├── build/                      # empty dir supposed to fill the created docs for rendering
├── make.bat                    # maker
├── Makefile                    # maker
└── source/                     # put your base files or directories of docs(.md) 
    ├── conf.py                 # configuration
    ├── index.rst               # table of contents and file location setting.    
    ├── _static/                # like django, static files in it
    └── _templates/             # like django, templates in it

5 directories, 5 files
```

- In a glance, you first put your base documents file trees INSIDE `source/`.
- Next, build `.html`s of them using maker command and sphinx will create them inside
  `build/` directory automatically.

#### 1.1.4 Try the sample html
```
(sphinx)docs$ sphinx-build -b html ./source/ ./build/html
```

- Open `docs/build/html/index.html` in your browser, and you will see Welcome page. Congrats!
  
To customize to render your markdown base docs, you need configure frameworks first.
 



### 1.2 Configuration
- in `cong.py`
- path
```
import os
import sys
sys.path.insert(0, os.path.abspath('../..'))
```

- extensions [ref](https://www.sphinx-doc.org/en/master/usage/markdown.html)

```
extensions = [
    'sphinx_rtd_theme',         # for 'read the doc' theme
    'myst_parser',              # for markdown along with `source_suffix`
    'sphinx.ext.mathjax',       # mathjax
]
source_suffix = {   
    '.rst': 'restructuredtext',
    '.txt': 'markdown',
    '.md': 'markdown',
}
```
- theme
```
html_theme = 'sphinx_rtd_theme'
```
### 1.3  Create you BASE documents
Let's try some test markdown files.
```
source/                    # put your base files or directories of docs(.md) 
    ├── folder1
    │   ├── doc1.md
    │   ├── doc2.md
    │   └── subfolder1
    │       └── doc3.md
    ├── folder2
    │   └── doc4.md
```


### 1.4 File-system-wise indexing your contents in `index.rst`

```
.. toctree::
   :maxdepth: 3
   :caption: folder1:

   folder1/doc1.md
   folder1/doc2.md

.. toctree::
   :maxdepth: 2
   :caption: folder1/subfolder2:

   folder1/subfolder2/doc3.md

.. toctree::
   :maxdepth: 3
   :caption: folder2:

   folder2/doc4.md
   
```
- Whenever you modify contents sources, remove everything in `build/` and remake.
```
(sphinx)docs$ make clean
(sphinx)docs$ make html
```

### 1.5 deploy in Github pages
- You need to create two files in `docs/` directory.
- The one is an empty `docs/.nojekyll` to prevent github action process to apply jekyll themes.
- And the other is an `docs/index.html` to redirect it to `docs/build/html/index.html` like this.
```
<html>
    <head>
      <meta http-equiv="refresh" content="0; url=./build/html/index.html">
    </head>
</html>
``` 
- Next, commit and push to github repo. Are you still in `gh-pages` branch, right?
```
(sphinx)docs$ git add .
(sphjnx)docs$ git commit -m "create docs/"
(sphinx)docs% git remote add origin https://github.com/USER/REPO.git
(sphinx)docs$ git push -u origin gh-pages
```

- Then, click `Settings` > `Pages` in your github repository site.
    - Make sure that showing branch be `gh-pages`.
    - Toggle down from `/(root)` to `/docs`
    - Deploying processing can be seen by clicking `Actions` tap.  

---
    
## 2. Jupyter Notebook 
- After exporting notebooks, do the similar steps as Markdown.