# Battleship Game - Bug Log

This document tracks every bug encountered during development, including what the bug was, what caused it, and how it was fixed.

---

## Bug #1: Miss markers nearly invisible on enemy grid

**What:** After firing at an empty cell on the enemy grid, the miss marker (a dot) was barely visible against the dark cell background. Players couldn't tell which cells they had already attacked with misses.

**Cause:** The CSS `::after` pseudo-element for `.cell.miss` used a color (`#546e7a`) that was too close to the dark cell background (`rgba(13, 31, 60, 0.8)`). The contrast ratio was insufficient, especially on monitors with lower brightness.

**Fix:** Increased the miss marker dot color brightness from `#546e7a` to `#90a4ae` (a lighter blue-grey), increased the dot font-size from `1.6rem` to `1.8rem`, and slightly increased the cell background opacity from `rgba(120, 144, 156, 0.2)` to `rgba(120, 144, 156, 0.25)` for a more noticeable background tint.

**File:** `index.html`, CSS `.cell.miss` and `.cell.miss::after`

---

## Bug #2: Ship placement preview partial rendering at grid boundaries

**What:** During the placement phase, when hovering near the grid edge with a ship that would extend beyond the boundary, the preview highlight only showed cells that were within bounds. Out-of-bounds cells were silently skipped.

**Cause:** The `handlePlacementHover()` function generates all cells for the ship placement using `getShipCells()`, then iterates and only highlights cells within grid bounds (boundary check before DOM query). Cells with out-of-bounds coordinates are skipped since no DOM element exists for them.

**Resolution:** Verified this is correct behavior. The preview shows red (invalid) on visible cells within bounds, the boundary validation correctly rejects the placement, and the status bar displays a clear error message. The combined visual feedback (red cells + status message) is sufficient. No code change needed.

**Edge cases tested:**
- Horizontal Carrier at column G (would extend to K — correctly rejected)
- Vertical Cruiser at row 9 (would extend to row 12 — correctly rejected)
- Ships at corner positions (row 10, col J — correctly rejected)

---

## Bug #3: Potential duplicate ship placement via stale `currentShipIndex`

**What:** After placing a ship, if the player clicked the grid before the UI advanced the `currentShipIndex`, a duplicate ship could theoretically be placed.

**Cause:** The `handlePlayerGridClick()` function uses `currentShipIndex` to determine which ship to place. If the index wasn't updated before the next click, the same ship definition could be used twice.

**Resolution:** The existing code correctly handles this. `handlePlayerGridClick()` first checks `isShipAlreadyPlaced(currentShipIndex)` and auto-advances to `findNextUnplacedShip()`. The `placed` CSS class on buttons also prevents re-selection via `handleShipButtonClick()`. Verified through rapid-click testing — no duplicates possible.

---

## Bug #4: AI target mode recursion depth concern

**What:** During code review, identified that if the AI's target queue contained only already-tried cells, the `aiTurn()` function would recursively call itself to skip them, potentially causing deep recursion.

**Cause:** The target mode in `aiTurn()` uses recursion to skip already-tried cells. Each call checks `aiState.triedCells` and recurses if the cell was already attacked.

**Resolution:** The recursion depth is bounded by the target queue size, which has a maximum of 4 entries per hit (up/down/left/right). Even in the worst case of multiple hits, the queue rarely exceeds ~8 entries. Stack overflow is not a practical concern. No code change needed.

---

## Bug #5: Status bar retaining stale CSS class from previous turn

**What:** After the AI fires and the status bar gets a colored class (e.g., `hit`, `miss`, `sunk`), a subsequent player action (like clicking an already-attacked cell) could display a new message while retaining the previous turn's color styling.

**Cause:** Investigated whether `setStatus()` properly clears the CSS class. The function accepts an optional `cssClass` parameter.

**Resolution:** The initial implementation already handles this correctly. `setStatus()` always sets `statusBar.className` to either the provided class or an empty string (via the else branch). Verified through testing: firing on an already-attacked cell shows the "already fired" message with neutral (no-class) styling, correctly clearing any previous hit/miss coloring.

---

## Testing Summary

All edge cases were tested and verified:

| Test Case | Result |
|-----------|--------|
| Ship placement at grid boundaries (all 4 edges) | PASS - correctly rejected with error message |
| Overlapping ship placement | PASS - correctly rejected with red preview |
| Repeated attacks on same cell | PASS - "You already fired there!" message, no state change |
| Win condition (all enemy ships sunk) | PASS - victory message, score update, NEW GAME button |
| Loss condition (all player ships sunk) | PASS - defeat message, score update, NEW GAME button |
| Restart preserves win/loss counter | PASS - scores persist across games |
| Random ship placement | PASS - all 5 ships placed without overlap |
| Manual ship placement with orientation toggle | PASS - horizontal and vertical placement both work |
| Reset placement button | PASS - clears all placed ships, resets to Carrier |
| AI hunt/target mode transitions | PASS - AI switches between modes correctly |
| Keyboard shortcut (R to rotate) | PASS - toggles orientation during placement |
