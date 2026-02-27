# POP FIELD — Code Map

Single file: `index.html` — 4354 lines total (4284 lines JavaScript)

This document maps the codebase structure for quick navigation in future development sessions.

---

## File Structure

```
MegaPhaser/
  index.html              — The entire game (HTML + CSS + JS, no dependencies)
  Images/
    BG_test1.jpeg         — The Watcher background image
    BG_Galax.jpg          — Galaxy background fallback image
    GalaxyBG_test.mp4     — Galaxy background video (playback rate scales with madness)
  DESIGN.md               — Game design document (mechanics, visuals, systems)
  LORE.md                 — In-universe lore (Substrate, Cells, Madness, Watcher)
  CHANGELOG.md            — Session-by-session development log
  CODE_MAP.md             — This file
  GAME_MECHANICS.md       — Detailed mechanics reference
  game-name-biomes.md     — Naming concepts & biome design ideas (reference)
  index.backup.html       — Session 1 backup
  index.backup_*.html     — Timestamped backups
```

---

## Code Sections (index.html)

| Section | Lines | Content |
|---------|-------|---------|
| **HTML/CSS** | 1-68 | Page structure, HUD, title/gameover screens, upgrade panel, biome button |
| **S1: Constants** | 70-103 | All game constants (BASE_TAPS, MAX_CIRCLES, BH_*, etc.) |
| **S2: Palettes** | 104-162 | PALETTES[5] + SPECIAL_BIOMES[1] (ABYSS). Colors, bg gradients, audio |
| **S3: Upgrades** | 164-178 | UPGRADE_DEFS object (10 upgrades with names, maxLvl, desc functions) |
| **S4: Game State** | 184-300 | `const G = {...}` main state object + `resetGame()` |
| **S5: Audio** | 302-470 | Web Audio API: `playPopSound()`, `playBonusTapSound()`, SC/bomb sounds |
| **S6: Utilities** | 474-592 | `lerp()`, `lerpColor()`, `getMadnessAmount()`, `getHorrorAmount()`, `horrorShift()`, `getClusterColor()` (returns `{color, colorIdx}`), `getCurrentPalette()`, `getCurrentBiome()`, `biomeColorShift()` |
| **S7: Circle Management** | 593-898 | `createCircle()`, `spawnInitialCircles()`, `spawnCircleFromEdge()`, `updateCircles(dt)` — movement physics, SC mutation lifecycle |
| **S7b: Color Synthesis** | 899-1041 | `colorDistance()`, `updateColorSynthesis(dt)` — spatial grid proximity, blending |
| **S8: Particle System** | 1042-1314 | Pool (800), `spawnParticle()`, `spawnPopParticles()`, `updateParticles()`, `updateAmbientParticles()` |
| **S9: Explosions** | 1315-1339 | `spawnExplosion()`, `updateExplosions()` |
| **S9b: Bombs** | 1340-1468 | Mine placement, fuse countdown, detonation |
| **S9b2: Rift Walls** | ~2529-2700 | `spawnRiftWall()`, `shatterRiftWall()`, `updateRiftWalls()`, `damageRiftWall()`, `renderRiftWalls()` — Phase 4 energy barriers |
| **S9b3: Gravity Bridges** | ~2736-2920 | `updateBridges()`, `renderBridges()` — Phase 6 same-colorIdx energy connections |
| **S9b4: Shadow Balls** | ~2934-3100 | `spawnShadowBall()`, `updateShadowBalls()`, `renderShadowBalls()` — non-interactive phantom orbs |
| **S10: Chain Reaction** | 1469-2001 | `startChain()`, `updateChainReaction()`, `popCircle()`, fracturing system, `endChain()`, delayed blast, shrapnel, rift wall blocking, bridge propagation |
| **Black Hole** | 1803-2001 | Constants, `createBlackHoleObj()`, `updateBlackHole()` (G.blackHoles[] array, max 4), `triggerBlackHoleCollapse()`, `tryTapBlackHole()` |
| **S11: Upgrades Logic** | 2378-2452 | `checkLevelUp()`, `showUpgradePanel()`, `applyUpgrade()` |
| **S12: Rendering** | 2453-3770 | Main `render()` pipeline (21 layers), all render functions |
| **S13: Post-Processing** | 3771-3991 | Vignette, chromatic aberration, nebula clouds, ABYSS background |
| **S14: Input** | 3992-4137 | `handleTap()`, touch/mouse/keyboard listeners, biome toggle |
| **S15: UI / HUD** | 4138-4237 | Enochian corruption, `corruptText()`, `updateHUD()`, `spawnFloatingText()` |
| **S16: Game Loop** | 4239-4319 | `update(dt)` main loop, `gameLoop()` RAF |
| **S17: Init / Boot** | 4320-4354 | Canvas setup, DPR, Watcher image loading, event binding, `gameLoop()` start |

---

## Key Functions

### Core Loop
- `gameLoop()` → `update(dt)` → all update functions → `render()`
- `update(dt)` calls (in order): `updateAbyssalPressure`, `updateChainReaction`, `updateCircles`, `updateColorSynthesis`, `updateExplosions`, `updateParticles`, `updateAmbientParticles`, `updateFloatingTexts`, `updateAftershocks`, `updateGravityWells`, `updateBlackHole`, `updateWatcher`, `updateBombs`, `updateRefillQueue`, `updateRiftWalls`, `updateBridges`, `updateShadowBalls`

