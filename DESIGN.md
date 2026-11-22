# DESIGN.md

The design system for SG Routes, taken from what's built in `index.html` and `solo/index.html`. When the code and this doc disagree, fix one of them so they match again.

## Direction

**Strava-inspired, dark, map-first.** The map does most of the work. The UI around it is a quiet, dense, charcoal panel, and one orange accent marks what matters: the primary action, the selected route, the thing you're drawing.

Principles:
- **One accent per context.** Orange for "mine", blue for "crew". Green only means "done". Never mix them.
- **Dense, not cramped.** Small type (10–14px), tight padding, hairline borders. It should read like a tool, not a landing page.
- **The map is the hero.** Panels float over it with translucent dark backgrounds and blur, so the map stays visible behind them.
- **State through colour and weight, not extra chrome.** A selected route goes from 40% to 100% opacity and gets thicker. A done card turns faintly green.

## Colour

### Core tokens (`:root`)

| Token | Value | Use |
|---|---|---|
| `--o` | `#FC4C02` | Brand orange. Primary buttons, active states, focus rings, logo dot, "mine" accent |
| `--od` | `rgba(252,76,2,0.13)` | Orange tint. Hint bar, selected crew chip, terrain chip background |
| `--ob` | `rgba(252,76,2,0.22)` | Orange border. Hint bar border |
| `--bg` | `#1c1c1c` | App background, sidebar |
| `--s1` | `#272727` | Surface 1. Route cards, stat cells, modal |
| `--s2` | `#313131` | Surface 2. Inputs, secondary buttons, active card, toast |
| `--s3` | `#3c3c3c` | Surface 3. Toggle track (off), icon-button hover |
| `--t` | `#fff` | Primary text |
| `--tm` | `#999` | Muted text. Labels, secondary buttons, meta |
| `--td` | `#555` | Dim text. Placeholders, empty-state icon, idle icon buttons |
| `--bd` | `rgba(255,255,255,0.08)` | Hairline divider |
| `--bds` | `rgba(255,255,255,0.15)` | Stronger border. Inputs, secondary buttons, floating panels |
| `--gc` | `#4caf50` | Done green |
| `--gd` | `rgba(76,175,80,0.13)` | Done tint |
| `--gb` | `rgba(76,175,80,0.25)` | Done border |

### Semantic colours (not yet tokenised)

| Value | Meaning | Where |
|---|---|---|
| `#4a90e2` | Crew blue | Crew view toggle, crew chip, loop chip, crew drawing accent |
| `#e05555` | Destructive red | Delete hover, waypoint-delete button and selected marker |
| `#00e676` | Tracing green | Waypoints and line while tracing over a screenshot |
| `#1a1a1a` | Map backdrop | `.leaflet-container` background while tiles load |
| `rgba(28,28,28,0.9–0.97)` | Floating glass | Search, tile toggle, overlay controls, delete banner |
| `rgba(25,38,25,0.7)` | Done card background | `.rc.done-card` |

### Route palettes

Routes cycle through a palette by list position. Warm for mine, cool for crew, so you can tell the two views apart at a glance on the map.

- **Mine / Solo:** `#FC4C02` `#f5a623` `#ff4081` `#ffb300` `#ff6d00`
- **Crew:** `#4a90e2` `#00bcd4` `#4caf50` `#e040fb` `#26c6da`

### Crew identity

| Member | Colour | Avatar background |
|---|---|---|
| Kevin | `#FC4C02` | `rgba(252,76,2,0.18)` |
| Andrea | `#4a90e2` | `rgba(74,144,226,0.18)` |
| Cheryl | `#e040fb` | `rgba(224,64,251,0.18)` |
| Shaokee | `#4caf50` | `rgba(76,175,80,0.18)` |

Avatars are 15px circles showing two-letter initials (`ini()`), 8px / 600 weight.

## Typography

System stack only, so there are no web fonts to load:
`-apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif`

