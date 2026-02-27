# POP FIELD — Changelog

---

## Session 5 — 2026-02-27

### Terminology Change
- **"Madness" → "World Transparency" (WT)** — all future references use WT. Code still uses `getMadnessAmount()` internally.

### Mutation Phase 1 — Smooth Void Seed
- Added "Void Seed" layer: subtle dark center gradient fading in from very first eyeIntensity
- Quadratic ease-in (`voidT²`) for imperceptible start, growing to visible dark spot
- Subtle per-orb breathing pulse for organic feel
- **Fixed pupil alpha jump:** All 3 eye types (manic/glass/sunken) now fade in pupil with `irisT` multiplier — eliminates the 50-70% opacity hard jump at threshold

### Desaturation Curve — Less Steep
- Phase 1: widened from 0-35% → 0-50%, pull reduced from 0.80 → 0.65
- Phase 2 (flesh tint): shifted from 20-60% → 30-65%
- Phase 3 (deep drain): shifted from 50-85% → 60-88%, pull reduced to 0.45
- Phase 4 (total ash): shifted from 85-100% → 88-100%
- Colors retain vibrancy significantly longer through mid-game

### Rift Walls → Space Canyon Rifts
- **Visual overhaul:** 6-layer rendering replaces simple purple line
  - Ambient void glow, jagged canyon edges with breathing animation
  - Dark void interior, flickering energy veins inside
  - Sparks/debris being pulled in, radial endpoint flares
- **Stronger mechanics:** HP 4→5, max walls 3→4, spawn interval 30s→24s, shatter pop radius 80→110px
- **New: Gravitational pull** — orbs within 120px drift toward the rift (35 force units)

### World Transparency Milestone Banners
- Dramatic full-width text banners at 50%, 75%, 90% WT thresholds
- 50%: "THE MEMBRANE THINS" (purple), 75%: "REALITY UNRAVELS" (pink-red), 90%: "THE OTHER SIDE BLEEDS THROUGH" (red)
- 3.5s duration with fade-in/hold/fade-out envelope
- Screen shake on trigger, dark band + accent lines for readability

### Void Stars — 2000 Background Stars
- 2000 twinkling stars from "the other side" bleeding through the membrane
- Appear at 35%+ WT, full visibility by ~70% WT
- 6 color varieties: cool white, pale blue, warm gold, pale violet, ice blue, dusty rose
- Individual blink rates (0.4-2.9 speed) and depths (15-75% variation)
- **Black hole reactive** — gently pulled with tangential swirl, deeper stars less affected
- **NOT cursor reactive** — immune to player cursor pull
- Bright stars get subtle cross-flare (+) effect
- Rendered between nebula clouds and paint splats (Layer 1c2)

### Bug Fix
- Deleted `bug.md` — black screen bug from Session 4 is resolved
- Game loads and runs correctly on both ports

---

## Session 4 — 2026-02-27

### Progression Phases 3-6 Implemented

#### Phase 3: Leviathan Bloat/Burst
- **Bloat state**: After eating 6+ orbs, Leviathan enters BLOAT (3s window) with 3x damage vulnerability
- **Successful burst**: Hitting during bloat drops all eaten orbs as free pops, "HUNGER RUPTURES" text
- **Failed window**: Shockwave scatters orbs + boss heals 30%, "IT FEEDS" text
- **Visuals**: Stressed red/green color shift, body wobble, pulse ring, countdown arc

#### Phase 4: Rift Walls
- **Energy barriers** spawn at 30%+ madness (up to 3 walls), blocking blast propagation
- Walls have 4 HP; blast radius contact damages them. Destroying walls pops nearby orbs
- Line-segment intersection test blocks chain propagation through walls
- Crackling purple energy lines with HP indicator pips
- Lore text: "MEMBRANE FRACTURE", "THE SCAR HARDENS", "THE MEMBRANE TEARS"

#### Phase 5: Archon Tether Rework
- **Tether formation**: Archons (SuperCells) tether to 2-4 nearby orbs on mutation
- **Implosion mechanic**: Popping the Archon rushes all tethered orbs inward + auto-pops them
- Gravity well scales with tether count (strength + life bonus per tether)
- Tether rendering: outer energy beam + inner bright core + glow rings + midpoint nodes
- Lore text: "THE ARCHON REMEMBERS", "FRAGMENTS REUNITE", "WHOLENESS DEVOURS"

#### Phase 6: Gravity Bridges
- **Same-color orbs** within 3x radius form bridges after 2s proximity
- Color matching uses `colorIdx` (palette segment index) — immune to horror desaturation
- Bridge propagation in `popCircle()`: blast travels along bridges, bypassing rift walls
- Spatial hash grid (cellSize 60) for O(n) proximity checks, timer-gated (0.3s)
- Thin pulsing lines (12-30% alpha) with glow nodes at endpoints. Max 60 bridges
- `getClusterColor()` now returns `{ color, colorIdx }` throughout

