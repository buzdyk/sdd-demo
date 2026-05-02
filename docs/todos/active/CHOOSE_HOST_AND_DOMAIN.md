---
type: todo
status: pending
description: Pick a host and domain — deploy will be driven from local
---
# Choose host and domain

## Problem

The blog is intended to be exposed publicly but has no host or domain selected. Deploys will be triggered from the author's local machine for the MVP; CI-driven deploys are iceboxed (see [[GITHUB_ACTIONS_DEPLOY]]).

## Approach

- Compare static hosts that accept a local-driven push (e.g. Cloudflare Pages via direct upload, Netlify CLI, GitHub Pages via `git push`, plain S3+CloudFront, a self-managed VPS over rsync/scp).
- Pick a domain name and register it.
- Capture the host choice as an ADR; record the domain in the GLOSSARY.

## Related

- [[001-STATIC_SITE]]
- [[CHOOSE_STATIC_GENERATOR]]
- [[SET_UP_LOCAL_DEPLOY]]
- [[GITHUB_ACTIONS_DEPLOY]]
