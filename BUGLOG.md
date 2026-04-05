# Battleship Game - Bug Log

This document tracks every bug encountered during development, including what the bug was, what caused it, and how it was fixed.

---

*No bugs logged yet for the current build. Testing in progress.*

---

## Bug #6: Game too zoomed / not fully responsive on mobile

**Problem:** The game grid uses fixed 40px cell sizes with only a single 900px media query breakpoint. On mobile phones (320–414px wide), the grid overflows the viewport or is too cramped, causing horizontal scrolling and inaccessible controls.

**Cause:** Fixed pixel cell sizes (`width: 40px; height: 40px`) with insufficient responsive breakpoints. The existing CSS only had one `@media (max-width: 900px)` rule that stacked boards vertically but never scaled down cell sizes for narrow screens. Additionally, no `touch-action: manipulation` was set, causing double-tap zoom issues on mobile grids.

**Fix:**
- Added `max-width: 100%` and `overflow-x: hidden` to `body` to prevent horizontal scrolling.
- Added `touch-action: manipulation` to `.cell` and `.btn` to prevent double-tap zoom on mobile.
- Added `@media (max-width: 600px)` breakpoint: scales cells to 28px, reduces body padding to 14px, tightens button padding.
- Added `@media (max-width: 420px)` breakpoint: scales cells to 24px, reduces body padding to 10px, scales down font sizes, tightens gaps and padding on controls, status bar, score tracker, and restart button.
- Added `@media (max-width: 350px)` breakpoint: scales cells to 22px, reduces body padding to 6px for very small screens (e.g., Galaxy Fold).
- Trophy/skull overlays are also scaled proportionally at each breakpoint.

---

## Testing Checklist

| Test Case | Result |
|-----------|--------|
| Left-click ship placement (horizontal) | |
| Right-click ship placement (vertical) | |
| Click placed ship to remove & reposition | |
| Double-click to randomize all ships | |
| R key to randomize all ships | |
| Start Battle button enables after all ships placed | |
| Ship placement at grid boundaries | |
| Overlapping ship placement rejected | |
| Repeated attacks on same cell | |
| Win condition (all enemy ships sunk) | |
| Loss condition (all player ships sunk) | |
| Win/loss score tracker increments correctly | |
| Restart preserves win/loss counter | |
| AI hunt/target mode transitions | |
| Trophy/skull overlays on game end | |
| Mobile responsiveness on 375px screen width | PASS |
| Mobile responsiveness on 320px screen width | PASS |
