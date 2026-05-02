---
type: adr
status: proposed
date: 2026-05-02
---
# ADR-002: Container runtime for local development

## Status
Proposed

## Context

The blog needs to run inside a container on the author's Apple Silicon Mac (see [[EXPOSE_VIA_DOCKER]]). The realistic options in 2026 are:

1. **Docker Desktop** — incumbent runtime, broad ecosystem, paid for non-trivial commercial use.
2. **OrbStack** — macOS-native runtime, drop-in compatible CLI, much lighter on resources.
3. **Dev Containers** (`devcontainer.json`) — a portable spec for describing dev environments, layered on top of one of the runtimes above.

A full comparison lives in [[01_MAC_CONTAINER_RUNTIMES_2026]]; that note is the source for everything cited below.

The decision splits into two: pick a runtime, then decide whether to layer the Dev Containers spec on top.

## Decision

### Runtime: OrbStack

- Single author, Apple Silicon, free for personal use.
- Idle resource footprint roughly an order of magnitude smaller than Docker Desktop; faster cold start, faster file sync, faster builds.
- Drop-in compatible with the Docker CLI and `docker compose`. Switching back later costs nothing structural — same images, same Compose files, same commands.
- The macOS-only constraint is a non-issue for a one-person project on one Mac.

### Spec layer: skip Dev Containers for now

- Dev Containers earns its keep when there are multiple contributors, multiple machines, a polyglot toolchain, or a Codespaces/cloud-dev-env story. None apply here.
- A `Dockerfile` plus a `docker-compose.yml` plus a paragraph in the README captures the same surface area for a solo project, with one fewer layer of indirection.
- The spec is designed to bolt on later. Adopt it when one of the triggers above appears.

## Consequences

- Local dev assumes macOS. A Linux/Windows contributor would need to bring their own Docker-compatible runtime; not a near-term concern.
- If commercial use kicks in (a paid product, a paying employer using the same setup), the OrbStack commercial license needs to be handled. Cost is modest and well-defined.
- Without a `devcontainer.json`, the dev environment is described informally (Dockerfile + Compose + README). New collaborators bootstrap by reading docs rather than by opening the repo in a container-aware editor.
- Migration off OrbStack is low-risk: the contract is the standard Docker CLI + OCI images. The same `Dockerfile` and Compose file run on Docker Desktop, Podman, or any other Docker-API-compatible runtime.
