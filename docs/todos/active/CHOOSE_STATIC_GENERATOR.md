---
type: todo
status: pending
description: Pick a Vue-friendly static site generator (Hugo ruled out)
---
# Choose a static site generator

## Problem

The project is committed to a static architecture (see [[001-STATIC_SITE]]) but no specific generator has been picked. The choice affects content authoring ergonomics, theme ecosystem, build speed, and how easy it is to keep the site running years from now.

## Constraints

- **Hugo is ruled out.** Tried before, did not enjoy working in it. Don't reconsider unless something material changes.
- **Lean toward the Vue ecosystem.** Day-to-day tooling is Vue, so a Vue-native generator keeps the mental model consistent.

## Approach

- Compare the leading Vue-friendly generators: VitePress, Nuxt Content, VuePress.
- Evaluate on: Markdown authoring fit, build speed, theming flexibility, project longevity, and how cleanly the build artifact can be served from a static host.
- Pick one and capture the choice in a new ADR.

## Related

- [[001-STATIC_SITE]]
- [[BUILD_LANDING_PAGE]]
