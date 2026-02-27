# POP FIELD -- Game Design Document

## Overview

**POP FIELD** is a single-file HTML5 canvas chain-reaction popping game with cosmic horror aesthetics. The player taps orbs to trigger chain reactions, earns taps and score, levels up to unlock upgrades, and watches the field descend into eldritch madness as score climbs.

**Platform:** Browser (desktop + mobile touch)
**Engine:** Vanilla HTML5 Canvas, zero dependencies
**Font:** Orbitron (Google Fonts)
**File:** `index.html` (~10400 lines)

---

## Core Loop

```
TAP ORB --> CHAIN REACTION --> EARN SCORE + XP --> LEVEL UP --> CHOOSE UPGRADE
                |                                                    |
                v                                                    v
        EARN BONUS TAPS                                    STRONGER CHAINS
                |                                                    |
                +---------> REPEAT UNTIL TAPS RUN OUT <--------------+
```

1. **Tap** an orb to pop it. Each pop blasts nearby orbs in a radius.
2. **Chain reactions** propagate -- popped orbs blast others, creating cascading combos.
3. **Score** increases per pop. XP fills a bar; when full, player levels up and picks an upgrade.
4. **Bonus taps** are earned from good chains, keeping the player alive.
5. **Game Over** when taps reach zero (timer drains taps if idle).

---

## Controls

| Input | Action |
|-------|--------|
| Tap / Click | Pop nearest orb, start chain reaction |
| Long Press (350ms) | Place a mine (if Mine Layer upgrade active) |
| Mouse Move / Touch Drag | Attract nearby orbs toward cursor |
| B key | Toggle ABYSS biome |

---

## Game States

1. **Lore Splash** -- Random lore excerpt fades in (3s), lingers (10s), tap to skip
2. **Instructions** -- "How to Play" screen, stays until clicked
3. **Playing** -- Main gameplay
4. **Level Up** -- Upgrade selection panel (pauses gameplay)
5. **Game Over** -- Final score display, tap to restart

---

## Progression Systems

### Scoring
- Each popped orb adds 10 points to score
- Score drives madness (see Madness System below)
- Score drives zoom level (camera pulls back as score grows)

### XP and Leveling
- **XP per pop:** Based on orb generation and size
  - Gen-0 (directly popped): 1.0 base
  - Gen-1 (chain): 0.8 | Gen-2: 0.5 | Gen-3: 0.3 | Gen-4: 0.2 | Gen-5: 0.15
  - Size scaling: `sizeXpScale = max(0.15, (radius / 18) * 0.6)`
  - Normal orb (18px): 0.6 XP | Merged 2x: 1.2 XP | Merged 2.5x: 1.5 XP
- **Diminishing returns:** After 15 pops in a chain, XP per pop = `15 / chainCount`
- **XP curve:** `xpToNext = 12 * 1.7^(level-1)` -- Level 2: 12, Level 3: 20, Level 5: 59, Level 10: 455

### Tap Economy
- Start with **10 taps**
- Taps drain on a timer (12s base, +0.5s per level, max 16s) when idle
- **Bonus taps** from chains:
  - Small chain (3-5 pops): 60% chance +1 tap (level 1-2)
  - Mid chain (6-11 pops): +1 tap
  - Big chain (12+ pops): +2 taps
- **Chain Bounty upgrade** multiplies tap rewards up to 2.8x
- **Black hole collapse:** +1 tap (or +2 if mass >= 8)
- **Supermassive collapse:** +2 to +5 taps based on mass
- **Field clear:** +3 taps

### Zoom System
- Starts at **2.0x** (zoomed in, intimate)
- Gradually zooms out as score increases: `zoomTarget = max(0.5, 2.0 - score * 1.5/200000)`
- At full zoom (0.5x), the play area is 4x larger
- Blast radius, orb count, and spawning all compensate for zoom

---

## Upgrades (10 Total)

Each level-up offers 3 random upgrade choices. Each upgrade has a max level.

