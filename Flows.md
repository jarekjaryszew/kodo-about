# FLOWS

## FLOW PROPERTIES

A flow is implemented as a flow of _data_ and _action_ [events](./Events.md) related to a _flow id_ (`fid`). Input demand is managed with _lock_ events. The `node` _name_ and `qual_name` uniquely identifies the flow. The `qual_name` represents a package/module path the absolute path name of the flow.

A dynamic property `state` is calculated based on the following rule:

* `error` if event _fatal_ exists
* else: `done` if event _done_ exists
* else: `locked` if a _lock_ exists and a corresponding `release` is missing
* else: `pending`

## FLOW INTERACTION

A flow interacts with the node and the agents which are part of an _agentic framework_ with the flow _event stream_. The node and the flow both are consumers and producers of this _event stream_. The node produces the initial stream of **data events** which are forwarded to the agent or crew of agents resulting in the flow's **process events**. Authorized clients produce **management events** and **lock events** through the nodes which are consumed and processed by the flow and the _agentic framework_. 

See [events](./Events.md)

### Node as Producer

The node procudes the following events with the launch of a flow: `inputs`, `version`, `config`, `flow` (AKA _crew_), and _agent, tasks and tools_. Endpoint is `[POST /flow](./Endpoints.md#post-flow)`. With `[PUT /{fid}/ack](./Endpoints.md#put-nodefidack)` a client acknowledges the flow if it is not _pending_, which produces the appropriate event _acknowlege_. A `[POST /{fid}/lock/{lid}/candidate](./Endpoints.md#post-fidlocklidcandidate)` supplies input with event _candidate_ data (see [lock with nodes](./Nodes.md#lock)). If the flow does not respond within a specified time the node triggers event _unlock-timeout_ which marks the _candidate_ obsolete.

A client manages the flow with the following _endpoints_ resulting in the corresponding _event_ produced and consumed by the node:

* [kill](./Endpoints.md#put-fidkill) - _kill_: the driver
* [restart](./Endpoints.md#put-fidrestart) - _restart_: the flow by creating a new flow with same input
* [delete](./Endpoints.md#put-fiddelete) - _delete_: all flow data
* [share](./Endpoints.md#put-fidshare) - _share_: the flow with other people

These events are directly processed by the node and copied to the event stream for information to whom it may concern. The _restart_ action is listed as a management event but is not recorded in the event stream. 

### Node as Consumer

An existing _lock_ event without a corresponding _unlock_ event renders the flow demand with [`GET /{fid}/lock/{lid}/candidate`](./Endpoints.md#get-fidlocklidcandidate). With a corresponding _unlock_ event a lock is considered _released_ and the `GET` request returns `404 Not Found`. With a corresponding _unlock-deny_ event a lock is considered _not released_ and with a corresponding `lock-timeout` event the node throws an exception and the flow is considered _fatal_.

The node retrieves the flow state with [`GET /{fid}/state`](./Endpoints.md#get-fidstate) based on the event stream. This includes progress, process, error and runtime state information among other. The event stream endpoint itself is [`GET /{fid}/stream`](./Endpoints.md#get-fidstream)`. The event stream is closed with events _final_ or _fatal_.

Events _share_, _kill_, _restart_, and _delete_ are produced and directly consumed by the node.

### Flow as the Producer

The flow observes _action_, _result_, _progress_, _final_ result, as well as processing errors (_action-error_, _result-error_, _fatal_). These events are pushed to the event stream. A _lock_ demand is created with the _lock_ event and released with the _unlock_ event. The _unlock-deny_ event marks a _candidate_ to unlock as obsolete.

### Flow as Consumer

Events _inputs_, _config_, _flow_ (AKA _crew_), _version_, _agents, tasks and tools_ represent the input to run the flow. Furthermore the flow consumes the _candidate_ event as supplied input to a lock.

## INSTRUMENTATION

The kodosumi framework must intercept and instrument agents', tasks', and tools' activities and results of _agentic frameworks_. kodosumi is framework-agnostic by providing adapters and proxies into all relevant agentic frameworks. 

The following programming patterns implement and instrument the frameworks to interact with kodosumi via the event state and stream. 

1. **Gateway Pattern** - wrap the flow application as a callable object and adopt it to trigger events
1. **Decorator Pattern** - wrap a flow's functions or class and adopt it to trigger events
2. **Logging Handler Pattern** - identify (filter & map) Python logging record patterns to trigger events
3. **Event Emitter Pattern** - use explicit function calls to trigger events (observer pattern)
4. **Class Proxy Pattern** - implement a proxy class OOP pattern into the agentic framework which triggers events
5. **Driver Declaration Pattern** - implement a human-readable yaml driver file to trigger events

The following sections discuss each pattern in a bit more detail. Each pattern is described with _crewai_ in mind.

### Flow Gateway Interface

This interface handles the flow as a callable. The approach borrows from PEP3333 and the web application gateway interface standards (synchronous [wsgi](https://peps.python.org/pep-3333/) and asynchronous [asgi](https://asgi.readthedocs.io/en/latest/)).

The FGI (Flow Gateway interface) is designed to promote a standard interface for agentic frameworks, and workflows to interact with kodosumi and related components (masumi and sokosumi). The flow is a callable object (function or method) as the main entry point. 

The [crewai quickstart example](https://docs.crewai.com/quickstart) with `crew.py` and **no** `main.py`:

```python
    # ...
    # crew implementation comes here, see
    # https://docs.crewai.com/quickstart
    # ...
    crew = LatestAiDevelopmentCrew().crew()
    if __name__ == "__main__":
        test = {
            'topic': 'AI Agents'
        }
        crew.kickoff(inputs=test)
```

With this pattern and the kodosumi CLI command `koco` the `crew` object instance is adopted with callbacks and log interceptors to identify and trigger kodosumi events.

Instead of `crewai run` (you do not use the `main.py` anymore, you only use `crew.py`) you spawn:

```bash
    koco run path.to.module:crew
```

### Decorator

With this pattern the crewai example decorated with `@CrewBase`, `@crew`, `@agent`, and `@task`, adds a `@KodoFlow` decorator:

    # src/latest_ai_development/crew.py
    from crewai import Agent, Crew, Process, Task
    from crewai.project import CrewBase, agent, crew, task
    from crewai_tools import SerperDevTool

    @KodoFlow  # <--- THIS IS NEW
    @CrewBase
    class LatestAiDevelopmentCrew():
    """LatestAiDevelopment crew"""

    # continues as in https://docs.crewai.com/quickstart

Wrapping the `CrewBase` adopts the crew with all required callbacks and log interceptors to identify and trigger kodosumi events.

You can also decorate individual functions and methods of a crewai object:

    @KodoEvent  # <--- THIS IS NEW
    def callback_function(output: TaskOutput):
        print(f"""
            Task completed!
            Task: {output.description}
            Output: {output.raw}
        """)

### Emitter

With this approach you directly emit kodosumi events in the methods/callbacks of the framework. Use for example crewai _task callbacks_ and _step callbacks_:

    def callback_function(output: TaskOutput):
        print(f"""
            Task completed!
            Task: {output.description}
            Output: {output.raw}
        """)
        kodo.core.emit(output)  # <--- THIS IS NEW

### Proxy

The proxy approach is saved for later. _crewai_ mainly follows the decorator approach and OOP is relevant with _crewai_'s concept of [flows](https://github.com/crewAIInc/crewAI/blob/main/src/crewai/flow/flow.py).

### Declarative Pattern

The declarative pattern emerges from the requirement to provide a human-readable YAML data structure as a UI to agent prompt engineers. The CLI command points to a yaml file instead of a _crewai_ callable object:

    koco run ./tests/assets/test.yaml

A template/example file to drive _crewai_ crew execution [can be found here](https://github.com/plan-net/kodo-core/blob/cli/docs/template.yaml).
