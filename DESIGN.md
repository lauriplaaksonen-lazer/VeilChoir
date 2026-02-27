# POP FIELD — Design Document

Single-file browser game (`index.html`). Canvas-based, no dependencies. Touch + mouse input.

---

## Core Loop

The player has a limited number of **taps**. Each tap starts a **chain reaction** — popping one orb triggers nearby orbs in sequence. Longer chains earn more score (chain^2), XP, and bonus taps. The game ends when taps run out.

A **tap timer** counts down between taps (raw time, unaffected by slo-mo). If it expires, a tap is consumed. This creates urgency — don't wait too long.

### Game States
- `title` — ambient orbs float, tap to start
- `playing` — main gameplay
- `game_over` — final score display, tap to restart

---

## Orbs (Circles)

The primary game objects. 50+ orbs float on screen at all times.

### Movement Model
Every orb has position + velocity (`vx`, `vy`). Each frame applies:
1. **Gravitational swirl** — pull toward a slowly-shifting attractor point + tangential push → orbiting motion
2. **Ambient wave** — sine/cosine overlay for organic drift
3. **Brownian nudge** — periodic random direction change
4. **Cursor gravity** — gentle inverse-square pull toward mouse/touch
5. **Cursor brush** — velocity-based push when cursor moves near orbs (like brushing marbles on a table). Range: 60px + orb radius
6. **Velocity damping** — drag factor `1 - 1.2*dt` per frame
7. **Boundary handling** — soft bounce for normal orbs, screen-wrap for high-speed shrapnel/fragments

Velocity is persistent — explosions, gravity wells, and black holes add to `vx/vy`, creating slingshots and orbital paths.

### Orb Types
- **Normal** — standard orbs, colored by position at creation (cluster gradient). Persistent color
- **Volatile** — 1.3x size, 3x blast radius when popped. Spawn chance: `8% * volatileOrbs upgrade level`
- **Shrapnel** — 40% size, spawned by chain combos. 350ms blast immunity so they can fly outward visibly. Worth 25% XP
- **Fission orbs** — 60% size, spawned by Fission upgrade. Normal chain behavior
- **Fracture fragments** — spawned when any orb is popped (see Fracturing System). Size decreases per generation

### Generations (Fracturing)
Orbs shatter into smaller pieces when popped, creating cascading chain reactions:
- Gen 0 (100%): normal orbs, can mutate into Super Cells. Pops spawn 2 gen-1
- Gen 1 (60%): fracture fragments. Pops spawn 2 gen-2. Worth 0.5 XP
- Gen 2 (35%): smaller shards. Pops spawn 1 gen-3. Worth 0.25 XP
- Gen 3 (20%): terminal — pops spawn nothing. Worth 0.15 XP

All fragments have 200ms shrapnel immunity on spawn to fly outward visibly.

### Cluster Coloring
Orb color is determined by screen position at creation time via `getClusterColor(x, y)`, which returns `{ color, colorIdx }`. The `color` is the interpolated hex value (affected by horror desaturation), while `colorIdx` is the palette segment index (0, 1, or 2) — immune to desaturation and used for gravity bridge matching. Colors come from the current palette (3 base colors interpolated across the field). Once assigned, colors are **persistent** — orbs keep their birth color as they drift, creating natural mixing. Colors also evolve through **color synthesis** when different-colored orbs touch (see Motion & Visual Polish).

---

## Super Cells (SC)

Random gen-0 orbs temporarily mutate into Super Cells. In the default biome they appear as **amorphous eye-cluster entities** — wobbly pseudopod bodies covered in scattered blinking eyes that track the cursor. In ABYSS biome they appear as **tentacle horrors** — writhing multi-segment appendages with suction cups, dark mantles, and bioluminescent slit-pupil eyes.

### Lifecycle
1. **Cooldown** — each orb has an independent `scCooldown` timer (3-11s)
2. **Mutation** — 9% chance per eligible orb when cooldown expires
3. **Fade in** — 0.34s morph into SC form (eye-cluster or tentacle horror), radius grows to 1.6x
4. **Active** — 2.1-5.5s lifespan, fully rendered
5. **Fade out** — 0.42s revert to normal orb