### Madness & Atmosphere Overhaul

#### Desaturation Rework
- **Faster B1 desaturation**: Phase 1 completes by 35% (was 50%), Phase 2 starts at 20% (was 30%)
- **Total desaturation at end**: New Phase 4 (85-100%) — all color dies, converges to dead grey-brown ash with final dimming. No color survives at 100%
- Background horrorShift at 0.7x madness (was 0.5x), with aggressive push to pure black above 80%

#### Background & Stars
- **Galaxy background replaced with looping video** (`GalaxyBG_test.mp4`)
  - Madness-scaled playback: 0.03x (glacial) at 0% → 0.25x at 100%
  - Smooth loop transitions: ramps down at video end, opacity dip, ramps back up at start
  - Falls back to static image if video fails
- Stars fade 2x faster with madness (field stars and galaxy stars)
- Nebulae peak earlier (30-40%) and gone by 55% (was 70%)

#### Shadow Orbs (10x increase)
- Max count: 400 (was 40), spawn threshold lowered to 55% (was 70%)
- 5x faster spawn rate with batch spawning (up to 5 per tick)
- Size range expanded: 2-12px (was 3-9px)

#### Orb Size Scaling
- Base orb size grows up to 1.85x at 100% madness (was 1.5x — about 35% bigger)
- Wider random size variation range (+30px max)

#### Multiple Eyes at High Madness
- At 85%+ madness: extra smaller eyes sprout on each orb surface
- 2 extra eyes per 7.5% above 85% (2 at 85%, 4 at 92.5%, 6 at 100%)
- Extra eyes blink independently, track cursor, have desaturated grey irises

#### Center Gravity Pull
- Above 75% madness, all orbs feel increasing gravitational pull toward field center
- Inverse-sqrt distance falloff, reaching 60 force units at full madness
- Creates "reality collapses inward" effect

### Grotesque End-Biome Monsters
- Archon (SuperCell) at 60%+ madness becomes progressively disfigured:
  - **Torn membrane wings** (2-5 wings with jagged tears, bone veins)
  - **Extra disfigured limbs** (+5 pseudopods with knobby joints)
  - **Visible rib cage** (3-7 curved bone arcs through body)
  - **+8 more eyes** extending along arms with dilated pupils
  - All colors fully desaturated — body, irises, sclera, motes

### Multiple Black Holes
- Converted `G.blackHole` (single) to `G.blackHoles[]` (array)
- Max simultaneous: 1 below 75% madness, ramping to 4 at 100%
- Shorter spawn intervals at high madness (15-25s vs 25-45s)
- All systems updated: shadow ball feeding, field star gravity, vortex swirl, rendering

### Boss-Orb Physical Interaction
- **Push zone** (1.2x boss radius): nearby orbs get physically repelled outward
- **Eat zone** (0.3x boss radius): orbs too close to boss core get consumed
- Leviathan eats more aggressively (1.5% vs 0.5%); consumed orbs feed bloat counter
- Erratic bosses push harder (250 vs 150 force)

### Gameplay Tuning
- **Density upgrade reworked**: Now +6% orb mass per level (was flat +6 orbs). Scales proportionally with field size and madness multiplier
- Boss spawn faster for testing: first boss at 15% madness, cooldown 10s
- Madness curve accelerated: starts at 10k score, 50% at 200k, 100% at 400k

### Visual Polish
- **Eye redesign**: 3 variations per orb (Manic Stare, Glass Eye, Sunken Hollow) with organic rendering
- **Chain count HUD**: Now lingers 2s after chain ends with fade-out
- **Hover proximity line**: More pronounced gradient with radial bloom at orb end
- **Lore text** added to all new mechanics (rift walls, archon implosion, leviathan bloat)

---

## Session 3 — 2026-02-26

### Madness System Overhaul
- **Unified 0%–100% madness scale**: All horror/weird effects now reference a single `getMadnessAmount()` function (0.0–1.0). Score + black hole horrorBoost combined, capped at 1.0
- **Eyeball intensity tied to madness** instead of raw score thresholds. The eye formation is now a smooth function of the unified madness value
- **Progressive eye formation** redesigned into 4 phases:
  - Phase 1 (10-25% madness): Dark spot/hole in center — looks like shadow, NOT recognizably an eye
  - Phase 2 (25-40%): Fleshy sclera emerges, rim forms
  - Phase 3 (40-65%): Iris visible, cursor tracking, veins spreading
  - Phase 4 (65-100%): Full detailed eye with fibers, specular highlights, branching veins

### Enochian Text Corruption
- **Deterministic corruption**: Each character position has a fixed madness threshold (based on hash). When madness exceeds that threshold, the character permanently flips to an occult glyph. No more per-frame random flickering
- **"One letter at a time" effect**: Letters corrupt individually as madness creeps up, creating slow creeping dread
- **Two-tier scaling**: Values (numbers) corrupt starting at 15% madness. Labels (Taps/Chain/Level/Score) corrupt starting at 40%
- Uses Unicode Runic (Elder Futhark) + Baybayin characters — renders natively, no custom font needed

