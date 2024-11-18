# Events

## Data Events

  * `input` - user or system input data (cli or api)
  * `version` - kodosumi version
  * `config` - yaml source
  * `crew` - crew info
  * `agent`, `task` and `tools` info
  * `done` - runtime in human readable format and total seconds
  * `acknowledged` - full download of the finished event stream by the user who launched the flow automatically acknowledges the flow. A `PUT /{fid}/ack` manually acknowledges a finished.

## Action Events

* `action` - action info
* `final` - final result
* `done` - execution time
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
