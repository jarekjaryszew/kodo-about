# kodo-about

design and other docs about kodosumi

## Concepts

* [UseCases](./UseCases.md)
* [Components](./Components.md)
  * [Registry](./Registry.md)
  * [Nodes](./Nodes.md)
  * [Flows](./Flows.md)
  * [Events](./Events.md)
  * [Authentication and Authorization](./Authentication.md)
* [Endpoints](./Endpoints.md)

## on _Privacy_ and _Security_

* All _input_ and _output_ data is considered private to the client which launches a [flow](./Flows.md) and the [node](./Nodes.md) running the [flow](./Flows.md). 
* A user can share a [flow](./Flows.md) with other users. A download link indicates that the other use is eligible to downstream the data from the [node](./Nodes.md).
* The [node](./Nodes.md) considers the [flow](./Flows.md) data _obsolete_ after the client who launched the [flow](./Flows.md) has fully downloaded the [flow](./Flows.md).
* Inter-node communication is considered to be secured and guarded by the network layer below kodosomi framework layer.
* Authentication and Authorization is handled by an [Auth Provider](./Authentication.md).
- A secure peer-to-peer communication between client and [node](./Nodes.md) ensures privacy.

## on _Scalability_

* kodosumi uses [Ray](https://github.com/ray-project/ray) to scale [flows](./Flows.md) from a laptop to the cloud.

## on _Error Handling_

* All [flows](./Flows.md) follow best practices to fail fast and fail hard. All _critical_ errors are considered a _fatal_ [event](./Events.md).

## on _Agent Lifecycle_ (Release Management)

A simple `bumpy.py` script overwrites the semantic version number (_major, minor, patch_) in `kodo`. No further release and version number are published for specific [flows](./Flows.md), crews, agents, tasks, or tools at the moment.

## on _Monitoring and Health Checks_

The [node](./Nodes.md) `POST /state` on a regular basis to the [registry](./Registry.md) service. This _heartbeat_ contains top-level [node](./Nodes.md) performance values (`PING`). `kodo` does **not** track usage information.

## on _Housekeeping_

The [node](./Nodes.md) driver creates event files and a vast amount of logging records for each flow execution. Each [node](./Nodes.md) must therefore cleanup the following storage elements in intervals:
* stale log files
* stale event files
* stale shares
* stale short-term and long-term memory
