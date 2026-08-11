---
"astro-icon": patch
---

`iconify()`/`createIconLoader()`'s info-level sync log no longer names the backing source(s) (e.g. `"fe"`, `"fe+ri"`) alongside the collection - just the collection name, icon count, and duration (`Loaded 3 icon(s) for the "social" collection in 210ms`), matching `localIcons()`'s existing format.
