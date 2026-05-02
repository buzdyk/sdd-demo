---
type: reading
date: 2026-05-02
---
# Static Site Frameworks — A 2026 Read for the JS-Shaped Brain

Reading note for picking a generator for this blog. Constraints declared up front: **easy hosting** (drop a `dist/` on Netlify/Cloudflare/GitHub Pages, no Node runtime needed), **custom JS when warranted** (an interactive carousel here, a graph component there — not the entire site), and a previous run at **Hugo that didn't feel right**.

The Hugo complaint is the load-bearing detail. It rules out a whole branch of the landscape, and naming *why* it didn't fit is the difference between picking blind and picking deliberately.

---

## What "didn't feel right" about Hugo, named precisely

Hugo is the fastest static generator in the space. A 10K-page rebuild in under a second, single Go binary, no Node runtime — by the constraints listed at the top this should have been the answer. The reasons it almost never is, for someone shaped by JS:

- **Go template syntax is alien.** `{{ if eq .Section "blog" }}{{ partial "card" . }}{{ end }}` is well-defined and composable, but the verbosity-to-power ratio rewards Go natives. Pipelines (`. | humanize | upper`) read backward to anyone coming from JSX or template literals.
- **No real component model.** Hugo has *partials* (template includes) and *shortcodes* (templated HTML you call from Markdown). Neither is a component in the modern sense — there is no props-and-state mental model, no scoped CSS, no JS lifecycle. Want one interactive widget? You hand-roll the JS, drop a `<script>` tag, and stitch it together yourself.
- **Theme inheritance is a deep system.** Overriding `single.html` requires understanding lookup order, theme overrides, base templates, and block inheritance — each gotcha-laden. People bounce off this on day three, not day one.
- **Hugo Pipes is its own asset world.** Resource processing, fingerprinting, SCSS, JS bundling all live inside Hugo's pipeline rather than reaching for the npm/Vite ecosystem. Fine if you commit. Friction if you want any tool that lives in the JS ecosystem.

This is the same set of complaints that bounce most JS-shaped people off Zola too — Tera templates are friendlier than Go templates, but the "no real component model" and "you bring your own JS bundler" tradeoffs are identical.

If those four points feel like nitpicks, Hugo is genuinely a fine pick. They almost never do.

---

## The serious contenders

Ranked by fit for the stated preferences. Bottom of the list is not a slight — it's a worse fit *for these preferences specifically*.

### Astro (recommend)

The frontrunner for "I want static output but a real component story when I need one." Concrete reasons:

- **Islands architecture.** Pages render to HTML at build time. Interactive components opt in to hydration via `client:load`, `client:idle`, `client:visible`, `client:only`. The default ships zero JS; you pay only for what you mark interactive. This is the exact mental model implied by "custom JS when warranted."
- **Framework-agnostic islands.** Same page, drop in React, Vue, Svelte, Solid, Preact components — no commitment to a frontend framework. For a personal blog this matters more than it sounds: when one interactive widget calls for Vue and another is easier in Svelte, you don't have to fight the framework.
- **First-class MDX.** Markdown plus JSX-shaped component imports. Content stays as Markdown for the linear prose; complex passages reach for components. Native, not a plugin afterthought.
- **Content Collections.** Type-safe frontmatter via Zod schemas. Catches typos in tag/date/slug fields at build time rather than at render. Real ergonomic win once you have more than ten posts.
- **Static output is the default.** `astro build` produces a `dist/` that drops anywhere. SSR adapters exist if needed later, but you don't pay for them while you don't need them.
- **Astro 5 (Dec 2024) added the Content Layer** — pluggable loaders for content from anywhere (filesystem, APIs, Notion, etc.) under the same Collections API. Server Islands (partial dynamic rendering on demand) is the recent direction for "mostly static, occasionally fresh."

The honest costs:
- **More setup than Hugo on day one.** Vite-shaped toolchain, npm install, dependency churn. You're back in the JS ecosystem.
- **Build times grow with hydrated component count.** Pure-static Hugo will always be faster on a 5K-page site.
- **The hydration mental model is a real thing to learn.** `client:load` vs `client:idle` vs `client:only` is genuinely useful and genuinely a thing you have to understand.

