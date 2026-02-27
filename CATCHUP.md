# Rainbow Unicorn Quest: 4-Player Co-Op Arcade

## Project Overview

A single-file HTML5 arcade platformer expanded from 2-player co-op to **4-player co-op** with a TMNT arcade feel. Built on the original `Glen-collab/coop_unicorn` codebase.

**Output:** Single `index.html` (~12,400 lines) + `sprites/` (53 PNGs) + `sounds/` (54 audio files)

---

## 4 Playable Characters

| Slot | Character  | Color     | Default Name | Form Flags                                    |
|------|-----------|-----------|-------------|-----------------------------------------------|
| P1   | Unicorn    | `#ffffff` | STAR        | Base form (no special flags)                  |
| P2   | Kittycorn  | `#ff88cc` | LUNA        | `isKittycorn = true`                          |
| P3   | Pixicorn   | `#cc88ff` | FAYE        | `isPixicorn = true`                           |
| P4   | Mousicorn  | `#aa8866` | PIP         | `isMouseicorn = true`                         |

## Controls

| Slot | Keyboard                          | Gamepad   |
|------|-----------------------------------|-----------|
| P1   | Arrows + Space/Shift/P            | Gamepad 0 |
| P2   | WASD + Q(jump)/F(shoot)/X(poop)   | Gamepad 1 |
| P3   | Gamepad only                      | Gamepad 2 |
| P4   | Gamepad only                      | Gamepad 3 |

---

## Architecture & Key Systems

### CHARACTER_DEFS System
Replaced the old P2 hue-rotate palette swap (`SPRITES_P2`, `generateP2Sprites()`) with a declarative config:

```javascript
const CHARACTER_DEFS = {
  unicorn:    { id, name, defaultName: "STAR", fallbackColor: "#ffffff", labelColor: "#ffffff", startForm: {...} },
  kittycorn:  { id, name, defaultName: "LUNA", fallbackColor: "#ff88cc", labelColor: "#ff88cc", startForm: { isKittycorn: true } },
  pixicorn:   { id, name, defaultName: "FAYE", fallbackColor: "#cc88ff", labelColor: "#cc88ff", startForm: { isPixicorn: true } },
  mousicorn:  { id, name, defaultName: "PIP",  fallbackColor: "#aa8866", labelColor: "#aa8866", startForm: { isMouseicorn: true } },
};
const CHARACTER_ORDER = ["unicorn", "kittycorn", "pixicorn", "mousicorn"];
```

### Sprite Rendering (No New Logic Needed!)
The existing `SpriteDrawer.drawPlayer()` already had form-aware branches for kittycorn/pixicorn/mousicorn sprites. Each player's permanent form flags naturally select the correct sprite sheet. The old P2-specific `isP2` parameter and palette swap code were simply removed.

### Player Class Extensions
- **`setCharacter(charId)`** — Sets `characterId`, `characterDef`, and base form flags
- **`hurt()`** — Strips only *acquired* (non-base) forms; always restores base form flags
- **`die()`** — Restores base form from `characterDef.startForm`
- **`reset()`** — Restores base form on respawn

### Input System
4 `InputManager` instances. P1 and P2 have keyboard bindings; P3/P4 return empty keyboard keys (gamepad-only). Backward-compat aliases `g.input` / `g.input2` preserved.

### Camera
New `Camera.followGroup(players, dt)` computes centroid of alive players (falls back to all players if none alive). Same dead-zone + lerp + clamp + shake logic as the original `followDuo`.

### Game Constructor
```javascript
this.players = [player1, player2, player3, player4];
this.activePlayers = [true, true, true, true];  // controlled by player select
this.playerNames = ["STAR", "LUNA", "FAYE", "PIP"];
this.playerCount = 4;  // set by player select screen
```

### Death & Respawn
- All players share `sharedLives` pool
- Dead player gets `respawnTimer = 2.0`, respawns near **nearest alive player**
- All active players dead with no respawn timers → game over (STATE_DYING)

