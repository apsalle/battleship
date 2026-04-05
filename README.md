# Battleship

A browser-based Battleship game against an AI opponent, built as a single HTML file with no dependencies.

## How to Play

### 1. Placement Phase

Place 5 ships on your 10×10 grid. Select a ship, choose orientation (horizontal/vertical — toggle with the button or press **R**), and click the grid to place it. You can also use **Random** to auto-place all ships, or **Reset** to start placement over.

### 2. Battle Phase

Take turns with the AI. Click a cell on the enemy grid to fire. Hits show fire, misses show a dot, and sunk ships are marked with **X**. The AI uses a hunt/target strategy.

### 3. Game Over

The first player to sink all 5 enemy ships wins. Click **New Game** to play again. Win/loss scores persist across games.

## Ships

| Ship       | Size |
|------------|------|
| Carrier    | 5    |
| Battleship | 4    |
| Cruiser    | 3    |
| Submarine  | 3    |
| Destroyer  | 2    |

## Running the Game

Just open `index.html` in any modern browser. No build step or server required.
