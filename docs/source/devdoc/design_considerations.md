# Design Considerations

## Data

---

## Core
### Error Propagations
The debugging of errors in the data repository is difficult, since the respective scripts are called via `subprocess`. The resulting challenge is to create a pipeline for both usefull debug messages and outputs in the nominal case. The following graphic depicts how error messages that arise during the execution of the execscript are propagated:

```{image} error_propagation.png
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

---

## Deployment

### Celery configuration
Even though the code of the celery configuration lives in core, this is more of a deployment topic.

Celery broker and backend use different services depending on whether the web server is run locally from the cli or from inside docker. From the cli [RabbitMQ](https://docs.celeryq.dev/en/stable/getting-started/backends-and-brokers/rabbitmq.html#broker-rabbitmq) is used, from inside docker we use [redis](https://docs.celeryq.dev/en/stable/getting-started/backends-and-brokers/redis.html#broker-redis). In the default case, we use RabbitMQ, unless a environment variable (usually only set inside the docker container) is specified.