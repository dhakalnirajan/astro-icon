# ADR 0002: Sprite requires prerendering, no SSR streaming support in v1

## Status

Accepted

## Context

`<Sprite>` (see [ADR 0001](0001-sprite-is-opt-in.md)) must fully render its slot content into a string via `Astro.slots.render` before it can scan and rewrite it. On a prerendered page this costs nothing - the whole page is generated once at build time and written to a file, so there's no live HTTP stream to a real browser at that point. On an `output: "server"` route rendered per-request, the same buffering would block incremental HTML streaming for that subtree.

Two ways to make `<Sprite>` streaming-safe on SSR routes were considered and rejected:

- **Client-side JS reconciliation** (a small script that patches missing/duplicate symbols after streaming finishes) - conflicts with astro-icon's zero-JS-by-default output; adds a moving part to what should be static markup.
- **Adapter-specific stream rewriting** (e.g. Cloudflare's HTMLRewriter) - only exists on some deploy targets, not uniformly across Node/Vercel/Netlify/etc. Inconsistent behavior across adapters is worse DX than a route simply not supporting `<Sprite>` at all.

There's also a structural issue neither of the above fixes: Astro **Server Islands** render in a genuinely separate HTTP request/response, spliced into the page client-side afterward. A shared inline `<symbol>` in the main response's HTML literally cannot reach a Server Island's markup, regardless of render ordering - this was one of the original reasons `is:inline` existed.

## Decision

`<Sprite>` guards on `Astro.isPrerendered` (a stable, public, first-party Astro API - no custom detection needed) and throws an `AstroIconError` if the current route isn't prerendered. SSR/streamed routes cannot use `<Sprite>` in v1; they use plain `<Icon>` instead (standalone `<svg>`, safe anywhere).

## Consequences

- `<Sprite>` is unusable on `output: "server"` routes without `export const prerender = true`. This is a real, user-facing limitation, not an oversight - expect users to ask "why can't I use Sprite here."
- If Astro ever gains a first-party mechanism for Server Islands to share defs with their host page, or a portable (adapter-agnostic) streaming rewrite primitive, this constraint is worth revisiting.
