---
"astro-icon": minor
---

Log how long each collection took to sync, and how many icons it loaded, so a slow build step is easy to attribute to icon loading versus everything else ([#166](https://github.com/natemoo-re/astro-icon/issues/166)).

- `createIconLoader()` (and everything built on it - `iconify()`, custom sources) logs `Loaded N icon(s) from "<source>" for the "<collection>" collection in <duration> (list: <duration>, resolve: <duration>)` at info level after every real sync, splitting out how long listing icons took from how long resolving/building them took. A sync skipped because nothing changed logs a shorter message at debug level instead.
- `localIcons()` logs `Loaded N icon(s) from "<collection>" in <duration>` at info level after its initial sync.
- `iconify()`/`iconifySource()` now also log debug-level timing for each pack resolution, separating a local install lookup from an Iconify API fallback fetch, visible with `astro build --verbose` (or `logLevel: "debug"`).