### Color Synthesis
- When orbs of different colors overlap, they **blend toward each other's colors**
- Spatial hash grid (50px cells) for O(n) proximity detection
- Asymmetric blending: smaller orbs shift more toward larger ones (area-weighted)
- Visual: 4 sparks + ripple glow at contact point
- Per-orb cooldown (~0.5s) prevents rapid-fire. Max 5 events/frame for performance
- Creates emergent color evolution — nearby clusters converge over time

### ABYSS Biome — Abstract Geometry
- **Replaced** 4 specific creature types (jellyfish/anglerfish/worm/spore) with **abstract twisted geometry**
- Each orb is a warped polygon (5-8 sides) with animated vertex distortion
- Multiple overlapping sine waves create organic warping
- Features: slow rotation, breathing, dark pulsing void center, bioluminescent edge glow, internal geometric stress lines
- Deterministic variation per orb ID (side count, warp frequency, rotation speed)
- Goal: unsettling without being recognizable as any particular creature

### The Watcher
- **Opacity reduced to 15%** of original (was 30%). Barely visible background presence
- Eye glow overlay reduced proportionally (4% strength, was 8%)

### Documentation
- Updated DESIGN.md: SC descriptions (amorphous eye-cluster, not angels), ABYSS abstract geometry, color synthesis section, madness progression table, eyeball phases, Watcher image-based design, key constants, rendering layers
- Updated madness section with full 0%-100% progression table
- Created CHANGELOG.md

### Documentation Audit & Fixes
- Fixed Generations section: added gen-3 (20%), corrected sizes (60%/35%/20% not 50%/25%)
- Fixed Orb Types: replaced "mini orbs at milestones" with fracture fragments description
- Fixed Black Hole gravity radius: 250px → 320px in behavioral description (constants table was already correct)
- Added cursor brush force as step 5 in Movement Model (was 6 steps, now 7)
- Added ABYSS tentacle-style chain link variant to Chain Links section
- Added keyboard 'B' toggle and taps-during-chains to Input section
- Fixed SC lifecycle: "angel form" → "SC form (eye-cluster or tentacle horror)"
- Created CODE_MAP.md: complete codebase navigation guide (sections, functions, state, render pipeline)
- Updated LORE.md: added The Madness, The Watcher, The Abyss, Color Synthesis lore sections

---

## Session 2 — 2026-02-25

### Major Features
- **The Watcher**: Replaced procedural vector rendering with background image (`images/BG_test1.jpeg`). Cover-fit canvas with breathing scale + parallax drift. Eye glow overlay during chains
- **Fracturing System**: 4-tier orb shattering (gen-0 → gen-1 → gen-2 → gen-3). XP scales by generation. 200ms shrapnel immunity on fragments
- **Black Hole Orbital Mechanics**: Increased gravity (BH_STRENGTH 350→800), added tangential force (BH_TANGENT_STRENGTH=0.55) for visible orbital motion, wider gravity radius (250→320)
- **Horror Progression via Black Holes**: `horrorBoost` state — black hole proximity permanently adds to horror. Collapse events cause large jumps
- **ABYSS Biome**: Special toggle biome with deep-sea theme, unique SC (tentacle horror), tentacle chain links, abyssal pressure waves (periodic crushing force), bioluminescent background effects
- **Field Clear Mechanic**: Clearing all orbs advances biome + grants 3 bonus taps

### Gameplay Changes
- **Taps during active combos**: Removed chain-active guard — player can now tap while a chain is running
- **Persistent orb colors**: Colors assigned at creation and kept permanently (no per-frame position-based update)
- **Cursor brush force**: Moving cursor pushes nearby orbs in movement direction

### Visual Polish
- **Motion blur/trails**: Fast-moving orbs leave ghost trail copies (max 5 positions)
- **Depth-of-field**: Higher-generation orbs fade at high madness
- **SC redesign**: Default = amorphous eye-cluster entity. ABYSS = tentacle horror
- **ABYSS chain links**: Tentacle-style cubic bezier with suction cups + bioluminescent pulse
- **ABYSS background**: Bioluminescent haze blobs + rising deep-sea particle motes

### Documentation
- Created DESIGN.md with comprehensive game design documentation
- Added sections: Madness System, Fracturing System, Cursor Interaction, ABYSS Biome, Motion & Visual Polish

---

## Session 1 — Initial Development

### Core Game
- Single-file HTML5 canvas game, ~4000 lines, no dependencies
- Chain reaction popping mechanic with score = chainCount²
- 10 upgrades, 5 palette phases, XP/leveling system
- Super Cells, bombs/mines, black holes, delayed blast mega-chains
- Particle system (800 pool), paint splats, screen effects (shake, chromatic aberration, white glow)
- Audio: Web Audio API with per-pop tones scaling by chain index
- Touch + mouse input with cursor gravity
