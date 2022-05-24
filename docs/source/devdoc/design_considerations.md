# Design Considerations

## Data

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

The broker can be run via docker (`docker run -d -p 5672:5672 rabbitmq` or `docker run -p 6379:6379 --name redis redis:6.2-alpine`), but celery does not connect to the broker. This is presumably due to the fact, that docker commands are run in separate container.
---

## Deployment

### Concept
```{image} images/deployment_structure.png
:width: 500
```
The ackrep-django container runs the web interface. The celery_worker container runs in the background. If the "check_solution" button is pressed, the check_view in views.py is called and an asynchronous task is started via the .delay call. This enables the ackrep-django container to continue without having to wait for the calculation result. The celery_worker however determines the correct environment for the current simulation and starts a new docker container with the desired spectification to run the simulation inside. This is achieved by exposing the docker socket of the host to the celery_worker container (including the corresponding permissions) to ensure the new container is a sibling rather a container inside a container. The environment container is configured to execute cli commands passed on startup. In this case, this is the corresponding ackrep-command. The propagation of the the results from env to celery module. The result of the asynchronous task is stored and collected once the page in question is reloaded, and then forgotten.

### Celery 
#### configuration
Even though the code of the celery configuration lives in core, this is more of a deployment topic.

Celery broker and backend use different services depending on whether the web server is run locally from the cli or from inside docker. From the cli [RabbitMQ](https://docs.celeryq.dev/en/stable/getting-started/backends-and-brokers/rabbitmq.html#broker-rabbitmq) is used, from inside docker we use [redis](https://docs.celeryq.dev/en/stable/getting-started/backends-and-brokers/redis.html#broker-redis). In the default case, we use RabbitMQ, unless a environment variable (usually only set inside the docker container) is specified.

#### operation schedule
Once a new simulation/ solution is requested, the asynchronous job is started and a database entry is created with key, celery_job_id and start_time. The key should be unique in this database, since there is exactly one simulation per key and if the same simulation is requested multiple times, the request is skipped and not added to the database. After starting the calculation, the page is reloaded periodically via javascript. If the job is done, the result is fetched and displayed, the corresponding database entry is deleted.

