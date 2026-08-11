---
"astro-icon": patch
---

Fixed `localIcons()`'s dev-mode watcher potentially destabilizing Astro's shared file watcher when the configured icon directory doesn't exist or briefly errors (e.g. `EPERM`/`EACCES` while a directory is being deleted, which Windows produces far more readily than POSIX). Node's `EventEmitter` throws synchronously on an unhandled `"error"` event, so an unlucky fs error from the watcher could previously take down live reload for unrelated files too, such as CSS (fixes [#260](https://github.com/natemoo-re/astro-icon/issues/260)). The loader still warns once when the directory is missing and still watches it so it recovers automatically if the directory is created later.
