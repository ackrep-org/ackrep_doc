# Contributing to Documentation 
This documentation is built using [sphinx doc](https://www.sphinx-doc.org/en/master/index.html) and [markdown](https://www.markdownguide.org/basic-syntax/) files. 

## Installation
- Clone the repository <https://github.com/ackrep-org/ackrep_doc.git>
- `pip install -r requirements.txt`
- `pip install rst-to-myst[sphinx]`
- `rst-to-myst[sphinx]` and `myst-parser` have conflicting dependancies regarding the `mdit-py-plugins`-version. The resulting warning can be ignored.

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
            ├── contributing_to_.._.md
            └── ...

## Usage
Building the documentation:
- `cd <project_dir>/ackrep_doc/docs`
- `make html`
- open `<project_dir>/ackrep_doc/docs/build/html/index.html` in your browser


## Restructured Text vs Markdown
The Sphinx documentation primarily uses restructured text (rst). Therefore most commands are better documented in rst. In theory, this documentation can handle rst files just as well, but we try to maintain uniformity and limit ourselves to markdown. To [converst rst files to md](https://docs.readthedocs.io/en/stable/guides/migrate-rest-myst.html#converting-existing-restructuredtext-documentation-to-myst), the following command can be used:

    `rst2myst convert <path/file_name>.rst` 