### SC Pop Mechanics
When popped, SCs:
- Have 1.5x blast radius
- Spawn a **gravity well** that pulls nearby orbs inward (strength/duration/radius scale with remaining tether count)
- **Phase 5 Implosion:** All tethered orbs rush inward and auto-pop
- Create larger visual explosion

### Archon Tethers (Phase 5)
On mutation, the Archon forms 2-4 energy tethers to nearby non-SC orbs. Tethered orbs pulse in sync and are chain-immune (cannot be caught in blast propagation). When the Archon is popped, all tethered orbs implode inward and auto-pop, creating a satisfying cluster burst. If the SC reverts naturally (timer expires), tethers are simply released.

### Grotesque SC Monsters (60%+ Madness)
At high madness, default biome SCs mutate into disfigured horrors. Progressive features include: torn membrane wings (2-5, flapping), exposed rib cage bones, extra disfigured arms with knobby joints, multiplied eyes scattered along limbs, and heavily desaturated flesh-tone coloring. The `grotesque` factor scales from 0 at 60% to 1.0 at 100% madness.

---

## Chain Reaction System

The core mechanic. When an orb is popped:

1. **Blast radius check** — all orbs within blast range get `queued`
2. **Chain links** — visual metaball-style connectors pull queued orbs toward the pop source
3. **Link tension** — after the link duration expires, the queued orb pops
4. **Cascading** — each pop repeats step 1, creating exponential chains

### Chain Links (Visual)
**Default biomes**: Bezier-curved blob connectors between pop source and target. Organic wobble, metaball-style width profile with smooth blobs at endpoints and necked middle. Target is physically pulled toward source during tension.

**ABYSS biome**: Tentacle-style connectors — sinuous cubic bezier curves with variable thickness, suction cup dots along the length, and a bioluminescent traveling pulse (accent color glow that moves from source to target).

### Delayed Blast (Mega-Chain Mechanic)
At high chain counts, a **delayed blast** triggers:
- **Minor** (20+ pops): 0.5s buildup
- **Major** (40+ pops): 0.65s buildup
- **Mega** (80+ pops): 0.75s buildup

During buildup: time slows, all chain links and queue freeze, targets are pulled toward the blast center. Then everything pops simultaneously with massive screen shake, chromatic aberration, and white-hot glow.

### Shrapnel Spawning
At chain count >= 2, each pop spawns shrapnel orbs launched outward at 400-650 px/s. Count scales with combo tier (base 2 + 50% per 5 pops). Shrapnel has 350ms immunity to avoid being instantly re-caught by the blast that spawned it.

---

## Black Holes

Gravity hazards that spawn periodically. At 75%+ madness, multiple black holes can exist simultaneously (up to 4 at 100% madness via `G.blackHoles[]` array).

### Behavior
- **Gravity pull** — all orbs within 320px are accelerated toward it (Star Control style — adds to velocity, not position). Includes tangential force (0.55x radial strength) creating visible orbital spin
- **Swallowing** — orbs touching the event horizon are destroyed and add to the black hole's **mass counter**
- **Compression pop** — when mass reaches threshold (5 orbs), the hole becomes "collapse-ready" (pulsing visual). After 3 more seconds it auto-triggers a **free chain reaction** centered on itself
- **Tap interaction** — tapping the black hole triggers an early collapse without costing a tap. Blast radius scales with mass: `baseBlast + mass * 12px`
- **Lifespan** — 8-16 seconds, fades out over last 1.5s. If it dies with enough mass, it collapses as a parting gift
- **Spawn interval** — 25-45s below 75% madness; 15-25s at 75%+ (more frequent at high madness)

### Multiple Black Holes (75%+ Madness)
- Max simultaneous: `1 + floor((madness - 0.75) / 0.25 * 3)` — 1 below 75%, up to 4 at 100%
- Each is independently tracked in `G.blackHoles[]` array
- `G.blackHole` is maintained as a compat alias for `G.blackHoles[0]`

### Visual
Subtle design: small black center with violet edge, single thin accretion disk ellipse, faint gravity field hint. Orbiting dots indicate swallowed mass. Pulsing ring when collapse-ready.

---

## Scoring & Progression

### Score
`score += chainCount^2` at end of each chain.

