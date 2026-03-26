# Battleship Game — Bug Log

This document tracks every bug found during development of `game.html`, including what the bug was, what caused it, and how it was fixed.

---

## Bug #1: Context menu appearing on player grid during battle phase

**What:** After transitioning from the placement phase to the battle phase, right-clicking on the player grid would display the browser's default context menu. During placement, the context menu was correctly suppressed by the `contextmenu` event handler, but this handler was removed when placement events were unbound at battle start.

**Cause:** The `unbindPlacementEvents()` function removed all placement-related event listeners from the player grid, including the `contextmenu` handler that called `e.preventDefault()`. Once unbound, the browser's native right-click context menu was free to appear again.

**Fix:** Added a global `contextmenu` suppressor on both the player and enemy grids that is registered once at initialization and never removed. This ensures the browser context menu is suppressed in all game phases — placement, battle, and game over.

**File:** `game.html`, lines ~923–926

---

## Bug #2: Player could fire during AI's turn delay

**What:** After the player fires at the enemy grid, there is a 400ms delay before the AI takes its turn. During this window, the player could click on another enemy cell and fire a second shot before the AI responded, effectively getting two turns in a row.

**Cause:** The `handlePlayerAttack()` function did not guard against clicks while the AI's `setTimeout` callback was pending. The 400ms delay was purely cosmetic (to give the player time to see their result), but left the enemy grid responsive to additional clicks.

**Fix:** Added a `playerTurnActive` flag that is set to `false` immediately after the player fires and before the AI timeout begins. The flag is checked at the top of `handlePlayerAttack()` — if `false`, the click is ignored. The flag is restored to `true` after the AI completes its turn, but only if the game is still in the `BATTLE` phase (not if the AI's attack triggered a game-over).

**File:** `game.html`, `handlePlayerAttack()` and `aiTurn()` callback

---

## Bug #3: Double-click firing two single-click placement events

**What:** In the browser, a `dblclick` event is always preceded by two `click` events. Without mitigation, double-clicking on the player grid during placement would first place a ship via the first `click`, then potentially place another ship via the second `click`, before the `dblclick` handler ran to randomize all ships.

**Cause:** The standard DOM event sequence for a double-click is: `click` → `click` → `dblclick`. JavaScript fires both `click` events before the `dblclick`, meaning the placement handler would run twice before the randomize handler could intervene.

**Fix:** Implemented a click-delay mechanism with a suppression flag:
1. Single-click events are deferred by 250ms using `setTimeout` before executing placement logic.
2. The `dblclick` handler immediately clears any pending click timer via `clearTimeout` and sets a `clickSuppressed` flag to `true`.
3. The deferred click handler checks `clickSuppressed` before executing — if `true`, it does nothing.
4. The suppression flag is reset to `false` after 300ms to allow normal clicking to resume.

This ensures that a double-click only triggers the randomize action, never individual ship placements.

**File:** `game.html`, `onPlayerGridClick()`, `onPlayerGridDblClick()`

---

## Bug #4: Right-click triggering browser context menu during placement

**What:** Right-clicking on the player grid to place a ship vertically would also trigger the browser's default context menu, overlaying it on top of the game UI.

**Cause:** The `contextmenu` event fires on right-click by default in all browsers. Without explicitly calling `e.preventDefault()` in the handler, the browser's native context menu appears.

**Fix:** The `onPlayerGridRightClick` handler calls both `e.preventDefault()` and `e.stopPropagation()` at the very top of the function, before any game logic. This suppresses the context menu regardless of whether the click results in a valid ship placement or not. Additionally, the global context menu suppressor (Bug #1 fix) provides a safety net for all phases.

**File:** `game.html`, `onPlayerGridRightClick()`

---

## Bug #5: Redundant context menu handler on enemy grid

**What:** The enemy grid had a separate `contextmenu` event handler (`onEnemyGridRightClick`) that was bound and unbound with the battle events. This was unnecessary after the global suppressor was added.

**Cause:** The original design bound a `contextmenu` handler to the enemy grid only during the battle phase. After adding the global suppressor (Bug #1 fix), this handler was redundant — the global suppressor already prevents context menus on the enemy grid at all times.

**Fix:** Removed the per-phase `contextmenu` binding/unbinding from `bindBattleEvents()` and `unbindBattleEvents()`. The global suppressor handles all context menu prevention for both grids.

**File:** `game.html`, `bindBattleEvents()`, `unbindBattleEvents()`

---

## Testing Summary

All required edge cases were tested and verified:

| Test Case | Result |
|-----------|--------|
| Right-click does not trigger browser context menu during placement | PASS |
| Double-click does not fire two single-click events before randomizing | PASS |
| Ships cannot overlap or be placed outside the grid boundary | PASS |
| Player cannot attack the same cell twice | PASS |
| Win condition triggers correctly when all ships are sunk | PASS |
| Game is fully restartable without refreshing the browser | PASS |
| All placement interactions are fully disabled once the attack phase begins | PASS |
| Player cannot fire during AI's turn delay | PASS |
| Context menu suppressed on both grids in all game phases | PASS |
