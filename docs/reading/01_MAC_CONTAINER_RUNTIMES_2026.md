---
type: reading
date: 2026-05-02
---
# Mac container runtimes in 2026

Snapshot of how Docker Desktop, OrbStack, and Dev Containers compare for development on Apple Silicon. Reflects information current as of early 2026; see "What's likely to change" at the end before betting a multi-year decision on it.

## TL;DR

- **Docker Desktop and OrbStack are runtimes.** Both run a Linux VM under the hood and expose the Docker CLI.
- **Dev Containers (`devcontainer.json`) is not a runtime.** It's a spec describing a development environment, which sits on top of a runtime.
- For solo development on Apple Silicon in 2026, **OrbStack is the default-good choice**. Docker Desktop is still defensible but no longer the obvious pick. Dev Containers earns its keep when there's more than one contributor or more than one machine.

## Docker Desktop

The incumbent. A bundled product from Docker, Inc. that ships a Linux VM, the Docker daemon, the Docker CLI, BuildKit, an embedded Kubernetes, a UI, an extensions marketplace, and tight integration with Docker Hub, Docker Build Cloud, and Docker Scout.

**Where it shines**

- Universally assumed. Every CI doc, every "how to run X" tutorial, every team Slack starts from "install Docker Desktop."
- Feature breadth. One-click Kubernetes, integrated image vulnerability scanning (Scout), shared remote build cache (Build Cloud) without assembling it yourself.
- Cross-platform. Same product on Mac, Windows, and Linux — useful when a team isn't all on the same OS.
- Has closed much of the perf gap on Apple Silicon: switched to Apple's `Virtualization.framework`, added Rosetta-based amd64 emulation, and made VirtioFS the default file-sharing layer (materially faster than the older osxfs / gRPC-FUSE path).

**Where it hurts**

- Resource-hungry at idle. The VM, the Docker daemon, and the Electron UI together routinely sit on 1–2 GB of RAM and noticeable CPU even with no containers running.
- Cold start, image pulls, and `docker compose up` all feel slower than OrbStack on the same hardware.
- File sync is faster than it was, but still not free; large `node_modules` mounts feel it.
- Commercial use requires a paid subscription for organizations above the free-tier thresholds (last-published cutoffs: >250 employees or >$10M revenue). Easy to overlook, easy to misjudge if a team grows.
- Strategic risk: Docker, Inc. has steadily moved value behind paid tiers (Hub rate limits, Scout limits, Build Cloud). The trend predates 2026 and shows no sign of reversing.

## OrbStack

A macOS-native, Apple Silicon-first container and Linux VM tool. Started in 2022, hit GA in 2023, has matured rapidly. Drop-in compatible with the Docker CLI and Docker Compose — `docker ps`, `docker compose up`, `docker buildx` all work unchanged.

**Where it shines**

- Resource use. Idle footprint is roughly an order of magnitude smaller than Docker Desktop. Battery life and fan noise reflect this directly.
- Speed. Faster boot, faster image pulls, faster builds, faster file sync. The difference is felt, not just measured.
- Native macOS integration. Containers get `*.orb.local` DNS names automatically, container filesystems are reachable via `/Volumes/`, and the menu-bar UI is small and fast.
- Also runs full Linux VMs (Ubuntu, Debian, Arch, NixOS) alongside containers — handy when something genuinely wants a Linux machine, not a container.
- Active, focused development. Updates ship frequently and tend to be small and targeted.

**Where it hurts**

- macOS-only. Nothing for Windows or Linux contributors.
- Smaller ecosystem. No Scout-equivalent depth for vulnerability scanning, no Build Cloud, fewer integrations baked into the UI.
- Single vendor. Smaller team than Docker, Inc.; less of a moat if priorities or pricing shift.
- Commercial license required for work use. Personal projects are free; if someone's employer benefits from it, they should pay. Pricing has been reasonable to date.
- Younger product. Edge cases (exotic networking, niche orchestrators) are less battle-tested.

## Dev Containers

The `devcontainer.json` spec describes a development environment as code: a base image, a set of "features" to layer on (languages, CLIs, tooling), forwarded ports, mounts, post-create commands, editor settings. Originated in VS Code, now an open spec maintained by Microsoft with implementations across VS Code, GitHub Codespaces, JetBrains IDEs, Cursor, and others.

It is not a runtime. It needs Docker Desktop, OrbStack, Podman, or another OCI-compatible runtime underneath.

**Where it shines**

- Reproducible dev environments. Clone a repo, open it in a Dev Containers-aware editor, and you're inside a fully provisioned environment in minutes — same as the next contributor.
- Polyglot projects. The "features" registry handles language toolchains, package managers, and CLIs as composable blocks rather than a hand-written Dockerfile.
- Codespaces and remote dev. The same `devcontainer.json` drives both a local container and a cloud container; you can move between them without changing files.
- Onboarding. New contributors don't have to install language toolchains on their host; the env is the repo.

**Where it hurts**

- Indirection cost. Another layer between source and runtime; debugging "why doesn't this start?" can mean checking the spec, the underlying Dockerfile, the runtime config, and the editor's container plugin.
- Editor coupling. The spec is portable, but the best UX is still in VS Code/Codespaces; other editors are functional but secondary.
- Diminishing returns for solo work. With one developer on one machine, the reproducibility benefit is mostly theoretical.
- Doesn't replace a `Dockerfile` for production — it's a development concern, not a deployment concern.

## How to choose

Two questions, not one.

### 1. Which runtime?

On Apple Silicon, in 2026, default to **OrbStack** unless something specific demands Docker Desktop:

- Mixed-OS team where everyone needs the same product → Docker Desktop.
- Heavy reliance on Scout / Build Cloud / Hub Pro features → Docker Desktop.
- Commercial use that doesn't fit Docker's free-tier thresholds → check OrbStack's commercial pricing first; it's usually the cheaper path.
- Otherwise → OrbStack.

### 2. Adopt Dev Containers?

Adopt when at least one of these is true:

- More than one contributor.
- More than one machine per contributor.
- Polyglot toolchain that's painful to set up on the host.
- Plan to use GitHub Codespaces or a similar cloud dev env.

Skip otherwise. A `Dockerfile` plus a `docker-compose.yml`, documented in the README, covers the same ground for a solo project at a fraction of the moving parts.

## What's likely to change after this writeup

Information here is current as of early 2026. Specific things that may shift:

- Docker, Inc.'s subscription thresholds and what's behind paid tiers — these have moved several times since 2021.
- OrbStack's feature set and commercial pricing — small, fast-moving company.
- Dev Containers spec features and editor support — actively evolving.
- Apple Silicon performance — each macOS major version has historically improved `Virtualization.framework` throughput.

Worth reverifying any of the above before committing to a long-horizon decision.