| Size | Weight | Style | Use |
|---|---|---|---|
| 14px | 500 | — | Stat values (`.sv`) |
| 13px | 500 | — | Modal title |
| 12px | 500 | — | Route name, primary/save buttons |
| 12px | 400 | — | Inputs, body, toast, empty state |
| 11px | 500 | UPPERCASE, +0.4px | Logo wordmark |
| 11px | 500 | UPPERCASE, +0.2px | `+ Add` button |
| 11px | 500 | — | Toggles, tile buttons, action buttons, hint |
| 11px | 400 | — | Route stats, notes, meta |
| 10px | 500 | UPPERCASE, +0.3–0.4px | Field labels (`.fl`), stat labels (`.sk`) |
| 10px | 400 | — | Chips, slider labels |
| 11px | 600 | — | Live distance readout, map distance chip |

Line height is 1.45 for multi-line text (notes, textareas) and 1.55–1.8 for hints and empty states.

## Shape

| Radius | Use |
|---|---|
| 50% | Logo dot, route dot, avatars, toggle knob |
| 6px | Buttons, inputs, selects, tile toggle, readout |
| 7px | Finish button, loop row |
| 8px | Route card, search box, view toggle pill, trace banner |
| 9px | Toggle track |
| 10px | Floating panels (overlay controls, delete banner), map distance chip |
| 12px | Modal (top corners only on mobile bottom sheet) |
| 20px | Chips, crew chips, toast |

Borders are always 1px. Use `--bd` for dividers inside a surface and `--bds` for the edge of an interactive element or floating panel.

## Spacing

There's no formal scale. Values sit on a 1–2px grid between 2 and 16px. Common ones:

- **Gaps:** 2, 4, 5, 6, 7, 8px
- **Control padding:** `5px 8–10px` (small), `6px 9px` (inputs), `7–8px` (full buttons)
- **Section padding:** `10px 12px` (header, forms), `9px 10px` (cards), `6px 7px` (list)
- **Floating panel inset:** 10px from map edges (14px for bottom panels)

When in doubt, copy the nearest neighbour.

## Layout

| | Under 560px (mobile) | 560px and up |
|---|---|---|
| Direction | Column: map on top, sidebar below | Row: map left, sidebar right |
| Sidebar | Full width, `max-height: 360px`, top border | `296px` wide, full height, left border |
| Edit modal | Bottom sheet, full width | Centred, `300px` |

The app fills `100dvh` with `overflow: hidden`. Only the route list scrolls (3px scrollbar, `--s2` thumb).

### Sidebar order (top to bottom)
1. Header: logo dot + wordmark, then ⬇ export, ⬆ import, 📷 trace, `+ Add`
2. View toggle pill (crew variant only)
3. Sort + filter selects (shown once there are routes)
4. Drawing hint + finish bar (while drawing)
5. Create form (after finishing)
6. Stats bar: Routes · Done · Total km
7. Route list

### Z-index layers

| z | Layer |
|---|---|
| 500 | Screenshot overlay image |
| 999 | Tile toggle |
| 1000 | Search box |
| 1001 | Overlay controls, trace banner |
| 1002 | Waypoint delete banner |
| 1003 | Live distance readout |
| 9998 | Edit modal scrim |
| 10000 | Toast |

## Components

### Buttons
- **Primary:** `--o` fill, white 12px/500, 6px radius. Hover drops to 85–88% opacity. In crew view the finish button turns crew blue.
- **Secondary:** transparent or `--s2`, `--tm` text, `--bds` border. Hover goes to `--t` text.
- **Icon button:** no background, `--td`. Hover is `--tm` on `--s3`. The delete variant hovers red on a 10% red tint.
- **Destructive:** `#e05555` fill, white text. Only used in the waypoint delete banner.

### Segmented toggle (view toggle)
`--s2` track, 2px padding, 8px radius. Active segment fills orange (mine) or blue (crew) with white text. 0.18s transition.

### Switch (loop toggle)
32×18 track, `--s3` off and `--o` on, with a 12px white knob that slides 14px. Always sits inside a full-width `.loop-row` so the whole row is tappable.

### Inputs
`--s2` background, `--bds` border, 6px radius, 12px text. On focus the border turns `--o` and there's no outline ring. Placeholders use `--td`. Field labels sit above in the 10px uppercase label style.

### Route card (`.rc`)
- **Default:** `--s1`, `--bd` border, 8px radius.
- **Hover:** border goes to 35% orange.
- **Active:** `--o` border, `--s2` background, and the action row (Done / Edit / …) appears.
- **Done:** dark green tint, green border, and a green border when active.
- **Anatomy:** colour dot + name + icon buttons, then stats row (distance, elevation), then chips, then notes clamped to 2 lines under a divider.

