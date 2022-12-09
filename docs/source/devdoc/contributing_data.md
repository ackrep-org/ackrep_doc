(contributing_data)=
# Contributing to the Data Repo
Preliminary Guide for Contributing to the Data Repo.

Please also refer to our [contributing constitution](ref_constitution)

```{contents} Table of contents
:backlinks: none
:depth: 3
:local: true
```

## Adding Problem-Methode-Solution tuples

- Preparation:
    - [Install](installation) the ackrep software:
- Integration of new data:
    - New `ProblemSpecification`:
        - Create a new subirectory: like `ackrep_data/problem_specifications/<your_problem>`.
        - Copy your problem-specification-file into the directory and change name to `problem.py`.
        - Generate a new random key: `ackrep --key`.
        - Copy the file `metadata.yml` from an existing entity of the same type and change its content beginning with the new key.
        - Ensure that the function `evaluate_solution` returns an instance of `ackrep_core.ResultContainer` see examples.
    - New `MethodPackage`:
        - Create a new subirectory: like `ackrep_data/method_packages/<your_method>/src`.
        - Copy your code into that directory.
        - Generate a new random key: `ackrep --key`.
        - Copy the file `metadata.yml` from an existing entity of the same type to `ackrep_data/method_packages/<your_method>` and change its content beginning with the new key.
    - New `ProblemSolution`:
        - Create a new subirectory: like `ackrep_data/problem_solutions/<your_problem_solution>`.
        - Copy your problem-solution-file into the directory and change name to `solution.py`.
        - Generate a new random key: `ackrep --key`.
        - Copy the file `metadata.yml` from an existing entity of the same type and change its content beginning with the new key.
        - Add all keys of solved problems and dependent packages.
- Evaluate solution:
    - Change working directory to `ackrep_data`.
    - Run `ackrep --load-repo-to-db .` in that directory.
    - Check a specific solution via `ackrep --check problem_solutions/<your_problem_solution>/metadata.yml`.
    - If an error occurs:
        - Manually run `python execscript.py` and see the error messages.
        - Common issues:
            - Forgot to run `ackrep --load-repo-to-db .` after changing metadata.
            - ImportError due to missing package in the python_path (see `execscript.py` what actually is inserted); Probable cause: missing or wrong key in `metadata.yml`
            - Bad return-value of `evaluate_solution`

## Adding System Models
directory for a new system model: `ackrep_data\system_models\<model_name>\`

1. Copy the `_template`-folder. Rename it in the following way: `<model_name>_system`
2. Generate a new key with `ackrep --key` 
3. Edit `metadata.yml`
    - insert the generated key
    - add a name, short description and suitable tags
    - fill in your full name and email in the creator space
        ``` 
         creator: 'Max Mustermann <max.mustermann{ät}gmail.com>'
        ```
    - add the creation date like `2022-04-21`
    - insert the estimated runtime of the simulation such as `10s`
4. Edit `system_model.py`
    - adjust `def initialize(self)`
        - define the number of inputs `self.u_dim`
        - define the system dimension `self.sys_dim`
        - in case your model is an n extendable system:
            - remove `self.sys_dim`, you don't have to define it
            - define `self.default_param_sys_dim` for the simulation, this dimension should suit the dimension of the example in your parameter file
    - adjust `def uu_default_function(self)`
        - in case your model doesn't need any input function, you can delete this whole function. Otherwise:
        - create symbolic input functions
        - transform symbolic to numeric function via `st.expr_to_func()`
        - adjust `def uu_rhs(t. xx_nv)`
            - create the numerical values 
            - return inputs within a list
    - adjust `def get_rhs_symbolic(self)`
        - get the symbolic state components, parameters and inputs
        - define the symbolic functions
        - return the functions within a list
5. Edit `parameters.py`
    - in case your model doesn't need any parameters, just leave a comment like `#This model does not need any parameters.` and delete everything else. Otherwise:
    - create the symbolic parameters such as 
        ```
        pp_symb = [l, g, a, omega, gamma] = sp.symbols('l, g, a, omega, gamma', real=True)
        ```
    - OPTIONAL: You can define a range your parameters. This range appears in the pdf. If a parameters is chose outside its designated range, a warning is issued.
        ````{note}
        Right now, you can only define a parameter range for *all* parameters or for none.
        ````
        You have to provide the variable `pp_range_list` as a `list` of the same length as `pp_symb`. `pp_range_list` has to contain a range object for each parameter in the same order as they appear in `pp_symb`. Valid values for `pp_range_list` are:

        | Information to be expressed                               | Syntax       |
        |-----------------------------------------------------------|--------------|
        | open Intervall (a,b) (use `np.inf` to represent infinity) | `list` (a,b) |
        | closed intervall [a,b]                                    | `list` [a,b] |
        | number field (e.g. Z)                                     | `str` "Z"    |
        | parameter is constant/  parameter shall not be changed    | ``None``         |

    - you can define auxiliary symbolic parameters, which should not be numerically represented in the parameter table
    - define symbolic parameter functions and add them to the list `pp_sf`
    - fill in the list for substitution, if you don't need it leave the list empty. **Don't** delete it.
    - define the LaTeX table
        - the table always contains the two columns `Symbols` and `Variables`. You can add other columns before and afte these, for example `Parameter Name` or `Unit` 
        - define for each new column the entries in a list
        - when it is about a column before the fixed ones, add it to the list `start_columns_list`, if not add it to `end_columns_list`
        - for example:
        ```
        tabular_header = ["Parameter Name", "Symbol", "Value", "Unit"]

        col_alignment = ["left", "center", "left", "center"]

        col_1 = ["acceleration due to gravity", 
                "distance of forces to mass center",
                "mass",
                "moment of inertia"
                ] 
        
        start_columns_list = [col_1]

        col_4 = [r"$\frac{\mathrm{m}}{\mathrm{s}^2}$", 
                "m",
                "kg",
                r"$\mathrm{kg} \cdot \mathrm{m}^2$"
                ]
    
        end_columns_list = [col_4]
        ```
        - the result in LaTeX:
        ```{image} images/latex_table.png
        :width: 500
        ```
6. Edit `simulation.py`
    - adjust `def simulate()`
        - define initial state values `xx0`, simulation time `tend` and vector of times for simulation `tt`
        - define inputfunction `uu`
    - adjust `def save_plot()`
        - plot the relevant and representing data of your model here
        - if you want to create multiple plots, either use subplots or create them separately and always call `save_plot_in_dir(<plot_name>.png)` after each plot. Make sure to chose unique names. Plots will appear in alphanumeric order.
    - adjust `def evaluate_simulation(simulation_data)`
        - fill in the `expected_final_states` of your model to check whether everything is working out
7. Got to `_system_model_data`
    - adjust `documentation.tex`
        - update the headline with your system model name
        - explain parameters, inputs and state components in the subsection `Nomenclature for Model Equations` 
        - define state vectors, input vectors and system equations
        - list parameters and outputs, if they don't exist just fill in `<not defined>`
        - note the assumptions made
        - describe  the `Derivation and Explanation` 
        - add your references 
8. Update the `parameters.tex` via `ackrep --update-parameter-tex <key>` 
9. Create the `documentation.pdf` via `ackrep --create-pdf <key>`
10. Test your model:
    - `ackrep -csm <key>` = `ackrep --check-system-model <key>` 
    - or via webserver:
        - `ackrep -l ../ackrep_data`
        - `python manage.py runserver`