### XP & Leveling
XP earned = sum of chain values (shrapnel worth 0.25, normal orbs worth 1). XP threshold scales: `base * 1.55^level`. On level-up, player chooses from 3 random upgrades.

### Bonus Taps
Chains earn bonus taps based on length:
- 15+ pops: 35% chance for +1 tap
- 25+ pops: guaranteed +1 tap
- 40+ pops: guaranteed +2 taps

Scaled by Chain Fuel upgrade multiplier.

---

## Upgrades (10 Total)

| Upgrade | Max | Effect |
|---------|-----|--------|
| Nova Radius | 8 | +12px blast radius per level |
| Echo Burst | 6 | 18% * lvl chance per pop for secondary 1.4x blast |
| Gravity Well | 6 | Pops pull nearby orbs inward (strength scales) |
| Volatile Orbs | 5 | 8% * lvl of orbs spawn as volatile (3x blast) |
| Aftershock | 5 | Pops leave hot zones for 0.5s * lvl — orbs drifting in get popped |
| Fission | 6 | Each pop spawns 1-4 mini orbs that fly outward |
| Chain Fuel | 6 | Bonus tap rewards multiplied by 1 + 0.3 * lvl |
| Density | 6 | +6% orb mass per level (multiplicative) |
| Mine Layer | 3 | First N taps place free mines (timed fuse, 140% blast) |
| Attractor | 5 | Taps/mines create gravity wells (range 60 + 30*lvl px) |

Upgrades are offered 3 at a time (random, filtered by max level). Selection immediately applies.

---

## Bombs (Mines)

Mine Layer upgrade lets first N taps per round place mines instead of popping. Mines:
- Sit on field for `BOMB_FUSE_TIME` (3.5s) with ticking visual countdown
- When fuse expires: pop with 1.4x blast radius
- If Attractor upgrade exists: also create a persistent gravity well
- Free — don't cost taps

---

## Eyeball Orbs (Madness-Driven)

Normal orbs progressively transform into eyeballs as madness increases (0–100% scale). The transformation is designed to be insidious — the player shouldn't realize what's happening until it's too late:

- **0–10% madness**: Normal colored orbs, no eye features
- **10–25%**: A dark spot/void appears in the center — looks like a shadow or hole, NOT recognizably an eye. Just "something off"
- **25–40%**: Fleshy sclera starts emerging around the dark center. Rim/eyelid shadow forms. First faint veins
- **40–60%**: Iris becomes visible inside the dark spot (seamless transition from void to pupil). Veins spread. The orb is now clearly an eye
- **60–80%**: Full iris detail with golden-brown fibers. Pupil tracks cursor. Branching red veins
- **80–100%**: Complete eyeball — specular highlights, dilating pupil during chains, dense vein networks. Every orb is staring at you
- **85–100%**: Extra eyes sprout — 2 per 7.5% above 85% (2 at 85%, 4 at 92.5%, 6 at 100%). Smaller secondary eyes scattered across the orb surface, each with full iris/pupil detail

---

## Visual Systems

### Palettes / Biomes
5 palettes cycling every 3 levels. Each defines: background gradient, 3 orb colors, accent color, base audio note, oscillator type. Smooth biome transitions on level-up.

### Rendering Layers (back to front)
1. Background gradient (near-pure-black at 80%+ madness)
1b. Galaxy background video (screen blend, 22% max opacity, madness-scaled playback rate)
2. Nebula clouds (ambient color blobs)
2b. ABYSS background effects (bioluminescent haze + rising motes)
2c. The Watcher (background image at 15% opacity)
3. Paint splats (persistent marks from pops)
4. Orb bloom glow
5. Aftershock zones
5b. Rift walls (Phase 4 energy barriers)
5b2. Gravity bridges (Phase 6 energy connections between same-colorIdx orbs)
6. Gravity wells
7. Black holes (up to 4 simultaneously at high madness)
7b. Shadow balls (non-interactive phantom orbs, up to 400)
8. Background particles
9. Chain links
10. Bombs
11. Circles (orbs / SCs / grotesque SC monsters)
12. Explosions
13. Light rays
14. Chain energy field
15. Foreground particles
16. Floating texts
17. White-hot glow overlay
18. Delayed blast buildup
19. Vignette
20. Chromatic aberration
21. Tap timer bar

