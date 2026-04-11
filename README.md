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
- 🆕 **Edge insets** *(added 2026-04-11)* — pull the mount area inward from any side (top / bottom / left / right) so the outer module rows overhang while brackets stay structurally sound

## How to use

1. Choose **print bed variant** — Mini, Standard, or XL (see below)
2. Set board **width** and **height** using the sliders (in 40mm cells)
3. Adjust **mount density** — how frequently the board is anchored to the wall
4. Switch between **Board layout**, **Wall mounting**, and **Parts catalog** tabs
5. Use the **Print list** in the sidebar as your shopping/printing checklist
6. Toggle **RU / EN** in the top-right corner to switch language
7. Toggle **☀ / ☾** to switch between dark and light themes

## Print bed variants

The 3D model comes in three variants for different printer build plate sizes. The planner supports all three — switch via the selector at the top of the sidebar.

| Variant | Max module size | Build plate | Module file | Plates |
|---|---|---|---|---|
| **Mini** | 160×160mm (4×4 cells) | ~180×180mm | `mini_comp.3mf` | 27 plates |
| **Standard** | 240×240mm (6×6 cells) | ~260×260mm | `standard_comp.3mf` | 27 plates |
| **XL** | 320×320mm (8×8 cells) | ~350×350mm | `XL_comp.3mf` | 35 plates |

Standard includes all mini plates (for modules ≤160mm) plus larger sizes. XL includes mini + standard + extra-large sizes.

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

### Exclusions and insets 🆕

> **New in 2026-04-11** — fine-grained control over which mount points are actually anchored to the wall.

Sometimes you can't (or don't want to) anchor every mount point — a wire runs behind the wall, you want a row of modules to overhang a desk, or you simply want to thin out the brackets in a specific spot.

The **Mount exclusions** panel offers two complementary tools:

- **Exclusion modes** — pick a mode (`Column` / `Row` / `Point`) and click a mount point on the front view to remove it. Click again on an excluded point to bring it back. The selected items appear as chips below the mode buttons; press *Clear* to reset everything. Exclusions are persisted in `localStorage` and reset automatically when the board size changes.
- **Edge insets** — pull the mount area inward from any side. Each input is in **modules** (not cells): `0` = brackets sit at the very edge (default), `1` = mount area shifted one module inward, `2` = two modules, etc. The outer module rows still get printed and assembled — they simply overhang the bracket grid. Useful for hanging the bottom row past a desk edge while keeping the rest of the board firmly anchored. The bracket grid is fully recomputed inside the inset region, so density and structural strength are preserved.

> Why values are in *modules*, not cells: shifting the mount edge by just one cell would land it adjacent to a corner module, where the bracket can't be physically attached. The planner internally adds the extra cell so the math always works out.

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

## Files

The planner is a **single self-contained HTML file** — no dependencies, no build step, works offline.

| File | Description |
|---|---|
| `index.html` | The planner app |
| `models/SKADIS_modular_board_3.0_mini_comp.3mf` | Mini variant modules (≤160mm) |
| `models/SKADIS_modular_board_3.0_standard_comp.3mf` | Standard variant modules (200–240mm) |
| `models/SKADIS_modular_board_3.0_XL_comp.3mf` | XL variant modules (280–320mm) |
| `models/SKADIS_modular_board_3.0_mounting.3mf` | Wall mounting brackets (shared) |
| `README.md` | This file |

## 3D model

**IKEA SKADIS Modular Pegboard 3.0** by AdamKozakGrafika
→ [MakerWorld](https://makerworld.com/ru/models/2572477-ikea-skadis-modular-pegboard-3-0)

---

*This planner is an unofficial fan-made tool and is not affiliated with IKEA or AdamKozakGrafika.*
