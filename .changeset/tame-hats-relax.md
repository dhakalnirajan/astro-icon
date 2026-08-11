---
"astro-icon": patch
---

`localIcons()` now skips re-reading and re-optimizing every `.svg` when nothing in the directory has changed since the last sync, the same whole-collection fast path `iconify()`/`createIconLoader()` already had - detected via each file's `mtime`/`size` (a cheap `stat`, not a content read), logged at debug level instead of the usual info-level "Loaded N icon(s)..." summary. Also simplified every loader's log label from `astro-icon/loaders/local`/`astro-icon/loaders/icon/<pack>` down to a single `astro-icon/loaders`, since the collection/source name is already in the message text.