### Screen Effects
- **Shake** — random translate, decays at 15/s
- **Chromatic aberration** — RGB channel offset, triggered at high chains
- **White-hot glow** — full-screen white overlay for mega pops
- **Time scale** — slo-mo during delayed blast buildup, recovers at 0.3/s

### Particles
Pool-based system (max 800). Types: spark, ember, ring, streak, smoke. Each has position, velocity, rotation, size, color, lifetime. Assigned to `bg` or `fg` layer.

### Paint Splats
Persistent marks left on the field from pops. Max 200, oldest removed first. Rendered as soft radial gradients with slow fade.

---

## Audio

Web Audio API. Each pop plays a tone:
- Frequency: `baseNote * 1.055^chainIndex` (capped at 4k Hz)
- Oscillator type: from current biome palette
- Volume: scales slightly with chain depth
- SC pops: lower frequency, longer sustain, square wave crunch + noise burst
- Bonus taps: reward sound
- Phantom pops (miss): distinct sound

---

## Input

- **Touch**: touchstart → handleTap, touchmove → cursor tracking
- **Mouse**: mousedown → handleTap, mousemove → cursor tracking
- **Keyboard**: `B` key toggles ABYSS biome override
- Cursor position drives: orb cursor-gravity, cursor brush force, eyeball iris tracking
- Taps during active chains are allowed — starts new chain or extends existing one

---

## Key Constants

| Constant | Value | Purpose |
|----------|-------|---------|
| BASE_TAPS | 3 | Starting taps |
| BASE_CIRCLE_COUNT | 50 | Starting orb count |
| BASE_CIRCLE_RADIUS | 18 | Normal orb radius |
| BASE_BLAST_RADIUS | 95 | Base pop blast radius |
| MAX_TAP_DISTANCE | 80 | Max tap-to-orb distance for direct pop |
| MAX_CIRCLES | 200 | Hard cap on orbs |
| LINK_TENSION_TIME | 0.18 | Base chain link duration |
| SC_SPAWN_CHANCE | 0.09 | 9% chance for SC mutation |
| TAP_TIMER_BASE | 12 | Seconds between forced tap consumption |
| BOMB_FUSE_TIME | 3.5 | Mine countdown |
| BH_GRAVITY_RADIUS | 320 | Black hole pull range |
| BH_STRENGTH | 800 | Black hole gravitational strength |
| BH_TANGENT_STRENGTH | 0.55 | Tangential force ratio for orbital spin |
| BH_SWALLOW_RADIUS_MULT | 0.5 | Fraction of visual radius = swallow zone |
| BH_COLLAPSE_THRESHOLD | 5 | Orbs swallowed before black hole can collapse |
| WATCHER_APPEAR_START | 90 | Seconds before The Watcher begins materializing |
| WATCHER_APPEAR_DURATION | 30 | Seconds for full materialization |

---

## The Watcher — Cosmic Lore

### What the Player Experiences

The Pop Field is not a game. It is an observation.

After roughly 90 seconds of play, the background begins to shift. Something is there — behind the nebula clouds, behind the floating orbs. A shape of absolute blackness, larger than the screen itself, gradually materializing. A silhouette: broad shoulders, a head crowned with dark curls, two points of cold white light where eyes should be.

**The Watcher** does not interfere. It does not help. It observes.

As chains grow longer, its eyes burn brighter — it is interested. When a black hole tears open the field, its arm begins to extend, a massive hand reaching through the void, fingers splayed as if to grasp the singularity. A galactic band — a ribbon of stars and warm light — runs along the arm like veins of creation.

The orbs are not particles. They are something the Watcher is studying. The chain reactions are not explosions — they are responses. The player is not playing a game. The player is conducting an experiment that something ancient is watching very, very carefully.

### Visual Design

The Watcher is rendered as a **background image** (`images/BG_test1.jpeg`) displayed behind all gameplay, after the background gradient and nebula clouds but before all interactive elements.

