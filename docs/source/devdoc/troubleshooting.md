(troubleshooting_dev)=
# Troubleshooting for devs

- common error when editing model types (problem/solution/sysmodel):
    - `no such column: ackrep_core_systemmodel.<...>` or `no such table: ...`
    - fix: rebuild database 
        - run `ackrep --bootstrap-db`
        - change working directory to `ackrep_core` and run `python -c "from ackrep_core import core; core.load_repo_to_db('../ackrep_data')"`


- broker connection refused:
    `kombu.exceptions.OperationalError: [Errno 111] Connection refused`
    
    did you start rabbitmq?

- Working Celery package configuration
    - celery==5.1.2
    - flower==1.0.0
    - redis==3.5.3
    - kombu==5.1.0

- permission denied: <br>
frequently, when accessing host files via a docker container, a permission denied error is thrown. To prevent this, change permission of files, to allow rw access by users of docker group (i.e. 999). Make sure to use the `--recursive` option. <br> Also, the `ackrep_deployment/start_docker.py` script should already change all necessary permissions.