| Upgrade | Max | Effect |
|---------|-----|--------|
| **Nova Radius** | 8 | +6px blast radius per level |
| **Echo Burst** | 6 | 10% chance per level for a second blast at 1.3x range |
| **Gravity Well** | 6 | Pops pull nearby orbs inward (clustering for bigger chains) |
| **Volatile Orbs** | 5 | 8% of orbs per level glow volatile -- 3x blast radius when popped |
| **Aftershock** | 5 | Pops leave hot zones lasting 0.5s per level that auto-pop orbs |
| **Fission** | 4 | Each pop spawns up to 2 tiny orbs that chain outward |
| **Chain Bounty** | 6 | Bonus tap rewards multiplied by 1.3x per level |
| **Density** | 6 | +6 orbs on the field per level |
| **Mine Layer** | 3 | Long press places mines (3.5s fuse, 140% blast) |
| **Attractor** | 5 | Taps and mines pull orbs inward (gravity lure) |

---

## Biomes

### Main Biome (Level-Based Palettes)

| Phase | Levels | Colors | Sound |
|-------|--------|--------|-------|
| PHASE I | 1-3 | Violet + Hot Pink + Gold | Sine, 240Hz |
| PHASE II | 4-6 | Purple + Red + Yellow | Sine, 262Hz |
| PHASE III | 7-9 | Deep Violet + Pink + Gold | Sawtooth, 294Hz |
| PHASE IV | 10-12 | Ultra Violet + Crimson + Gold | Sawtooth, 330Hz |
| PHASE V | 13+ | Vivid Purple + Red + Pale Gold | Sine, 349Hz |

Each phase has unique background gradients, particle colors, and oscillator tones.

### ABYSS (Special Biome)
- Deep ocean trench aesthetic: teal, green, bioluminescent
- Volumetric light shafts, marine snow, depth fog
- Pressure waves instead of whirlwinds
- Progressive tentacle mutation on orbs (tied to madness)
- Toggle with B key or Abyss button

### LT — Lightning Test (Special Biome)
- Debug lighting engine (`biomeOverride === 1`, formerly "TESTLEVEL")
- Cursor-centered radial light source with shadow casting from orbs
- Multi-pass feathered shadows (4-layer penumbra→core system)
- Orb saturation shifts by light proximity (+15% near, -30% far)
- Self-glow on orbs, black hole variants, explosions, and chain links
- Orb body shadow (directional gradient, lit side vs dark side)
- Toggle with T key or TESTLEVEL button

---

## World Transparency (WT) System

World Transparency (formerly "Madness") is the central meta-progression that transforms the entire game experience from a colorful puzzler into cosmic horror. As the membrane between worlds thins, the other side bleeds through.

**WT Amount (0% to 100%):**
- **Score contribution:** 0% below 10k score, ramps 0-50% from 10k-200k, 50-100% from 200k-400k
- **Horror Boost:** Permanent addition from black hole exposure (capped at 35-50%)
- **Dev Override:** Madness toggle button forces 100%

### Effects by WT Level

| Range | Effects |
|-------|---------|
| 0-10% | Clean, colorful, normal gameplay |
| 10%+ | **Mutation Phase 1:** Subtle dark void seed appears in orb centers (smooth fade-in) |
| 30%+ | **Rift Walls** (space canyon rifts) begin spawning, blocking blast propagation |
| 35%+ | **Void Stars** — 2000 twinkling stars from the other side start bleeding through |
| 40%+ | HUD labels begin corrupting into Enochian runes |
| 50%+ | Beat pulse system activates (100 BPM), **"THE MEMBRANE THINS"** banner, shadow balls appear |
| 55%+ | Gravity bridges form between same-color orbs |
| 66%+ | Orb size variation expands dramatically (tiny specks to huge balls) |
| 75%+ | **Supermassive Black Holes** begin spawning, **"REALITY UNRAVELS"** banner, center gravity pull |
| 80%+ | Orb count reaches 2.5x normal, whirlwind/pressure waves triple strength |
| 85%+ | Extra eyes sprout on orbs (2-6 additional) |
| 90%+ | **"THE OTHER SIDE BLEEDS THROUGH"** banner, full horror transformation |
| 100% | Complete Enochian UI corruption, maximum physics chaos, Watcher fully visible |

