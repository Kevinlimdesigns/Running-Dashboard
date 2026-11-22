# CLAUDE.md

Guidance for Claude (and any other contributor) working on this repo.

## What this is

SG Routes — a running-route planner for Singapore. You draw routes on a map (or trace them over a screenshot), and it saves distance, elevation, terrain, notes and a done/not-done flag. It's for personal use and a small running crew.

Two variants, each a single self-contained HTML file:

| File | Title | Who it's for | Storage key(s) |
|---|---|---|---|
| `index.html` | SG Routes | Kevin + crew (My Routes / Crew Routes toggle) | `sg_routes_mine_v1`, `sg_routes_crew_v1` |
| `solo/index.html` | My Routes | Anyone, solo use, no crew UI | `sg_solo_routes_v1` |

Live on GitHub Pages from `main` at the repo root:
- Crew: https://kevinlimdesigns.github.io/Running-Dashboard/
- Solo: https://kevinlimdesigns.github.io/Running-Dashboard/solo/

Design tokens, colours and component specs live in [DESIGN.md](DESIGN.md). Read it before any UI change.

## Hard rules

1. **Never rename a storage key.** localStorage is the only persistence. Renaming a key wipes every user's routes on next load. If the schema really must change, add a new key and write a one-time migration that reads the old key.
2. **No build step.** Vanilla HTML/CSS/JS, one file per variant. No npm, no bundler, no framework. Leaflet 1.9.4 is the only dependency (JS from unpkg, CSS inlined in `<head>`).
3. **No paid or keyed services.** Every external service must work with no API key and no account:
   - Tiles: OpenStreetMap (dark mode is OSM + a CSS filter on Leaflet's `tilePane`), Esri World Imagery for satellite.
   - Search: Nominatim, limited to `countrycodes=sg`.
   - CARTO and Stadia were dropped because both now need keys or sign-up. Don't bring them back.
4. **Keep the two variants in sync.** A bug fix or shared UI change in one almost always belongs in the other. Diff them when you're done.
5. **Don't break export/import formats.**
   - Crew: `{ app: 'sg-routes', version: 1, exportedAt, mine: [], crew: [] }`
   - Solo: `{ app: 'sg-solo-routes', version: 1, exportedAt, routes: [] }`
   - Solo import also accepts a crew export (takes `mine`, strips `crew`/`view`).
   - Import merges and skips duplicate `id`s. It never overwrites.

## Data model

```js
{
  id: 1717000000000,      // Date.now() at creation, also used as dedupe key
  name: 'Bedok Reservoir loop',
  dist: 8.4,              // km, already doubled if loop
  baseDist: 4.2,          // km, one-way
  loop: true,             // there-and-back, so drawn path is mirrored
  elev: 40,               // m
  terr: 'flat',           // free text, defaults to 'mixed'
  notes: '',
  crew: ['kevin','andrea'],  // crew variant only
  done: false,
  view: 'mine',           // crew variant only: 'mine' | 'crew'
  wpts: [[lat,lng], ...]
}
```

Crew members are hard-coded in `CREW` near the top of the `<script>` in `index.html`.

## Code conventions

- Compact style to match the existing file: short names (`wpts`, `rc`, `sv`), terse one-line CSS rules, minimal whitespace. Match what's around you rather than reformatting.
- Use the CSS custom properties in `:root` (see DESIGN.md). Don't add new raw hex values when a token exists.
- Global window listeners get registered **once** in `init()`. Don't add `window.addEventListener` inside functions that can run more than once (this caused the overlay-drag leak, now fixed with `_ovMove` / `_ovUp`).
- Inline `onclick` handlers are the existing pattern. Fine to keep using them.
- Mobile first. Check both layouts: stacked under 560px, side-by-side at 560px and up.

## Testing a change

No test suite. Open the file in a browser and check:

1. Map tiles load in all three modes (🌙 dark / 🗺 street / 🛰 satellite).
2. Draw a route, finish it, save it, reload the page. It should still be there.
3. Edit, mark done, delete.
4. Trace from screenshot: upload, drag, scale, rotate, lock, trace.
5. Export, then import the same file. You should see "No new routes".
6. Crew variant: switch My Routes / Crew Routes, and only that view's routes should show.
7. Resize below 560px.

Claude's artifact preview blocks remote images, so map tiles never show there. Use it for layout only. Test real map behaviour on the Pages URL or a local server (`python3 -m http.server`).

## Deploying

Pushing to `main` triggers GitHub's built-in `pages-build-deployment`. It takes about 1–2 minutes. If a push doesn't trigger it, an empty commit to `main` will.
