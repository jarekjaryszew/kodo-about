# Events

This is an overview of the four kinds of events: _data_, _process_, _manage_, and _lock_, their producers and processors.

| KIND    | EVENT               | PRODUCER | CONSUMER |
| ------- | ------------------- | -------- | -------- |
| data    | inputs              | node     | flow     |
| data    | version             | node     | flow     |
| data    | config              | node     | flow     |
| data    | crew                | node     | flow     |
| data    | agent, tasks, tools | node     | flow     |
| process | progress            | flow     | -        |
| process | action              | flow     | -        |
| process | action-error        | flow     | -        |
| process | result              | flow     | -        |
| process | result-error        | flow     | -        |
| process | final               | flow     | -        |
| process | fatal               | flow     | -        |
| manage  | acknowledge         | node     | node     |
| manage  | share               | node     | node     |
| manage  | (restart)           | node     | node     |
| manage  | delete              | node     | node     |
| manage  | kill                | node     | node     |
| lock    | lock                | flow     | node     |
| lock    | lock-timeout        | flow     | node     |
| lock    | candidate           | node     | flow     |
| lock    | unlock              | flow     | node     |
| lock    | unlock-deny         | flow     | node     |
| lock    | unlock-timeout      | node     | client   |

**RFC:** 
* rename _crew_ into _flow_
* the _done_ and _final_ event are redundant and can be combined into one (_final_).

# Details 

## Data Events

  * `input` - user or system input data (cli or api)
  * `version` - kodosumi version
  * `config` - yaml source
  * `crew` - crew info (**rename: _flow_**)
  * `agent`, `task` and `tools` info

## Action Events

* `action` - action info
* `final` - final result including runtime in human readable format and total seconds
* `result` - intermediate result, grouped by agents
* `progress` - progress indicator
* error and exception handling:
    * `action-error` - action exception info
    * `result-error` - result exception info
    * `fatal` - exception not handled otherwise

## Lock Events

* `lock` - contains the _lock id_ (`lid`) and demand attributes
* `candidate` - supply attributes related to a `lock`
* `unlock` - attributes releasing a `lock` related to a `candidate` within `unlock-teamout`
* `unlock-deny` - deny attributes related to a `lock` and a `candidate` within `unlock-teamout`
* `unlock-timeout` - related to a `lock` and `candidate` within i.e. 60 seconds after `POST {fid}/lock/{lid}/candidate`
* `lock-timeout` - related to a  `lock` within i.e. 18 hours after lock with no successful `unlock`

## Management Events

* `acknowledged` - full download of the finished event stream by the user who launched the flow automatically acknowledges the flow. A `PUT /{fid}/ack` manually acknowledges a flow if the flow is not _pending_.
* `share` - grant access permission to the supplied users and roles including the level of access and read-only/execution rights.
* `delete` - all flow data if the event is acknowledged from client and node.
* `kill` - the event if the flow is still _pending_ and request the [driver](./Components.md#driver) to stop the processing.
* Action _restart_ is not logged as an event. Instead it copies all `inputs` and starts a fresh flow with the same input. _Process_, _manage_, and _lock_ events are not copied from the previous to the new flow.
