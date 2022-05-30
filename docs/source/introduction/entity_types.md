(sec_entity_types)=
# Entity Types


The project aims to represent knowledge (in the field of automatic control) in a special way:
as a combination of *problem-specifications* and *problem-solutions*. Both, problems and solutions
can be represented by software modules. To ensure integrity and reproducability,
solution-modules are checked automatically against the respective problems. To facilitate the
operation of this process, some metadata and some other components are also managed in this repo.
Alltogether there are the following entities:



- problem class
    - e.g. Trajectory Planning for Mechanical Systems
- problem specification
    - e.g. Swingup control for the Acrobot with specific parameters
- problem solution
    - e.g. Invocation of an allgorithm which calculates a swingup trajectory for the acrobot
- method package
    - e.g. A collection of functions which together implement an algorithm to calculate state space
    transitions for dynamical systems
- system model
    - e.g. Differential Equations of the Lorenz System
- documentation
    - e.g. A mathematical description of an problem or algorithm e.g. as `.tex`-file or an different kind of documentation
- environment specification
    - A set of rules to setup a software environment such that the above software components
    can be executed
- comment
    - A structured way to add remarks or questions to any entity

**Summary**: In some sense ACKrep translates the concept of continous integration from software
engineering to research in automatic control.

---
Read about the [terminology](sec_terminology) used in this project.