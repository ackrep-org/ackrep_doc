(contributing_deployment)=
# Deployment Documentation

```{contents} Table of contents
:backlinks: none
:depth: 3
:local: true
```

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
    │   ├── .git/
    │   ├── README.md                      ← the currently displayed file (README.md)
    │   ├── deploy.py                      ← deployment script
    │   ├── ...
    │   ├── custom_settings__demo          ← instance-specific settings (not included in the repo)
    │   │   ├── settings.yml
    │   │   └── ...
    │   └── custom_settings__local         ← settings for local testing deployment (included for reference)
    │       ├── settings.yml
    │       └── ...
    │
    │
    │
    ├── ackrep_data/                      ← separate repository for ackrep_data
    │   ├── .git/
    │   └── ...
    │
    ├── ackrep_data_for_unittests/        ← expected to be a clone/copy of ackrep_data
    │   ├── .git/                            (must be created manually)
    │   └── ...
    └── ackrep_core/                      ← separate repository for ackrep_core
        ├── .git/
        └── ...

The components *ackrep_core* and *ackrep_data* are maintained in separate repositories.
For the deployment to work it is expected to clone them separately one level up in the directory structure.

## Deployment and Testing:


### Without Docker

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
- build the all the containers: `docker-compose build`
- **deprecated:** run the main container (other containers are started due to dependancies): `docker-compose up celery_worker`
- **new** run `python start_docker.py` to avoid permission issues
- visit <http://localhost:8000/> with your browser and check if the ackrep landing page is shown


## Troubleshooting:
- One problem is/was to be able to run a docker container from within another container, i.e. a script in the celery worker container has to start some environment container to do some actual maths. This is done by creating a sibling container and exposing the host docker socket to the first container. The problem is, that the socket is root owned and cant be excessed by the appuser inside the first docker container, typical error message: 

    `Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/images/json?filters=%7B%22reference%22%3A%7B%22ackrep_deployment_test_environment%22%3Atrue%7D%7D": dial unix /var/run/docker.sock: connect: permission denied`

    Workaround: manually change the ownership of `/var/run/docker.sock` to appuser
    - inide the active container: `id` -> gets uid and gid of the appuser e.g. 999:999
    - on host: `cd /var/run/`
    - if `ls -n docker.sock` looks like this `srw-rw---- 1 0 998 0 Mai 18 08:01 docker.sock`, then the socket can only be accessed by root and group 998 (sidenote: adding appuser to group 998 (docker group on host) did not accomplish anything). 
    - run `sudo chown <uid>:<gui> docker.sock` to give the container access.
    - this change of permission doesnt seem to persist through restarts