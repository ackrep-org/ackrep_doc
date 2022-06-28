(run_local)=
# Reproducing Results

```{contents} Table of contents
:backlinks: none
:depth: 3
:local: true
```

In order to evaluate the database locally, the command line interface with the command `ackrep` is used.

A list of all commands is provided at the [API page](ref_api) and is also shown by invoking `ackrep --help`.
## Run existing Code

1. The first step after the successfull installation is to load the database:
    
    `ackrep --load-repo-to-db <path/to/ackrep_data>`

2. Now a specific problem solution/ system model can be tested (the syntax is different depending on whether you set up Docker or not): <br>

    without Docker:
    - Either with by specifying the path `ackrep --check problem_solutions/<your_problem_solution>/metadata.yml`
    - or by passing the key `ackrep -c <key>`.

    With Docker: 
    - Either with by specifying the path `ackrep --check-with-docker problem_solutions/<your_problem_solution>/metadata.yml`
    - or by passing the key `ackrep -cwd <key>`.


## Editing Code
The existing Problems and System Models can be modified easily. Regardless of whether you are susing docker or not, changes in your ackrep_data folder will be present in both cases. But the workflow still differs a bit:

Without Docker a smooth workflow would be:
- Pick out the entity you are interested in (system model/ solution/ solution with a specific method package) and find out its key (using the web interface)
- Run `ackrep --prepare-script <entity_key>` to create a python script (`execscript.py` located in ackrep_data) with all necessary imports.
- Now you can make adjustments to the imported files (parameters, plots, `plt.show()`, etc.) or edit the execscript to create a new program using e.g. system models or method packages. If you want to edit the execscript, you sould make a copy and rename the edited file.
- Run `python execscript.py` in the ackrep_data folder to view the result. **Note:** Everytime you run `ackrep -c <key>` the execscript will be overwritten!

With Docker a smooth workflow would be:
- Pick out the entity you are interested in (system model/ solution/ solution with a specific method package) and find out its key (using the web interface)
- Get the correct environment key for your entity (web interface) and run `ackrep --run-interactive-environment <env_key>` (or `ackrep -rie <env_key>`). This will start an environment container with an interactive cli. Invoking `mc` will open the Midnight Commander.
- Run `ackrep --prepare-script <entity_key>` from the container cli to create a python script (`execscript.py` located in ackrep_data) with all necessary imports.
- Note that the ackrep_data directory is shared between your machine (host) and the container, therefore you can make modifications on your host system using your favorite editor.
- Make adjustments to the imported files (parameters, plots, etc.) or edit the execscript to create a new program using e.g. system models or method packages. If you want to edit the execscript, you sould make a copy and rename the edited file.
- To test your script, run `python execscript.py` from the container cli. Onfortunately, since the container is headless, plots have to be saved and examined on the host system.


---
If you are having trouble, see [troubleshooting](troubleshooting_users).

Continue understanding the [strucure of the data repo](ref_data_structure).