- **Opacity**: Very subtle — 15% of the image's full opacity. The Watcher should be a barely-there presence, more felt than seen
- **Breathing**: Slow 0.5% scale oscillation gives it organic presence
- **Parallax drift**: Slight position offset tied to breath phase
- **Eye glow overlay**: During chains, the image is re-drawn in `lighter` composite mode at very low opacity (4% of eye glow strength), creating a subtle brightening
- **Materialization**: Fades in starting at 90 seconds into gameplay over 30 seconds

### Behavioral Reactions

| Event | Watcher Response |
|-------|-----------------|
| Chain reaction active | Eyes brighten proportionally to chain length |
| Chain idle | Eyes dim to faint glow |
| Black hole present | Arm slowly reaches toward the field |
| Black hole gains mass | Reach extends further |
| Black hole disappears | Arm gradually retracts |
| Normal gameplay | Gentle breathing animation (0.8% body scale oscillation) |

### The Horror Arc

The Watcher's appearance coincides with the game's madness progression:

- **0% madness**: Vibrant colors, normal orbs, The Watcher hasn't materialized yet. Pure arcade joy
- **~90 seconds in**: The Watcher begins to fade in behind the field. At 15% opacity it's barely visible — you might not notice
- **10-25% madness**: Dark spots appear in orb centers. Colors slightly muted. The Watcher is a faint presence. Something feels... off
- **25-50% madness**: Orbs are developing fleshy rims and veins. Colors desaturating. UI text has scattered Enochian characters. Smaller orbs seem further away
- **50-75% madness**: Full eyeball orbs tracking the cursor. Sickly flesh-toned palette. UI labels corrupting. The Watcher brightens during chains. Shadow balls begin appearing (55%+). Gravity bridges form between same-color orbs (55%+). Rift walls block blast propagation (30%+). SC entities begin grotesque mutation (60%+)
- **75-100% madness**: Eyes everywhere, staring. Branching veins, dilating pupils. UI is unreadable occult script. Total ash convergence (85-100% horrorShift). Multiple black holes tear the field (up to 4). Center gravity pulls everything inward. Extra eyes sprout on orbs (85%+). Background goes near-pure-black (80%+). Galaxy BG video accelerates. SC monsters fully grotesque — wings, ribs, extra eyes, all desaturated

The player was never in control. They were always being watched.

---

## Madness System

Madness is the game's unified 0%–100% transformation scale. At 0% everything is normal colorful arcade. At 100% everything is maximally weird/horrifying. All visual effects reference a single `getMadnessAmount()` function.

### Madness Sources
- **Score-based**: 0% below 100k score, ramps to 50% at 500k, reaches 100% around 900k
- **Black hole corruption** (`horrorBoost`): Permanent cumulative corruption from black hole proximity. Each second of exposure adds madness. Swallowing orbs accelerates it. Collapse events cause large jumps. Capped at 60%. Combined with score-based, total caps at 100%

### Madness Progression (0% → 100%)

| Madness | Palette | Orb Eyes | UI Text | Depth-of-Field | Watcher |
|---------|---------|----------|---------|----------------|---------|
| **0%** | Vibrant, saturated | Normal colored orbs | Clean, readable | Normal | Barely visible (15% opacity) |
| **10%** | Slight desaturation | Dark spot/hole appears in center — looks like shading | Clean | Normal | Subtle breathing |
| **15%** | Noticeable desaturation | Holes deepen | First value characters corrupt to Enochian | Normal | Faint |
| **25%** | Muted colors | Fleshy sclera emerges around holes, rim forms | More values corrupted | Slight gen-1+ fade | Breathing visible |
| **35%** | Color draining | First veins appear on orbs | ~1/3 of values are Enochian | Noticeable gen-2+ fade | Present |
| **40%** | Sickly undertones | Veins spreading, iris forming | Labels start corrupting too | Moderate | Glows on chains |
| **50%** | Half-desaturated, flesh tint starting | Clear iris + pupil, tracking cursor | Half of all text Enochian | Gen-2 orbs quite faint | Brightens during chains |
| **55%** | Deepening desaturation | Eyes tracking, shadow balls appear | Text corrupting | Faint gen-2+ | Bridges form between same-color orbs |
| **65%** | Heavy flesh/bile tint, SCs grotesqueing | Full eyes, SC wings/ribs emerging | Most text corrupted | Gen-3 nearly invisible | Pulses |
| **75%** | Near-monochrome sickly | Detailed eyes, specular highlights | Labels mostly unreadable | Strong depth separation | Multiple BHs, center gravity pull |
| **85%** | Ash convergence begins | Extra eyes sprout (2+), full detail | Nearly all Enochian | Extreme depth fade | Galaxy BG speeds up |
| **90%** | Near-total ash, dark | Full eyes dilating, 4+ extra eyes | Nearly all Enochian | Extreme depth fade | Omnipresent |
| **100%** | Dead grey-brown ash | Staring eyes everywhere, 6 extra each | Completely Enochian | Small orbs ghostly | Full presence, 4 black holes |

