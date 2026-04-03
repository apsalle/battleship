# Testing the Battleship Game

## Local Server
```bash
python3 -m http.server 8080
```

## Game Files
- `index.html` — Full game with ship selection buttons, orientation toggle, randomize/reset
- `game.html` — Alternative game with click-based placement (left click horizontal, right click vertical, double-click randomize)

## Testing Flows

### game.html
1. Open http://localhost:8080/game.html
2. Verify placement legend (instructions) appears at top as horizontal bar
3. Verify status bar shows dynamic message like "Placing: Carrier (5)"
4. Double-click grid to randomize all ships
5. Click "Start Battle" — verify legend/controls hide
6. Play through battle or reload to test restart flow
7. After game ends, click "New Game" — verify legend reappears with correct status

### index.html
1. Open http://localhost:8080/index.html
2. Verify placement controls (ship buttons) appear at top as horizontal bar
3. Verify status bar shows dynamic message like "Place your Carrier (5 cells)"
4. Click ship buttons to select, click grid to place
5. Click "Start Battle" — verify controls hide
6. Play through battle or reload to test restart flow

## Key DOM Elements
- `game.html`: `.placement-legend` (instructions), `.status-bar` / `#statusBar`, `#topControls` (Start Battle), `#placementInfo`
- `index.html`: `#placement-controls` (ship buttons), `#status-bar`, `#start-battle-btn`

## Phase Transitions
- **Placement phase**: Legend/controls visible, status shows current ship
- **Battle phase**: Legend/controls hidden via `.hidden` class (game.html) or `display:none` (index.html)
- **Game over**: New Game button appears; clicking it restores placement phase UI
