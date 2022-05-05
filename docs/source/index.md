# Active Control Knowledge REPository (ACKREP) Dokumentation

% TODO: copy pasta aus ackrep.org

Traditionally, human knowledge is represented mainly in form of written natural language, symbolic formulas and pictures.
The project **ACKREP** aims to supplement these representation forms with additional formal (i.e. machine interpretable)
representations. The scope of this project is control theory and control engineering (subsumed under "automatic control").

This discipline is characterized by a wide range of application fields from different physical domains and by a
heterogeneous methodical landscape from different areas of mathematics, engineering and computer science.

Basically, the motivation for ACKREP is to facilitate knowledge transfer both

- within the discipline of automatic control (i.e. within subdisciplines), as well as
- with areas of potential application (e.g. mechanical engineering, chemical process engineering, etc.).

How best to achieve these goals is an open question and subject to ongoing research. More specifically, ACKREP Code
constitutes a repository of control-related problems and solutions (represented as executable code). The Methodnet is a
more abstract formal representation of control-theoretic methods which can be applied to a concrete setting by linking
them via a path-finding algorithm.

This research is carried out at the Institute of Control Theory, TU Dresden.

## Important Links

- [Web Interface](https://testing.ackrep.org).
- [Official website](https://ackrep.org/) of the project.
- [Publication](https://ieeexplore.ieee.org/document/9259657) of the research.

## General Concept of the Project (Working Title)

Information regarding an overview of the project and some important terms and concepts.

```{toctree}
:caption: Introductory Information
:maxdepth: 1
introduction/overview
introduction/entity_types
introduction/terminology
```


## User Documentation
Information regarding the local setup and passive usage of ACKREP.
```{toctree}
:maxdepth: 1
:caption: User Documentation
userdoc/web
userdoc/installation
userdoc/run
userdoc/data_structure
userdoc/troubleshooting
```

## Developer Documentation
Information regarding actively contributing to ACKREP on different levels.
```{toctree}
:maxdepth: 1
:caption: Developer Documentation
devdoc/constitution
devdoc/contributing_data
devdoc/contributing_framework
devdoc/contributing_deployment
devdoc/design_considerations
devdoc/troubleshooting
devdoc/contributing_docs
```
