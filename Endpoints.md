# Endpoints

## Registry API

### `PUT /up`

At startup the [node](./Nodes.md) creates the initial local [registry](./Registry.md) and sends it as payload to the first configured registry service.

**payload:**
```
{
    node: "flows.serviceplan.com",
    flows: [
        {
            name: "Competitor Analysis",
            source: "./yaml/competitor.yaml",
            description: "foo bar",
            author: "m.rau@house-of-communication.com",
            organization: "Plan.Net Journey GmbH & Co. KG",
            published: "2024-11-12T11:12+00:00",
            release: "0.1.2",
            rph: 0.0124928
        },
        ...
    ]
}
```

The registry server to connect to are configured with OS variable `KODO_REGISTRY`.

```text
# .env
KODO_REGISTRY="1.2.3.4 5.6.7.8"
```

**response:**
Redirect to `GET /nodes`.

### `PUT /down`

At shutdown the [node](./Nodes.md) announces to leave the network.

**payload:**
from http headers

**response:**
Redirect to `GET /nodes`

### `GET /nodes`

Retrieves [nodes](./Nodes.md) information from the global _[registry](./Registry.md)_.

**parameters:**
* `search` - glob search in [nodes](./Nodes.md).
* `query` - pandas `.query` method argument to search the [nodes](./Nodes.md)
* `p` - current page in paginated output
* `pp` - records per page in paginated output
* `by` - list of sort order properties as comma-separated in format `name (asc|desc)`

**response:**
* `total_records` - total number of records
* `filtered_records` - total number of hidden records
* `total_pages` - total number of pages
* `search`, `query`, `by`, `p` and `pp` (see above)
* `result` - list of [node](./Nodes.md) properties

### `GET /flows`
Retrieves the global _[registry](./Registry.md)_.

**parameters:**
* `search` - glob search in [flows](./Flows.md) `node`, `name`, `description`, `author`, `organization` values
* `query` - pandas `.query` method argument to search the [flows](./Flows.md)
* `p` - current page in paginated output
* `pp` - records per page in paginated output
* `by` - list of sort order properties as comma-separated in format `name (asc|desc)`

**response:**
* `total_records` - total number of records
* `filtered_records` - total number of hidden records
* `total_pages` - total number of pages
* `search`, `query`, `by`, `p` and `pp` (see above)
* `result` - list of [flow](./Flows.md) properties with [node](./Nodes.md) properties unwinded: `node`, `tags`, `availability`, `performance`, `online`, `ping` 

## Admin Panel API

### `POST /register`

With the admin panel administrators can register a [node](./Nodes.md). This add the [node](./Nodes.md) to `registry. admins.nodes`:

**payload:**
```
{
    node: "flows.serviceplan.com"
}
```

**response:**
`200 OK` or `201 Created`.

### `DELETE /register`

With the admin panel administrators can unregister a [node](./Nodes.md). This removes the [node](./Nodes.md) from `registry.admins.nodes` and `registry.nodes`.

**payload:**
```
{
    node: "flows.serviceplan.com"
}
```

**response:**
`200 OK` or `404 Not Found`.

### `POST /nodes`

With the admin panel administrators can post [node](./Nodes.md) meta data to `registry.admins.nodes`. This method replaces the complete entry in `registry.admins.nodes` with the playload. The property `registry.admins.nodes.*.flows` remains unchanged. The record is identified by the [node](./Nodes.md) name and must exist.

**payload:**
```
{
    node: "flows.serviceplan.com"
    tags: [
        "hidden"
    ],
    availability: 0.99,
    performance: 1.23,
    online: "2007-08-31T16:47+00:00",
    ping: "2024-11-12T11:12+00:00"
}
```

**response:**
`200 OK` with full node data or `404 Not Found`.

### `PUT /nodes`

With the admin panel administrators can update dedicated [node](./Nodes.md) meta data. The record is identified by the [node](./Nodes.md) name. All other properties are copied to the [registry](./Registry.md). The record must exist.

**payload:**
```
{
    node: "flows.serviceplan.com"
    ping: "2024-11-12T11:12+00:00"
    online: "2007-08-31T16:47+00:00",
    ping: "2024-11-12T11:12+00:00"
}
```

**response:**
`200 OK` with full node data or `404 Not Found`.

### `POST /flows`

With the admin panel administrators can post [flow](./Flows.md) meta data to `registry.admins.nodes.*.flows`. This method replaces the complete entry in `registry.admins.nodes.*.flows` with the playload. All other [node](./Nodes.md) properties remain unchanged. The record is identified by the `node` name and the flow `name`. The node must exist.

**payload:**
```
{
    node: "flows.serviceplan.com",
    name: "Competitor Analysis",
    tags: [
        "hidden"
    ],
    description: "foo bar"
}
```

