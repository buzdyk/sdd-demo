---
type: todo
status: icebox
description: Move deploy from local-driven to a GitHub Actions workflow
reason: Out of scope for MVP — author chose to deploy locally first
---
# GitHub Actions deploy

## Problem

Once the local-driven publish loop is working, deploys could be moved into a GitHub Actions workflow so a `git push` to the main branch triggers a build and publish. This removes the dependency on the author's local machine being available and credentialled.

## Approach

- Reuse the publish command produced by [[SET_UP_LOCAL_DEPLOY]] as the body of the workflow.
- Move whatever credentials it needs out of local config and into repo secrets.
- Keep the local-driven path working as a fallback.

## Related

- [[SET_UP_LOCAL_DEPLOY]]
- [[CHOOSE_HOST_AND_DOMAIN]]
