---
type: adr
status: proposed
date: 2026-05-02
---
# ADR-001: Render the blog as a static site

## Status
Proposed

## Context
The project is a public-facing professional tech blog. It has a single author, no user accounts, no dynamic content, and no need for server-side computation per request. Operational simplicity and longevity matter more than runtime flexibility. The author has stated a preference for staying static.

## Proposed decision
The blog will be rendered as a fully static site — pre-built HTML/CSS/JS, served from a CDN or static host. No application server, no database, no per-request rendering.

The specific static site generator and host are deliberately not chosen here; that decision will be captured in a follow-up ADR once evaluated.

## Consequences
- No server to operate, patch, or scale; hosting cost trends toward zero.
- Build-time rendering means content changes require a rebuild + redeploy.
- Interactive features (comments, search, analytics) must be added as third-party embeds, build-time generation, or client-side JS.
- Content lives in version control alongside the site itself, which fits the "project is the docs" workflow.
