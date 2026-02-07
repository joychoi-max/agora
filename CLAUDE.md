# CLAUDE.md

## Project Overview

**Neon Typing Game** (네온 타이핑 게임) — A Korean-language typing game built as a single-file web application. Players type falling Korean words before they reach the bottom of the screen. Created as a student project during an agora class.

## Repository Structure

```
agora/
├── CLAUDE.md            # This file — AI assistant guide
├── README.md            # Brief project description (Korean)
└── typing-game.html     # Entire application (HTML + CSS + JS)
```

This is a **single-file application**. All HTML, CSS, and JavaScript live in `typing-game.html` (~805 lines).

## Tech Stack

- **HTML5** — Document structure and game UI
- **CSS3** — Styling, animations (neon aesthetic, particle effects, glass-morphism)
- **Vanilla JavaScript (ES6+)** — Game logic, no frameworks or libraries
- **External dependency:** Google Fonts (Orbitron, Noto Sans KR)

## How to Run

Open `typing-game.html` in any modern web browser. No build step, no server, no package manager required.

## Architecture

The application is organized into three inline sections within `typing-game.html`:

| Section | Lines | Description |
|---------|-------|-------------|
| CSS | 8–444 | CSS variables, animations, layout, neon effects |
| HTML | 446–509 | Game UI: start screen, game area, input, game-over screen |
| JavaScript | 511–803 | Game state, word bank, core game loop, event handlers |

### Key JavaScript Components

- **`gameState` object** (line 513) — Central state: score, level, lives, active words, combo, intervals
- **`wordBank` array** (line 526) — 72 Korean words used as typing targets
- **Core functions:**
  - `startGame()` / `restartGame()` — Initialize and reset game state
  - `spawnWord()` — Creates falling word elements with CSS animation
  - `destroyWord()` — Removes matched words, triggers particle effects
  - `loseLife()` — Decrements lives, triggers game over at 0
  - `levelUp()` — Increases difficulty (faster spawn rate, faster fall)
  - `gameOver()` — Ends game, shows final score
  - `updateDisplays()` — Syncs DOM with game state
- **Input handler** (line 637) — Matches typed input against active falling words

### Game Mechanics

- **Scoring:** 100 base + (level x 50) + (combo x 25) per word
- **Levels:** Level up at `score >= level * 1000`; spawn interval = `max(2000/level, 500)ms`
- **Fall speed:** `max(8 - level * 0.5, 3)` seconds per word
- **Lives:** 3 (displayed as hearts); missed word = lose 1 life
- **Combo:** Increments on consecutive matches, resets on miss or no-match input

### CSS Variables (Design Tokens)

Defined in `:root` at the top of the `<style>` block:
- `--neon-pink`, `--neon-cyan`, `--neon-purple`, `--neon-yellow`, `--neon-green`
- `--dark-bg`, `--darker-bg`

## Development Conventions

### Language
- **UI text:** Korean (한국어)
- **Code:** English variable/function names
- **Word bank:** Korean words

### Code Style
- No linter or formatter configured
- Functions use standard `function` declarations (not arrow functions for top-level)
- Arrow functions used in callbacks and event handlers
- DOM elements cached in module-scope `const` variables
- Game state managed via a single mutable `gameState` object

### No Build Tools
There is no package.json, no bundler, no transpiler, no task runner. This is intentional — the project is meant to be a simple, portable single-file application.

### No Tests
No automated test framework. Testing is done manually by opening the HTML file in a browser.

### No CI/CD
No GitHub Actions, no deployment pipeline.

## Guidelines for AI Assistants

1. **Single-file constraint:** Keep all code in `typing-game.html` unless the user explicitly requests splitting it into multiple files.
2. **No unnecessary dependencies:** The project intentionally uses vanilla JS with no frameworks. Do not introduce npm, webpack, React, or similar unless explicitly asked.
3. **Korean UI:** All user-facing text should remain in Korean. Code identifiers stay in English.
4. **Preserve the neon aesthetic:** CSS uses a consistent neon/cyberpunk color scheme via CSS variables. Respect this when adding UI elements.
5. **Game state pattern:** All mutable game state flows through the `gameState` object. Follow this convention for new features.
6. **Browser-only:** No Node.js, no server-side code. Everything runs client-side in the browser.
