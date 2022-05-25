(ref_api)=
# API
Summary of ACKREP - commands.

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
#### Check Solution/ Simulation
Check to validity of ProblemSpecification - ProblemSolution tuple or a SystemModel specified by the entity key or the path to the `metadata.yml` file. 

    --check, -c     <key or file_path>

Shows "Success" if valid, "Inaccurate" if the simulation finished but the results did not match the expectation (this happens sometimes with chaotic systems on different operating systems) or "Fail" if the simulation encountered an error. 

#### Check all Solutions
(This may take some time.)

    --check-all-solutions

#### Check all System Models
(This may take some time.)

    --check-all-system-models

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

E.g. cwd is `ackrep_core`: `ackrep -l ../ackrep_data`

### Running local Web Interface
Also refer to [additional commands](ref_additional_run_web).
(ref_ackrep_run_web)=
#### Start Celery Workers
Start background celery process to pick up asynchronous tasks once they are started.

    --start-workers

### Interacting with Environment Images
#### Create Interactive Session
Start an interactive session with a docker container of an environment image of your choice. Environment key must be specified.

    --run-interactive-environment       <key>

#### Prepate execscript
Render the execscript corresponding to problem solution or system model and place it in the docker transfer folder (specified by path to metadata file or key of solution or system model).

    --prepare-script        <key>

## Additional Commands
(ref_additional_run_web)=
### Running local Web Interface
Also refer to [ackrep commands](ref_ackrep_run_web).
#### Run Server
Run the server at <http://127.0.0.1:8000/>. This command presumes that the current working directory is `ackrep_core`, that the database is loaded and that the celery workers are up and running.

    python manage.py runserver

### Run Tests
Also refer to the [unittest documentation](ref_unittests)

#### Test Core

    python manage.py test --keepdb --nocapture ackrep_core.test.test_core
    python manage.py test --keepdb --nocapture ackrep_core.test.test_core:TestCases2
    python manage.py test --keepdb --nocapture ackrep_core.test.test_core:TestCases3.test_check_system_model

#### Test Web

    python manage.py test --keepdb --nocapture ackrep_web.test.test_web
    python manage.py test --keepdb --nocapture ackrep_web.test.test_web:TestCases1
    python manage.py test --keepdb --nocapture ackrep_web.test.test_web:TestCases2.test_check_solution