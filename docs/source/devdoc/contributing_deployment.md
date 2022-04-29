(contributing_deployment)=
# Deployment Documentation

## General Information

This repository contains the code that runs on <demo.ackrep.org>. It should also provide a starting point to deploy an own instance.

## Deployment concept

The ackrep project consists of several components which are maintained each in their own repository.


**Functional Components**

- *[ackrep_core](https://github.com/cknoll/ackrep_core)*
    - main code of the ackrep engine and command-line-interface (cli)
- *[ackrep_data](https://github.com/cknoll/ackrep_data)*
    - actual data for the repository
- (*[ackrep_web](https://github.com/cknoll/ackrep_core/tree/main/ackrep_web)*)
    - code for the web front-end
    - currently still part of ackrep_core

**Auxiliary Components**

- *ackrep_deployment*
    - code for simple deployment of the functional components on a virtual server
- *custom_settings*
    - data used for customization and maintainance. For privacy and security reasons these settings are not published.

<a name="directory-layout"></a>
These components are represented by the following **directory layout**:

    <ackrep_project_dir>/
    ├── ackrep_deployment/                ← repo with deployment code for the ackrep project
    │  ├── .git/
    │  ├── README.md                      ← the currently displayed file (README.md)
    │  ├── deploy.py                      ← deployment script
    │  ├── ...
    │  ├── custom_settings__demo          ← instance-specific settings (not included in the repo)
    │  │   ├── settings.yml
    │  │   └── ...
    │  └── custom_settings__local         ← settings for local testing deployment (included for reference)
    │      ├── settings.yml
    │      └── ...
    │
    │
    │
    ├── ackrep_data/                      ← separate repository for ackrep_data
    │  ├── .git/
    │  └── ...
    │
    ├── ackrep_data_for_unittests/        ← expected to be a clone/copy of ackrep_data
    │  ├── .git/                            (must be created manually)
    │  └── ...
    └── ackrep_core/                      ← separate repository for ackrep_core
       ├── .git/
       └── ...

The components *ackrep_core* and *ackrep_data* are maintained in separate repositories.
For the deployment to work it is expected to clone them separately one level up in the directory structure.

### Deployment and Testing:


#### Without Docker

- clone *ackrep_core* and enter the repo directory
- `pip install -r requirements.txt`
- `python3 manage.py runserver`
- visit <http://localhost:8000/> with your browser and check if the ackrep landing page is shown


### With Docker


**steps for local testing deployment**
- install docker (e.g. via `apt install docker-ce docker-ce-cli docker-ce-rootless-extras`)
- install docker-compose (e.g. via `pip install docker-compose`)
- create a directory structure like above
- `cd ackrep_deployment`
- build the main container: `docker-compose up -d --remove-orphans --build ackrep-django`
- run the main container: `docker-compose up ackrep-django`
- visit <http://localhost:8000/> with your browser and check if the ackrep landing page is shown