### Rendering Pipeline (back→front)
`renderBackground` → `renderGalaxyBackground` → `renderNebulaClouds` → `renderAbyssBackground` → `renderWatcher` → `renderPaintSplats` → `renderOrbBloom` → `renderAftershocks` → `renderRiftWalls` → `renderBridges` → `renderGravityWells` → `renderBlackHole` → `renderShadowBalls` → particles(bg) → `renderChainLinks` → `renderBombs` → `renderCircles` → `renderExplosions` → `renderLightRays` → `renderChainEnergyField` → particles(fg) → `renderFloatingTexts` → whiteGlow → delayedBlast → vignette → chromaticAb → tapTimer

### Madness System
- `getMadnessAmount()` — single source of truth, returns 0.0–1.0
- `getHorrorAmount()` — alias for `getMadnessAmount()`
- Score: 0% below 100k, ramps to 50% at 500k, 100% at ~900k
- `G.horrorBoost`: cumulative from black holes, capped 0.6

### Color System
- `getClusterColor(x, y)` — returns `{ color, colorIdx }` where `colorIdx` is the palette segment index (0, 1, or 2), immune to desaturation. Used for bridge matching
- `horrorShift(hex, amount)` — 4-phase desaturation: rapid desat (0-35%), flesh tint (20-60%), deep drain (50-85%), total ash convergence (85-100%)
- `biomeColorShift(hex, madness)` — unified color shift dispatcher: `horrorShift` in default biomes, `abyssDesaturate` in ABYSS

---

## Game State Object (G)

### Core State
```
state, taps, score, level, xp, xpToNext, bestChain, time, dt, rawDt, timeScale
```

### Chain State
```
chainActive, chainCount, chainXpValue, chainQueue[], chainTimer, chainOriginX/Y
```

### Visual Effects
```
shakeAmount, whiteGlow, chromaticAb
```

### Upgrades
```
upgrades: { novaRadius, echoBurst, gravityWell, volatileOrbs, aftershock, fission, chainFuel, density, bombCharges, attractorField }
```

### Cursor
```
cursorX, cursorY, cursorVx, cursorVy, prevCursorX, prevCursorY
```

### Collections
```
circles[], particles[], explosions[], floatingTexts[], paintSplats[], aftershocks[], chainLinks[], gravityWells[], bombs[]
riftWalls[], bridges[], shadowBalls[]
```

### Biome
```
biomeIndex, prevBiomeIndex, biomeTransition, biomeOverride (-1=level-based, 0+=SPECIAL_BIOMES index), prevPalette
```

### Entities
```
blackHoles[] (array, max 4 at 100% madness), blackHole (compat alias for blackHoles[0])
blackHoleTimer, superBlackHole (null or object)
watcher: { opacity, eyeGlow, reachAmount, breathPhase }
riftWallTimer
```

### Horror
```
horrorBoost (0–0.6, cumulative from black holes)
```

### Other
```
canvas, ctx, W, H, dpr, audioCtx, audioUnlocked, scanlinePattern
pendingLevelUp, lastTime, nextCircleId, refillQueue[], refillTimer
delayedBlast, tapTimer, tapTimerMax, bombsPlacedThisRound
```

---

## Circle Object Properties

```javascript
{
  id, x, y, radius, baseRadius,
  color, colorEdge,          // persistent hex color
  colorIdx,                  // palette segment index (immune to desaturation, used for bridge matching)
  volatile, superCell,       // type flags
  spikeCount, scFade, scTimer, scLifespan, scCooldown,  // SC lifecycle
  scTethers: [],             // Phase 5: Archon tethers to nearby orbs [{orbId, pulsePhase}]
  tethered,                  // true when tethered to a boss or Archon (chain-immune)
  popped, queued, alive,     // chain state
  pulsePhase,                // visual pulse
  vx, vy,                    // velocity
  driftAngle, driftTimer,    // brownian motion
  generation,                // 0-3 fracturing tier
  shrapnel, shrapnelImmune,  // shrapnel flags
  spawnAnim, fadeIn,         // spawn animations
  trail: [],                 // position history for motion blur
  synthCooldown              // color synthesis cooldown
}
```

---

## Special Rendering Modes

### Default Biome Orbs
- Normal orbs: solid color circle + highlight gradient
- At madness >10%: progressive eyeball transformation (dark hole → sclera → veins → iris → full eye)
- 4 vector eye variants per orb (`eyeVariant = c.id % 4`): V0 Pale, V1 Striated, V2 Slit, V3 Cracked — desaturated grey tones, no outlines
- At madness >85%: extra eye orbs (2 per 7.5% above 85%, up to 6 at 100%) using 4-variant system
- SC: amorphous eye-cluster entity with pseudopods and blinking eyes
- SC at madness >60%: grotesque mutation — torn membrane wings, rib cage, extra arms/joints, more eyes, all desaturated (`grotesque` factor 0→1 from 60-100%)

### ABYSS Biome Orbs
- Abstract twisted polygons with animated vertex distortion
- Dark pulsing void center, bioluminescent edge glow, geometric stress lines
- SC: tentacle horror with writhing appendages and slit-pupil eyes
- Chain links: tentacle-style with suction cups and traveling bioluminescent pulse

---

## Performance Notes

- MAX_CIRCLES: 300 (hard cap, prevents fracturing explosion)
- MAX_PARTICLES: 800 (pool-based, reused)
- Shadow balls: max 400 (non-interactive, simple render)
- Gravity bridges: max 60, proximity scan every 0.3s
- Rift walls: max 3 on field
- Black holes: max 4 simultaneously at 100% madness
- Color synthesis: spatial hash grid (50px cells), max 5 events/frame
- Motion blur: max 5 trail positions per orb, only recorded when speed > 15
- Ambient motes: 15/sec base, up to 40/sec during chains
- Galaxy BG: video element with fallback to static image, playback rate 0.03x-0.25x
