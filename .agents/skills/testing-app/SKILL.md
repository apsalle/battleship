# Testing the Battleship Game

## Overview
Single-file HTML/CSS/JS browser game — no frameworks, no build step, no dependencies.

## Local Setup
```bash
cd /home/ubuntu/repos/battleship
python3 -m http.server 8080
# Open http://localhost:8080/index.html in browser
```

No credentials, API keys, or database needed.

## Testing Approach
- All testing is done via browser UI at `http://localhost:8080/index.html`
- No unit test framework exists — manual browser testing only
- Use screen recording for visual proof of feature behavior

## Game Phases
1. **Placement Phase** (default on load): Place ships on the left grid
   - Left-click = horizontal placement
   - Right-click = vertical placement
   - Double-click grid = randomize all ships
   - Press R = randomize all ships
   - Click a placed ship = remove for repositioning
2. **Battle Phase** (after clicking "Start Battle"): Click enemy grid cells to fire
3. **Game Over**: Shows trophy/skull overlays, "New Game" button appears

## Key UI Elements
- `#how-to-play-btn` / `#how-to-play-panel`: Toggleable instructions panel (between title and score tracker)
- `#startBtn`: "Start Battle" button (disabled until all ships placed, flashes when ready)
- `#restartBtn`: "New Game" button (hidden until game over)
- `#statusBar`: Shows current game status messages
- `#score-tracker`: Persistent win/loss counter
- `#playerGrid` / `#enemyGrid`: 10x10 game grids

## Quick Ship Placement for Testing
To quickly get to battle phase:
1. Double-click the player grid (randomizes all ships)
2. Click "Start Battle"

## Devin Secrets Needed
None — this is a fully static local project.
