# POP FIELD — Session State (for next Claude instance)

## Last Session: Session 10 — 2026-02-27

### Session History (Recent)

**Session 10** — B3 THE EXPANSE desert biome
- New special biome (`biomeOverride === 2`) with full visual identity
- WEIGHT mechanic: 45% orb count, 2.5× orb size (sparse giants)
- Weathered orb rendering: rough polygons, inner shadow, rim light, cracks
- Desert background: horizon glow, heat haze blobs, 200 sand particles
- 4 red eye pairs at screen edges with independent blinking
- 2× XP, 1.5× score multiplier for ancient orbs
- Debug button "Expanse" at top:248px, keyboard shortcut X
- 3-way mutual exclusion (Abyss/Test/Expanse)
- Boss spawn guard (no boss yet for Expanse)
- 5 desert lore quotes from game-name-biomes.md

**Session 9** — LT polish, visual fixes, gameplay tuning
- Softer center light (lightR 840→1050, gentler gradient curve)
- 4-pass feathered shadow edges (replaces 2-layer flat shadows)
- Per-orb saturation shift by light proximity (+15% near, -30% far)
- Eye sclera covers full orb (0.85-0.92× → 0.96-1.0×), eliminates colored ring
- Void Seed shapes match eye variant (V2 vertical slit, V3 horizontal ellipse)
- Dark Prism less frequent (balls 8→12, radius 25→35, threshold 7→10, cooldown 2→5s)
- Mini BH 2× wider gravity radius (150→300)
- Level 6 density dampening at low WT (25% fewer orbs, scales to normal by 40% WT)
- Faster zoom-out (score curve 400k→250k, lerp dt*2→dt*3.5)
- Lore quotes on WT milestone banners, banner duration 3.5→5s

**Session 8** — Wormhole, Dark Prism, gameplay tuning
- Wormhole spawns at peak madness after 4 boss kills in B1
- Smooth gravity inversion (ease in/out, 1.8s cooldown)
- Purple radial pulse + shadow orb growth during Dark Prism
- Mini black hole debug spawn button
- Coming Next roadmap in changelog modal
- Stronger cursor pull near cursor (close-range bonus)
- Reduced gravity reaction at high WT
- More saturated B1 orb palette, reduced orbBrighten wash
- Archon tethers: more connections, inter-tether web, sigil-seeking
- Fix orb teleport after splash screens

**Session 7** — LT Lighting Engine
- TESTLEVEL lighting engine (`biomeOverride === 1`)
- Point light at cursor with tangent shadow casting from orbs
- Dynamic light sources: mini black holes, chain links, explosions
- Orb self-glow, dust layer, soft penumbra shadows
- Screen-space compositing (bypasses zoom transform)
- Boss visual overhaul: 4 vector mouth variants (fanged, screamer, serrated, abyssal maw)
- Bigger eyes within orb containers, enhanced sclera/iris

**Session 6** — Unified 4-Variant Vector Eye System
- Replaced old 3-type (manic/glass/sunken) with 4 variants: V0 Pale, V1 Striated, V2 Slit, V3 Cracked
- Removed all hard circular outlines from orb eye rendering
- Extra eyes at 85%+ WT use 4-variant system

**Session 5** — WT system, visual overhaul, atmosphere
- "Madness" → "World Transparency" (WT) terminology
- Mutation Phase 1 (Void Seed), desaturation curve gentler
- Rift Walls → Space Canyon Rifts (6-layer rendering)
- WT Milestone Banners at 50%/75%/90%
- 2000 Void Stars (BH-reactive, not cursor-reactive)

### Terminology
- **"Madness" is now "World Transparency" (WT)** in all design docs and conversation
- Code still uses `getMadnessAmount()`, `G.horrorBoost`, etc. internally
- The first eye mutation phase is called **"Mutation: Phase 1"**
- **LT** — Lightning Test biome (`biomeOverride === 1`). Debug lighting engine with shadow casting, light sources, saturation effects, and feathered multi-pass shadows. Toggle: T key or TESTLEVEL button.
- **B3 / THE EXPANSE** — Desert biome (`biomeOverride === 2`). Sparse giant orbs, weathered aesthetic, sand particles. Toggle: X key or Expanse button.
- **Dark Prism** — Shadow ball cluster event. When 10+ shadow balls within 35px radius, triggers gravity inversion + purple pulse + shadow orb growth. Cooldown 5s.

### Pending / Discussed But Not Implemented

- **Shadow Orb Gameplay Mechanic** — Currently shadow orbs are purely cosmetic (spawn at 55%+ WT, up to 400, non-interactive). Three gameplay alternatives were proposed:
  - **Option A: "Resonance Parasites"** — Shadow orbs absorb blast energy, creating dead zones. Spend taps to purge them.
  - **Option B: "Void Echoes"** — Shadow orbs get charged by passing blasts, detonate after 1.5s delay for secondary chain wave. Uncharged ones hatch into hazards.
  - **Option C: "The Membrane"** — Shadow orbs release Void Essence currency when caught in blasts. Spend on abilities, but harvesting accelerates WT. Unharvested ones heal bosses.
  - **No choice was made yet** — user needs to pick a direction.

