# Rainbow Unicorn Quest

**4-Player Co-Op Arcade Platformer**

A TMNT-style arcade beat-em-up platformer featuring four playable unicorn characters, each with unique abilities. Stomp enemies, collect stars, defeat bosses, and save the rainbow kingdom!

## Play Now

[**Play Rainbow Unicorn Quest**](https://glen-collab.github.io/unicorn_arcade/)

## Characters

| Player | Character | Name | Special |
|--------|-----------|------|---------|
| P1 | Unicorn | STAR | Balanced |
| P2 | Kittycorn | LUNA | Fast |
| P3 | Piggicorn | OINK | Powerful |
| P4 | Mousicorn | PIP | Agile |

## How to Play Multiplayer

1. **Title Screen** — Press Enter or Start to begin
2. **Player Select** — Use Up/Down to choose 1-4 players, press Enter/Start to confirm
3. **Character Select** — Each player picks a character with Left/Right, confirm with Enter/A button
   - P1 picks first, then P2, P3, P4 in order
   - P1's keyboard controls P3/P4 character select if they don't have a gamepad
   - Press Escape to go back to the previous player's pick
4. **Name Entry** — Type a name on keyboard or use D-pad Up/Down to scroll letters, A to add, B to delete, Start/Enter to confirm
   - P1's keyboard works as fallback for P3/P4 name entry too
   - Press Enter with the default name to skip quickly
5. **Story** — Press Enter/Start/A to advance through story scenes
6. **Gameplay** — Co-op! Shared lives pool. If you die, you respawn near the nearest alive player

## Controls

### Keyboard

| Action | P1 | P2 |
|--------|----|----|
| Move | Arrow Keys | WASD |
| Jump | Space | Q |
| Shoot | Left Shift | F |
| Poop | P or Right Shift | X |
| Confirm / Start | Enter | E |

P3 and P4 have no keyboard bindings — they require gamepads.
P1's keyboard is used as a fallback for P3/P4 during menus (character select, name entry).

### Gamepad (all players)

Each player maps to a gamepad index: P1 = gamepad 0, P2 = gamepad 1, P3 = gamepad 2, P4 = gamepad 3.

| Action | Button |
|--------|--------|
| Move | Left Stick or D-pad |
| Jump | A button |
| Shoot | B button |
| Poop | Down + B |
| Confirm / Start | Start (button 9) |
| Code Entry | Select (button 8) |

## Features

- 4-player local co-op with shared lives
- 8 themed worlds (meadow, cave, forest, clouds, lakeside, underwater, treetop, pyramid)
- Boss fights at the end of each world
- TMNT arcade-style visuals: scanlines, bordered HUD, score combos, entity shadows
- Procedural parallax backgrounds and environmental props
- Story mode with cutscenes between worlds
- Save code system for progress

## Running Locally

Just open `index.html` in a modern browser. No build step or server required.

## Tech

Single-file HTML5 Canvas game (~13,000 lines). All rendering is procedural — no external asset dependencies beyond optional sprite sheets and sound files in `sprites/` and `sounds/`.
