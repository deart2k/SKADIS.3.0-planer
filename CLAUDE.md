# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

Single-file HTML app (`index.html`) — interactive layout calculator for the IKEA SKADIS Modular Pegboard 3.0 by AdamKozakGrafika.

**Live:** https://deart2k.github.io/SKADIS.3.0-planer/
**Model:** https://makerworld.com/en/models/2572477-ikea-skadis-modular-pegboard-3-0

No build system, no dependencies, no external requests. Fully self-contained, works offline.

## Development

**No build, no lint, no tests.** Open `index.html` in a browser to develop. All HTML, CSS, and JS live in that single file (~1100 lines). Changes are verified visually.

To deploy: push to `main` — GitHub Pages serves `index.html` automatically.

## Architecture

### Key constants

```js
const S = 40;   // mm per cell (1 cell = 40×40mm)
const MAX = 4;  // max cells per standard module = 160mm
```

### Data flow

```
[Sliders] → go() → computeMountPts() → computeLayout() → computeBOM()
                                      ↓                  ↓
                                 drawBoard()          renderBOM()
                                 drawMount()          renderCatalog()
```

### Global state

```js
let G    = null;  // { wC, hC, lay, pts, bom } — current layout state
let HK   = null;  // highlight key for board canvas hover
let MHK  = null;  // highlight key for mount canvas hover
let LANG = 'en';  // current language ('en' | 'ru')
```

## Module system (verified from 3mf files)

### Board modules (SKADIS_modular_board_3_0_mini_comp.3mf)

4 module types — NOT interchangeable, different profiles:

| Role | Plates | Position | Size |
|------|--------|----------|------|
| `corner` | 25 (TL+BL), 27 (TR+BR) | 4 corners only | 40×40mm |
| `topbot` | 17–20 | Top & bottom border | 40mm tall |
| `side` | 21–24 | Left & right border | 40mm wide |
| `std` | 01–16 | Center fill | up to 160×160mm |
| connector | 26 | All module intersections | 20×20mm, 25/sheet |

### Mount brackets (SKADIS_modular_board_3_0_mounting.3mf)

7 plates, each mount point = 2 parts:

| Key | Bracket plate | Dist. connector plate |
|-----|--------------|----------------------|
| `corner` | 01 | 05 |
| `side` | 02 | 06 (shared) |
| `topbot` | 03 | 06 (shared) |
| `mid` | 04 | 07 |

**Critical:** A bracket REPLACES the module at that position (role `'mount'` in layout). Corner mount replaces corner module. Side mount replaces side module. Etc.

## Layout algorithm

### computeMountPts(wC, hC, maxH, maxV)

Generates wall mount positions using `pickBounds()` — always includes board corners (x=0, x=wC, y=0, y=hC). Mount density controlled by `maxH` (horizontal span) and `maxV` (vertical span) in cells.

### computeLayout(wC, hC, pts)

Builds module grid. At each mount position, module role = `'mount'` instead of regular type. Uses `segsWithMountCells()` to force 1-cell segments at mount positions so brackets fit exactly.

Mount module has `mountKey`: `'corner'` | `'side'` | `'topbot'` | `'mid'`

### Connector grid

Connectors placed at ALL actual module boundary intersections (derived from `realColB`/`realRowB` of placed modules — NOT from independent grid). This prevents phantom connectors inside large modules.

## BOM logic

- Regular modules grouped by `role|wMm|hMm`
- Mount modules (`role === 'mount'`) grouped by `mount_${mountKey}`
- Corner modules split by variant: TL+BL → plate 25, TR+BR → plate 27
- Connector count = actual intersections, sheets = ceil(count/25)
- Bracket counts derived from `lay.mods` (not from `pts`) — ensures accuracy

## i18n system

```js
const I18N = { en: {...}, ru: {...} };
function t(key, ...args) { /* returns string or calls function */ }
function applyI18n()     { /* updates all [data-i18n] elements */ }
function toggleLang()    { /* switches LANG, calls applyI18n() + go() */ }
```

Static HTML uses `data-i18n="key"` attributes. Dynamic JS content calls `t('key')`.
When adding new user-visible strings, add entries to both `en` and `ru` in the `I18N` object.

## Canvas rendering

Two canvases:
- `#cvs` — front view (board layout), drawn by `drawBoard()`
- `#cvsm` — rear view (mount brackets), drawn by `drawMount()`

Both use DPR scaling for retina displays. Cell size auto-calculated via `csz()` to fit available space. HiDPI: `canvas.width = W * dpr`, `ctx.scale(dpr, dpr)`.

### Highlight system

- **Board canvas hover** → `HK` set → `redrawBoard()` + `highlightBOMRow(HK)`
- **Mount canvas hover** → `MHK` set → `redrawMount()` + `highlightBOMRow('mount_'+MHK)`
- **BOM row hover** → sets `HK` or `MHK`, triggers both canvas redraw and row highlight

## Known issues / past bugs

- `totBrkt` was declared twice (fixed) — caused blank screen
- Connectors were placed on independent grid → phantom dots inside large modules (fixed: use actual module boundaries)
- Corner modules were always shown in BOM even when replaced by corner mounts (fixed: count from `lay.mods`)
- Google Fonts blocked in iframe → blank screen (fixed: switched to system fonts)
- `role:'mount'` was removed then restored — it IS needed, mount brackets replace modules

## Density levels

```js
const DENS = [
  {h:4,  v:3 },   // Very dense  ~160×120mm
  {h:7,  v:5 },   // Dense       ~280×200mm
  {h:10, v:7 },   // Normal      ~400×280mm  ← default
  {h:15, v:10},   // Sparse      ~600×400mm
  {h:20, v:15},   // Very sparse ~800×600mm
];
```

Vertical span always stricter than horizontal (sag from load).

## CSS color variables

```css
--cf / --cb / --ct   /* corner module: fill / border / text */
--tf / --tb / --tt   /* topbot module */
--sf / --sb / --st   /* side module */
--df / --db / --dt   /* std module */
--mf / --mb / --mt   /* mount bracket (amber) */
--acc  #e55c25       /* orange accent */
--acc2 #efa04e       /* amber accent */
```

## Possible improvements

- Export BOM as CSV/PDF
- Custom board shapes (L-shape etc.)
- Zoom/pan on large boards
- Show mm distances between mount points on rear view