[astro.build](https://astro.build) · [docs](https://docs.astro.build) · [content collections](https://docs.astro.build/en/guides/content-collections/)

### Eleventy / 11ty (strong dissent)

The minimalist's pick. **No JS framework opinion**, just templates → HTML. Liquid, Nunjucks, MDX, plain JS — pick your templating, point at a folder, get a static site.

- **Pure static, fast, lean.** v3.0 (Sep 2024) moved to ESM and gained Edge runtime; mature project under the 11ty Foundation now.
- **No bundler bundled.** This is a feature *and* the catch. Want React/Vue/Svelte components? You wire up Vite or esbuild yourself, or use the official **WebC** component system (web-components-shaped, smaller community than React/Vue).
- **The right pick if** you want minimal magic, are comfortable assembling the toolchain, and your "custom JS" is mostly vanilla or web components rather than framework components.

The honest cost: **assembly required.** What Astro hands you in an `npm create astro` is what you're hand-wiring with 11ty. Worth it if you value the control; pure overhead if you don't.

[11ty.dev](https://www.11ty.dev) · [WebC](https://www.11ty.dev/docs/languages/webc/)

### SvelteKit with adapter-static (dissent if you want Svelte everywhere)

If "custom JS when warranted" turns out to mean "I want real component-driven interactivity in lots of places," and you're willing to commit to one framework: SvelteKit's `@sveltejs/adapter-static` produces a fully static build, and Svelte is the most ergonomic of the major frameworks for hand-rolling components.

The cost is the inverse of Astro's framework-agnosticism: you're now in the Svelte universe. Markdown→Svelte goes through `mdsvex`, which is solid but less mature than Astro's MDX integration. Worth it if you actively want Svelte; not worth it just to get static output.

[kit.svelte.dev](https://kit.svelte.dev) · [adapter-static](https://kit.svelte.dev/docs/adapter-static)

### VitePress (dissent if you're Vue-first)

Vue's answer to "lightweight static generator with real components." Vite-powered, Vue-shaped, fast, opinionated for docs/marketing sites but flexible enough for a blog. Custom Vue components in any Markdown page. The narrowest fit if your interactivity needs are Vue-shaped — lighter than Astro for that specific case.

[vitepress.dev](https://vitepress.dev)

### Next.js / Nuxt with static export (works, but overkill)

Both can produce static output (`output: 'export'` for Next, `nuxi generate` for Nuxt). Both ship a complete React/Vue framework, an SSR engine, an API routing layer, and a build pipeline — most of which a static blog never uses. Next's App Router with React Server Components has made `output: 'export'` more constrained, not less. The overhead-to-benefit ratio is wrong for this shape of project.

The actual case for picking these: you already know the framework deeply, or you expect the project to grow into needing SSR/edge functions later. Otherwise the heavier setup is paying for capabilities you won't exercise.

### Jekyll (legacy choice, not a new pick)

Still works, still the GitHub Pages default. The Ruby toolchain in 2026 is awkward to set up fresh on macOS (Apple Silicon especially), the plugin ecosystem has thinned, and there is no JS-component story at all. Pick if you specifically want zero-config GitHub Pages and have Ruby already; otherwise this is a 2015 answer.

### Gatsby (effectively dormant — skip)

Netlify acquired Gatsby in Feb 2023 and the team was substantially reduced shortly after. Active development has trailed off, the GraphQL data layer that was the project's distinctive selling point is mostly seen as overhead now, and a new project starting in 2026 picking Gatsby is signing up for slow-moving maintenance. The community has migrated to Astro and Next. **Don't.**

---

## The pick, defended

**Astro**, for these reasons in this order:

1. The islands architecture *is* the "custom JS when warranted" mental model expressed as a build target. Default static, opt into JS exactly where the interactivity lives. No other framework gets this part right by default.
2. Framework-agnostic components remove the largest commitment cost of every other JS-flavored option. You're not betting on React or Vue or Svelte to pick the framework; you're picking *whichever* fits the next interactive widget.
3. Static output is the default and works everywhere. The constraint "easy hosting" is satisfied without thought.
4. MDX + Content Collections together solve the part of the project most likely to expand (lots of posts, lots of metadata, image/video embeds).
5. The Hugo complaints don't recur. Components are real. Templates are JSX-flavored. The asset pipeline is Vite, which is the JS ecosystem's current consensus.

The principled dissents to take seriously:
- **You actively prefer minimalism over batteries → 11ty.** You'll wire more by hand and the result will be smaller.
- **You want one framework end to end → SvelteKit.** Astro's framework-agnosticism is a cost, not a feature, in that world.
- **You're already deep in Vue → VitePress.** Lighter and more direct.

---

## Tradeoffs you're signing up for with Astro specifically

Worth naming so they aren't surprises:

- **Vite ecosystem churn.** Plugins, integrations, and adapter APIs evolve faster than Hugo's. Lock files matter; expect occasional breaking-change cleanup work on major upgrades.
- **MDX is powerful and a foot-gun.** Importing components into prose means content files now have code dependencies. Refactor a component → check every post that imports it. Use sparingly.
- **Hydration discipline matters.** It is too easy to slap `client:load` on everything and end up shipping the bundle you were trying to avoid. The mental model only pays off if you reach for `client:visible` / `client:idle` / `client:only` deliberately.
- **Build times grow with content + components.** Still fast for blog-scale sites; not Hugo-fast. If you'll write 10,000 posts in a year, this matters; otherwise not.

---

## Sources and reference

- [Astro docs](https://docs.astro.build) — canonical reference
- [Astro 5 release notes](https://astro.build/blog/astro-5/) — content layer + server islands
- [11ty 3.0 release notes](https://www.11ty.dev/blog/three/) — ESM, Edge runtime
- [Hugo docs](https://gohugo.io/documentation/) — for honest comparison of what you'd be giving up on raw build speed
- [Jamstack site generators comparison](https://jamstack.org/generators/) — broader survey if you want to widen the search
- [State of JS 2024 — Rendering frameworks](https://2024.stateofjs.com/en-US/libraries/rendering-frameworks/) — usage and satisfaction trends; Astro's retention/satisfaction has been the highest in the static-generator category for two years running

---

## Angles to mine for the article (or a follow-up)

- **The "JS escape hatch" axis.** Most static-generator comparisons rank by build speed and template syntax. The more useful axis for hybrid content+interactive sites is *how cheap is it to drop in one real component*. Reframe the comparison around that and the ranking changes sharply.
- **Why Hugo's speed doesn't win the argument.** A 10x faster build matters for sites with 10K+ pages. For the long tail of personal sites, build speed is a vanity metric and component ergonomics are the actual constraint.
- **The dead and the dormant.** Gatsby's collapse and Jekyll's gradual fade are worth a paragraph — they're cautionary tales about platform risk in the static-site space, and Astro is winning their migrations.
- **MDX as a contract.** Once posts import components, the content/code boundary blurs. Worth a separate note on how to keep that contract stable across years of posts.
