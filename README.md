# The Pig Game (Vanilla JS)

Two-player browser implementation of the classic “Pig” dice game.

- No build step, no framework
- Static HTML/CSS/JS + images
- Supports **1 die** (base rules) and **2 dice** (variation)

Demo: https://piggame.fcjamison.com/

## Quick Start (Local)

This is a static site. You can run it in any modern browser.

### Option A: open the file

- Open `index.html` directly.

This works for gameplay, but note that fonts/icons are loaded via CDN and may be blocked by some browser privacy settings when using `file://`.

### Option B: run a local server (recommended)

From the project root:

- Python: `python -m http.server 5500`
- Node: `npx http-server -p 5500`

Then open `http://localhost:5500/`.

### Option C: VS Code task / custom hostname

This workspace includes a task that opens `http://thepiggame.localhost/` in Chrome.

- `*.localhost` often resolves to `127.0.0.1` automatically, but not always.
- You still need a local web server configured to serve this folder at that hostname.

## How To Play

UI controls:

- **New game**: reset scores/state
- **Roll dice**: roll 1 or 2 dice depending on mode
- **Hold**: bank current round points into global score, then switch players
- **Final score** input: winning target (invalid/blank/< 1 defaults to **100**)
- **Mode (1 Die / 2 Dice)**: changing mode re-initializes the game to avoid mixing rule sets

## Rules

### 1 Die (base)

- Players alternate turns.
- Each roll adds to the player’s **Current** (round) score.
- Rolling a **1** loses the Current score and ends the turn.
- **Hold** adds Current → **Global** and ends the turn.
- First to reach the winning score wins.

### 2 Dice (variation)

When **2 Dice** is selected:

- Rolling a **1** on either die loses the Current score and ends the turn.
- Rolling **double 1s** (“snake eyes”) resets the active player’s **Global** score to 0 and ends the turn.

## Project Layout

```
.
├─ index.html         # DOM structure + CDN links
├─ css/
│  └─ style.css       # layout + state styling (active/winner)
├─ js/
│  └─ app.js          # rules + state + DOM updates
└─ images/
   ├─ back.jpg
   ├─ dice-1.png
   ├─ dice-2.png
   └─ ... dice-6.png
```

External dependencies (via CDN in `index.html`):

- Google Fonts (Lato)
- Ionicons 2.x

## Developer Guide (Code Tour)

All game logic is in `js/app.js`. The code is intentionally small and DOM-driven.

### Development workflow

- Edit `js/app.js`, `css/style.css`, or `index.html`
- Refresh the browser (no bundler / no hot reload unless your server provides it)
- Use DevTools to debug:
  - Console errors usually indicate a selector mismatch (IDs/classes changed in `index.html`)
  - Set breakpoints in the Roll/Hold handlers to inspect `scores`, `roundScore`, and `activePlayer`

### State

Top-level state variables:

- `scores`: global totals as `[p0, p1]`
- `roundScore`: points accumulated this turn
- `activePlayer`: `0` or `1`
- `gamePlaying`: `true` until a winner is declared
- `gameVersion`: `'1'` or `'2'` (reads from `<select class="version">`)
- `dice1`, `dice2`: the latest rolls

### DOM contract (what the JS expects)

`app.js` assumes these IDs/classes exist:

- Player panels: `.player-0-panel`, `.player-1-panel`
- Name labels: `#name-0`, `#name-1`
- Global scores: `#score-0`, `#score-1`
- Current scores: `#current-0`, `#current-1`
- Buttons: `.btn-new`, `.btn-roll`, `.btn-hold`
- Mode selector: `.version`
- Winning score input: `.final-score`
- Dice images: `#dice-1`, `#dice-2`

If you change markup in `index.html`, keep these selectors in sync.

### Key functions

- `initializeGame()`
  - Resets all state, clears UI, removes winner styling, sets Player 1 active.
  - Reads mode from `getGameVersion()`.
- `switchPlayers()`
  - Toggles `activePlayer`, resets `roundScore`, clears both Current fields, hides dice.
- `getGameVersion()`
  - Returns the current `<select class="version">` value (`'1'` or `'2'`).

### Event flow

- Mode change (`.version`): calls `initializeGame()`.
- Roll (`.btn-roll`):
  - Always rolls die #1 and updates `#dice-1`.
  - Rolls die #2 only when `gameVersion === '2'` and updates `#dice-2`.
  - Snake eyes: if `(dice1 === 1 && dice2 === 1)`, resets the active player’s global score to 0, then switches.
  - Otherwise: if neither roll is 1, adds `dice1 + dice2` to `roundScore`. If a 1 appears, switches.
- Hold (`.btn-hold`):
  - Adds `roundScore` to the active player’s global total.
  - Parses `.final-score` via `parseInt`; defaults to `100` if invalid.
  - Declares winner (sets name to “Winner!”, applies `.winner`, disables play) or switches.

### Implementation details worth knowing

- In **1-die** mode, `dice2` remains `0`, so `roundScore += dice1 + dice2` effectively adds only die #1.
- Dice images are swapped by setting `img.src` to `images/dice-{n}.png`.

## Common Changes

- **Change the default winning score**: update the `winningScore = 100;` branch in the Hold handler (`js/app.js`).
- **Change the win condition** (e.g., “exactly equals”): adjust the `if (scores[activePlayer] >= winningScore)` check.
- **Add a rule tweak**: the Roll handler is the single place where “bad rolls” (1 / snake eyes) are applied.

## Troubleshooting

- Dice not visible: dice are intentionally hidden until the first roll (`display: none` until Roll).
- Fonts/icons missing: they are loaded from CDNs; confirm your network allows `fonts.googleapis.com` and `code.ionicframework.com`.
- `thepiggame.localhost` doesn’t load: you need a local server mapped to that hostname (and possibly a hosts entry).

## Deployment

Deploy to any static host (GitHub Pages, Netlify, S3, etc.). Ensure `images/` is included and paths remain relative.
