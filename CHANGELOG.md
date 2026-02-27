# Rainbow Unicorn Quest - Changelog

## TMNT Arcade Visual Overhaul (Feb 2026)

Transformed the game from basic retro visuals into a rich TMNT arcade cabinet aesthetic while preserving all existing gameplay mechanics.

---

### Score System & Combo Mechanic

Added TMNT-style score tracking with floating damage numbers and a combo multiplier system.

**Score events:**
| Action | Points |
|--------|--------|
| Kill fly/fish/jelly/narwhal/bird | +100 |
| Kill gnome | +200 |
| Boss hit | +500 |
| Boss defeat | +1000 |
| Star collect | +50 |

**Combo system:**
- Consecutive kills within a 2-second window stack a combo multiplier (2x, 3x, 4x...)
- "Nx COMBO!" text displays mid-screen in gold, fading out over 1.5s
- Score is multiplied by combo count
- Score resets on new game, persists across levels

**Files changed:** `Game` constructor, `addScore()`, `addComboHit()`, `updateCombo()`, `updatePlaying()`, all enemy kill handlers, `boss.hit()`, star collection

---

### Entity Shadows

Elliptical ground shadows under all game entities for visual grounding.

| Entity | Shadow Size | Opacity |
|--------|-------------|---------|
| Players | 10*scale+4 x 3*scale+1 | 0.25 (already existed) |
| Flies | 8 x 3 | 0.2 |
| Gnomes | 10 x 4 | 0.25 |
| Boss | w/2+6 x 6 | 0.3 |

**Files changed:** `SpriteDrawer.drawFly()`, `SpriteDrawer.drawGnome()`, `SpriteDrawer.drawBoss()`

---

### TMNT-Style HUD

Replaced the simple transparent top bar with bordered arcade panels.

**Layout:**
```
[P1: STAR ██████ ★12]  [P2: LUNA ██████ ★5]  ┌─STAGE 1-1─┐  [P3: FAYE ██████ ★8]  [P4: PIP ██████ ★3]
                                                │ ♥♥♥ x5    │
                                                │  0012350   │
                                                └────────────┘
```

**Player panels (per player):**
- Dark background with character-colored border (1.5px)
- Player name in character color (bold 7px)
- Health bar: green gradient when alive, flashing red when respawning
- Star count in gold

**Center panel:**
- Stage indicator ("STAGE X-Y")
- Hearts + shared lives count
- Zero-padded 7-digit score in gold
- Timer with warning flash

**Combo display:** Large "Nx COMBO!" text mid-screen when combo > 1

**Files changed:** Complete rewrite of `HUD` class, `renderGame()` HUD call updated to pass game reference

---

### CRT/Arcade Overlay

Subtle arcade cabinet aesthetic applied as final render pass on ALL game states.

- **Scanlines:** 1px black lines every 3 pixels at 4% opacity
- **Vignette:** Radial gradient darkening corners to 25% at edges

**Files changed:** `render()` method, after all state rendering

---

### Enhanced Tile Rendering

Richer ground and platform tiles with TMNT-level detail.

**Grass tiles (TILE_GRASS):**
- Gradient dirt body (lighter top, darker bottom)
- 2px bright highlight line at surface with secondary glow line
- 4 varied-height grass blades per tile (deterministic seed)
- Occasional flower accent dots (4 colors, seed-based)
- Drop shadow when tile has air below

**Dirt tiles (TILE_DIRT):**
- Vertical gradient fill
- Horizontal line accents (TMNT ground lines)
- More varied specks + subtle light specks
- Crack patterns on ~10% of tiles (seed-based)
- Drop shadow when tile has air below

**Files changed:** `TileRenderer.drawTiles()`

---

### Environmental Props System

Decorative background objects placed procedurally per theme.

**Props per theme:**
| Theme | Props |
|-------|-------|
| Meadow | Mushroom (red), Rock, Flowers, Log, Sign |
| Cave | Crystal (blue glow), Rock, Stalagmite |
| Forest | Stump, Berry bush, Mushroom (gold) |
| Clouds | Rock, Flowers |
| Lakeside | Rock, Flowers, Log |
| Underwater | Coral, Rock |
| Treetop | Mushroom (green), Flowers |
| Pyramid | Rock |
| Pyramid Interior | Crystal (orange glow) |
| Sewer | Rock |

