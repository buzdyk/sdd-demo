---
type: todo
status: pending
description: Wire up a local-driven build + deploy command
---
# Set up local deploy

## Problem

MVP deploys are driven from the author's local machine, not CI. There needs to be a single, repeatable command that builds the static output and pushes it to the chosen host. CI-driven deploy is iceboxed ([[GITHUB_ACTIONS_DEPLOY]]).

## Approach

- After the host is picked ([[CHOOSE_HOST_AND_DOMAIN]]), define the publish command (e.g. `wrangler pages deploy`, `netlify deploy --prod`, `rsync -a`, `git push` to a Pages branch).
- Wrap it in a `scripts/deploy` (or generator-native task) so future deploys are one command.
- Document any required local credentials, where they live, and how to rotate them.

## Related

- [[CHOOSE_HOST_AND_DOMAIN]]
- [[BUILD_LANDING_PAGE]]
- [[GITHUB_ACTIONS_DEPLOY]]
