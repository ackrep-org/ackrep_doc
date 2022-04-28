# Overview 
[server_url]: https://testing.ackrep.org


The [Web Server][server_url] contains the follwoing features:

## List all entities in database 


This page displays a list of all [entities](entity_types) in the database. In particular *problem_specifications*,
*problem_solutions* and *system_models* are of interest. 

Each problem specification has a corresponding problem solution that usually uses at least one method to solve the 
problem. This process can be examined by clicking `check solution` of a particular solution entity. 

Problem specifications can reference the underlying system model and vice versa. System Models can also be checked for 
consitency via their `simulate this model` function.

In each case, the `Result` container indicates, whether the underlying source code is implemented correctly.

## List all merge requests

## New merge request

## Search for entities in ontology (SPARQL)