**Generation:** ~10 props per screen-width, placed on ground surface using `SeededRNG(seed + 777)`. Zone-aware for multi-zone levels.

**Rendering:** Drawn after tiles, before entities. All procedural geometric shapes (rectangles, ellipses, triangles). Flowers sway with `Math.sin(gt)`.

**Files changed:** New `THEME_PROPS` config, new `drawProp()` function, `Level` constructor (`this.props`), `Level.generate()`, `renderGame()`

---

### Hit Feedback Enhancement

More satisfying combat feedback.

- **White impact burst:** 5 white particles on every enemy stomp/kill (on top of colored burst)
- **Screen shake on kills:** Magnitude 3 for regular enemies
- **Screen shake on boss hits:** Magnitude 6 (hit) / 8 (defeat)
- **Score popups:** Gold "+N" floating text on all score events
- **Boss damage:** `boss.hit()` now accepts game reference for score tracking

**Files changed:** Stomp enemy handlers, `boss.hit()` signature updated, all boss.hit() call sites

---

### Enhanced Parallax Backgrounds

More visual detail in existing theme layers + TMNT-style depth.

**Meadow enhancements:**
- Far mountains: snow caps on tallest peaks, gradient fill
- Near mountains: tree silhouettes on ridgelines
- Hills: gradient fill, fence posts, fence rail, windmill silhouette
- Bushes: wildflower clusters (5 colors), butterfly silhouettes

**Cave enhancements:**
- Crystals: larger glow radius (2.5x), inner glow layer, 5th color added
- Dripping water drops at stalactite tips
- Moss patches on rocks (green accents)

**Forest enhancements:**
- Light ray beams through canopy (4 semi-transparent streaks)
- Hanging vine silhouettes (12 vines)

**Parallax speed overhaul (all themes):**
| Layer | Old Speed | New Speed | Effect |
|-------|-----------|-----------|--------|
| Sky | 0.02 | 0.02 | Nearly static |
| Far | 0.05 | 0.06 | Barely crawls |
| Mid | 0.12 | 0.18 | Slow drift |
| Near | 0.25 | 0.38 | Moderate scroll |
| Floor | 0.40 | 0.65 | Fast scroll |
| **Foreground (NEW)** | — | **0.85** | **Near camera speed** |

**New foreground layer (6th layer, all themes):**
- Meadow: tall grass blades + floating pollen motes
- Forest/Treetop: large leaf clusters + dangling vines
- Cave/Pyramid: hanging rock shards + floor boulders
- Fairy: floating sparkle motes
- Sewer: pipe section silhouettes

**Files changed:** All `ParallaxBackground.build()` speed values, all builder methods for meadow/cave/forest, new `buildForeground()` method

---

### Title Screen Polish

TMNT arcade cabinet title screen aesthetic.

**Additions:**
- "★ A R C A D E ★" header above title (purple, letterspaced)
- "━━ RAINBOW QUEST ━━" styled subtitle with line decorations
- Character showcase with colored glow auras (pulsing)
- Character names (STAR/LUNA/FAYE/PIP) instead of P1/P2/P3/P4
- SPD/PWR stat bars per character (TMNT select screen style)
- "INSERT COIN" pulsing gold text replacing "PRESS ENTER"
- Copyright line at bottom
- Darker background gradient

**Player select enhancements:**
- "★ SELECT YOUR HEROES ★" header
- Bordered card panels with glow effect on selected item
- Dark purple highlight panel with gold border on selection

**Files changed:** `renderTitle()`, `renderPlayerSelect()`

---

### Technical Notes

- All changes in single file: `index.html` (~13,200 lines)
- No new assets — all visual changes are procedural/canvas-drawn
- Score resets on fresh game via `startLevel(freshGame=true)`
- `boss.hit()` signature changed to `hit(particles, audio, game)` — backward compatible (game param optional)
- Foreground parallax layer uses `clearRect` so it's transparent — only decorative elements show
- All prop generation uses deterministic `SeededRNG` for reproducibility

---

## Initial Release: 4-Player Co-Op Expansion

Original expansion from 2-player to 4-player co-op. See `CATCHUP.md` for full architecture documentation.