- **Fluid/Liquid Physics near Black Holes** — DIY SPH (Smoothed Particle Hydrodynamics) approach was recommended for liquid-like orb behavior near black holes. The game already has 80% of the infrastructure (velocity-based physics, gravity, spatial hash grids). Not implemented — user was still evaluating options.

- **Nexus Boss Upgrades** — Detailed brief in `nexus-brief.md`:
  - Void Beam: charges toward last tap position, fires destructive beam vaporizing orbs
  - Singularity Pulse: big move with counter-play opportunity
  - Not yet implemented.

- **New Biome Concepts** — Detailed in `biome-concepts.md`:
  - **The Eclipse** — inverse biome where shadow balls are primary, normal orbs are phantoms
  - **The Bloom** — organic/plant-growth biome
  - **The Delirium** — self-referential geometry biome (detailed in `delirium-concept.md`)

- **Music System** — Comprehensive per-biome audio design in `music-concepts.md`. Procedural Web Audio API, tied to lore.

- **Progression Plan Phase 7** — Peak Boss Polish: not started
- **Progression Plan Phase 8** — Lore & Polish Pass: not started

### Known State
- Game runs on `http://localhost:9090/index.html` (python3 http.server)
- Port 8080 may have a different prototype running
- Server config: `.claude/launch.json` → port 9090
- Latest backup: `backups/index_backup_20260227_181311.html` (502KB, Session 10)
- `index.html` is ~13,353 lines, single-file game
- Git: 5 commits, HEAD is `b981cb9` (Session 10)

### Key Files
| File | Purpose |
|------|---------|
| `index.html` | The entire game (HTML + CSS + JS, ~13,353 lines) |
| `DESIGN.md` | Comprehensive design doc (newer, more detailed than GAME_DESIGN.md) |
| `GAME_DESIGN.md` | High-level design doc (original, kept updated) |
| `GAME_MECHANICS.md` | Detailed mechanics reference (constants, formulas) |
| `CHANGELOG.md` | Session-by-session changelog (Sessions 1-6; 7-10 in git commits only) |
| `CODE_MAP.md` | Codebase navigation (sections, functions, render pipeline) — **outdated, reflects ~4354 lines** |
| `LORE_BIBLE.md` | Deep lore (The Substrate, Enumerators, etc.) |
| `LORE.md` | In-game lore text |
| `BALANCE_GUIDE.md` | Balancing philosophy |
| `PLAN-progression-rework.md` | 8-phase progression plan (Phases 1-6 done, 7-8 pending) |
| `boss-design.md` | Extended 5-boss design (future direction) |
| `nexus-brief.md` | Nexus boss upgrade brief (Void Beam + Singularity Pulse) |
| `biome-concepts.md` | 3 new biome concepts (Eclipse, Bloom, Delirium) |
| `delirium-concept.md` | Deep dive on Delirium biome |
| `futureideas.md` | Lighting engine research, implementation plans |
| `music-concepts.md` | Per-biome procedural audio design |
| `game-name-biomes.md` | Naming concepts & biome design ideas |
| `layered-music-guide.md` | Layered music system reference |
| `quotes-md.md` | Lore quotes collection |
| `SESSION_STATE.md` | This file |

### Code Architecture Quick Reference
- Global state: `G` object (~80+ properties)
- Main loop: `gameLoop()` → `update(dt)` + `render()`
- Key functions: `popCircle()`, `updateCircles()`, `renderCircles()`, `getMadnessAmount()`
- Render pipeline: 21+ layers from background to HUD
- Biomes: B1 (default, 5 level-based palettes), ABYSS (`biomeOverride === 0`), LT (`biomeOverride === 1`), EXPANSE (`biomeOverride === 2`)
- Dev buttons: ABYSS (B key), TESTLEVEL/LT (T key), EXPANSE (X key), MADNESS (force 100% WT), AUTO LVL, INFINITE TAPS, Mini BH
- Dev madness override: `devMadnessOverride` variable (set via console or MADNESS button)
- Eye system: 4 vector variants (V0 Pale, V1 Striated, V2 Slit, V3 Cracked), `eyeVariant = c.id % 4`
- Shadow casting: LT biome only, tangent projection from cursor light source

### Progression Plan Status
| Phase | Feature | Status |
|-------|---------|--------|
| Phase 1 | B1 Madness Color Fix | **Done** (Session 5 curve + Session 9 palette tuning) |
| Phase 2 | Nexus Shield Tethers | Done (Session 4) |
| Phase 3 | Leviathan Bloat/Burst | Done (Session 4) |
| Phase 4 | Rift Walls | Done (Session 4) + Visual overhaul (Session 5) |
| Phase 5 | Archon Tether Rework | Done (Session 4) + Enhanced (Session 8) |
| Phase 6 | Gravity Bridges | Done (Session 4) |
| Phase 7 | Peak Boss Polish | Not started |
| Phase 8 | Lore & Polish Pass | Not started |

### What Needs Updating
- **CHANGELOG.md** — Missing Sessions 7-10 entries (info is in git commit messages)
- **CODE_MAP.md** — Outdated, reflects ~4354 lines. Game is now ~13,353 lines. Needs complete rewrite.
- **GAME_MECHANICS.md** — May be missing Sessions 7-10 additions (LT engine, Dark Prism, Wormhole, Expanse biome)
