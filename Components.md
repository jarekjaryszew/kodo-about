## COMPONENT OVERVIEW

### [registry](./Registry.md)

#### Purpose:

The [registry](./Registry.md) is the central repository for managing and tracking [nodes](./Nodes.md), flows, and their interaction. The [registry](./Registry.md) facilitates coordination, discovery, and communication between different [nodes](./Nodes.md). 

#### Collaboration & Interfaces

It synchronises and interacts with all involved parties via restful APIs:
* clients - **read-only** access to navigate the [registry](./Registry.md)
* [nodes](./Nodes.md) - **read/write** access to go online and offline, heartbeat and health data
* flows - **no access** 
    * flows run on the [node](./Nodes.md) and the execution is uncorrelated to the _[registry](./Registry.md)_
    * flow usage and health monitoring is the responsibility of the _[node](./Nodes.md)_
* masumi - **not in scope, yet** - some resource needs to be shared
    * is masumi the Auth Provider?

### node

#### Purpose:

The [node](./Nodes.md) is the primary contact for clients and peers to manage and execute flows. 

#### Collaboration & Interfaces:

It interacts with clients and the [registry](./Registry.md) (handshaking and heartbeats). The [node](./Nodes.md) is driving flow execution and shares state with the flow.

### flow

#### Purpose:

A flow runs and orchestrates one or multiple crews. The entry-point of a flow is either
1) a yaml file with crew flow information to kick-off, i.e. `koco run ./assets/search.yaml` or
2) a `crewai.crew` `Crew` or `Flow` object to kick-off, i.e. `koco run flows.serviceplan.Search`

#### Collaboration & Interfaces:

It feeds events into the shared flow state (`Actor`). The [node(./Nodes.md)] processes and persists the flow event stream. Clients consume this event stream connecting to the [node(./Nodes.md)]. On _command-line-level_ (CLI) the `koco` (_kodosumi controller_) intercepts agentic frameworks with callbacks, and stream monitoring.

### event state and stream

#### Purpose:

The event stream facilitates real-time communication and synchronization of state changes of flows (_Oberservability_).

#### Collaboration & Interfaces:

The flow event stream including state change requests are of interest to various parties with granted access permissions:
1) the user who executed a flow wants to review the result 
2) the developer and operator want to review flow usage, runtime/execution behavior

see [event types](Events.md)

### worker

#### Purpose:

A worker is a distributed computation/processing unit. The worker facilitates scaling. 

#### Collaboration & Interfaces:

A cluster of workers execute (stateless) remote procedures and share stateful `Actors` as the event state and stream of flows. The worker shares the event state and stream with other workers and the [node(./Nodes.md)] which drives the execution of flow in a distributed, concurrent cluster environment.

### auth provider

#### Purpose:

The purpose of the Auth Provider is user and group (role) management, and permission management. All communication cross network depends and relies on the Auth Provider to authenticate and authorize users to access

1) [nodes(./Nodes.md)],
2) flows, 
3) events (flow details and results)

A worker is a distributed computation/processing unit. The worker facilitates scaling. 

#### Collaboration & Interfaces:

All parties rely on the Auth Provider to authorize users and to query access permissions for users.

### client

The various forms of clients in the system are:

* **all**
    * navigate the [registry](./Registry.md) (AKA sokosumi)
    * launch agents
    * submit forms to supply release candidates (to _locks_)
    * reconnect, revisit, review, follow, restart, kill, remove, share flow executions 

* **developer** and **administrator** (devops)
    * a simple local restful API and to navigate the local [registry](./Registry.md)
    * a collection of desktop tools and clients to develop flows, crews, agents, tasks, and tools (AKA Development Environment or IDE)
    * review runtime behavior
    * mesh the [registry](./Registry.md) - there might be [registry](./Registry.md) properties which merge, allow tweaking
    * cleanup stale data, logs and states, locally and in the cluster
    * scale the cluster of [node](./Nodes.md) and workers up and down

### vacuum & cleanup

#### Purpose:

The [node](./Nodes.md) driver creates event files and a vast amount of logging records for each flow execution. Each [node](./Nodes.md) must therefore cleanup the following storage elements:
* stale log files
* stale event files
* stale flow shares
* stale short-term and long-term memory

#### Collaboration & Interfaces:

No collaboration required. Each [node](./Nodes.md) operator is responsible to run regular cleanup procedures.