### Enochian Text Corruption
UI text (Taps, Chain, Level, Score) progressively transforms into occult Runic/Baybayin glyphs. The corruption is **deterministic** — each character position has a fixed madness threshold based on a hash of its position and value. When madness exceeds that threshold, the character permanently flips to Enochian. This creates a creepy "one letter at a time" effect with no flickering. Values corrupt first (starting at 15% madness), labels corrupt later (starting at 40%).

---

## Fracturing System

Orbs shatter into progressively smaller pieces when popped, creating cascading chain reactions.

### Generation Tiers
| Generation | Size (% of base) | Spawns on Pop | XP Value | Notes |
|-----------|-----------------|---------------|----------|-------|
| Gen 0 | 100% | 2 gen-1 | 1.0 | Standard orbs |
| Gen 1 | 60% | 2 gen-2 | 0.5 | First fragments |
| Gen 2 | 35% | 1 gen-3 | 0.25 | Small shards |
| Gen 3 | 20% | Nothing | 0.15 | Terminal, tiny |

### Balance
- A single gen-0 orb can cascade into: 1 → 2 → 4 → 4 = 11 total pops from one initial orb
- With 50 base orbs, theoretical max = 550 pops per field clear
- MAX_CIRCLES (200) caps simultaneous orbs, preventing explosion
- Fragments have 200ms shrapnel immunity to fly out visibly
- Score = chainCount² makes long fracturing chains extremely valuable

### Field Clear Mechanic
If a chain reaction pops every single orb on screen (including all fracture generations), the player earns a **Field Clear bonus**: +3 taps and automatic biome advance. This is the theoretical endgame — a perfect chain that consumes everything.

---

## Cursor Interaction

### Brush Force
Mouse/touch movement generates a velocity vector. When the cursor moves near orbs at speed, it **pushes** them like brushing marbles on a table. The push force is proportional to cursor speed and inversely proportional to distance. Range: 60px + orb radius.

### Gravity Pull
Static cursor position gently attracts nearby orbs (inverse-square falloff). Combined with brush, this lets players herd orbs into clusters for better chains.

---

## ABYSS Biome

A special toggle-able biome (button or 'B' key) that transforms the game into a deep-sea horror experience.

