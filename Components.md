## COMPONENT OVERVIEW

This document provides an overview of the key components of the kodosumi and related frameworks, focusing on its capabilities in observability, interoperability, certifiability, operability, and commerciability. 

kodosumi is designed to facilitate the management and execution of agentic flows at scale and within a distributed peer network. It ensures **observability** allowing users and developers to monitor the state changes and results of flows. **Interoperability** is enabling seamless interaction between clients, nodes, and flows. The framework supports **certifiability** ensuring that only authorized users can access flows and nodes. Furthermore certified badges qualify and quantify service and quality level. **Operability** is maintained by monitoring logs, metrics, events, and alerts to ensure smooth operation of the system. Finally, **commerciability** within a marketplace facilitates trust, contracting and pricing of agents, flows and nodes.

### [registry](./Registry.md)

#### Purpose:

The [registry](./Registry.md) is the central repository for managing and tracking [nodes](./Nodes.md), [flows](./Flows.md), and their interaction. The [registry](./Registry.md) facilitates coordination, discovery, and communication between different [nodes](./Nodes.md). 

#### Collaboration & Interfaces

It synchronises and interacts with all involved parties via restful APIs:
* clients - **read-only** access to navigate the [registry](./Registry.md)
* [nodes](./Nodes.md) - **read/write** access to go online and offline, heartbeat and health data
* [flows](./Flows.md) - **no access** 
    * [flows](./Flows.md) run on the [node](./Nodes.md) and the execution is uncorrelated to the _[registry](./Registry.md)_
    * [flow](./Flows.md) usage and health monitoring is the responsibility of the _[node](./Nodes.md)_
* masumi - **not in scope, yet**  as the [Auth Provider](./Authentication.md)

### [node](./Nodes.md)

#### Purpose:

The [node](./Nodes.md) is the primary contact for clients and peers to manage and execute [flows](./Flows.md). 

#### Collaboration & Interfaces:

It interacts with clients and the [registry](./Registry.md) (handshaking and heartbeats). The [node](./Nodes.md) is driving [flow](./Flows.md) execution and shares state with the [flow](./Flows.md).

### [flow](./Flows.md)

#### Purpose:

A [flow](./Flows.md) runs and orchestrates one or multiple crews. The entry-point of a [flow](./Flows.md) is either
1) a yaml file with crew [flow](./Flows.md) information to kick-off, i.e. `koco run ./assets/search.yaml` or
2) a `crewai.crew` `Crew` or `Flow` object to kick-off, i.e. `koco run flows.serviceplan.Search`

#### Collaboration & Interfaces:

It feeds events into the shared [flow](./Flows.md) state (`Actor`). The [node](./Nodes.md) processes and persists the [flow](./Flows.md) event stream. Clients consume this event stream connecting to the [node](./Nodes.md). On _command-line-level_ (CLI) the `koco` (_kodosumi controller_) intercepts agentic frameworks with callbacks, and stream monitoring.

### [event state and stream](./Events.md)

#### Purpose:

The event stream facilitates real-time communication and synchronization of state changes of [flows](./Flows.md) (_Oberservability_).

#### Collaboration & Interfaces:

The [flow](./Flows.md) event stream including state change requests are of interest to various parties with granted access permissions:
1) the user who executed a [flow](./Flows.md) wants to review the result 
2) the developer and operator want to review [flows](./Flow.md) usage, runtime/execution behavior

### [worker](https://docs.ray.io/en/latest/ray-references/glossary.html#term-Worker-process-worker)

#### Purpose:

A worker is a distributed computation/processing unit. The worker facilitates scaling and the concept is borrowed from [Ray](http://www.ray.io)

#### Collaboration & Interfaces:

A cluster of workers execute (stateless) remote procedures and share stateful `Actors` as the event state and stream of flows. The worker shares the event state and stream with other workers and the [node](./Nodes.md) which drives the execution of flow in a distributed, concurrent cluster environment.

### [auth provider](./Authentication.md)

#### Purpose:

The purpose of the [Auth Provider](./Authentication.md) is user and group (role) management, and certificate management. All communication cross network depends and relies on the [Auth Provider](./Authentication.md) to authenticate, authorize and certify

1) users to access and manage [nodes](./Nodes.md),
2) users to access and manage [flows](./Flows.md), 
3) users to access and manage [events](./Events.md) including flow details and results
4) nodes and flows to hold certificates

#### Collaboration & Interfaces:

All parties rely on the [Auth Provider](./Authentication.md) to authorize users and to query access and other certificates for users, nodes and flows.

### [driver](https://docs.ray.io/en/latest/ray-references/glossary.html#term-Driver)

#### Purpose:

The driver is the process that initiates and manages the execution of flows. It is essentially the main program that uses the Ray library to distribute and parallelize computations across a cluster of nodes. The driver is responsible for defining the tasks and actors, submitting them to the Ray cluster, and collecting the results. The term and concept is borrowed from [Ray](http://www.ray.io).

#### Collaboration & Interfaces:

The driver is executed on the _node_ and interacts with Ray components to facilitate distributed and concurrent flow execution. The worker shares the event state and stream with other workers and the [node](./Nodes.md) which drives the execution of flow in a distributed, concurrent cluster environment.

### engine

#### Purpose:

The _engine_ is responsible to interact with the _agentic framework_ which runs the _flow_. While the driver facilitates _interaction of workers_ the _engine_ manages and oversees the processing _within_ the agentic framework.

The engine facilitates:

* Observability - to capture information events emitted by the agentic framework responsible to run the flow
* Interoperability - to interact with humans and systems (form based, request/response, natural language)
* Operability - to persist logs, metrics, events, results, and to alert

The _node_, _driver_, _worker_ and _engine_ are ignorant and agnostic to the agentic framework. The _driver_ and _worker_ are concepts relevant to scale and borrowed from [Ray](https://www.ray.io/). The _engine_ on the other side intercepts the agentic framework and is the adapter.

#### Collaboration & Interfaces:

* the **_engine_** is executing and observing the flow running on a _worker_
* the **_engine_** uses the flow _event state and stream_ to establish event-driven communication between agentic framework and _kodosumi_.
