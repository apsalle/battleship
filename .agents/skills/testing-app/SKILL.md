# Battleship Game — Testing Skill

## Local Development Setup

1. Start a local HTTP server from the repo root:
   ```bash
   cd /home/ubuntu/repos/battleship
   python3 -m http.server 8080 &
   ```
2. Open `http://localhost:8080/index.html` in Chrome.

No build step, no dependencies — it's a single-file HTML/CSS/JS app.

## Testing Mobile Responsiveness

The game has CSS breakpoints at:
- **900px**: boards stack vertically
- **600px**: cells scale to 28px
- **420px**: cells scale to 24px, fonts/gaps reduced
- **350px**: cells scale to 22px, minimal padding

### How to test with Chrome DevTools:
1. Open DevTools (F12)
2. Toggle device toolbar (Ctrl+Shift+M)
3. Set viewport width in the dimension field at the top
4. Key widths to test: 375px (iPhone SE), 320px (small phone), 500px (tablet-ish), 280px (Galaxy Fold)

### Key assertions to verify at each breakpoint:
```javascript
// Cell size
document.querySelector('.cell').getBoundingClientRect().width
// Expected: 28 at ≤600px, 24 at ≤420px, 22 at ≤350px, 40 at >600px

// No horizontal scroll
document.documentElement.scrollWidth <= document.documentElement.clientWidth
// Expected: true

// Touch-action (prevents double-tap zoom on mobile)
getComputedStyle(document.querySelector('.cell')).touchAction
// Expected: "manipulation"
```

## Gameplay Regression Testing

Quick sanity check at any viewport:
1. Press **R** key to randomize all ships (or double-click the player grid)
2. Verify all 5 ships appear as purple cells on the player grid
3. Click **Start Battle** button
4. Click any cell on the **Enemy Waters** grid
5. Verify a hit (fire emoji) or miss (dot) marker appears
6. Verify the AI responds with its own attack on your fleet
7. Status bar should update with hit/miss/sunk messages

## Ship Placement Testing

- **Left-click** on player grid: places ship horizontally
- **Right-click** on player grid: places ship vertically
- **Click on a placed ship**: removes it for repositioning
- **Double-click** or **R key**: randomizes all ships
- Start Battle button enables only when all 5 ships are placed

## Devin Secrets Needed

None — this is a fully static client-side game with no backend or authentication.

## Notes

- The game uses CSS class names (`.cell`, `.btn`, `.status-bar`, `.boards-container`, `.controls`) not IDs for most styling. Some bug specs may reference IDs that don't exist (e.g., `#placement-controls`, `#status-bar`) — map these to the actual CSS selectors.
- Trophy/skull overlays need to be scaled proportionally when cell sizes change (they use absolute positioning offset by the label row/column size).
- The `wmctrl` package may need to be installed for maximizing the browser window before recording: `sudo apt-get install -y wmctrl`