### Visual Changes
- **Background**: Dark ocean gradients (#000a12 to #001018), bioluminescent haze blobs, slowly rising deep-sea particle motes
- **Orbs**: Abstract twisted geometry — warped polygons (5–8 sides) with animated vertex distortion driven by multiple overlapping sine waves. Each orb slowly rotates, breathes, and has:
  - Dark pulsing void center
  - Bioluminescent edge glow (accent color)
  - Internal geometric stress lines that shift and curve
  - Deterministic shape variation per orb ID (different side count, warp frequency, rotation speed)
  - NOT recognizable creatures — just unsettling, undulating geometric forms
- **Super Cells**: Tentacle horrors — writhing multi-segment appendages with suction cups, dark mantle body, bioluminescent slit-pupil eyes
- **Chain Links**: Tentacle-style connectors — sinuous cubic bezier curves with suction cup dots and bioluminescent traveling pulses (vs metaball blobs in normal biomes)
- **Audio**: Triangle wave oscillator at 180Hz base note — eerie, submarine

### Unique Mechanic: Abyssal Pressure
Every 8–14 seconds, a **pressure wave** pulses from the edges, crushing all orbs toward screen center over 1.5 seconds (sine-shaped intensity). This creates dense clusters perfect for chain reactions, but the compression is relentless — creating urgency to pop before orbs get too tightly packed.

---

## Motion & Visual Polish

### Orb Colors
Colors are assigned at creation based on screen position (cluster gradient from the current palette). Once assigned, colors are **persistent** — orbs keep their birth color as they drift, creating a natural mixing effect.

### Motion Blur
Fast-moving orbs leave ghost trails (up to 5 positions). Each trail point renders as a progressively smaller, more transparent copy of the orb, creating a natural motion blur effect. Trails fade when orbs slow down.

### Depth of Field
At high madness levels, smaller-generation orbs (gen-1, gen-2, gen-3) become slightly more transparent, creating the illusion that they exist at different depth planes — as if the field has become three-dimensional and some orbs have drifted deeper into the void.

### Color Synthesis
When orbs of sufficiently different colors overlap, they **synthesize** — blending toward each other's colors. This creates emergent color evolution across the entire field over time.

**Mechanics:**
- Spatial hash grid (50px cells) for O(n) proximity detection — avoids O(n²) brute force
- Color distance check: only triggers when `|r1-r2| + |g1-g2| + |b1-b2| > 80` (enough visual difference)
- Asymmetric blending: smaller orbs shift more toward larger ones (area-weighted, 8% per contact)
- Per-orb cooldown: ~0.5s between synthesis events (prevents rapid-fire)
- Max 5 synthesis events per frame (performance cap)
- Gentle push apart on contact (15px/s impulse)

**Visual effect:** 4 tiny sparks + a ripple glow ring at the contact point, colored with the blended result.

**Emergent behavior:** Over time, nearby orb clusters converge to similar colors while distant clusters remain distinct, creating organic color neighborhoods that shift as orbs drift.

---

## Rift Walls (Phase 4)

At 30%+ madness, energy barriers spawn on the field that **block blast propagation**. When a chain reaction hits a rift wall, orbs on the far side are shielded. Walls have 4 HP and take damage from overlapping blasts. When destroyed, they shatter and pop all orbs within 80px. Max 3 walls on field, spawning every 30 seconds. Forces the player to think about blast paths and wall destruction strategy.

---

## Gravity Bridges (Phase 6)

At 55%+ madness, orbs with the same `colorIdx` that remain within 3x their average radius for 2 seconds form **energy bridges**. Bridges allow blast propagation to travel along them — bypassing rift walls. When a connected orb pops, the bridge transmits the blast to its partner, then the bridge is consumed. Max 60 bridges. This creates an emergent network where spatial color clustering (from cluster coloring) becomes strategically important.

---

## Shadow Balls

At 55%+ madness, non-interactive dark phantom orbs fill the field. Up to 400 at full madness (10x the original count). They drift slowly, are pulled by black hole gravity, but cannot be popped or chained. Purely atmospheric — the field fills with dark shapes as reality deteriorates.

---

## Leviathan Bloat/Burst (Phase 3)

The Leviathan boss (ABYSS biome) consumes orbs and after eating 6+, enters a **bloated state** for 3 seconds. During bloat: the body inflates and pulses violently, movement slows dramatically, and the color shifts from teal to stressed red/green. **All damage during bloat is 3x** — the optimal attack window. If hit during bloat, the boss drops all held orbs as free pops ("HUNGER RUPTURES"). If the timer expires, the boss fires a shockwave scattering all nearby orbs and heals HP.

---

## Galaxy Background

The background behind the Watcher in default biomes is a **video** (`Images/GalaxyBG_test.mp4`) rendered in screen blend mode. Playback rate scales with madness: glacially slow at 0.03x (0% madness), rising to 0.25x at 100%. Smooth loop transitions prevent visible seams. Falls back to a static image (`Images/BG_Galax.jpg`) if video fails. Fades out in ABYSS biome. Max opacity 22%.

---

## Center Gravity & Background Darkening

At **75%+ madness**, all orbs feel an increasing gravitational pull toward the center of the field. Strength ramps from 0 to 60 force units at 100% madness, with inverse-sqrt falloff. Combined with multiple black holes, this creates a collapsing-reality effect.

At **80%+ madness**, the background gradient is pushed aggressively toward near-pure-black. By 100% madness, the field is essentially black with only the faintest hint of color remaining.
