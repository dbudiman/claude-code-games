# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Git Workflow

Commit and push to GitHub after each meaningful unit of work — a feature, a fix, a refactor. Never leave completed work uncommitted. Push frequently so no progress is lost.

**Commit format:** [Conventional Commits](https://www.conventionalcommits.org/)

```
<type>: <short description in lowercase>
```

Common types: `feat`, `fix`, `docs`, `refactor`, `chore`

Examples:
```
feat: add 90-second countdown timer
fix: stop timer on 6th-guess loss
docs: update CLAUDE.md with style guidelines
```

## Running the Games

No build step required. Open either HTML file directly in a browser:

```
open index.html    # Tic Tac Toe
open wordle.html   # Wordle
```

## Architecture

This repo contains two standalone browser games, each a single self-contained HTML file with inline CSS and JavaScript — no dependencies, no bundler, no server required.

**`index.html` — Tic Tac Toe**
- State: `board` (9-element array), `current` (active player), `gameOver`, `scores`
- Win detection via `WINS` (8 hardcoded combos); `checkWin()` scans them after each move
- Scores are in-memory only (reset on page reload)

**`wordle.html` — Wordle**
- State: `answer`, `guesses` (array of scored guess arrays), `currentGuess`, `gameOver`, `revealInProgress`
- `scoreGuess()` implements two-pass scoring: correct positions first, then present letters (avoids double-counting)
- `STATE_PRIORITY` map prevents keyboard colors from downgrading (e.g. correct → present)
- Stats (played, wins, streak, distribution) are persisted to `localStorage` under key `wordle-stats`
- Word list is a hardcoded array filtered to unique 5-letter words at load time
- Input handled via both on-screen keyboard clicks and `document.addEventListener('keydown')`

## Style

**HTML structure:** Each game is one file — `<style>` block → `<body>` → `<script>` block. No external files.

**CSS conventions:**
- Dark theme: background `#121213`, surface `#3a3a3c`, text `#fff`
- State colors: correct `#538d4e` (green), present `#b59f3b` (yellow), absent `#3a3a3c` (dark grey)
- Animations via `@keyframes` (flip, pop, shake, confettiFall); trigger by adding/removing CSS classes
- `box-sizing: border-box` applied globally; flexbox for layout

**JS coding style:**
- No comments unless the WHY is non-obvious
- Plain `let`/`const`, no classes — functions operating on module-level state variables
- DOM built programmatically in `buildBoard()` / `buildKeyboard()`; never manipulate HTML directly outside those builders
- Game state variables declared at the top of the script block

## Testing

No test framework. Verify changes by opening the file in a browser and manually testing:
- Golden path: play a full game to win and to lose
- Edge cases: duplicate letters, hard-mode constraint violations, timer expiry
- Regression: confirm existing features (keyboard colors, stats, animations) still work after each change

## Constraints

- **No dependencies** — no npm, no bundler, no external libraries or CDN imports
- **Single HTML file** — each game must remain fully self-contained in one `.html` file
- **No backend** — all state lives in memory or `localStorage`; no fetch/XHR calls
- **Browser-only** — must run by opening the file directly; no build step, no dev server required
- **No unrequested refactoring** — do not refactor working code unless specifically asked
- **No rule changes** — do not change game mechanics without explicit approval
- **No inline event handlers** — use `addEventListener` only; never use `onclick="..."` or other `on*` HTML attributes
