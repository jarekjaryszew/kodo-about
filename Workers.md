# Workers
## Overview
- Workers will be implemented by adding a connector to an existing agent project
- Workers wil expose an interface towards the node
- Flow developer will have to create a configuration that glues worker with flows
- Node address will be passed by env variables
- Worker will notify the node on startup
- Node will monitor if worker is ready to process by a health enpoint
- TBC - Worker may perfrom 1 flow at the time
- TBC - 1 worker contains 1 flow (crew)

## Scaling and lifetime
### Static nodes
- Simplest example, no scaling. Worker will just register itself with the node on startup.

### Externally managed workers
This option is directer towards power users of runtime. For example organizations that develop their own Kubernetes controllers and operators (Kubernetes Patterns, 2nd edition, chapter 27 and 28)
- These workers will automatically register themselves with their node on startup (or will be registered by external manager).
- Registration does not mean they have to be up from the start. External manager can handle that through webhooks.
- Health endpoint may direct to external manager (it will mean I am ready to start workers)
- Node will expose hooks so the external manager could perform actions on the infrastructure
- Node will expose endpoints containing current queue size, pending requests (details TBD) and other data necessary for external manager to decide

### Node managed workers
- This workers will be defined either in node's config or database (TBD)
- Node developers/maintainers will have the ability to implement classes contating pre and post flow action (adding worker instance, removing worker instance)
- Configuration will contain path and the name of the class
- TBD - Kodosumi may provide implementation of those actions for most popular runtimes (Docker, k8s, AWS ECS, GCP CloudRun).
