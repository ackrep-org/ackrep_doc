# Design Considerations

```{contents} Table of contents
:backlinks: none
:depth: 3
:local: true
```

## Data
todo
---

## Core
### Error Propagations
The debugging of errors in the data repository is difficult, since the respective scripts are called via `subprocess`. The resulting challenge is to create a pipeline for both usefull debug messages and outputs in the nominal case. The following graphic depicts how error messages that arise during the execution of the execscript are propagated:

```{image} images/error_propagation.png
:width: 500
```


Which information is show in the different cases of settings, error type and execution methode is shown the following table:

|                      | Web Gui                            |                                   | cmd             |
|----------------------|------------------------------------|-----------------------------------|-----------------|
|                      | **settings.DEBUG == True**         | **settings.DEBUG == False**       |                 |
| **nominal**          | show output, result: "success"     | show output, result: "success"    | log, "success"    |
| **numnerical error** | show output, result: "inaccurate"   | show output, result: "inaccurate" | log, "inaccurate" |
| **script error**     | show debug, result: "script error" | result: "script error"      | log, "fail"       |

---

## Web
### Post Celery+Docker Unittests
- challenge: unittests simulate web interface -> checks are performed via view.py and therefore asynchronous and inside docker environment container
- celery workers are run in background (to have the test script continue) in setup of test 
    ```
    cmd = "ackrep --start-workers"
    self.worker = subprocess.Popen(f"nohup {cmd}", shell=True, stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)
    ```
    and killed on tearDown
    ```
    os.chdir(os.path.join(core.root_path, "ackrep_core"))
    res = subprocess.run(["celery", "-A", "ackrep_web", "control", "shutdown"], text=True, capture_output=True)
    ```
- problems: 
    - if test changes data (e.g. to test debug printing) this change is not reflected inside the container
    - loading db insider container
    - inside container there is no difference between ut and normal use case
- solution:
    - ut-repo gets a permanently broken system model for testing debug messaging (LRHZX)
    - loading test db is done by running test setup methode in dockerfile
    to distinguish between ut and nominal use case, environment variables are passed with the docker command to the container, specificly tailored to work inside the container

## CI
Continuous Integration for core works fine.

CI for ut_web is problematic, since celery + broker and docker have to be run inside the ci-container. Docker can be run by adding the step:

    - setup_remote_docker:
        version: 20.10.14
        docker_layer_caching: true


- **Solution**: run rabbit image parallel to main job and let celery connect via localhost
    ```
    jobs: # A basic unit of work in a run
    setup: 
        # directory where steps are run
        working_directory: ~/ackrep_core
        docker: # run the steps with Docker
        # CircleCI Python images available at: https://hub.docker.com/r/circleci/python/
        - image: cimg/python:3.8
            environment: # environment variables for primary container
            SKIP_TEST_CREATE_PDF: "True"
            CI: "True"
        - image: rabbitmq:3.8.16-management-alpine
        steps: # steps that comprise the `build` job
        - checkout
        ...
    ```
    ```
    broker_url = "pyamqp://guest@localhost//"
    result_backend = "rpc://"
    ```

- volume mounting does not exist inside celery (see <https://circleci.com/docs/2.0/building-docker-images/#mounting-folders>). The used workaround is to create a dummy container with a dummy volume (**without** mapping to host!), copy all files into the dummy container (at the volume location) and the start the main container with `--volumes-from dummy`.

- The image url part of the tests is skipped when in the CI since the file would have to be copied back to the primary container during the test.

- some permissions have to be changed, in order to allow the container to modify the copied files


---

## Deployment

### Concept
```{image} images/deployment_structure.png
:width: 500
```
The ackrep-django container runs the web interface. The celery_worker container runs in the background. If the "check_solution" button is pressed, the check_view in views.py is called and an asynchronous task is started via the .delay call. This enables the ackrep-django container to continue without having to wait for the calculation result. The celery_worker however determines the correct environment for the current simulation and starts a new docker container with the desired spectification to run the simulation inside. This is achieved by exposing the docker socket of the host to the celery_worker container (including the corresponding permissions) to ensure the new container is a sibling rather a container inside a container. The environment container is configured to execute cli commands passed on startup. In this case, this is the corresponding ackrep-command. The propagation of the the results from env to celery module. The result of the asynchronous task is stored and collected once the page in question is reloaded, and then forgotten.

**data repo**

The ackrep_data repository is not part of the docker images. Rather it is mounted to each container on their respective startup. Naturally, after mounting the volume the database has to be loaded in each container, which is done by an entrypoint script. 
The volume mapping is specified in the `docker-compose.yml` file, which in turn is called by the `start_docker.py` script. Mountaing a volume to containers is easily done for the `ackrep-django` and `celery_worker` container but is problematic for the `environment` containers. Although these are started from inside the `celery_worker` container (reminder that this creates siblings, and not docker in docker) the volume mapping still has to be `host/.../ackrep_data:code/ackrep_data`. This means the ``celery_worker`` container has to have some information abour its context. This is done by passing the `DATA_REPO_HOST_ADDRESS` env variable from the starter script first to the ``celery_worker`` container and then to the env container with the correct volume mapping. In case of local running without ``ackrep-django`` container, this env variable is created by the celery process which runs directly on host and thus knows the data repo location. In the unit test case, instead of `ackrep_data`, `ackrep_data_for_unittests` is mounted and the corresponding environment variables are passed.

Since the data is only ever added on container startup, the databases are loaded by the entrypoint script of each container. In the case of the environment containers, the correct environment variable has to be passed for the database loading to happen. An exemplary docker command issued by the celery process (`core.py/check`) could look like this: <br>
normal case:

    docker-compose --file ../ackrep_deployment/docker-compose.yml run --rm 
    -e ACKREP_DATABASE_PATH=/code/ackrep_core/db_for_unittests.sqlite3 
    -e ACKREP_DATA_PATH=/code/ackrep_data_for_unittests 
    -v /home/Documents/ackrep/ackrep_data:/code/ackrep_data 
    default_environment 
    ackrep -c UXMFA

unittest case:

    docker run --rm 
    -e ACKREP_DATABASE_PATH=/code/ackrep_core/db_for_unittests.sqlite3 
    -e ACKREP_DATA_PATH=/code/ackrep_data_for_unittests 
    --volumes-from dummy 
    ghcr.io/ackrep-org/default_environment 
    ackrep -c LRHZX

The `start_docker.py` script also changes some permissions (docker.sock, ackrep_data, ackrep_data_for_unittests) so every container can access the files correctly. Maybe a password prompt is encountered.

### Celery 
#### configuration
Even though the code of the celery configuration lives in core, this is more of a deployment topic.

Celery broker and backend use different services depending on whether the web server is run locally from the cli or from inside docker. From the cli [RabbitMQ](https://docs.celeryq.dev/en/stable/getting-started/backends-and-brokers/rabbitmq.html#broker-rabbitmq) is used, from inside docker we use [redis](https://docs.celeryq.dev/en/stable/getting-started/backends-and-brokers/redis.html#broker-redis). In the default case, we use RabbitMQ, unless a environment variable (usually only set inside the docker container) is specified.

#### operation schedule
Once a new simulation/ solution is requested, the asynchronous job is started and a database entry is created with key, celery_job_id and start_time. The key should be unique in this database, since there is exactly one simulation per key and if the same simulation is requested multiple times, the request is skipped and not added to the database. After starting the calculation, the page is reloaded periodically via javascript. If the job is done, the result is fetched and displayed, the corresponding database entry is deleted.