### Visual Corruption
- **Color desaturation:** `horrorShift()` gently drains saturation (Phase 1: 0-50%), then tints toward flesh/bile tones. Colors retain vibrancy through mid-game.
- **Text corruption:** Deterministic replacement of UI text with Enochian/Runic glyphs
- **Beat pulse:** Random 15-40% of orbs pulse on a 100 BPM rhythm at 50%+ WT
- **Void Stars:** 2000 background stars twinkling through from the other side (35%+ WT)
- **WT Milestone Banners:** Dramatic text at 50%, 75%, 90% thresholds

---

## Key Entities

### Orbs (Circles)
- Base radius: 18px (varies with madness-driven size scaling)
- Drift gently with random angle changes
- Attracted toward cursor
- Can merge when overlapping (bigger = more XP, bigger blast)
- Color based on horizontal screen position (cluster gradient)

### Archons (Super Cells)
- Random orbs "ascend" into Archon form at level 5+ (9% chance on cooldown)
- 1.6x radius, 5-8 spikes, tendrils
- When popped: 1.5x blast radius + gravity well pull (200 strength, 0.4s)
- Deep boom + crunch sound effect

### Void Cracks (Armored Orbs)
- Rare (3% chance, level 3+)
- 2 HP: First hit cracks (pulls orbs in), second hit shatters (pushes orbs out)
- Purple energy visual, unique crack sound

### Black Holes
- Spawn every 15-35 seconds
- Gravity radius: 420px, strength: 1100
- Orbital mechanics: radial pull + tangential spin
- Swallow orbs that reach event horizon (grows with each orb eaten)
- Collapse at 5 mass: free chain pop + tap reward
- Tappable for early collapse
- Star dust particles spiral inside the void

### Supermassive Black Holes (Madness 75%+)
- Spawn every 20-35 seconds at madness > 75%
- Gravity covers 55-70% of play area diagonal
- Base strength: 3500 (vs 1100 for regular)
- 1/sqrt(d) falloff for wider effective range
- Collapse at 8 mass: massive dual explosion, up to +5 taps, boss damage
- Persistent screen rumble, chromatic aberration
- Dramatic red/violet visual with distortion rings

### Boss Entities
- Spawn after 30 seconds, then periodically
- Must be damaged by chain blast overlap
- Rewards taps and XP on death
- Death sound: dramatic bass drop + noise burst

### The Watcher
- Cosmic entity image looming behind the field
- Opacity tied to madness (up to 17% main biome, 12% ABYSS)
- Breathing animation, eye glow reacts to chains
- Reach extends when black holes are present
- Two images: main biome Watcher vs ABYSS Watcher

### Mines (Bombs)
- Placed via long press (350ms)
- 3.5 second fuse, 140% blast radius
- Visual: pulsing warning circle with fuse timer
- Charges per tap based on Mine Layer level

---

## Physics Systems

### Gravity
- **Cursor attraction:** Orbs gently drift toward mouse/touch position. Reach doubles from 10% to 100% madness.
- **Inter-orb gravity:** Bigger orbs pull smaller ones (strength 15, range = radius * 4.5)
- **Gravity wells:** Created by Archon pops -- temporary pull point
- **Black hole gravity:** Orbital mechanics with radial + tangential force
- **Madness physics scale:** 1.0 at 0%, ramps to 1.7 at 100% (aggressive from 50%+)

### Whirlwind (Main Biome)
- Periodic tangential swirl toward field center (every 8-14s)
- Duration: 1.8-2.3 seconds
- Strength triples at full madness
- Chaotic jitter added above 30% madness

### Abyssal Pressure Waves (ABYSS)
- Replaces whirlwind in ABYSS biome
- Periodic crushing force pushes orbs toward center
- Strength triples at full madness
- Accompanied by deep woosh sound

