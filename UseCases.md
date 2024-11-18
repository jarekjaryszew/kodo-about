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
    * implements, maintains and tests _(skue#1?)_ a [flow](./Flows.md)
    * integrates an existing [flow](./Flows.md) into his [flow](./Flows.md) _(skue#3 and skue#5)_.
    * publishes, deploys and promotes a [flow](./Flows.md) to a [node](./Nodes.md) _(skue#1 and skue#2)_
    * publishes, deploys and promotes long-term memory updates to a [node](./Nodes.md) _(skue#6)_
    * scales the [node](./Nodes.md)  up/down/auto _(skue#1 on docker)_
    * reviews the usage, runtime, errors, and results of [flows](./Flows.md) and [node](./Nodes.md) _(skue#4)_
* An operator
    * creates and manages users and access groups
    * grants and revokes [node](./Nodes.md) and [flow](./Flows.md) access to users and groups
* A business user
    * browses and searches for available [flows](./Flows.md) _(skue#2 and skue#3)_
    * launches a [flow](./Flows.md) with input _(skue#3)_
    * kills a running [flow](./Flows.md)
    * restarts a [flow](./Flows.md)
    * reviews _(skue#4)_ and monitors a running [flow](./Flows.md) _(skue#1)_
    * revisits a finished [flow](./Flows.md) _(skue#3)_

## Roles

The following roles use and maintain the kodosumi system

* Operator - manage users, access groups and operations at scale
* (Software) Developer - implement, optimize and deploy agents and tools
* (System) Developer - implement the framework
* (Workflow) Developer - implement and optimize prompts
* Business User - start agents and consume results

# Clients

The clients of the system are used to

* navigate the [registry](./Registry.md)
* launch flows on a [node](./Nodes.md)
* submit forms to supply input to flows
* reconnect, follow, revisit, review, restart, kill, remove, and share individual [flow](./Flows.md) executions
* a collection of desktop tools and clients to develop flows, crews, agents, tasks, and tools (AKA Development Environment or IDE)
* review runtime behavior on a [node](./Nodes.md)
* mesh the [registry](./Registry.md)
* cleanup stale data, logs and states
* scale the [node](./Nodes.md) and workers up and down
