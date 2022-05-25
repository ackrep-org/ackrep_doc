# Contributing to Documentation 
This documentation is built using [sphinx doc](https://www.sphinx-doc.org/en/master/index.html) and [markdown](https://www.markdownguide.org/basic-syntax/) files. 

## Installation
- Clone the repository <https://github.com/ackrep-org/ackrep_doc.git>
- `pip install rst-to-myst[sphinx]`
- `pip install -r requirements.txt`
- `rst-to-myst[sphinx]` and `myst-parser` have conflicting dependancies regarding the `mdit-py-plugins`-version. The resulting warning can be ignored.
- The order of installation matters, since `rst-to-myst` wants `mdit-py-plugins==0.2.8` but we need `mdit-py-plugins==0.3.0`.

## Structure

    source/
        ├── index.md                    ← landing page, links to all other pages
        │  
        ├── conf.py                     ← configuration file
        │
        |
        ├── introduction/               ← introductory content
        │   ├── overview.md
        │   └── ...
        │
        ├── userdoc/                    ← content for users
        │   ├── installation.md                            
        │   └── ...
        |
        └── devdoc/                     ← content for developers
            ├── images/
            ├── contributing_to_.._.md
            └── ...

The documentation of this project can be divided into three main parts:

First, introduction contains general information about the project, goals, use cases, links to publications and some theoretical background.

Part two is the user doc, which is designed to guide users, that primarily want to use this project passively. E.g. by using the code in the repo to recreate results and use existing packages for own projects. This includes users who only use the web interface, users who install and run the repo locally as well as user who want to run simulations inside docker containers.

Lastly, the developer documentation. This part is interesting for people who want to contribute to the project by adding solutions, models and methods. Additionally, here information about design considerations is stored.

## Usage
Building the documentation:
- `cd <project_dir>/ackrep_doc/docs`
- `make html`
- open `<project_dir>/ackrep_doc/docs/build/html/index.html` in your browser


## Restructured Text vs Markdown
The Sphinx documentation primarily uses restructured text (rst). Therefore most commands are better documented in rst. In theory, this documentation can handle rst files just as well, but we try to maintain uniformity and limit ourselves to markdown. To [converst rst files to md](https://docs.readthedocs.io/en/stable/guides/migrate-rest-myst.html#converting-existing-restructuredtext-documentation-to-myst), the following command can be used:

`rst2myst convert <path/file_name>.rst`

## Usefull Links
- Interactive table generator: <https://www.tablesgenerator.com/markdown_tables#>
- Adding Images <https://sublime-and-sphinx-guide.readthedocs.io/en/latest/images.html>, then use `rst2myst`

## Deploying Docs Online:
Following the [tutorial](https://docs.readthedocs.io/en/stable/tutorial/index.html#getting-started) is a good start but leaves some questions unanswered. Its also necessary to create a [.readthedocs.yml](https://docs.readthedocs.io/en/stable/config-file/index.html) and specify the location of `conf.py` and the path to the `requirements.txt`. Additionally, `source_suffix` and `source_parser` have to be specified to include markdown files. **This problem only arises when deploying online, local building will work without these settings!** 

Setting `fail_on_warning: true` will prevent builds prom passing that definately should not pass. 

