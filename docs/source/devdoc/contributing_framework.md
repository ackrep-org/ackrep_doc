(contributing_core)=
# Contributing to Core Framework
## Project Structure
When making changes to the core framework, unittests are employed to endure the integrity of the framwork. The goal is not to test the data of the database (implemented solutions etc.), but rather to ensure that the framework handles the data correctly (e.g. the check function is working correctly). Therefore we use a clone of the data repository `ackrep_for_unittests` for validation. This repository is expected to be located in the project directory and needs to have the **`for_unittests`-branch** checked out. The project structure should looke like this:
(contr_core_structure)=

    <ackrep_project_dir>/
    │
    ├── ackrep_data/                   ← separate repository for ackrep_data
    │   ├── .git/
    │   └── ...
    │
    ├── ackrep_core/                   ← separate repository for ackrep_core
    |   ├── .git/
    |   └── ...
    |
    └── ackrep_data_for_unittests/     ← expected to be a clone/copy of ackrep_data
        ├── .git/                         (must be created and renamed manually)
        └── ...

## Unittests

As the software is still an early prototype and defining the concrete feature set is subject to ongoing research, only a fraction of the functionality is already covered by tests. However this fraction will increase in the future. The unittest depend on data which is maintained outside of this repo: It is assumed that there is a copy of the `acrep_data` repository named `acrep_data_for_unittests` next to it (see [directory layout](contr_core_structure)) and that its HEAD points to a defined commit in the `for_unittests`-branch (see `ackrep_core.core.test.test_core.default_repo_head_hash`).

Before the tests can be run for the first time the test database has to be set up like this
(tested with bash on Linux):

```
# Option 1: 
ackrep --bootstrap-test-db

# Option 2 (deprecated leagacy approach):
cd $ACKREP_CORE_DIR
export ACKREP_DATABASE_PATH=db_for_unittests.sqlite3
rm -f db_for_unittests.sqlite3
python manage.py migrate --run-syncdb
unset ACKREP_DATABASE_PATH
```

There are several options to run the tests. The following is recommended (`--rednose` is optional):

- `python3 manage.py test --nocapture --rednose` (all tests)
- `python3 manage.py test --nocapture --rednose ackrep_core.test.test_core` (tests for _core only)
- `python3 manage.py test --nocapture --rednose ackrep_core.test.test_web` (tests for _web only)
- `python3 manage.py test --nocapture --rednose ackrep_core.test.test_core:TestCases1` (all tests of one class)
- `python3 manage.py test --nocapture --rednose ackrep_core.test.test_core:TestCases1.test_00` (one specific test)

Slow tests are skipped by default. Enable them with `python3 manage.py test --include-slow ...`.


Testing can be made faster by using the following (still experimental)

```
# Option 1: only regenerate the databse once for all tests
export ACKREP_TEST_DB_REGENERATION_MODE=2
python3 manage.py test --keepdb --nocapture --rednose --ips ackrep_core.test.test_core:TestCases2

# Option2: do not regenerate the database at all
export ACKREP_TEST_DB_REGENERATION_MODE=3
python3 manage.py test --keepdb --nocapture --rednose --ips ackrep_core.test.test_core:TestCases2.test_get_metadata_path_from_key
```

See `test._test_utils.load_repo_to_db_for_tests` for details.


Usual test execution with  `python -m unittest <path>` will not work because django is needed to create an empty test-database etc.

At the current stage frontend testing does not happen. However the backend introduces some *unit_tests_comments* (`utc_...`) to the served html sources, such that the tests cases can roughly check if the expected content is shown.

### Test Web
When running the unittests for ackrep_web, the `TestUI` tests are usually skipped. If you want to run those tests as well you need to:
- install splinter and selenium
  ```
  pip install splinter
  pip install selenium
  ```
- download [chromedriver](https://chromedriver.chromium.org/home)
- add the directory of the chrome driver executable to your PATH

## Running local server
### Linux
When developing the frontend, a local server can be run to look at the result.

1. `sudo apt-get install rabbitmq-server`
2. `docker run -d -p 5672:5672 rabbitmq`
1. `pip install -r requirements_celery.txt`
1. Change to working directory to `ackre_core`.
2. Run `python -c "from ackrep_core import core; core.load_repo_to_db('../ackrep_data')"`
3. Run `python manage.py runserver`
4. Open a new shell and navigate to `ackrep_core`
5. Run `celery -A ackrep_web worker --loglevel=INFO -c 4` to start concurrent workers

**Test:**
- visit <http://localhost:8000/> with your browser and check if the ackrep landing page is shown
- visit <http://localhost:8000/entities>, search for key (UKJZI), click on "check this solution"; this should load some curves after about 3s.

### Windows
When developing the frontend, a local server can be run to look at the result.

1. checkout a windows compatible branch of core, e.g `feature_CI`
1. Change to working directory to `ackre_core`.
2. Run `python -c "from ackrep_core import core; core.load_repo_to_db('../ackrep_data')"`
3. Run `python manage.py runserver`

**Test:**
- visit <http://localhost:8000/> with your browser and check if the ackrep landing page is shown
- visit <http://localhost:8000/entities>, search for key (UKJZI), click on "check this solution"; this should load some curves after about 3s.