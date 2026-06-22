# HeyLolo — design gallery (public mirror)

Static, self-contained HTML design prototypes for the HeyLolo / elumi-kids app, plus a
zero-dependency comment server that injects a Figma-style pin-comment overlay into every page.

This repo exists **only** to host the gallery (the app's source lives in a separate private repo).
It is deployed on Dokploy at https://heylolodesign.wladefant.de.

- `docs/design/` — the prototypes (open `index.html`) + design notes.
- `tools/design-gallery/` — the Node static+comment server and its Dockerfile.

Owner comments (verified server-side via an `OWNER_KEY` env var, never committed) are authoritative;
guest comments are suggestions.
