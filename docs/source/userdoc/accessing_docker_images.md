(docker_images)=
# Accessing Docker Images
We asume that with future developments of source code (python and dependancies) as well as developments of operating systems it might be difficult to run code examples, that were created years ago for different hard- and software. Therefore we use docker images to freeze a working snapshop of our framework in time. 

This page will explain, how to access the docker images and run your own code inside the the specified environment. This is useful, because one can be fairly certain, that the methode packages will work as intended. 

**Disclaimer**
The following content has only been tested on Linux OS!

## Installing Docker
1. Check out the [detailed guide](https://docs.docker.com/engine/install/) on how to install the docker engine. 
1. You might need to take some [post installation steps](https://docs.docker.com/engine/install/linux-postinstall/) to make sure it runs smoothly.
1. Verify your installation by running `docker run hello-world`.

## Using Ackrep Docker Images
The published docker images can be found at <https://github.com/orgs/ackrep-org/packages>.

To access an environment image, pull it from github with
`docker pull ghcr.io/ackrep-org/<environment_name>:latest`

TODO: gid is not yet dynamic

Run `ackrep --run-interactive-environment <image_name>`, which should open a bash shell inside the docker container. From here you have a couple of options:
- Starting the preinstalled Midnight Commander (`mc`) or running `ls` will show you the directory structure inside the container:
    ```
    /
    ├── ackrep_transfer/        ← transfer folder, mapped to `<project_dir>/ackrep_transfer`
    │  
    └── code/                   ← snapshot version of ackrep,
        |                         might not be compatible with the newest version
        ├── ackrep_data/               
        │
        └── ackrep_core/                  
    ```

- You can run ackrep commands inside the shell to check solutions (see `ackrep --help`).
- You can edit existing examples or run your own code utilizing the installed methode packages:
    1. Choose a ProblemSolution you would like to edit or use as a starting point for your own project. Get the key (any solution or system_model key will work).
    1. Run `ackrep --prepare-script <key>` to prepare the corresponding execscript and place it inside the `ackrep_transfer` directory. This directory is a shared volume between your host os and the docker container. On host you can find it at `<project_dir>/ackrep_transfer`, inside the container it resides in the base directory (`/ackrep_transfer`).
    1. You can now edit this script, either on your host or inside the container (e.g. via mc). You can also add new filed to the container by copying them to the transfer folder.
    1. To run your script, use `python execscript.py` inside the `/ackrep_transfer` directory.

