## STARTUP SEQUENCE

> **NOTE** on Authorization & Access Control
> 
> Authorization and access control provides the **Auth Provider** and is _not_ 
> in scope of the _node_ or _registry_ services.
>
> Each service (node and registry) contact the AuthProvider on at their own
> discretion.

### NODE STARTUP
A node starts up and opens the following endpoints. The list of flows is based on a scope file (i.e. `node1.yaml`) or some _auto-discovery_ mechanism.
* `GET /flows` - query the list of flows
* `GET /nodes` - query the list of nodes (`n=1`)
* `GET /flow` - query the list of flows in state _error_, _done_, _locked_, or _pending_ (paginated and in details)
* `POST /flow` - launch a flow
* `GET | PUT /{fid}/{action}`, with _action_ `state, stream, restart, kill, delete, ack, share, lock` - manage a flow
* `GET /state`  - give health information
* `GET | POST /clean` - cleanup stale storage
* `POST /shutdown` - gracefully shut down the node

If the node connects to one or more registries, then the node uses `POST /connect` and `POST /disconnect` to each registry service. See **REGISTRY STARTUP** and **HANDSHAKE**.

### REGISTRY STARTUP
A registry starts up and opens the following endpoints. The list of flows is based on a scope file (i.e. `node1.yaml`) or some _auto-discovery_. 
* `GET /nodes` - query the list of nodes (`n>=0`)
* `GET /flows` - query the list of flows
* `POST /connect` and `POST /disconnect` for nodes
* `GET /state`  - give health information
* `GET | POST /clean` - cleanup stale storage
* `POST /shutdown` - gracefully shut down the node

### HANDSHAKE BETWEEN NODE AND REGISTRY
If a node interacts with a registry it connects with 
* `POST http://registry/connect` 

**payload:**
```json
{
    "node": "flows.serviceplan.com",
    "flows": [
        {
            "path": "flows.serviceplan.public.CompetitorAnalysis",
            "name": "Competitor Analysis",
            "source": "./yaml/competitor.yaml",
            "description": "...",
            "author": "m.rau@house-of-communication.com",
            "organization": "Plan.Net Journey GmbH & Co. KG",
            "published": "2024-11-12T11:12+00:00",
            "release": "0.1.2"
        },
        {
            "path": "flows.serviceplan.trend.Analysis()",
            "name": "Trend Analysis",
            "source": "flows.serviceplan.trend.Analysis()",
            "description": "...",
            "author": "m.rau@house-of-communication.com",
            "organization": "Plan.Net Journey GmbH & Co. KG",
            "published": "2024-11-12T11:12+00:00",
            "release": "0.1.2"
        }
    ]
}
```
This is the minimum required information for each flow. A flow record with incomplete data is rejected. The node is responsible to maintain a unique `path`. The display `name` is human readable and can be changed.

All flows and the node are rejected if the registry did not register the node before with `POST /register`. This `POST` creates the following **`registry.admins`** record:
```json
{
    "admins": {
        "nodes": ["flows.serviceplan.com"]
    }
}
```

An admin can revoke this registry record with `DELETE /register` which pops the node from `registry.admins.nodes`.

The response of `POST /connect` lists the accepted list of flows for the connecting node.
```json
{
    "name": "flows.serviceplan.com",
    "flows": [
        "flows.serviceplan.public.CompetitorAnalysis",
        "flows.serviceplan.trend.Analysis()",
    ]
}
```
### REGISTRY SERVICE
After node connection, the registry service responds to clients' `GET /flows` and `GET /nodes` with the list of nodes and related flows for all nodes. This list contains additional node properties from the registry.

```json
{
    "nodes": [
        {
            "node": "flows.serviceplan.com",
            "online": "2024-11-12T08:00:00",
            "heartbeat": "2024-11-12T11:12:01",
            "flows": [
                {
                    "path": "flows.serviceplan.public.CompetitorAnalysis",
                    "name": "Competitor Analysis",
                    "source": "./yaml/competitor.yaml",
                    "description": "...",
                    "author": "m.rau@house-of-communication.com",
                    "organization": "Plan.Net Journey GmbH & Co. KG",
                    "published": "2024-11-12T11:12+00:00",
                    "release": "0.1.2"
                },
                {
                    "path": "flows.serviceplan.trend.Analysis()",
                    "name": "Trend Analysis",
                    "source": "flows.serviceplan.trend.Analysis()",
                    "description": "...",
                    "author": "m.rau@house-of-communication.com",
                    "organization": "Plan.Net Journey GmbH & Co. KG",
                    "published": "2024-11-12T11:12+00:00",
                    "release": "0.1.2"
                }
            ]
        }
    ]
}
```

### HEARTBEAT
#### ALTERNATIVE 1: active node
The node sends _heartbeats_ to the registry in regular intervals. This ping mechanism allows the registry to decide which nodes are reachable. A node with `now() - heartbeat > 300'` is considered _not alive_:
```
PUT /state
```

#### ALTERNATIVE 2: active registry
It is the responsibility of the _registry_ service to contact all known hosts in regular intervals. The _registry_ contacts each node to gather health information with
```
GET /state
```

It it the decision and responsibility of the _node_ to deliver additional information like _usage_, _availability_, _performance_.

### REGISTRY DISCONNECTION
The node disconnects from the registry with `POST /disconnect`. The method updates attribute `nodes["flows.serviceplan.com"]["online"] = None`. The client must decide how to handle nodes offline (_hide_ or _show_ them).