**response:**
`200 OK` with full [flow](./Flows.md) data or `404 Not Found`.

### `PUT /flows`

With the admin panel administrators can update dedicated [flow](./Flows.md) meta data. The record is identified by the `node` name and the flow `name`. All other attributes are copied to the [registry](./Registry.md). The record must exist.

**payload:**
```
{
    node: "flows.serviceplan.com",
    name: "Competitor Analysis",
    description: "foo bar"
}
```

**response:**
`200 OK` with full flow data or `404 Not Found`.

## General API

### `GET /state`

Retrieves [node](./Nodes.md) and [registry](./Registry.md) server state with

**response:**
* `name`
* `python` - version
* `version` - kodosumi version
* `registry` - timestamp

### `PUT /state`

Sends [node](./Nodes.md) service state to the [registry](./Registry.md) service. This represents the heartbeat.

**payload:**
```
{
    node: "flows.serviceplan.com",
    registry: "2024-11-12T11:12+00:00"
}
```

which updates the `ping` property.

**response:**
`200 OK` or `406 Not Acceptable` if the [registry](./Registry.md) is timed out or `404 Not Found` if the [node](./Nodes.md) is unknown. Response data with

* `name`
* `python` - version
* `version` - kodosumi version
* `registry` - timestamp

## Flow API

### `POST /flow`

Launches a [flow](./Flows.md) on the connected [node](./Nodes.md). The [flow](./Flows.md) is identified by its name which is unique on the [node](./Nodes.md).

**payload:**
```
{
    name: "Competitor Analysis",
    inputs: {
        ...
    }
}
```

**response:**
`200 OK` or `403 Forbidden` or `404 Not Found` with response data

* `fid` - flow id

### `GET /{fid}/state`

Retrieve current [flow](./Flows.md) state.

**response:**
`200 OK` or `403 Forbidden` or `404 Not Found` with response data

* `fid` - flow id
* `name` - flow name
* `progress` - flow progress 0% - 100%
* `inputs` - client inputs
* `version` - kodosumi semantic version
* `config` - original yaml
* `crew` - name
* `tags` - list of tags (admin panel)
* `agents` with `tasks` and `tools`
* `done` - with runtime
* `final` - final result
* `acknowledged` - indicator if the flow has been acknowledged
* `state` - derived state _pending_, _error_, _locked_, _done_
* `lock` - lock demand attributes
* `author`
* `organization`
* `created` - date/time when the flow was first published
* `modified` - date/time when the flow was last modified
* `release` - flow semantic version

### `GET /{fid}/stream`

SSE stream of the flow [events](./Events.md).

### `PUT /{fid}/restart`

Copy and launch the [flow](./Flows.md).

### `PUT /{fid}/kill`

Kill a [flow](./Flows.md) in state _pending_ or _locked_, else fail with `400 Bad Request` or `404 Not Found`.

### `PUT /{fid}/delete`

Remove a [flow](./Flows.md) in state _error_ or _done_ with all correlated data, else fail with `400 Bad Request` or `404 Not Found`.

### `PUT /{node}/{fid}/ack`

Acknowledge a [flow](./Flows.md) in state _error_ or _done_, else fail with `400 Bad Request` or `404 Not Found`.

### `PUT /{fid}/share`

Share a [flow](./Flows.md) with a user. This request creates a `.role`  file next to the flow's _event log_ (`.log`) and _event stream_ (`.ev`). This allows the enrolled users to download the [flow](./Flows.md) from the [node](./Nodes.md).

**payload:**
```
{
    name: "Competitor Analysis",
    roles: [
        ...
    ]
}
```

**response:**
`200 OK` or `403 Forbidden` or `404 Not Found`.

## Lock API

### `GET /{fid}/lock/{lid}/candidate`

Renders the lock demand `lid` of flow `fid` as JSON or as an HTML form.

### `POST /{fid}/lock/{lid}/candidate`

Posts a unlock candidate attributes with lock `lid` and flow `fid`.

**payload:**
```
{
    fid: "1234-5678-91011",
    lid: "1234-5678-91011",
    supply: {
        ...
    }
}
```

**response:**
`200 OK` or `403 Forbidden` or `404 Not Found`.

## Housekeeping API

### `GET /cleanup`

retrieve list of
* acknowledged, outdated files
* on [registry](./Registry.md)
    * [nodes](./Nodes.md) and [flows](./Flows.md) which have not been announced recently (i.e. last month)

### `POST /cleanup`

remove
* acknowledged, outdated files

# Open Issues
* there will exist orphan nodes and orphan flows which require cleanup
* on [registry](./Registry.md)
    * [nodes](./Nodes.md) and [flows](./Flows.md) which have not been announced recently (i.e. last month)
