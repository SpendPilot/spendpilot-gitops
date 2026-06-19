# CI/CD GitOps

## Dev

- Pushes to `main` build the image once.
- Dev uses automatic short SHA tags such as `sha-a1b2c3d` or the current repo short-sha style used by the workflow.
- Dev values store both the readable tag and the immutable digest.
- Dev deployment should use the digest as the deployment source of truth when present.

## Staging

- Staging promotion is manual.
- Staging copies the exact image tag and digest from `dev`.
- Staging does not rebuild the image.

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

## Deployment Behavior

- Helm should render `repository@sha256:...` when a digest is present.
- The tag remains in values for readability, auditability, and release tracking.

## Rollback

- Roll back by reverting the prod values file commit or restoring a previous prod tag and digest pair.
- Do not rely on `latest`.
