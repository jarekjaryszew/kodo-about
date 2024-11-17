> The purpose of this document is to analyse the registry's stakeholders, each stakeholder's need for control over the registry and make a decision: **who is responsible for the registry**.
> 
> The conclusion is based on the **PURPOSE** of the registry as **the central repository for managing and tracking nodes, flows, and their interaction.**

## INTERACTION

The *registry* is a central object and all parties involved have *demand* and provide *supply* for the _registry_.

### PARTY MAP

| STAKEHOLDER  | GET                                      | POST                                       | DELETE          | PUT                        |
| ------------ | ---------------------------------------- | ------------------------------------------ | --------------- | -------------------------- |
| node         | `/state` (1), `/nodes` (2), `/flows` (3) | -                                          | -               | `/up` (6), `/down` (7),    |
| flow         | `/flows` (3)                             | -                                          | -               | -                          |
| event stream | -                                        | -                                          | -               | -                          |
| worker       | -                                        | -                                          | -               | -                          |
| marketplace  | `/state` (1), `/nodes` (2), `/flows` (3) | -                                          | -               | -                          |
| admin        | `/state` (1), `/nodes` (2), `/flows` (3) | `/register`(4), `/nodes` (8), `/flows` (9) | `/register` (5) | `/nodes` (8), `/flows` (9) |

#### comments
(1) query health information
(2) query node information (paginated)
(3) query flow information (paginated)
(4) register a node to the registry
(5) remove a node from the registry
(6) provide "startup" announcement from the node service (driver) to the _registry_
(7) provide "shutdown" announcement from the node (driver) to the _registry_
(8) provide additional node constraints to the _registry_
(9) provide additional flow constraints to the _registry_ 

> **important:** 
> the `/flows` endpoint is different to the `/flow` statement which is not related to _registry_ interaction

## REGISTRY FORMAT & STRUCTURE

The registry is a _dictionary_ (`dict`) with two top-level keys `.nodes` and `.admins` with separate concerns:
* `registry.nodes` (`dict`) - contains all announcements sent from the node services
    * keys are the _node names_
* `registry.admins` - contains all constraints set by the admin panel
    * keys are the _node names_

The following yaml contains all valid keys and example values.

```yaml
nodes:
  flows.serviceplan.com:
    flows:
    - source: ./yaml/competitor.yaml
      name: Competitor Analysis
      author: m.rau@house-of-communication.com
      organization: Plan.Net Journey GmbH & Co. KG
      published: 2024-10-14
      release: 0.1.0
    - source: mpf.flows.billing.Dispatch()
      name: Mediaplus Forschung Billing Dispatcher
      author: Adam+Eva@house-of-communication.com
      organization: Plan.Net Journey GmbH & Co. KG
      published: 2024-10-14
      release: 0.1.0

admins:
  flows.serviceplan.com:
    tags:
    - hidden
    availability: 0.99
    performance: 1.23
    online: 2007-08-31T16:47+00:00
    ping: 2024-11-12T11:12+00:00
    flows:
    - name: Competitor Analysis:
      tags:
      - test
    - name: Mediaplus Forschung Billing Dispatcher
      tags:
      - hidden
```

The registry record of `flows.serviceplan.com/Mediaplus Forschung Billing Dispatcher` is merged into the following payload:

```yaml
flows.serviceplan.com:
  tags:
  - hidden
  availability: 0.99
  performance: 1.23
  online: 2007-08-31T16:47+00:00
  ping: 2024-11-12T11:12+00:00
  flows:
  - source: ./yaml/competitor.yaml
    name: Competitor Analysis
    author: m.rau@house-of-communication.com
    organization: Plan.Net Journey GmbH & Co. KG
    published: 2024-10-14
    release: 0.1.0
    requests per hour: 0.0124928
    tags:
    - test
  - source: mpf.flows.billing.Dispatch()
    name: Mediaplus Forschung Billing Dispatcher
    author: Adam+Eva@house-of-communication.com
    organization: Plan.Net Journey GmbH & Co. KG
    published: 2024-10-14
    release: 0.1.0
    requests per minute: 0.0001929
    tags:
    - hidden
```

The nodes are merged based on the key. The flows are merged based on their `name` value. All records existing in `registry.nodes` are overwritten with values from `registry.admins.nodes`.

The following additional rules apply to merge the registry:
1) A key is created or updated if exists with `PUT /up` if a corresponding key exists in `registry.admins.nodes`.
2) A key is created in `registry.admins.nodes` with `POST /register`.
3) A key is deleted from `registry.admins.nodes` and from `registry.nodes` if exists with `DELETE /register`.
5) A `PUT /down` sets `registry.admins.nodes.*.online = None`
6) Active _pings_ to each node update the value in `registry.admins.nodes.*.ping` and `registry.admins.nodes.*.availability`.
7) Active _performance_ tests to each node update the value in `registry.admins.nodes.*.performance`.

Usage statistics are optional as in the following example:

```yaml
nodes:
  flows.serviceplan.com:
    flows:
    - source: ./yaml/competitor.yaml
      name: Competitor Analysis
      author: m.rau@house-of-communication.com
      organization: Plan.Net Journey GmbH & Co. KG
      published: 2024-10-14
      release: 0.1.0
      requests per hour: 0.0124928    # as promoted by the node 
      completion per hour: 0.0124901  # as promoted by the node 
```
