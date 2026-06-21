# CI/CD GitOps

## Dev

- Pushes to `main` build the image once.
- Dev image build workflows now also run a non-blocking Trivy image scan after the image is pushed.
- Dev uses automatic short SHA tags such as `sha-a1b2c3d` or the current repo short-sha style used by the workflow.
- Dev values store both the readable tag and the immutable digest.
- Dev deployment should use the digest as the deployment source of truth when present.
- Dev values should represent the dev hostname contract `dev.costpilot.online`.
- Dev values now also carry the async email queue settings consumed by the AKS workloads:
  - `notifications.enabled`
  - `notifications.serviceBusFullyQualifiedNamespace`
  - `notifications.serviceBusQueueName`
- After the workflow updates `environments/dev/values/spendpilot-values.yaml`, Argo CD dev auto-sync should reconcile the change.

## Staging

- Staging promotion is manual.
- Staging copies the exact image tag and digest from `dev`.
- Staging does not rebuild the image.
- Staging values should represent the staging hostname contract `stage.costpilot.online`.
- Staging promotion workflows require typed confirmation `promote-to-staging`.
- Staging promotion should update only `environments/staging/values/spendpilot-values.yaml`.
- After the GitOps PR is merged, Argo CD staging auto-sync should reconcile the change.

## Production

- Production promotion is manual.
- The user must enter a `release_version` such as `v1.0.0`.
- The workflow reads the exact image digest from `staging`.
- The workflow creates the production release tag from the staging digest.
- The workflow does not use a GitHub Environment gate and instead relies on the GitOps pull request review step for approval.
- Prod values store both:
  - the semantic version tag
  - the immutable digest
- The semantic version is only a human-readable release label.
- The digest is the actual immutable artifact that is deployed.
- The production workflow must never rebuild the image.
- Prod values should represent the production hostname contract `costpilot.online`.
- Production promotion should update only `environments/prod/values/spendpilot-values.yaml`.
- After the GitOps PR is merged, Argo CD prod auto-sync should reconcile the change.

## Deployment Behavior

- Helm should render `repository@sha256:...` when a digest is present.
- The tag remains in values for readability, auditability, and release tracking.
- Async email application config is still part of the GitOps-managed AKS workload config.
- The Azure Function that drains the queue is not part of the GitOps repo and is deployed from `spendpilot-services/.github/workflows/email-function-deploy.yml`.
- Deterministic Function App names are expected to be:
  - `spendpilot-dev-email-func`
  - `spendpilot-staging-email-func`
  - `spendpilot-prod-email-func`

## Rollback

- Roll back by reverting the prod values file commit or restoring a previous prod tag and digest pair.
- Do not rely on `latest`.

## Required Workflow Credentials

- Azure registry-tagging workflows expect these GitHub variables or fallback secrets:
  - `ACR_NAME`
  - `ACR_LOGIN_SERVER`
  - `AZURE_CLIENT_ID`
  - `AZURE_TENANT_ID`
  - `AZURE_SUBSCRIPTION_ID`
- Cross-repo GitOps updates and PR creation expect:
  - `GITOPS_REPO_TOKEN`
