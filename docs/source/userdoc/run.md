# Work locally

In order to evaluate the database locally, the command line interface with the command `ackrep` is used.

A list of all commands is is shown when invoking `ackrep --help`.
## Run existing Code

1. The first step after is to lead the database:
    - Change working directory to `ackrep_data`.
    - Run `ackrep --load-repo-to-db .` in that directory.

2. Now a specific problem solution can be tested: 
    - Either with by specifying the path `ackrep --check-solution problem_solutions/<your_problem_solution>/metadata.yml`
    - or by passing the key `ackrep -cs <key>`.
3. Analogous to checking problem solution, system models can be simulated:
    - Either with `ackrep --check-system-model system_models/<your_system_model>/metadata.yml`
    - or with `ackrep -csm <key>`.

4. If an error occurs:
    - Manually run `python execscript.py` and see the error messages.
    - Common issues:
        - Forgot to run `ackrep --load-repo-to-db .` after changing metadata.
        - ImportError due to missing package in the python_path (see `execscript.py` what actually is inserted); Probable cause: missing or wrong key in `metadata.yml`
        - Bad return-value of `evaluate_solution`

## Edit Code
The existing Problems and System Models can be easily modified, e.g. by changing the initial values of the simulation.
...
TODO
## Reuse Code for personal projects
The methode packages can be copied and incorporated in personal projects.
...
TODO