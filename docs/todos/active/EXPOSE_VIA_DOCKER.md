---
type: todo
status: pending
description: Run the built site locally inside a container
---
# Expose blog locally via docker

## Problem

The blog needs to be runnable on the author's machine inside a container — both to give parity with how it will be served in production and to validate the build artifact in isolation from whatever happens to be on the host. This is a separate concern from [[SET_UP_LOCAL_DEPLOY]], which is about pushing to the public host.

## Approach

- Settle the runtime question; see [[002-CONTAINER_RUNTIME]] (proposed) and the comparison in [[01_MAC_CONTAINER_RUNTIMES_2026]].
- Add a `Dockerfile` that serves the static build output via a small HTTP server (caddy / nginx / a tiny Node static server — pick one in the spirit of "minimal moving parts").
- Add a `docker-compose.yml` that maps the chosen port and mounts the build directory for fast iteration.
- Document the run command in the top-level project README.

## Related

- [[002-CONTAINER_RUNTIME]]
- [[01_MAC_CONTAINER_RUNTIMES_2026]]
- [[BUILD_LANDING_PAGE]]
- [[CHOOSE_STATIC_GENERATOR]]
- [[SET_UP_LOCAL_DEPLOY]]
