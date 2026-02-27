# POP FIELD -- Game Mechanics Reference

## Table of Contents
1. [Core Constants](#core-constants)
2. [Orb Lifecycle](#orb-lifecycle)
3. [Chain Reaction System](#chain-reaction-system)
4. [Blast Radius Scaling](#blast-radius-scaling)
5. [Fracturing and Generations](#fracturing-and-generations)
6. [Upgrade Effects (Detailed)](#upgrade-effects-detailed)
7. [Black Holes](#black-holes)
8. [Supermassive Black Holes](#supermassive-black-holes)
9. [Archons (Super Cells)](#archons-super-cells)
10. [Void Cracks (Armored Orbs)](#void-cracks-armored-orbs)
11. [Bosses](#bosses)
12. [Mines (Bombs)](#mines-bombs)
13. [Gravity Systems](#gravity-systems)
14. [Madness Physics Scaling](#madness-physics-scaling)
15. [Orb Count Scaling](#orb-count-scaling)
16. [Size Variation at High Madness](#size-variation-at-high-madness)
17. [Tap Timer System](#tap-timer-system)
18. [Delayed Blast Combos](#delayed-blast-combos)
19. [Field Clear Mechanic](#field-clear-mechanic)
20. [Directional Respawn Bias](#directional-respawn-bias)
21. [Orb Merging](#orb-merging)
22. [Color Synthesis](#color-synthesis)
23. [Beat Pulse System](#beat-pulse-system)
24. [Biome Transitions](#biome-transitions)
25. [ABYSS-Specific Mechanics](#abyss-specific-mechanics)
26. [The Watcher](#the-watcher)
27. [Zoom System](#zoom-system)
28. [Text Corruption](#text-corruption)
29. [Rift Walls (Phase 4)](#rift-walls-phase-4)
30. [Gravity Bridges (Phase 6)](#gravity-bridges-phase-6)
31. [Shadow Balls](#shadow-balls)
32. [Leviathan Bloat/Burst (Phase 3)](#leviathan-bloatburst-phase-3)
33. [Archon Tethers (Phase 5)](#archon-tethers-phase-5)
34. [Boss-Orb Physical Interaction](#boss-orb-physical-interaction)
35. [Center Gravity Pull](#center-gravity-pull)
36. [Galaxy Background Video](#galaxy-background-video)
37. [Horror Shift System](#horror-shift-system)
38. [Extra Eye Orbs](#extra-eye-orbs)
39. [Grotesque SC Monsters](#grotesque-sc-monsters)

---

## Core Constants

```
BASE_TAPS             = 10      Starting taps
BASE_CIRCLE_COUNT     = 35      Starting orb count
BASE_CIRCLE_RADIUS    = 18      Default orb radius (px)
BASE_BLAST_RADIUS     = 60      Blast radius at level 1 (px)
MAX_BLAST_RADIUS      = 135     Maximum blast radius cap (px)
MAX_TAP_DISTANCE      = 80      Max distance to register a tap on an orb (px)
MAX_CIRCLES           = 300     Hard cap on simultaneous orbs
MAX_PARTICLES         = 800     Particle pool size
MAX_EXPLOSIONS        = 40      Explosion pool size
MAX_FLOATING_TEXTS    = 15      Floating text pool size
MAX_PAINT_SPLATS      = 200     Paint splat pool size
MAX_AFTERSHOCKS       = 20      Aftershock zone pool size
XP_BASE               = 12      XP needed for level 2
XP_SCALE              = 1.7     Exponential XP growth factor
INTER_ORB_GRAVITY     = 15      Base inter-orb gravitational pull
CHAIN_POP_DELAY_MIN   = 0.015s  Minimum delay between chain pops
CHAIN_POP_DELAY_MAX   = 0.045s  Maximum delay between chain pops
LINK_TENSION_TIME_BASE= 0.18s   Base chain link tension duration
LINK_PULL_STRENGTH    = 120     How strongly chain links pull targets
SC_SPAWN_CHANCE       = 0.09    9% chance per cooldown cycle for Archon form
SC_RADIUS_MULT        = 1.6     Archon radius multiplier
SC_GRAVITY_LIFE       = 0.4s    Duration of Archon gravity well
SC_GRAVITY_STRENGTH   = 200     Archon gravity well pull strength
DELAYED_BLAST_MINOR   = 20      Chain count threshold for minor delayed blast
DELAYED_BLAST_MAJOR   = 35      Chain count threshold for major delayed blast
DELAYED_BLAST_MEGA    = 55      Chain count threshold for mega delayed blast
TAP_TIMER_BASE        = 12s     Base time before a tap decays
BOMB_FUSE_TIME        = 3.5s    Mine detonation timer
BOMB_BLAST_MULT       = 1.4     Mine blast radius multiplier (140%)
```

---

## Orb Lifecycle

1. **Spawn:** From edge (60%) or inside play area via fade-in (40%)
2. **Drift:** Random angle, gentle velocity, affected by cursor pull + gravity
3. **Ascension:** May become an Archon (9% chance after cooldown, level 5+)
4. **Merge:** Overlapping orbs may merge into larger orbs
5. **Pop:** Blasted by chain reaction or tapped directly
6. **Fragment:** Popped orbs spawn smaller generation fragments (level 3+)
7. **Respawn:** New orbs spawn to maintain target count

### Orb Properties
```
id, x, y, radius, baseRadius
color, colorEdge
volatile (bool)         -- glows, 3x blast radius
armored (bool)          -- Void Crack, 2 HP
armorHP (0-2)           -- 2=fresh, 1=cracked, 0=normal
superCell (bool)        -- Archon form active
spikeCount, scFade      -- Archon visual state
popped, queued, alive
vx, vy                  -- velocity
generation (0-5)        -- fracture depth
shrapnel (bool)         -- reduced XP
fissionSpawn (bool)     -- prevents recursive fission
shrapnelImmune (timer)  -- blast immunity countdown
mergeCount              -- times this orb has merged
```

---

## Chain Reaction System

### Starting a Chain
1. Player taps within `MAX_TAP_DISTANCE` (80px) of an orb
2. Nearest orb is popped, creating an explosion
3. `chainOriginX/Y` recorded (used for delayed blast positioning)
4. Chain timer starts

### Chain Propagation
1. Popped orb creates blast circle with `getBlastRadius()` + upgrades
2. All orbs within blast radius are **queued** (not instant-popped)
3. Queued orbs get a **chain link** -- visual tension line pulling them toward pop source
4. After tension time expires (~0.135s for normal, ~0.18s for big combos), queued orb pops
5. That pop creates its own blast, queuing more orbs -- cascade continues
6. Chain ends when no more orbs are within any blast radius

### Chain Link Tension
- Duration: `0.18s * 0.75 = 0.135s` for chains < 40 pops, full `0.18s` for 40+
- Pull strength: 120 (orbs slide toward the source point during tension)
- Creates satisfying visual "pull then pop" effect

### XP and Scoring per Pop
```
Generation XP:  gen-0=1.0, gen-1=0.8, gen-2=0.5, gen-3=0.3, gen-4=0.2, gen-5=0.15
Size scaling:   sizeXpScale = max(0.15, (radius / 18) * 0.6)
Diminishing:    After 15 pops, factor = 15 / chainCount
Shrapnel:       0.15 XP flat (fission orbs)
Score per pop:  10 points
```

---

## Blast Radius Scaling

```javascript
function getBlastRadius() {
  t = min(1, (level - 1) / 16)       // 0 at lvl1, 1 at lvl17
  growth = t^0.6                       // gentler than sqrt
  base = 60 + 75 * growth             // 60px to 135px
  zoomScale = min(1, 0.3 + 0.7/zoom)  // compensate for zoom
  return base * zoomScale * madnessPhysicsScale
}
```

### Blast at Key Levels
| Level | t | growth | Base Radius |
|-------|---|--------|-------------|
| 1 | 0 | 0 | 60px |
| 3 | 0.125 | 0.22 | 77px |
| 5 | 0.25 | 0.38 | 88px |
| 7 | 0.375 | 0.53 | 100px |
| 10 | 0.5625 | 0.70 | 112px |
| 13 | 0.75 | 0.83 | 122px |
| 17+ | 1.0 | 1.0 | 135px |

Plus Nova Radius: +6px per upgrade level (max +48px at level 8).

---

## Fracturing and Generations

Each popped orb spawns smaller fragments based on level progression:

```
Level 1-2:  No fracturing
Level 3-4:  gen-0 spawns 1 gen-1 fragment
Level 5-7:  gen-0 -> 1 gen-1, gen-1 -> 1 gen-2
Level 8-9:  gen-0 -> 2 gen-1, gen-1 -> 1 gen-2
Level 10-11: gen-0 -> 2 gen-1, gen-1 -> 1 gen-2, gen-2 -> 1 gen-3
Level 12-13: gen-0 -> 2, gen-1 -> 2, gen-2 -> 1, gen-3 -> 0
Level 14-15: gen-0 -> 2, gen-1 -> 2, gen-2 -> 1, gen-3 -> 1
Level 16+:   gen-0 -> 2, gen-1 -> 2, gen-2 -> 1, gen-3 -> 1, gen-4 -> 1
```

Fragment radius scales: `0.4 + random*3` px at gen-1, decreasing per generation.

### Cascade Bursts (Long Chain Bonus)
During extended chains, smaller generation orbs spawn extra micro-fragments:
```
Gen-1 at chain 18+: 1 burst (2 at chain 35+)
Gen-2 at chain 28+: 1 burst
Gen-3 at chain 35+: 1 burst
Gen-4 at chain 45+: 1 burst
```

---

## Upgrade Effects (Detailed)

### Nova Radius (max 8)
- Adds `level * 6` px to blast radius
- At max: +48px (total possible blast ~183px at high level)

### Echo Burst (max 6)
- `level * 10`% chance per pop to trigger a second blast
- Echo blast: 1.3x the primary blast radius
- At max: 60% chance of echo on every pop

### Gravity Well (max 6)
- Each pop creates a temporary gravity pull toward the pop point
- Pull strength: `level * 22 * sizeScale * madnessPhysicsScale`
- Pull range: `blastRadius * 2.8 * madnessPhysicsScale`
- Clusters orbs for follow-up chain reactions

### Volatile Orbs (max 5)
- `level * 8`% of spawned orbs are volatile (max 40%)
- Volatile orbs glow with a bright aura
- When popped: 3x blast radius
- Cannot be armored simultaneously

### Aftershock (max 5)
- Pops leave lingering "hot zones" at the pop location
- Duration: `level * 0.5` seconds (max 2.5s)
- Any orb drifting into the zone is auto-popped
- Visual: pulsing glow circle

### Fission (max 4)
- Each pop spawns `min(2, level)` mini orbs
- Mini orbs: 0.4x base radius (tiny), gen-3 minimum
- Speed: 150-230 px/s, fly outward from pop
- **Anti-recursion:** Fission orbs have `fissionSpawn = true` -- they cannot trigger more fission
- **Blast immunity:** 0.6 seconds (prevents instant chain from parent blast)

### Chain Bounty (max 6)
- Bonus tap rewards multiplied by `1 + 0.3 * level`
- At max: 2.8x multiplier on all bonus taps from chains

### Density (max 6)
- +6% orb mass per level (multiplicative with field size)
- Applied as multiplier: `densityMult = 1.0 + G.upgrades.density * 0.06`
- At max: 1.36x orb count (base 35 * 1.36 = ~48 before level/madness scaling)

### Mine Layer (max 3)
- Long press (350ms hold) places a mine at cursor position
- `level` charges per tap cycle (1-3 mines per tap)
- Mine detonation: 3.5 second fuse
- Blast: normal blast radius * 1.4

### Attractor (max 5)
- Taps and mine detonations pull nearby orbs inward
- Range: `60 + level * 30` px
- Pull duration: 0.3 seconds

---

## Black Holes

### Spawn Conditions
- Timer: 25 + random 20 seconds between spawns below 75% madness; 15 + random 10 at 75%+
- Position: random within play area (80px margin from edges)
- Below 75% madness: only one regular black hole at a time
- At 75%+ madness: multiple simultaneous black holes (G.blackHoles[] array)
  - Max count: `1 + floor((madness - 0.75) / 0.25 * 3)` — ramps from 1 to 4 at 100%

### Constants
```
BH_VISUAL_RADIUS      = 25px    Base visual size
BH_GRAVITY_RADIUS     = 420px   Base gravity reach
BH_STRENGTH           = 1100    Base gravitational pull
BH_TANGENT_STRENGTH   = 0.55    Orbital spin ratio
BH_COLLAPSE_THRESHOLD = 5       Orbs needed for collapse
BH_SWALLOW_RADIUS_MULT= 0.5    Swallow zone = 50% of visual radius
```

### Growth per Orb Swallowed
```
radius       += 3px per orb    (25 -> 40 at 5 mass)
gravityRadius += 15px per orb  (420 -> 495 at 5 mass)
strength      += 80 per orb    (1100 -> 1500 at 5 mass)
```

### Lifetime
- 8-16 seconds random
- Fade in: 1.2 seconds
- Fade out: last 1.5 seconds

### Gravity Mechanics
- Radial pull: `strength * madnessPhysicsScale * fade / max(distance, 20)` per frame
- Tangential spin: 55% of radial pull, perpendicular -- creates orbital motion
- Also pulls bombs (at 20% strength)
- Pulls field stars

### Collapse
- **Auto-collapse:** When mass >= 5 and ready for 3 seconds
- **Tap-collapse:** Player taps within 2.5x visual radius
- **Effects:**
  - Blast radius: `60 * 0.8 + mass * 12` px
  - Free chain pops (no tap cost)
  - Screen shake: 3 + mass * 1.5
  - Horror boost: +0.02 + mass * 0.005
  - Tap reward: +1 (or +2 if mass >= 8)

### Horror Corruption
- While alive: `horrorBoost += (0.003 + mass * 0.002) * dt` (capped at 0.6)

---

## Supermassive Black Holes

### Spawn Conditions
- **Requires:** madness > 75%
- Timer: 20-35 seconds between spawns
- Position: center-biased (within 40% of center of play area)

### Constants
```
SBH_SPAWN_INTERVAL_MIN = 20s
SBH_SPAWN_INTERVAL_MAX = 35s
SBH_LIFETIME           = 8s
SBH_COLLAPSE_MASS      = 8 orbs
SBH_SWALLOW_FRAC       = 0.12   (12% of visual radius)
```

### Size
- Visual radius: 12% of shorter play area dimension
- Gravity radius: 55-70% of play area diagonal (scales with madness)
- Base strength: 3500 (3.2x stronger than regular BH)

### Gravity
- Uses `1/sqrt(distance)` falloff (wider effective range than regular BH's `1/distance`)
- Tangential ratio: 0.4 (orbital spin)
- Practically pulls everything on screen

### Growth per Orb
```
radius        += 5px
gravityRadius += 20px
strength      += 150
```

### Collapse
- At mass >= 8 (auto) or any mass (tap)
- **Dual explosion:** Red + White bursts
- **Blast radius:** `60 * 1.5 + mass * 18` px
- **Tap reward:** `max(2, min(5, 1 + floor(mass * 0.5)))`
- **Boss damage:** min(3, mass) hits
- **Screen effects:** shake 8 + mass*2, glow 0.2 + mass*0.05, chromatic 6 + mass

### Persistent Effects While Active
- Screen rumble: fadeIn * (0.5 + mass * 0.15) * 0.8
- Chromatic aberration: fadeIn * (2 + mass * 0.5)
- Horror boost: (0.006 + mass * 0.003) per second (capped at 0.8)

---

## Archons (Super Cells)

### Ascension
- 9% chance per cooldown cycle (cooldown: 3-8s random)
- Only at level 5+
- Orb grows to 1.6x radius, sprouts 5-8 spikes
- Visual: spiky, pulsing, with tendrils in ABYSS
- **Phase 5 Tethers:** On mutation, forms 2-4 tethers to nearby non-SC orbs within range (180px + radius * 2). Tethered orbs pulse in sync with Archon and are chain-immune

### Pop Behavior
- 1.5x blast radius
- Creates gravity well: 200 strength, 0.4s duration (scales with tether count)
- **Phase 5 Implosion:** Remaining tethered orbs rush inward and auto-pop on Archon death
  - Gravity well strength: `SC_GRAVITY_STRENGTH * (1 + tetherCount * 0.5)`
  - Gravity well life: `SC_GRAVITY_LIFE * (1 + tetherCount * 0.3)`
  - Gravity well radius: `blastRadius * (2.5 + tetherCount * 0.5)`
  - Extra screen shake: `4 + tetherCount`
- Unique deep boom + crunch sound

### Visual
- Spike animation with rotation
- Fade-in transition when ascending
- Tendrils in ABYSS biome (tied to madness level)
- Blinking "eye" effect at high madness
- Pulsing energy tether beams to connected orbs (Phase 5)
- At 60%+ madness: grotesque mutation — torn membrane wings, rib cage, extra arms, more eyes, desaturated colors

---

## Void Cracks (Armored Orbs)

### Spawn
- 3% chance at level 3+ (mutually exclusive with volatile)
- Visual: purple energy shell

### Two-Hit Mechanic
1. **First hit (HP 2 -> 1):** "Cracked" state
   - **Implosion:** Pulls nearby orbs inward (vacuum effect)
   - Crack sound effect
   - Visual: fractured energy shell
2. **Second hit (HP 1 -> 0):** Shatters
   - **Explosion:** Pushes orbs outward (reversed gravity well)
   - Normal pop behavior plus shatter effect

---

## Bosses

### Spawn
- First boss after 30 seconds
- Subsequent bosses spawn periodically (timer-based)
- HP scales with kills: more bosses killed = tougher bosses
- Two types: **Nexus** (default biome) and **Leviathan** (ABYSS biome)

### Mechanics
- Positioned on the field, can be damaged by chain blast overlap
- `damageBoss(explosionX, explosionY, blastRadius)` checks if blast reaches boss
- Hit cooldown between damage instances
- Death: dramatic explosion, bonus taps, boss kill counter increments

### Boss-Orb Physical Interaction
- **Push zone** (radius * 1.2): orbs near the boss body are pushed away
  - Push strength: 150 (250 for erratic bosses)
- **Eat zone** (radius * 0.3): orbs touching the core are consumed
  - Leviathan: 1.5% chance per orb per frame
  - Nexus: 0.5% chance per orb per frame

### Leviathan Bloat/Burst (Phase 3)
- After eating 6+ orbs: enters `bloated` state (3s window)
- **During bloat:** inflated body, violent pulsing, slowed movement, stressed color shift
- **3x damage window:** all damage is tripled during bloat
- **On hit during bloat:** drops all held orbs as free pops, "HUNGER RUPTURES"
- **On miss (timer expires):** shockwave scatters orbs (400px radius), boss heals `min(3, floor(orbsEaten * 0.3))` HP

### Nexus Shield Tethers (Phase 2)
- Nexus boss forms 3-4 shield tethers to nearby orbs
- While tethers active: damage is blocked
- Breaking all tethered orbs drops the shield
- Tethers regenerate after a brief cooldown

### Boss Death Sound
- Multi-layered: bass sweep + noise burst + high ring
- Full screen shake and white glow

---

## Mines (Bombs)

### Placement
- Long press (350ms) at cursor position
- Charges: equal to Mine Layer upgrade level (1-3 per tap)
- Charges reset each tap cycle
- Visual feedback: "MINE PLACED" floating text

### Detonation
- Fuse: 3.5 seconds (countdown timer visible)
- Blast: normal `getBlastRadius()` * 1.4
- Starts a chain if not already active
- Pulled by black hole gravity

---

## Gravity Systems

### Cursor Attraction
```
effectiveDistance = distance / cursorReachMultiplier
pull = min(65 * speedBoost * madPhys, 16000 * speedBoost * madPhys / (effDist^2 + 80)) * dt
cursorReachMult = 1.0 at 0% madness -> 2.0 at 100% (ramps from 10%)
```

### Inter-Orb Gravity
- Bigger orbs pull smaller ones
- Strength: `15 * (bigRadius / 18) * madPhys / max(distance, 10)`
- Range: `bigRadius * 4.5 * sizeBonus * madPhys`
- Merged orbs pull 1.5x harder
- Falloff near edge of range

### Madness Physics Scale
```
0-50% madness:   1.0 + madness * 0.3    (1.0 -> 1.15)
50-100% madness: 1.15 + (madness-0.5) * 1.1  (1.15 -> 1.7)
```
Applied to: blast radius, gravity ranges, pull strengths, whirlwind, pressure waves.

---

## Orb Count Scaling

```
baseCount = 35 + min(20, floor((level-1) * 1.5))
zoomFactor = min(1, 0.3 + 0.7 / zoomLevel)
densityMult = 1.0 + density_upgrade * 0.06    // 6% per level, multiplicative

madnessOrbMult:
  0-10%:  1.0x
  10-80%: 1.0 + (madness-0.1)/0.7 * 1.0  (ramps to 2.0x)
  80-100%: 2.0 + (madness-0.8) * 2.5      (peaks at 2.5x — was 3.5x)

targetCount = round(baseCount * zoomFactor * madnessOrbMult * densityMult)
```

Example at level 10, 90% madness, density 3: `(35+13) * 1.0 * 2.25 * 1.18 = ~127 orbs`

---

## Size Variation at High Madness

At madness > 66%:
```
madSizeFrac = min(1, (madness - 0.66) / 0.34)   // 0-1 from 66% to 100%
sizeVar     = 4 + madSizeFrac * 16               // random range: 4px to 20px
sizeMin     = 1.0 - madSizeFrac * 0.55           // minimum multiplier: 1.0 to 0.45

radius = max(5, BASE_RADIUS * sizeMin + random * sizeVar)
```

At 100% madness: orbs range from ~8px (tiny specks) to ~38px (huge balls).

---

## Tap Timer System

- Each tap resets the timer to `tapTimerMax`
- Timer counts down using raw delta time (unaffected by slow motion)
- When timer reaches 0: one tap is lost, timer resets
- `tapTimerMax = min(16, 12 + (level-1) * 0.5)` -- grows with level
- Timer pauses during: active chains, pending level up
- Timer bar rendered at very top of screen (y=0), 6px height

---

## Delayed Blast Combos

Triggered when a chain ends with enough pops:

| Tier | Pops Needed | Buildup | Effects |
|------|-------------|---------|---------|
| Minor | 20+ | 0.8s | Small explosion at chain origin |
| Major | 35+ | 1.2s | Medium explosion, screen shake 8 |
| Mega | 55+ | 1.6s | Massive explosion, shake 15, glow, chromatic aberration |

All delayed blasts:
- Center at `chainOriginX/Y` (where player initially tapped)
- Include rising pitch buildup sound
- Blast radius: tier-dependent multiplier of `getBlastRadius()`
- Screen darkening overlay during buildup

---

## Field Clear Mechanic

When `G.circles.length === 0` at chain end:
1. Mega explosion at screen center (60% of screen dimension)
2. Screen shake 15, full white glow, chromatic aberration 8
3. Boss takes 5 HP damage (with cooldown reset between hits)
4. Biome advances to next phase
5. +3 bonus taps
6. "FIELD CLEARED!" floating text
7. Boss death sound plays

---

## Directional Respawn Bias

### Pop Position Tracking
- Exponential moving average: `popCenterX = popCenterX * 0.9 + circle.x * 0.1`
- Tracked after every pop

### Spawn Bias (after 5+ tracked pops)
- 50% chance: spawn from opposite side of recent pop center
- If pops are right of center -> spawn from left edge (and vice versa)
- If pops are below center -> spawn from top edge (and vice versa)
- 50% chance: random edge (no bias)

---

## Orb Merging

- Orbs overlapping significantly may merge
- Merged orb radius: `sqrt(combined area / PI)` (capped at 3.5x base)
- Merged orbs track `mergeCount`
- Benefits: more XP, bigger blast radius, stronger gravity pull (1.5x)

---

## Color Synthesis

- Orb color based on horizontal screen position
- Left side: palette color 1 -> center: color 2 -> right: color 3
- Colors shift when biome changes (smooth transition)
- Horror desaturation applied on top at high madness

---

## Beat Pulse System

- Activates at 50%+ madness
- 100 BPM rhythm (0.6s period)
- On each beat: 15-40% of living orbs selected to pulse
- Pulse: brief radius expansion + brightness flash
- Creates eerie synchronized breathing effect

---

## Biome Transitions

- Smooth palette crossfade over ~2 seconds
- All orbs re-colored to new palette
- Lore text fades in at center-top (randomly selected from 3 options per biome)
- Lore text: italic, zoom-compensated, slow drift upward, 3.5s lifetime

---

## ABYSS-Specific Mechanics

### Pressure Waves (replaces Whirlwind)
- Periodic crushing force pushes orbs toward center
- Strength triples at full madness (gradual from 50%+)
- Accompanied by deep woosh sound

### Progressive Tentacle Mutation
- Orbs grow tentacles in ABYSS biome
- Tentacle count and length tied to madness level
- Start as subtle nubs, grow into full medusa-like appendages
- Animated: wave and undulate

### Visual Environment
- Bioluminescent haze layers
- Volumetric light shafts (zoom-compensated)
- Marine snow particles drifting downward
- Depth fog at edges
- Underwater current distortion

---

## The Watcher

### Appearance
- Background image that fades in behind all gameplay
- Main biome: `Watcher_test1.jpeg` (max 17% opacity)
- ABYSS biome: `Watchr_2.jpeg` (max 12% opacity)
- Opacity = madness * maxOpacity

### Animation
- Breathing: subtle scale pulse (0.3% amplitude)
- Eye glow: brightens during active chains
- Reach: extends when black holes are present (stronger for supermassive)
- Fade speed: slow fade in (0.5 rate), fast fade out (3.0 rate)

---

## Zoom System

### Progression
```
zoomTarget = max(0.5, 2.0 - score * 1.5/200000)
```
- Score 0: zoom 2.0x (intimate, zoomed in)
- Score 100k: zoom 1.25x
- Score 200k+: zoom 0.5x (fully zoomed out, 4x area)

### Smooth Transition
- `zoomLevel = lerp(zoomLevel, zoomTarget, dt * 2)` -- gradual zoom change

### Coordinate Spaces
- **Screen space:** Raw canvas pixels (0,0 = top-left corner)
- **World space:** Game coordinates inside zoom transform
- Transform: `screenX = canvasCenterX + (worldX - canvasCenterX) * zoomLevel`
- Effects rendered outside zoom (white glow, HUD) must use screen-space coordinates

### Compensations
- Blast radius: softened by `min(1, 0.3 + 0.7/zoom)` at high zoom
- Orb count: reduced at high zoom via same factor
- ABYSS effects: computed for visible area using `visW = W / zoomLevel`

---

## Text Corruption

### Enochian Corruption
- Characters drawn from Runic, Baybayin, and Ethiopic Unicode blocks
- **Deterministic:** Each character position has a fixed threshold based on hash
- **Progressive:** As madness increases, more characters flip to glyphs (no flickering)
- **Preserved:** Spaces, punctuation, digits remain readable
- HUD labels begin corrupting at 40% madness, fully corrupted at 100%

---

## Rift Walls — Space Canyon Rifts (Phase 4)

Canyon-like rifts in space that block blast propagation during chain reactions. Introduced at 30%+ WT. Visually overhauled in Session 5 from simple purple lines to multi-layered space canyons.

### Constants
```
RIFT_WALL_MIN_MADNESS    = 0.30    WT threshold to start spawning
RIFT_WALL_SPAWN_INTERVAL = 24s     Seconds between spawn attempts (was 30)
RIFT_WALL_MAX            = 4       Max walls on field simultaneously (was 3)
RIFT_WALL_HP             = 5       Hits to destroy (was 4)
RIFT_WALL_POP_RADIUS     = 110     Pop orbs within this distance on shatter (was 80)
RIFT_WALL_GRAVITY        = 35      Pull strength — orbs drift toward the rift
RIFT_WALL_GRAVITY_RANGE  = 120px   Gravity pull range from wall segment
```

### Behavior
- Spawn as canyon-like space rifts on the field at 30%+ WT
- Block blast propagation: when a blast circle crosses a rift wall, orbs on the other side are not queued
- Take damage from overlapping blast radii (`damageRiftWall()`)
- Each hit reduces HP by 1; at HP 0 the wall shatters
- **Shatter:** Pops all orbs within 110px of the wall
- **Gravitational pull:** Orbs within 120px are pulled toward the rift (closest point on wall segment)
- Below WT threshold: existing walls fade out and despawn
- If no walls exist and 5+ seconds have passed, a new wall spawns immediately

### Visual (6-Layer Rendering)
1. **Ambient void glow** — wide dark-purple haze around the rift
2. **Jagged canyon edges** — procedurally generated rocky edges on both sides with breathing animation
3. **Dark void interior** — deep black center line (the canyon depth)
4. **Energy veins** — thin flickering purple lines deep inside the canyon
5. **Sparks & debris** — particles being pulled into the rift
6. **Endpoint flares** — radial glow where the rift terminates

### Chain Interaction
- During `popCircle()`: blast radius check tests line-of-sight through rift walls
- Blasts that overlap a wall deal damage to it but are blocked from propagating past it
- Bridges bypass rift walls (Phase 6 interaction)

---

## Gravity Bridges (Phase 6)

Same-colorIdx orbs form energy connections after sustained proximity. Blasts travel along bridges, bypassing walls.

### Constants
```
BRIDGE_MIN_MADNESS     = 0.55    Madness threshold to start forming bridges
BRIDGE_PROXIMITY       = 3.0     Within 3× average radius of the pair
BRIDGE_FORM_TIME       = 2.0s    Seconds of proximity required to form a bridge
BRIDGE_MAX             = 60      Cap for performance
BRIDGE_CHECK_INTERVAL  = 0.3s    Seconds between proximity scans
```

### Formation
- Only forms between orbs with matching `colorIdx` (palette segment index, immune to desaturation)
- Orbs must be within `3× average radius` of each other for `2.0 seconds` continuously
- Proximity scan runs every 0.3s for performance
- Dead/popped/queued/tethered orbs cannot form or maintain bridges
- Max 60 bridges on field

### Chain Propagation
- When an orb pops, blast travels along all connected bridges to the partner orb
- Partner orb is queued for chain pop (same as being in blast radius)
- **Bridge bypasses rift walls** — allows blast to reach orbs on the other side
- Used bridge is consumed (removed from bridge list)
- Boss-tethered orbs are immune to bridge propagation

### Visual
- Pulsing energy lines between connected orbs, colored by the orb's visual color
- Fade-in over first 0.5s of bridge life
- Pulse phase animates at 2.5 rad/s

---

## Shadow Balls

Non-interactive dark phantom orbs that fill the field at high madness. Purely cosmetic.

### Constants
```
SHADOW_BALL_MIN_MADNESS = 0.55    Madness threshold to start spawning
SHADOW_BALL_MAX_COUNT   = 400     Max shadow balls on field (10× from original 40)
SHADOW_BALL_SIZE_MIN    = 2px     Smallest shadow ball radius
SHADOW_BALL_SIZE_MAX    = 12px    Largest shadow ball radius
SHADOW_BALL_SPAWN_RATE  = 0.03s   Seconds between spawn attempts (5× faster than original)
```

### Behavior
- Spawned at edges, drift inward with slow velocity
- Non-interactive: do not participate in chains, blasts, or collisions
- Affected by black hole gravity (pulled toward singularity)
- Target count scales linearly from 0 at threshold to 400 at 100% madness
- Batch-spawned (up to 10 per attempt) to fill the field faster
- Below threshold: all shadow balls gradually fade and despawn

---

## Leviathan Bloat/Burst (Phase 3)

Boss mechanic specific to the Leviathan boss type (ABYSS biome). Creates a 3x damage window.

### Trigger
- Leviathan consumes orbs through eat zone and tentacle grabs
- After eating 6+ orbs in a cycle: enters `bloated` state

### Bloated State (3s window)
- Visual: body inflates 15-30% larger, pulsing violently
- Color shifts from dark teal to stressed green/red as timer runs out
- Movement slows dramatically (0.2x tracking speed)
- Cannot eat more orbs while bloated
- Countdown arc rendered around body

### Damage Window
- **All damage dealt during bloat is 3x** — the optimal time to hit
- On hit during bloat: "HUNGER RUPTURES" text, drops all held orbs as free pops

### Burst (on hit)
- All held orbs (`bloatOrbsHeld`) are dropped in a ring around the boss
- Each dropped orb spawns as a normal orb that can be chained
- Bloat cycle resets

### Miss (timer expires)
- **Shockwave:** All orbs within 400px are scattered to edges
- Boss heals: `+min(3, floor(orbsEaten * 0.3))` HP
- "IT FEEDS +NHP" floating text
- Bloat cycle resets

---

## Archon Tethers (Phase 5)

Reworked Archon (Super Cell) mechanic: 2-4 tethers form on mutation, implosion on pop, gravity well scaling.

### Tether Formation
- On SC mutation: 2-4 nearby orbs are tethered (range: 180px + radius * 2)
- Tethered orbs must be alive, not popped, not queued, not another SC, not already tethered
- Candidates sorted by distance, closest selected first
- Each tether has a `pulsePhase` for synchronized visual pulsing

### While Active
- Tethered orbs pulse in sync with the Archon (pulsePhase += dt * 3.5)
- Tethered orbs are **chain-immune** — cannot be popped by blast propagation
- Tethers are pruned when the connected orb dies or is popped externally

### On Archon Pop (Implosion)
- All remaining tethered orbs rush inward toward the Archon position and auto-pop
- Gravity well scales with tether count:
  - Strength: `200 * (1 + tetherCount * 0.5)`
  - Duration: `0.4s * (1 + tetherCount * 0.3)`
  - Radius: `blastR * (2.5 + tetherCount * 0.5)`
- Screen shake: `4 + tetherCount`

### On SC Revert (timer expires)
- All tethers released, tethered orbs become normal again

---

## Boss-Orb Physical Interaction

Bosses physically interact with nearby orbs through push and eat zones.

### Zones
- **Push zone:** `radius * 1.2` — orbs within this range are pushed away from boss center
  - Push strength: 150 (250 for erratic bosses)
- **Eat zone:** `radius * 0.3` — orbs touching the core are consumed
  - Leviathan: 1.5% chance per orb per frame to consume
  - Nexus: 0.5% chance per orb per frame
  - Consumed orbs feed bloat cycle (Leviathan) or heal boss

---

## Center Gravity Pull

At 75%+ madness, all orbs feel an increasing gravitational pull toward the field center.

### Formula
```
centerStr = (madness - 0.75) / 0.25     // 0→1 from 75-100%
centerPull = centerStr * 60 * (1 / sqrt(distance/50 + 1)) * dt
```

- Inverse-sqrt falloff (wider effective range than inverse-square)
- Max force: 60 units at 100% madness
- Applied every frame to all living orbs
- "Reality collapses inward" — everything slowly drawn to the void's center

---

## Galaxy Background Video

The galaxy background is rendered as a video (`Images/GalaxyBG_test.mp4`) behind the Watcher in default biomes.

### Properties
- Ultra-slow playback rate that scales with madness: `0.03x` at 0% → `0.25x` at 100%
- Muted, looping, playsInline
- Fallback to static image (`Images/BG_Galax.jpg`) if video fails to load
- Rendered with `screen` composite mode for additive blending
- Max opacity: 22% (fades in from 5% madness)
- Smooth loop transition: playback rate ramps down in last 1.5s, opacity dips at seam

### ABYSS Behavior
- Galaxy BG fades out in ABYSS biome (opacity → 0 at 2.0/s)

---

## Horror Shift System (Desaturation)

`horrorShift(hex, amount)` applies 4-phase color desaturation as World Transparency (WT) increases. Session 5 made the curve gentler and later to preserve color vibrancy longer.

### Phase Breakdown
```
Phase 1 (0→50%):   Gradual desaturation — 65% pull toward greyscale (gentler ramp)
Phase 2 (30→65%):  Flesh/bile tint — sickly warmth creeps in
Phase 3 (60→88%):  Deep drain — colors collapse toward muted flesh void (45% pull)
Phase 4 (88→100%): TOTAL ash convergence — all color dies, grey-brown ash rgb(75,65,58), final dimming
```

### Background Darkening
- Background gradient uses `horrorShift` at `madness * 0.7` (faster than orbs)
- Additional darkening: `lerpColor(color, '#020004', madness * 0.5)`
- At 80%+ madness: aggressive push toward pure black (`abyssStr` 0→1 over 80-100%)

---

## Extra Eye Orbs

At 85%+ madness, normal orbs sprout additional eyes across their surface.

### Scaling
```
extraEyeCount = madness >= 0.85 ? floor(((madness - 0.85) / 0.075) * 2) + 2 : 1
```
- 85% madness: 2 extra eyes
- 92.5% madness: 4 extra eyes
- 100% madness: 6 extra eyes (max 7 including primary)

### Visual
- Extra eyes are smaller than the primary eye
- Scattered across the orb surface at deterministic angles (seed-based)
- Each has full iris/pupil/specular detail
- Only renders on orbs with radius > 7px and eye intensity > 0.4

---

## Grotesque SC Monsters

At high madness (60%+), default biome Super Cells mutate into grotesque horror entities.

### Grotesque Factor
```
grotesque = max(0, min(1, (madness - 0.6) / 0.4))   // 0→1 from 60-100%
```

### Features (progressive with grotesque factor)
- **Wings:** 2-5 torn membrane wings with jagged tears, flapping irregularly, visible bone veins
- **Extra arms:** Additional disfigured pseudopod limbs with knobby joints
- **Rib cage:** 3-7 curved bone arcs visible through the body
- **More eyes:** Up to 12 extra eyes scattered across body and arms, extending further outward
- **Color drain:** SC body desaturated at `madness * 1.2` (faster than normal orbs)
- **Dimmed radiance:** Eerie glow dims and greys at high grotesque
- **Muted palette:** Warm gold/amber eyes shift to dull grey-brown; sclera yellows

---

## Mutation Phase 1 — Void Seed (Session 5)

Smooth transition for the first visible sign of eye mutation. Replaces the previous hard jump from normal orb to dark center.

### Rendering
- **Void Seed:** A tiny dark center gradient that fades in from `eyeIntensity > 0` (WT 10%+)
- Quadratic ease-in: `voidT² * 0.35` alpha — starts imperceptibly
- Grows from 4% → 18% of orb radius over `ei = 0 → 0.3`
- Subtle breathing pulse per orb (`sin(time * 1.2 + orbId * 2.7)`)
- **Pupil fade-in fix:** All 3 eye types (manic/glass/sunken) now multiply pupil alpha by `irisT`, preventing the previous 50-70% opacity jump at threshold

---

## Void Stars (Session 5)

2000 twinkling stars from "the other side" bleeding through the thinning membrane. Visible at mid-high WT, layered between BG and orbs.

### Constants
```
VOID_STAR_COUNT      = 2000
VOID_STAR_MIN_WT     = 0.35    Start appearing at 35% WT
Full visibility      @ ~70% WT  (ramps over 35% range)
```

### Properties per Star
- Position (0-1 normalized), velocity, size (0.3-1.7px)
- Brightness (0.15-1.0), blink speed (0.4-2.9), blink depth (0.15-0.75)
- Layer depth (0-1, affects gravity response and dimming)
- Color type: 6 varieties (cool white, pale blue, warm gold, pale violet, ice blue, dusty rose)

### Behavior
- Gentle drift with individual velocities
- **Black hole reactive:** Pulled by regular and supermassive black holes with tangential swirl
  - Deeper-layer stars less affected (`0.3 + layer * 0.7` multiplier)
- **NOT cursor reactive** — no cursor gravity
- Velocity damping (0.2/s) keeps them calm
- Wrap-around at edges
- Brighter stars get subtle cross-flare (+) when twinkling high

### Render Layer
- After nebula clouds, before paint splats (Layer 1c2)
- Behind all gameplay elements, in front of deep background

---

## World Transparency Milestone Banners (Session 5)

Dramatic full-width text banners displayed when crossing WT thresholds.

### Milestones
| WT | Main Text | Subtext | Color | Screen Shake |
|----|-----------|---------|-------|-------------|
| 50% | THE MEMBRANE THINS | 50% WORLD TRANSPARENCY | #b060ff (purple) | 4 |
| 75% | REALITY UNRAVELS | 75% WORLD TRANSPARENCY | #ff5080 (pink-red) | 4 |
| 90% | THE OTHER SIDE BLEEDS THROUGH | 90% WORLD TRANSPARENCY | #ff2020 (red) | 8 |

### Behavior
- Each milestone triggers once per game session (tracked in `G.wtMilestones`)
- Banner duration: 3.5 seconds
- Envelope: fast fade-in (12%), hold (to 70%), slow fade-out (30%)
- Slight upward drift during display
- Dark vignette band behind text for readability
- Horizontal accent lines flanking the text
- Main text: large white with colored glow, subtext: smaller colored

### Terminology
- "Madness" is now referred to as "World Transparency" (WT) going forward
- Code still uses `getMadnessAmount()` internally
