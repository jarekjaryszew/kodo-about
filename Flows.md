# Flow Properties

A flow is implemented as a flow of _data_ and _action_ [events](./Events.md) related to a _flow id_ (`fid`). Input demand is managed with _lock_ events. The `node` _name_ and `qual_name` uniquely identifies the flow. The `qual_name` represents a package/module path the absolute path name of the flow.

A dynamic property `state` is calculated based on the following rule:

* `error` if event _fatal_ exists
* else: `done` if event _done_ exists
* else: `locked` if a _lock_ exists
* else: `pending`
