(installation)=
# Installation

```{contents} Table of contents
:backlinks: none
:depth: 3
:local: true
```

## Without Docker

### Basic Setup:

1. Highly recommended: create new environment (e.g. using anaconda). The current version of the code is tested only for **python 3.8**
2. Create a new empty root directory for the project (e.g. "ackrep")

    `cd <root directory>`

3. Clone [ackrep_data](https://github.com/ackrep-org/ackrep_data.git)

    `git clone https://github.com/ackrep-org/ackrep_data.git`

1. Clone [ackrep_core](https://github.com/ackrep-org/ackrep_core.git)

    `git clone https://github.com/ackrep-org/ackrep_core.git`

    Your project directory should look like this:

        <ackrep_project_dir>/
        │
        ├── ackrep_data/                   ← separate repository for ackrep_data
        │  ├── .git/
        │  └── ...
        │
        └── ackrep_core/                   ← separate repository for ackrep_core
        ├── .git/
        └── ...


1. Enter the ackrep_core-repo directory, run the following commands from there.

    `cd .\ackrep_core`
1. Install dependencies

    `pip install -r requirements.txt`
1. Setup ackrep
 
    `pip install -e .`
1. Validate Installation 
    ```
    ackrep --say-hello
    # Hello!
    ```
1. Setup the database
  
    `python manage.py makemigrations`
    
    `python manage.py migrate --noinput --run-syncdb`

### Setup advanced control theory algorithms
- Install Dependencies

    `pip install -r requirements_runner.txt`

- If the installation of slycot fails, refer to <https://github.com/python-control/Slycot#installing>.

## With Docker

TODO

---
Continue with [running the code locally](run_local).