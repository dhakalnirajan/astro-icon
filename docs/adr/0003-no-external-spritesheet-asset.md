# ADR 0003: No external spritesheet asset file in v1

## Status

Accepted

## Context

A real, cross-page-cacheable spritesheet - a separate static asset (e.g. `/icons.svg`) referenced via `<use href="/icons.svg#id">` - was raised as a possible direction for `<Sprite>`. It would let the browser cache icon defs once across every page, instead of each page's `<Sprite>` re-emitting its own defs block. It requires a build integration to collect icon usage across the whole site and emit a hashed static asset - materially more machinery than the inline, per-page `<Sprite>` boundary described in [ADR 0001](0001-sprite-is-opt-in.md).

## Decision

Out of scope for v1. `<Sprite>` only ever emits an inline defs block scoped to its own page.

## Consequences

Recorded here specifically so this idea doesn't get silently folded into `<Sprite>`'s current design if revisited later - it's a distinct feature (cross-page asset + build-time collection) layered on top of, not a variation of, the current per-page mechanism.
