# Edge inset feature — postponed

> Removed from `index.html` on 2026-04-11 because it interacted badly with the
> existing layout algorithm. Keeping the analysis here so we can come back to
> it once the underlying issues are understood.

## Goal

Allow the user to "pull" the wall-mount area inward from any board edge
(top / bottom / left / right). The outer rows of modules still get printed
and assembled — they simply overhang the bracket grid. Use case: bottom row
of the board overhangs the edge of a desk, but the rest of the board is
still securely anchored.

UI: four numeric inputs in the sidebar (`top`, `bot`, `left`, `right`),
values in cells (40 mm each). `0` = brackets at the very edge (current
behavior). `1, 2, ...` = mount area shifted N cells inward.

## Why it was removed

Two interacting bugs surfaced and the fix kept moving the goalposts:

### Bug 1 — phantom bracket row at default settings (Standard / XL sets)

`pickBounds()` for `MAX=6` (Standard) or `MAX=8` (XL) and certain heights
selects `y = hC-1` (or `x = wC-1`) as a mount position. That position is
physically occupied by the topbot/side border row, so a mid-mount cell on
that row clobbers the border layout and produces an extra "phantom" row of
brackets. With `MAX=4` (mini) the greedy `pickBounds` happens to skip
those positions, so the bug only appeared in Standard/XL.

The original code had a "dead zone" filter inside `computeMountPts()` that
silently dropped any point with `y === 1`, `y === hC-1`, `x === 1`, or
`x === wC-1`. That filter was load-bearing — it hid Bug 1 — but I removed
it during refactoring, which exposed the bug.

### Bug 2 — `bot=1` produced two stacked topbot rows

When the user set `bot=1`, the mount edge moved to `y = hC-1`, which is
the same row as the bottom topbot border. The layout then drew a second
topbot row on top of the first, with mid-mounts squashed between them.

To "fix" this I added a `bump` (`v -> v+1` for any nonzero inset), so
`bot=1` actually shifted by 2 cells. That worked numerically but is
unintuitive: the user types `1`, sees a 2-cell shift.

### Bug 3 — `bot=1` with `bump=1` made all bottom mounts disappear

When `bump` was off and `bot=1`, the mount edge landed in the dead-zone
filter, so `borderMtInner` returned no candidates and the entire bottom
row of brackets was filtered out.

### The full circular dependency

```
no bump + dead-zone filter        -> bot=1 wipes bottom mounts (Bug 3)
no bump + no dead-zone filter     -> phantom row in Standard/XL (Bug 1)
bump + dead-zone filter           -> works, but `1` means 2-cell shift
bump + no dead-zone filter        -> Bug 2 (two topbot rows)
```

The only stable combination was `bump + dead-zone filter`, which is what
shipped briefly. But it's confusing because the displayed value doesn't
match the visible result.

## What needs to be solved before re-introducing this

1. **Fix the underlying `pickBounds` issue.** The algorithm should never
   pick mount positions that lie inside an existing border row. Either:
   - Filter `1` and `total-1` out of `allBounds()` before `pickBounds()`,
     OR
   - Make `pickBounds()` aware of the "real" placement constraints, OR
   - Have `borderMtInner()` / `computeLayout()` resolve the conflict
     symbolically rather than dropping the point.

2. **Decide what `1` means in the UI.** Two viable options:
   - **Cells**: `1` = exactly one cell shift. Then the algorithm must
     handle the "mount edge sits next to the border row" case correctly,
     which means changing `computeLayout` to know about insets.
   - **Modules**: `1` = one module width shift. Then internally we just
     `bump` to cells, but the label should clearly say "modules" and the
     hint should explain that one module is at minimum 2 cells.

3. **Pass `INSET` into `computeLayout`** so the layout function knows to:
   - Skip drawing the second topbot row when the mount edge is inside the
     board.
   - Treat the cells between the inset edge and the physical edge as
     plain `std` modules, not as another border row.
   - Place mid-mount cells correctly on the new "interior border".

4. **Handle the connector grid.** Connectors are placed at module
   intersections — when the mount area is inset, the connector pattern
   inside the overhang region should differ from the inset region.

## Files that were touched (for reference, see git history before revert)

- `index.html`:
  - State: `let INSET = {t:0, b:0, l:0, r:0}` (around line 540)
  - UI panel: "Mount exclusions" / "Edge inset (cells)" panel (around 133)
  - `updateInset()` function (around 542)
  - `saveInset()` / `loadInset()` (around 559)
  - `computeMountPts()` — `bump` and dead-zone filter (around 627)
  - i18n keys: `insetTitle`, `insetHint`, `insetT/B/L/R` (around 250 / 288)
  - Init at bottom: load inputs into `ins-t/b/l/r` fields (around 1490)

## Suggested next attempt

Start by **fixing Bug 1 in isolation**, with no inset feature at all.
Verify all three module sets (mini / standard / XL) at every density
setting produce no phantom bracket rows. Once the layout algorithm is
robust against `pickBounds` picking border-adjacent rows, the inset
feature becomes a much smaller change: just shrink `wM` / `hM` in
`computeMountPts` and pass the offsets through to layout for connector
placement.

The mount-exclusions feature (column / row / point chips) is unrelated
and stays in the app.
