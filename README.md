# SpendPilot GitOps

This folder now holds the initial desired-state layout for the future standalone GitOps repository.

Current structure:

- `environments/dev/`
- `environments/staging/`
- `environments/prod/`

Current execution status:

- one `Application` manifest per environment
- environment-local GitOps values files for SpendPilot
- remote Git repo URLs now point at the `SpendPilot` GitHub organization
