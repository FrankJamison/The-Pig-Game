# The Pig Game

Two-player browser game implementing the classic “Pig” dice rules in **Vanilla JS**, with a **1-die** mode and a **2-dice** variation.

No build step, no framework—just HTML/CSS/JS and static assets.

## Demo

- Public demo: https://2020-thepiggame.fcjamison.com/

## Gameplay Rules

### 1 Die (Base Rules)

- Players alternate turns.
- **Roll dice** adds to your **Current** (round) score.
- Rolling a **1** loses your Current score and ends your turn.
- **Hold** banks Current into your **Global** score and ends your turn.
- First to reach the **winning score** wins.

### 2 Dice (Variation)

When **2 Dice** is selected:

- Rolling a **1** on either die loses Current and ends your turn.
- Rolling **double 1s** (snake eyes) resets the active player’s **Global** score to 0 and ends the turn.

## Controls

- **New game**: resets all state and UI.
- **Roll dice**: rolls 1 or 2 dice depending on mode, and updates Current.
- **Hold**: adds Current to Global, then switches players.
- **Final score** input: winning score target.
  - Blank/invalid/< 1 defaults to **100**.
- **Mode selector (1 Die / 2 Dice)**: changing it re-initializes the game to avoid mixed-rule ambiguity.

## Tech Stack

- HTML (static)
- CSS (layout + state styling)
- JavaScript (rules engine + DOM updates)

## Project Structure

```
.
├─ index.html
├─ css/
│  └─ style.css
├─ js/
│  └─ app.js
└─ images/
   ├─ back.jpg
   ├─ dice-1.png
   ├─ ...
   └─ dice-6.png
```

## Run Locally

This is a static site—any static server works.

### Option A: Open the file directly

Open `index.html` in your browser.

### Option B: Serve with a local HTTP server (recommended)

From the project folder:

- Python: `python -m http.server 5500`
- Node: `npx http-server -p 5500`

Then visit `http://localhost:5500/`.

### Option C: VS Code “Live Server”

If you use the Live Server extension, right-click `index.html` → “Open with Live Server”.

### Option D: Use `thepiggame.localhost`

This workspace includes a VS Code task that opens `http://thepiggame.localhost/` in Chrome.

Notes:

- `*.localhost` commonly resolves to `127.0.0.1` in modern environments. If it doesn’t on your machine, add a hosts entry mapping it to localhost.
- You still need a local server (Apache/Nginx/other) configured to serve this folder at that hostname.

## Developer Notes (How the Code Works)

All gameplay logic lives in `js/app.js`.

### State Model

The game keeps a small, explicit state:

- `scores`: global totals `[p0, p1]`
- `roundScore`: the current turn accumulator
- `activePlayer`: `0` or `1`
- `gamePlaying`: disables actions after someone wins
- `gameVersion`: `'1'` or `'2'` from the `<select class="version">`

### Core Functions

- `initializeGame()`
  - Resets variables, scores, winner/active classes, and hides dice.
  - Reads the current mode via `getGameVersion()`.
- `switchPlayers()`
  - Toggles `activePlayer`, resets `roundScore`, clears both Current fields, and hides dice.
- `getGameVersion()`
  - Returns the `<select>` value (`'1'` or `'2'`).

### Event Flow

- Mode change (`.version`): re-initializes game.
- Roll (`.btn-roll`):
  - Always rolls die 1.
  - Rolls die 2 only in 2-dice mode.
  - Updates Current unless a 1 was rolled, in which case it switches players.
  - Snake eyes (double 1s) sets the active player’s Global score to 0 and switches players.
- Hold (`.btn-hold`):
  - Adds Current to Global.
  - Reads winning score from `.final-score` (defaults to 100).
  - Declares winner or switches players.

### 1-Die Mode Implementation Detail

In 1-die mode, the code keeps `dice2` at `0`, so the scoring line `roundScore += dice1 + dice2` effectively adds only die 1.

## Deployment

Because this is static, you can deploy it to any static host (GitHub Pages, Netlify, S3, etc.). Make sure the `images/` folder is included.

## Credits

- Game concept: classic “Pig” dice game
- Icons: Ionicons (CDN)
- Font: Lato (Google Fonts)

## License

No license specified. Add a license (for example, MIT) if you plan to distribute this publicly.