### Spawn System
- 40% of orbs spawn inside the play area (fade-in)
- 60% spawn from edges
- **Directional bias:** 50% chance orbs spawn from opposite side of recent pops (after 5+ tracked pops)

---

## Chain Reaction Mechanics

### Blast Radius
- Base: 60px at level 1
- Growth: `60 + 75 * (level/16)^0.6` -- caps at 135px
- Modified by: Nova Radius upgrade (+6px per level), zoom factor, madness physics scale
- Volatile orbs: 3x blast radius
- Archons: 1.5x blast radius
- **Size scaling:** Big merged orbs blast 45% wider, tiny shrapnel blasts 18% smaller

### Fracturing
- Chain pops spawn smaller fragment orbs based on level
- No fracturing at levels 1-2
- Gradually adds generations: gen-0 spawns gen-1, gen-1 spawns gen-2, etc.
- Full fracture tree (5 generations) at level 16+

### Cascade Bursts
- During long chains, smaller generation orbs spawn bonus micro-fragments
- Gen-1 at chain 18+, gen-2 at chain 28+, gen-3 at chain 35+, gen-4 at chain 45+

### Delayed Blast Combos
- **Minor Blast** (20+ pops): Small bonus explosion at chain origin
- **Major Blast** (35+ pops): Medium explosion with screen shake
- **Mega Blast** (55+ pops): Massive explosion with dramatic sound

### Field Clear
- Popping every orb on screen triggers:
  - Mega white explosion covering 60% of screen
  - 15 screen shake, full white glow, chromatic aberration
  - Boss takes 5 damage
  - Biome advances
  - +3 bonus taps
  - "FIELD CLEARED!" floating text

---

## Visual Effects

- **Screen shake:** Triggered by big chains, collapses, bosses
- **Chromatic aberration:** Color channel offset on big events
- **White glow:** Flash overlay on massive combos
- **Paint splats:** Persistent colored marks on the field from pops
- **Explosions:** Expanding ring animation on every pop
- **Particle system:** Sparks and debris from pops
- **Scanline overlay:** Subtle CRT-style screen lines
- **Field stars:** 200 background stars that drift and get pulled by black holes
- **Void stars:** 2000 twinkling stars bleeding through at mid-high WT, BH-reactive
- **Rift canyon rifts:** 6-layer space canyon visuals with gravitational pull
- **Chain links:** Visible tension lines connecting queued pops

---

## Audio System

All audio is procedurally generated using Web Audio API oscillators:

| Sound | Description |
|-------|-------------|
| Pop | Rising pitch based on chain index (biome oscillator type) |
| Bonus Tap | 880Hz sine rising to 1400Hz |
| Phantom | Low 80Hz sine dropping to 40Hz |
| Archon Pop | Deep 60Hz sine sweep + square wave crunch |
| Void Crack | Descending 400Hz sine + sub rumble |
| Abyss Woosh | Sub-bass 35Hz sweep + triangle wave whoosh |
| Delayed Blast Buildup | Rising sawtooth sweep + sub rumble |
| Delayed Blast Explosion | Bass drop + white noise burst + high ring |
| Boss Death | Multi-layer dramatic explosion |

---

## Dev Tools

| Button | Position | Function |
|--------|----------|----------|
| **Abyss** | Top-right | Toggle ABYSS biome |
| **Madness** | Below Abyss | Force 100% madness |
| **Auto LvL** | Below Madness | Auto-pick random upgrade on level up |
| **Infinite Taps** | Below Auto LvL | Keep taps at 99, no timer drain |

---

## Technical Architecture

- **Single file:** All HTML, CSS, and JS in one `index.html`
- **Game state:** Global `G` object with all mutable state
- **Game loop:** `requestAnimationFrame` with delta time, time scale support
- **Rendering:** 17-layer composited canvas render pipeline
- **DPR support:** Canvas scaled by device pixel ratio (max 2x)
- **Coordinate systems:** Screen space (canvas pixels) vs World space (game coordinates, affected by zoom)
- **Zoom transform:** `ctx.scale(zoomLevel, zoomLevel)` centered on screen center