### Chips
10px text, 20px radius, `2px 7px` padding. Each chip is a tinted background plus a 25–30% border in the same hue.

| Chip | Hue |
|---|---|
| Terrain | Orange, uppercase |
| Done | Green |
| Loop | Blue |
| Mine | Orange |
| Crew | Blue |
| Crew member | That member's colour |

### Stats bar
Three equal cells on `--s1`, separated by 1px `--bd` gaps. Value is 14px/500, label 10px uppercase muted.

### Floating map panels
Background `rgba(28,28,28,0.9–0.97)`, `--bds` border, `backdrop-filter: blur(4–6px)`, 8–10px radius. Used for search, tile toggle, overlay controls and the delete banner.

### Banners (on map)
Orange at 95% opacity, white 12px/500 text, 8px radius, centred at the top. The trace banner shows the live point count.

### Toast
Bottom-centre pill, `--s2` with a `--bds` border, 12px text. Fades in over 0.2s. Not interactive.

## Map styling

### Tiles
- **🌙 Dark (default):** OSM with `filter: invert(1) hue-rotate(180deg) brightness(0.95) contrast(0.9)` applied to `tilePane` only, so routes and markers keep their real colours.
- **🗺 Street:** plain OSM.
- **🛰 Satellite:** Esri World Imagery.
- Default view is Singapore centre `[1.3521, 103.8198]`, zoom 12.

### Routes
| State | Weight | Opacity |
|---|---|---|
| Idle | 3 | 0.4 |
| Selected | 4 | 1.0 (and the map fits to its bounds) |
| Other view | 0 | 0 (hidden) |
| Drawing | 3, dashed `7 5` | 0.9 |

### Markers
- **Start:** hollow circle, radius 6, route colour stroke, `#1c1c1c` fill.
- **End:** filled circle, radius 6, route colour. Hidden for loops because start and end are the same point.
- **Drawing waypoints:** first point radius 7 hollow, others radius 5 filled, stroke 2.
- **Selected waypoint (for delete):** radius 9, red.
- **Distance chip:** 66×22 SVG pill in the route colour at 92%, white 10px/600 label (`8.4 km` or `850 m`). Only shown on the selected route.

## Iconography

Emoji, not an icon font. It's zero-dependency and it renders natively everywhere.

| Icon | Meaning |
|---|---|
| 🌙 🗺 🛰 | Dark / street / satellite tiles |
| ⬇ ⬆ | Export / import |
| 📷 | Trace from screenshot |
| 🏃 👥 | My Routes / Crew Routes |
| 🔄 | Loop |
| 🏁 | Finish route |
| 💾 | Save |
| 🗑 | Delete |
| 🔍 | Search |

## Voice & copy

- Sentence case everywhere except the uppercase micro-labels (wordmark, field labels, stat labels, `+ Add`).
- Short and direct: "Finish & name this route", "Aligned — start tracing", "Keep".
- Placeholders use real Singapore places and running details: "e.g. Bedok Reservoir loop", "Water points, best time, highlights…", "Search a place in Singapore…".
- Toasts say what happened with a count: "Exported 12 routes", "Imported 3 new routes", "No new routes (all already present)".

## Known inconsistencies (design debt)

Worth cleaning up when you're next touching these areas:

1. **Crew blue, red and tracing green aren't tokens.** `#4a90e2`, `#e05555` and `#00e676` are hard-coded in CSS and JS. Suggested tokens: `--c` / `--cd` / `--cb`, `--r`, `--tr`.
2. **Lots of inline styles.** The search box, overlay controls, banners and header icon buttons are styled inline. Moving them to classes would make theming easier.
3. **Loop chip uses crew blue.** In the crew view that clashes with the crew chip. A neutral or a distinct hue would read more clearly.
4. **Radius drift.** 6, 7, 8 and 9px are all in use for similar controls. Two values (6px controls, 10px containers) would be tidier.
5. **Leftover icon-font rules.** `.vt-btn i`, `.loop-label i` and `.rs i` style `<i>` tags, but every icon is now an emoji.
6. **Small tap targets.** Several controls are under 32px tall on mobile. 44px is the comfortable minimum for thumbs, especially mid-run.
