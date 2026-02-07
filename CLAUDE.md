# CLAUDE.md

## Project Overview

A collection of Korean-language browser games with a neon/cyberpunk aesthetic. Each game is a standalone single-file web application. Created as a student project during an agora class.

## Repository Structure

```
agora/
├── CLAUDE.md            # This file — AI assistant guide
├── README.md            # Brief project description (Korean)
├── typing-game.html     # 네온 타이핑 게임 (Korean typing game)
└── snake-game.html      # 네온 스네이크 게임 (Snake game)
```

Each game is a **single-file application** — all HTML, CSS, and JavaScript are self-contained in one `.html` file.

## Tech Stack

- **HTML5** — Document structure and game UI
- **CSS3** — Styling, animations (neon aesthetic, particle effects, glass-morphism)
- **Vanilla JavaScript (ES6+)** — Game logic, no frameworks or libraries
- **External dependencies:** Google Fonts (Orbitron, Noto Sans KR), Supabase JS Client (CDN)

## How to Run

Open any `.html` game file in a modern web browser. No build step, no server, no package manager required.

## Architecture

Each game follows the same single-file pattern with inline CSS and JavaScript.

### typing-game.html (네온 타이핑 게임)

The application is organized into three inline sections:

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

### snake-game.html (네온 스네이크 게임)

| Section | Description |
|---------|-------------|
| CSS | CSS variables, animations, neon glow effects, overlays, mobile controls |
| HTML | HUD (score/high score/level), canvas, start/game-over overlays, mobile D-pad |
| JavaScript | Game state, snake movement, food spawning, collision detection, rendering |

**Key JavaScript Components:**
- **`gameState` object** — Central state: snake segments, food position, direction, score, level, speed
- **Core functions:**
  - `startGame()` / `restartGame()` — Initialize and reset game state
  - `tick()` — Main game loop: move snake, check collisions, eat food
  - `draw()` — Canvas rendering: grid, snake with gradient body, food with glow, head eyes
  - `spawnFood()` — Places food at random position not occupied by snake
  - `togglePause()` — Pause/resume with P key
  - `gameOver()` — Ends game, updates high score in localStorage

**Game Mechanics:**
- **Grid:** 20x20 cells, 20px each (400x400 canvas)
- **Scoring:** 10 points per food
- **Levels:** Level up every 50 points; speed increases by 5ms per level (min 60ms)
- **Controls:** Arrow keys, WASD, or mobile D-pad
- **High score:** Persisted in localStorage

### CSS Variables (Design Tokens)

Defined in `:root` at the top of the `<style>` block:
- `--neon-pink`, `--neon-cyan`, `--neon-purple`, `--neon-yellow`, `--neon-green`
- `--dark-bg`, `--darker-bg`

## Supabase (게임 기록 저장)

Both games save scores to a shared Supabase `game_records` table and display a leaderboard.

### Setup

1. [Supabase](https://supabase.com)에서 프로젝트 생성
2. SQL Editor에서 아래 SQL 실행:

```sql
create table game_records (
  id bigint generated always as identity primary key,
  game_name text not null,
  player_name text not null default '익명',
  score integer not null,
  level integer not null default 1,
  created_at timestamptz not null default now()
);

-- 누구나 읽기 가능
alter table game_records enable row level security;
create policy "Anyone can read records" on game_records for select using (true);
create policy "Anyone can insert records" on game_records for insert with check (true);

-- 성능을 위한 인덱스
create index idx_game_records_leaderboard on game_records (game_name, score desc);
```

3. 각 HTML 파일 상단의 `SUPABASE_URL`과 `SUPABASE_ANON_KEY`를 본인 프로젝트 값으로 변경:
```js
const SUPABASE_URL = 'https://YOUR_PROJECT_ID.supabase.co';
const SUPABASE_ANON_KEY = 'YOUR_ANON_KEY';
```

### How It Works

- **게임 오버 시** `game_records` 테이블에 `{ game_name, player_name, score, level }` 자동 저장
- **시작 화면 / 게임 오버 화면**에 Top 10 리더보드 표시
- Supabase 미설정 시에도 게임은 정상 작동 (리더보드만 비활성)

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

1. **Single-file constraint:** Keep each game as a self-contained `.html` file unless the user explicitly requests splitting.
2. **No unnecessary dependencies:** The project intentionally uses vanilla JS with no frameworks. Do not introduce npm, webpack, React, or similar unless explicitly asked.
3. **Korean UI:** All user-facing text should remain in Korean. Code identifiers stay in English.
4. **Preserve the neon aesthetic:** CSS uses a consistent neon/cyberpunk color scheme via CSS variables. Respect this when adding UI elements.
5. **Game state pattern:** All mutable game state flows through the `gameState` object. Follow this convention for new features.
6. **Browser-only:** No Node.js, no server-side code. Everything runs client-side in the browser.
