# Use Cases

## Sebastian Küpers Original Use Cases

1. a SP developer can **launch an agentic crew** as API with improved logging and agent monitoring (through a docker container, to allow to easily launch an agentic service)
2. a SP developer and admin can **discover** available agentic crews within the Serviceplan Group, through a webUI, which is running as part of the docker container, to understand which other services are out there
3. a SP developer can **submit a request** to any agentic crew through the WebUI and get the final results to have an easy way to test other agentic crews
4. a SP developer can see how many **requests** these crews are getting and if there are any errors through a WebUI, to understand how popular and proper working their agentic crews are
5. a SP developer can **build an agentic crew** which delegates work to another agentic crew through a custom agentic tool for crewAI and waits for their response to process it further
6. a SP developer can leverage a **shared knowledge base** about clients and topics for their agentic crews to tap into the wider knowledge of the SP group and their agents

### Use Case Details

> **Flows and Crews**
> _note:_ In the context of use cases the terms _flow_ and _crew_ are used as synonyms.

* A developer 
    * implements, maintains and tests _(skue#1?)_ a flow
    * integrates an existing flow into his flow _(skue#3 and skue#5)_.
    * publishes, deploys and promotes a flow _(skue#1 and skue#2)_
    * publishes, deploys and promotes long-term memory updates _(skue#6)_
    * scales the cluster up/down/auto _(skue#1 on docker)_
    * reviews the usage, runtime, errors, and results of flows _(skue#4)_
* An operator
    * creates and manages users and access groups
    * grants and revokes node and flow access to users and groups
* A business user
    * browses and searches for available flows _(skue#2 and skue#3)_
    * launches a flow with input _(skue#3)_
    * kills a running flow
    * restarts a flow
    * reviews _(skue#4)_ and monitors a running flow _(skue#1)_
    * revisits a finished flow _(skue#3)_

## Roles

The following roles use and maintain the kodosumi system

* Operator - manage users, access groups and operations at scale
* (Software) Developer - implement, optimize and deploy agents and tools
* (System) Developer - implement the framework
* (Workflow) Developer - implement and optimize prompts
* Business User - start agents and consume results
