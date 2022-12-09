(installation)=
# Installation

```{contents} Table of contents
:backlinks: none
:depth: 3
:local: true
```
## Software Requirements
- [Python](https://www.python.org/) (we currently recommend **Python 3.8**)
- [Git](https://git-scm.com/)

(ackrep_setup)=
## Ackrep Setup:

1. (Optional:) Create new environment (e.g. using anaconda).
2. Create a new empty root directory for the project (e.g. "ackrep")

    `cd <root directory>`

3. Clone [ackrep_data](https://github.com/ackrep-org/ackrep_data.git)

    `git clone https://github.com/ackrep-org/ackrep_data.git`

1. Clone [ackrep_core](https://github.com/ackrep-org/ackrep_core.git)

    `git clone https://github.com/ackrep-org/ackrep_core.git`

    Your project directory should look like this:

        <root_dir>/
        ├── ackrep/
        │   ├── ackrep_data/                   ← separate repository for ackrep_data
        │   │   ├── .git/
        │   │   └── ...
        │   │
        │   └── ackrep_core/                   ← separate repository for ackrep_core
        │       ├── .git/
        │       └── ...
        │
        └── erk/                               ← if this is your first visit here, dont worry about this.
            └── erk-data                         if you want to use pyerk functionality, checkout the links below
                └─ ocse/
                    ├── .git/
                    └── ...

    ([PyERK documentation](https://pyerk-core.readthedocs.io/en/develop_os/index.html),
    [PYERK repository](https://github.com/ackrep-org/pyerk-core))

1. Enter the ackrep_core-repo directory, run the following commands from there.

    `cd ackrep_core`
1. Install dependencies

    `pip install -r requirements.txt`
1. Setup ackrep
 
    `pip install -e .`
1. Validate Installation 
    ```
    ackrep --version
    # Version 0.6.0
    ```
1. Setup the database
  
    `python manage.py makemigrations` <br>
    `python manage.py migrate --noinput --run-syncdb`

---

## Setup advanced control theory algorithms

## With Docker
````{note}
Installing docker comes with some challenges, especially for windows users. But the advantage to be gained is the ability to execute any piece of ackrep code in a deliberately set up environment with all necessary dependancies already installed. One can be fairely certain, that the code inside the environment will behave as intended even after a long period of time.
````
- [Install Docker Engine](https://docs.docker.com/engine/install/)
- Linux Users: consider these [post installation steps](https://docs.docker.com/engine/install/linux-postinstall/)
- Validate the installation with

    `docker run hello-world`

- If not already done, complete the above described [ackrep setup](ackrep_setup). You do not need to install runner dependancies!

Now you are ready to execute all examples on your own machine:<br>
Continue with [running the code from the command line](run_local).

---

### Without Docker
````{warning}
If you do not already have docker set up on your machine, this is a relatively fast way to get startet. Most of the examples should be possible to execute without the need for docker. But some of the Ackrep examples require a speacial environment to run in, therefore usually docker containers are used.  
````
- Install Dependencies

    `pip install -r requirements_runner.txt`

    If the installation of ``slycot`` fails, refer to <https://github.com/python-control/Slycot#installing> or remove the packages `slycot` and `casadi` from the requirements file.

Now you are ready to execute some examples on your own machine:<br>
Continue with [running the code from the command line](run_local).


