# SKADIS 3.0 Planner

Interactive layout calculator for the **IKEA SKADIS Modular Pegboard 3.0** — a 3D-printable modular pegboard system by [AdamKozakGrafika](https://makerworld.com/ru/models/2572477-ikea-skadis-modular-pegboard-3-0).

🔗 **[Open Planner →](https://deart2k.github.io/SKADIS.3.0-planer/)**

---

## What it does

- **Board layout** — visual front view of your board with all module types color-coded
- **Wall mounting** — rear view showing bracket placement
- **Parts catalog** — every part with exact Bambu Studio plate number
- **Print list** — complete bill of materials, auto-calculated for your board size
- **Interactive highlights** — hover a part in the list to highlight it on the board, and vice versa
- 🆕 **Mount exclusions** *(added 2026-04-11)* — skip individual mount points, whole rows, or whole columns (e.g. when a wire runs behind that part of the wall)

## How to use

1. Choose **printer bed size** — Mini (180×180), Standard (256×256), or XL (320×320)
2. Set **max part size** width and height (limits how large each printed module can be)
3. Set board **width** and **height** using the sliders (in 40mm cells)
4. Adjust **mount density** — how frequently the board is anchored to the wall
5. Switch between **Board layout**, **Wall mounting**, and **Parts catalog** tabs
6. Use the **Print list** in the sidebar as your shopping/printing checklist
7. Toggle **RU / EN** in the top-right corner to switch language
8. Toggle **☀ / ☾** to switch between dark and light themes

## Printer bed size

The 3D model comes in three package variants for different printer build volumes. Pick your bed in the sidebar, then set **Max part size** width/height to control how large each printed module can be (fewer larger parts vs more smaller square parts).

| Bed | Printer class | Max part width | Max part height | Module file | Plates |
|---|---|---|---|---|---|
| **Mini (180×180)** | A1 mini class | 40–160mm | 40–160mm | `mini_comp.3mf` | 27 plates |
| **Standard (256×256)** | P1S / X1 class | 40–240mm | 40–200mm | `standard_comp.3mf` | 27 plates |
| **XL (320×320)** | Large beds | 40–320mm | 40–320mm | `XL_comp.3mf` | 35 plates |

Standard height defaults to 200mm because P1S-class beds are deep enough for 240mm left-to-right but not always front-to-back once skirts/margins are included. Lower either axis further if you prefer smaller square modules.

Standard includes all mini plates (≤160mm) plus 200–240mm sizes. XL includes mini + standard + 280–320mm sizes.

### Plate mapping per variant

**Mini** (`mini_comp.3mf`):

| Role | Plates | Sizes |
|---|---|---|
| Standard module | 01–16 | 40–160mm combinations |
| Top & bottom | 17–20 | 40–160mm wide |
| Side | 21–24 | 40–160mm tall |
| Corner TL+BL / TR+BR | 25 / 27 | 40×40mm |
| Connector | 26 | 20×20mm, 25 per sheet |

**Standard** (`standard_comp.3mf`) — adds:

| Role | Plates | Sizes |
|---|---|---|
| Standard module | 01–20 | 200–240mm combinations |
| Top & bottom | 21–22 | 200–240mm wide |
| Side | 23–24 | 200–240mm tall |
| Corner TL+BL / TR+BR | 26 / 27 | 40×40mm |
| Connector | 25 | 20×20mm, 25 per sheet |

**XL** (`XL_comp.3mf`) — adds:

| Role | Plates | Sizes |
|---|---|---|
| Standard module | 01–28 | 280–320mm combinations |
| Top & bottom | 31–32 | 280–320mm wide |
| Side | 29–30 | 280–320mm tall |
| Corner TL+BL / TR+BR | 34 / 35 | 40×40mm |
| Connector | 33 | 20×20mm, 50 per sheet |

## Module types

The system uses four different border module types — they are not interchangeable:

| Color | Type | Description |
|---|---|---|
| 🟡 Gold | Corner module | 4 corners only, 40×40mm |
| 🟢 Green | Top & bottom module | Top and bottom borders |
| 🔵 Blue | Side module | Left and right borders |
| ⬛ Dark | Standard module | Center fill |
| 🟠 Amber hatch | Wall bracket | Replaces module at mount point |

## Wall mounting

### Mount exclusions 🆕

> **New in 2026-04-11** — skip mount points where you can't anchor to the wall (e.g. hidden wires or pipes).

Pick a mode (`Column` / `Row` / `Point`) in the sidebar and click a mount point on the front view to toggle it. Click an excluded point again to bring it back. The selected items appear as chips below the mode buttons; press *Clear* to reset everything. Exclusions are persisted in `localStorage` and reset automatically when the board size changes.

### Mount parts

Each mount point uses **2 parts** from `SKADIS_modular_board_3.0_mounting.3mf`:

| Part | Plates |
|---|---|
| Corner mounts | pl. 01 |
| Side mounts | pl. 02 |
| Top & bottom mounts | pl. 03 |
| Mid board mount | pl. 04 |
| Corner module distance connector | pl. 05 |
| Side, top & bottom module distance connector | pl. 06 |
| Mid board module distance connector | pl. 07 |

## Repository layout

The planner is a **single self-contained HTML file** — no dependencies, no build step, works offline.

| File | Description |
|---|---|
| `index.html` | The planner app |
| `README.md` | This file |

## 3D model

**IKEA SKADIS Modular Pegboard 3.0** by AdamKozakGrafika
→ [MakerWorld](https://makerworld.com/ru/models/2572477-ikea-skadis-modular-pegboard-3-0)

The planner references these `.3mf` files by name, but they are **not** shipped in this repository — download them from MakerWorld:

| File | Contents |
|---|---|
| `SKADIS_modular_board_3.0_mini_comp.3mf` | Mini variant modules (≤160mm) |
| `SKADIS_modular_board_3.0_standard_comp.3mf` | Standard variant modules (200–240mm) |
| `SKADIS_modular_board_3.0_XL_comp.3mf` | XL variant modules (280–320mm) |
| `SKADIS_modular_board_3.0_mounting.3mf` | Wall mounting brackets (shared across variants) |

---

*This planner is an unofficial fan-made tool and is not affiliated with IKEA or AdamKozakGrafika.*
