---
"astro-icon": patch
---

`iconify()`/`createIconLoader()`'s sync log now keeps the list/resolve timing breakdown out of the default info-level summary (`Loaded N icon(s) from "<pack>" for the "<collection>" collection in <time>.`), moving it to a separate debug-level line (`"<collection>" breakdown: list <time>, resolve <time>.`) instead of cramming it into the same message.
