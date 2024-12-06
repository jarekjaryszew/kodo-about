# Workers
## Overview
- Workers will be implemented by adding a connector to an existing agent project
- Workers wil expose an interface towards the node
- Flow developer will have to create a configuration that glues worker with flows
- Node address will be passed by env variables
- Worker will notifythe node on startup
- Node will monitor if worker is healthy by a rest endpoint
- TBC - Worker may perfrom 1 flow at the time

## Scaling and lifetime
### Externally managed workers
- These workers will automatically register themselves with their node on startup.
- Node will expose hooks so the external manager could perform actions on the infrastructure
- Node will expose endpoints containing current queue size, pending requests (details TBD) and other data necessary for external manager to decide

### Kodosumi managed workers
- This workers will be defined either in node's config or database (TBD)
- Node developers/maintainers will have the ability to implement classes contating pre and post flow action (adding worker instance, removing worker instance)
- Configuration will contain path and the name of the class
- TBD - Kodosumi may provide implementation of those actions for most popular runtimes (Docker, k8s, AWS ECS, GCP CloudRun).
