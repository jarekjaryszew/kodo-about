# Kodosumi Codebase
## General
All the deliverables will be written in Python
## Package overview
```mermaid
classDiagram
    kodosumi-base <|-- kodosumi-registry
    kodosumi-base <|-- kodosumi-node
    kodosumi-base <|-- kodosumi-connector-crewai
    kodosumi-base <|.. kodosumi-connector-autogen
    crewai <|-- kodosumi-connector-crewai
    autogen <|.. kodosumi-connector-autogen
```
- kodosumi-base : contains all the models (DTOs) and the base connector interface
- kodosumi-registry
- kodosumi-node
- kodosumi-connector-* - those will contain concrete implementations of the connectors/workers