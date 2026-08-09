# ADR 0001: Sprite is opt-in and explicit, not automatic

## Status

Accepted

## Context

Two prior designs tried to give `<Icon>` "free" spritesheet deduping:

1. An explicit `<Sprite>`/`<Sprite.Provider>` pair (removed pre-v1) that required wrapping the *entire page* in a provider, and had a documented render-order race condition between usage and provider.
2. An implicit per-request dedup baked into `<Icon>` itself (`packages/core/components/cache.ts`, a `WeakMap<Request, Map<name, count>>`): the first occurrence of a name rendered a `<symbol>`, later ones rendered `<use>`. This depended on document render order within a single response, which silently breaks for independently-hydrated islands, Server Islands (rendered in a *separate* HTTP request/response entirely - a shared inline `<symbol>` can never reach them), and MDX/content sub-renders. The `is:inline` prop existed purely as an escape hatch users had to remember to reach for when the implicit dedup couldn't be trusted.

Neither gave real, predictable value without a matching cost: (1) required a page-wide structural commitment for a benefit that only mattered when repeats existed; (2) was invisible until it broke.

## Decision

`<Icon>` no longer attempts any deduping - it always renders a plain, standalone `<svg>` (`is:inline` and `cache.ts` removed). The only place deduping happens is a new `<Sprite>` component, and it's opt-in: wrap `<Icon>` usages you want deduped as its slotted children.

`<Sprite>` renders its own slot content fully (`Astro.slots.render`), scans the resulting HTML for the icon marker (`data-icon="collection:name"`, see `CONTEXT.md`), and rewrites: every occurrence becomes `<use href="#id">` (its own opening tag and any per-instance `<title>`/`<desc>` preserved), with one `<symbol>` per unique icon collected into a single defs block emitted once. This is a pure post-render string transform - no context-threading, no dependency on when or whether other components render, so it's immune to the ordering failures `is:inline` existed to route around.

One `<Sprite>` per page is the supported pattern (each `Sprite` is independent - no cross-Sprite coordination); a dev-only warning fires if a second one renders on the same page, via `spriteRenderedForRequest` (`packages/core/src/core/spriteRequestMarker.ts`), a boolean marker with none of the per-icon identity the old `cache.ts` carried.

## Consequences

- No `is:inline` prop to remember - icons are either inside a `Sprite` boundary (deduped) or not (never deduped). No per-instance override needed.
- Real dedup value now requires an explicit choice, not an incidental side effect of usage count.
- See [ADR 0002](0002-sprite-requires-prerendering.md) for the follow-on constraint this design's mechanism (full slot buffering) imposes.
