# Preliminary Guide for Contributing to the Data Repo

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
        - Copy your problem-specification-file into the directory and change name to `solution.py`.
        - Generate a new random key: `ackrep --key`.
        - Copy the file `metadata.yml` from an existing entity of the same type and change its content beginning with the new key.
        - Add all keys of solved problems and dependent packages.
- Evaluate solution:
    - Change working directory to `ackrep_data`.
    - Run `ackrep --load-repo-to-db .` in that directory.
    - Check a specific solution via `ackrep --check-solution problem_solutions/<your_problem_solution>/metadata.yml`.
    - If an error occurs:
        - Manually run `python execscript.py` and see the error messages.
        - Common issues:
            - Forgot to run `ackrep --load-repo-to-db .` after changing metadata.
            - ImportError due to missing package in the python_path (see `execscript.py` what actually is inserted); Probable cause: missing or wrong key in `metadata.yml`
            - Bad return-value of `evaluate_solution`

## Adding System Models
TODO
- neue System-Modell-Daten kommen aus [Rockstrohrepo](https://github.com/JRockstroh/Catalog-of-dynamical-and-control-system-models) im folgenden als `R\` bezeichnet. Ort der neuen System Modelle: `ackrep_data\system_models\<model_name>\`
1. Kopiere `_template`-Ornder. Ordner geeignet umbenennen (something_system?).
2. Neuen Key mit `ackrep --key` erzeugen und in metadata.yml eintragen, descriptions in .yml anpassen
3. `system_model.py` anpassen: `class Model(GenericModel)` aus `R\implementations\<model>\..._class.py` kopieren (siehe Kommentare im template)
        in `__init__` die Fehlerexception abändern:
        
        try:
            params.get_default_parameters()
        except AttributeError:
            self.has_params = False 
    - Sonderfall: keine Parameter benötigt: leere parameter Datei mit Kommentar versehen
5. `parameters.py` anpassen: modell-abhängigen Teil aus `R\implementations\<model>\..._parameters.py` kopieren (siehe Kommentare im template)
6. `simulation.py` anpassen: entsprechende Teile aus `R\implementations\<model>\..._test.py` an die markierten Stellen kopieren (siehe Kommentare im template).
    - Hier ist etwas **Anpassungsarbeit** notwendig!
    - `R\implementations\<model>\..._test.py` ausführen und finale Zustände notieren für `evaluate_simulation`
7. aus `R\models\<model>\` beide `.tex`-Dateien und die `.pdf` nach `ackrep_data\system_models\<model>\_system_model_data\` kopieren
    - `..._Documentation.tex` in `documentation.tex` umbenennen
    - `..._Documentation.pdf` in `documentation.pdf` umbenennen 
8. zum testen: 
    - `ackrep -csm <key>` = `ackrep --check-system-model <key>` führt umständlich `simulation.evaluate` aus
    - test über webserver: 
        - Bild
        - Pdf
        - Verlinkung