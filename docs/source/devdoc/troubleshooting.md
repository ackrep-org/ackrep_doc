(troubleshooting_dev)=
# Troubleshooting for devs

- common error when editing model types (problem/solution/sysmodel):
    - `no such column: ackrep_core_systemmodel.<...>` or `no such table: ...`
    - fix: rebuild database 
        - run `ackrep --bootstrap-db`
        - change working directory to `ackrep_core` and run `python -c "from ackrep_core import core; core.load_repo_to_db('../ackrep_data')"`




