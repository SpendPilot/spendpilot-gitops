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

Current image promotion model:

- `environments/dev/values/spendpilot-values.yaml` is updated by application repo build workflows after a successful push to ACR
- `environments/staging/values/spendpilot-values.yaml` is updated only through GitOps PRs created by manual staging promotion workflows
- `environments/prod/values/spendpilot-values.yaml` is updated only through GitOps PRs created by manual prod promotion workflows
- image promotion is digest-first:
  - `repository`
  - `tag`
  - `digest`
- staging must promote only what is already in `dev`
- prod must promote only what is already in `staging`
