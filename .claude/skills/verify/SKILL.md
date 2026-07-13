---
name: verify
description: How to run and verify this static GitHub Pages document site locally
---

# Verify rabergeau.github.io locally

Static site, no build step. Pages fetch the file list live from the GitHub API
(`api.github.com/repos/rabergeau/rabergeau.github.io/contents/<folder>`), which
allows CORS, so the real document list loads even from localhost.

1. Serve: `python -m http.server 8123` from the repo root (background).
2. Drive with Playwright headless Chrome (no browser download needed):
   `npm install playwright` in the scratchpad, then
   `chromium.launch({ channel: 'chrome', headless: true })`.
3. Flows worth driving on `http://localhost:8123/`:
   - Gate: wrong password → "Mot de passe incorrect."; `6975` → `#site` appears.
   - Documents: wait for two `#sections h2` (Citya, Roy); count `tbody tr` rows.
   - Search box `#search` filters rows; empty result shows "Aucun document trouvé."
   - Reload → sessionStorage skips the gate AND documents still load
     (regression: an early `unlock()` call once hit a TDZ error on `loaded`).
   - New browser context → gate shows again.
   - `/citya/` meta-redirects to `/`.

Gotchas: entry files are `index.htm` (not `.html`); a `/favicon.ico` 404 in the
console is expected; unauthenticated GitHub API is limited to 60 req/hr/IP.
