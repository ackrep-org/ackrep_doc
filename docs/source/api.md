(ref_api)=
# API
Summary of ACKREP - commands. For more details, refer to `script.py` in the [source code](https://github.com/ackrep-org/ackrep_core/blob/main/ackrep_core/script.py).

```{contents} Table of contents
:backlinks: none
:depth: 3
:local: true
```
## ACKREP - Commands

    ackrep [COMMAND] [PARAMETER]

### Utility Commands
#### Show Version

    --version

#### Generate new Entity Key

    --key

#### Get Metadata File Location
Show location of the metadata file in your file system corresponding to the given key. 
- Absolute path:

        --get-metadata-abs-path-from-key        <key>

- Relative path to the root directory of the project

        --get-metadata-rel-path-from-key        <key>



### Simulation/ Calculation
#### Check Entity (depricated)
```{note}
Depricated. Use `--check-with-docker` instead. Use this only if you dont have docker installed. Results may vary.
```
Check to validity of ProblemSpecification - ProblemSolution tuple or a SystemModel specified by the entity key or the path to the `metadata.yml` file. Uses only locally installed packages.

    --check, -c     <key or file_path>

Shows "Success" if valid, "Inaccurate" if the simulation finished but the results did not match the expectation (this happens sometimes with chaotic systems on different operating systems) or "Fail" if the simulation encountered an error. 

#### Check Entity with Docker
Check to validity of ProblemSpecification - ProblemSolution tuple or a SystemModel specified by the entity key or the path to the `metadata.yml` file. This uses the correct environment specification in `metadata.yml`.

    --check-with-docker, -cwd     <key or file_path>

Shows "Success" if valid, "Inaccurate" if the simulation finished but the results did not match the expectation (this happens sometimes with chaotic systems on different operating systems) or "Fail" if the simulation encountered an error. 

#### Check all Entities
(This may take some time.)

    --check-all-entities


### Generating Outputs
<!-- todo working title -->
### Update Parameter Tex File
System Models sometimes have a parameter file. If these are changed, this methode creates the corresponding Tex file.

    --update-parameter-tex      <key or file_path>

#### Create System Model PDF
Creates a PDF file describing a **system model**.

    --create-pdf        <key or file_path>

#### Generate PDF of all System Models

    --create-system-model-list-pdf

### Manipulating the Database
#### Rebuild/ Bootstrap Database
Delete database and recreate it (without data)

    --bootstrap-db

Delete database for unittests and recreate it (without data)

    --bootstrap-test-db

#### Loading Database
Load repo to database. 

    -l, --load-repo-to-db       <path>

E.g. in `ackrep_core` directory, run: `ackrep -l ../ackrep_data`

### Running local Web Interface
Also refer to [additional commands](ref_additional_run_web).
(ref_ackrep_run_web)=

### Interacting with Environment Images
#### Create Interactive Session
Start an interactive session with a docker container of an environment image of your choice. Environment key must be specified. (e.g. default_env key: CDAMA)

    --run-interactive-environment       <key>

#### Prepate execscript
Render the execscript corresponding to problem solution or system model and place it in the ackrep_data folder (specified by path to metadata file or key of solution or system model).

    --prepare-script        <key>

### Developer Commands
Additional commands for developers / for debugging can be found in the [source code](https://github.com/ackrep-org/ackrep_core/blob/main/ackrep_core/script.py).


## Additional Commands
(ref_additional_run_web)=
### Running local Web Interface
Also refer to [ackrep commands](ref_ackrep_run_web) and [Running local server](ref_running_local_server).
#### Run Server
Run the server at <http://localhost:8000/>. This command presumes that the current working directory is `ackrep_core` and that the database is loaded.

    python manage.py runserver

### Run Tests
Also refer to the [unittest documentation](ref_unittests).

#### Test Core
Working directory is presumed to be `ackrep_core`. Test the whole framework, a single test case or just a single test.

    python manage.py test --keepdb --nocapture ackrep_core.test.test_core
    python manage.py test --keepdb --nocapture ackrep_core.test.test_core:TestCases2
    python manage.py test --keepdb --nocapture ackrep_core.test.test_core:TestCases3.test_check_system_model

#### Test Web
Working directory is presumed to be `ackrep_core`. Test the whole framework, a single test case or just a single test.

    python manage.py test --keepdb --nocapture ackrep_web.test.test_web
    python manage.py test --keepdb --nocapture ackrep_web.test.test_web:TestCases1
    python manage.py test --keepdb --nocapture ackrep_web.test.test_web:TestCases2.test_check_solution