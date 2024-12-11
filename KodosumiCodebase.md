# Kodosumi Codebase
## General
All the deliverables will be written in Python
## Package overview
```mermaid
classDiagram
    kodosumi-base <|-- kodosumi-registry
    kodosumi-base <|-- kodosumi-node
    kodosumi-base <|-- kodosumi-connector-base
    kodosumi-connector-base <|-- kodosumi-connector-crewai
    kodosumi-connector-base <|.. kodosumi-connector-autogen
    crewai <|-- kodosumi-connector-crewai
    autogen <|.. kodosumi-connector-autogen

    class kodosumi-registry{
        Concrete implenentation
    }

    class kodosumi-node{
        Concrete implenentation
        or base package?
    }

    class kodosumi-connector-base{
        Base package
    }

    class kodosumi-connector-crewai{
        Base package
        to be installed
        alongside crews
    }

    class kodosumi-connector-autogen{
        Base package
        to be installed
        alongside autogen
    }
```
- kodosumi-base : contains all the models (DTOs) and the base connector interface
- kodosumi-registry
- kodosumi-node
- kodosumi-connector-* - those will contain concrete implementations of the connectors/workers