### HUD Layout
```
[P1 ★n] [P2 ★n]  |  ♥xN  LEVEL NAME  TIMER  |  [P3 ★n] [P4 ★n]
```
Each player segment colored by their `characterDef.labelColor`. Respawning players show indicator.

### Game Flow Screens
- **Player Select**: 4 options ("1 PLAYER" through "4 PLAYERS")
- **Name Entry**: Cycles through all active player slots
- **Title Screen**: Shows all 4 characters animated with "4 PLAYER CO-OP ARCADE" subtitle
- **Story**: Supports `{P1}` through `{P4}` name templates

---

## What Was Changed (from original coop_unicorn)

### Removed
- `SPRITES_P2` object and `P2_PLAYER_SPRITE_KEYS` array
- `generateP2Sprites()` function and `_p2SpritesGenerated` flag
- `isP2` parameter from `drawPlayer()` and `drawPlayerFallback()`
- All hardcoded 2-player iteration patterns

### Added
- `CHARACTER_DEFS` and `CHARACTER_ORDER` config
- `Player.setCharacter()`, `Player.characterId`, `Player.characterDef`
- `Camera.followGroup()` method
- 4 `InputManager` instances (P3/P4 gamepad-only)
- `player3`, `player4`, `players[]` array, `activePlayers[]` array
- N-player game loop iteration throughout `updatePlaying`, `renderGame`, HUD, etc.
- Base form preservation in `hurt()`, `die()`, `reset()`
- 4-option player select screen
- N-player name entry cycling
- 4-character title screen showcase
- Story text `{P3}` and `{P4}` template support

### Modified
- Game constructor (4 players, 4 inputs, particle pool 800)
- `updatePlaying()` — iterates `activePlayers`, N-player death/respawn logic
- `renderGame()` — draws all active players with colored name labels
- `HUD.draw()` — new 4-player layout
- `updatePlayerSelect()` — 4 menu options
- `updateNameEntry()` / `_submitName()` — cycles N player slots
- `updateTitle()` — N-player input check
- `updatePaused()`, `updateLevelIntro()`, `updateComplete()` — N-player input
- `onStateEnter(STATE_DYING)` — N-player respawn check
- Zone-aware underwater transitions — preserve base forms

---

## File Structure
```
unicorn_arcade/
├── index.html          # Complete game (~12,400 lines, ~458KB)
├── .gitignore
├── CATCHUP.md          # This file
├── sprites/            # 53 PNG sprite sheets
│   ├── run.png, idle.png, jump.png, fall.png    # Unicorn sprites
│   ├── kittycornRun.png, kittycornStand.png     # Kittycorn sprites
│   ├── pixicorn.png                              # Pixicorn (single sheet)
│   ├── mousicorn.png                             # Mousicorn (single sheet)
│   ├── wings*.png, mermaid*.png                  # Form variant sprites
│   └── ... (enemies, items, effects, etc.)
└── sounds/             # 54 WAV/MP3 audio files
    ├── jump*.wav, coin*.wav, hurt*.wav           # Player SFX
    ├── bgm*.mp3                                   # Background music
    └── ... (various game sound effects)
```

---

## How to Run
1. Open `index.html` in any modern browser
2. Select number of players (1-4) on the player select screen
3. Enter names for each active player
4. Play! P1/P2 use keyboard, P3/P4 need gamepads

## Verification Checklist
- [ ] All 4 characters render with distinct sprites
- [ ] P1 (Arrows) and P2 (WASD) respond to keyboard
- [ ] P3/P4 respond to gamepads
- [ ] Camera follows centroid of alive players
- [ ] HUD shows all active player stats
- [ ] Death/respawn: dead player respawns near alive player after 2s
- [ ] All players dead = game over
- [ ] Player select correctly activates 1-4 players
- [ ] Name entry cycles through all active slots
