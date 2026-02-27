# POP FIELD — Session State (for next Claude instance)

## Last Session: Session 5 — 2026-02-27

### What Was Done
1. **Mutation Phase 1 (Void Seed)** — Smooth transition from normal orb to dark center. Added void seed gradient + fixed pupil alpha jump in all 3 eye types.
2. **Desaturation curve** — Made less steep/later. Colors retain vibrancy longer (Phase 1: 0-50% at 0.65 pull, was 0-35% at 0.80).
3. **Rift Walls → Space Canyon Rifts** — Complete visual overhaul (6-layer rendering) + stronger gameplay (HP 5, max 4, gravity pull 120px range, wider shatter).
4. **WT Milestone Banners** — Big dramatic text at 50%/75%/90% World Transparency thresholds.
5. **Void Stars** — 2000 twinkling background stars appearing at 35%+ WT, BH-reactive, not cursor-reactive.
6. **Deleted bug.md** — Black screen bug from Session 4 is resolved.

### Terminology
- **"Madness" is now "World Transparency" (WT)** in all design docs and conversation
- Code still uses `getMadnessAmount()`, `G.horrorBoost`, etc. internally
- The first eye mutation phase is called **"Mutation: Phase 1"**

### Pending / Discussed But Not Implemented
- **Shadow Orb Gameplay Mechanic** — Currently shadow orbs are purely cosmetic (spawn at 55%+ WT, up to 400, non-interactive). Three gameplay alternatives were proposed:
  - **Option A: "Resonance Parasites"** — Shadow orbs absorb blast energy, creating dead zones. Spend taps to purge them.
  - **Option B: "Void Echoes"** — Shadow orbs get charged by passing blasts, detonate after 1.5s delay for secondary chain wave. Uncharged ones hatch into hazards.
  - **Option C: "The Membrane"** — Shadow orbs release Void Essence currency when caught in blasts. Spend on abilities, but harvesting accelerates WT. Unharvested ones heal bosses.
  - **No choice was made yet** — user needs to pick a direction.

### Known State
- Game runs on `http://localhost:9090/index.html` (python3 http.server)
- Port 8080 may have a different prototype running
- Server config: `.claude/launch.json` → port 9090
- Latest backup: `backups/index_backup_20260227_103356.html` (410KB, all Session 5 changes)
- `index.html` is ~10,400 lines, single-file game
- Git: 2 commits (`088e54e` = current WIP, `0669186` = initial working)

### Key Files
| File | Purpose |
|------|---------|
| `index.html` | The entire game (HTML + CSS + JS) |
| `GAME_DESIGN.md` | High-level design doc |
| `GAME_MECHANICS.md` | Detailed mechanics reference (constants, formulas) |
| `CHANGELOG.md` | Session-by-session changelog |
| `LORE_BIBLE.md` | Deep lore (The Substrate, Enumerators, etc.) |
| `LORE.md` | In-game lore text |
| `CODE_MAP.md` | Codebase navigation (sections, functions, render pipeline) |
| `BALANCE_GUIDE.md` | Balancing philosophy |
| `PLAN-progression-rework.md` | 8-phase progression plan (Phases 1-6 done, 7-8 pending) |
| `boss-design.md` | Extended 5-boss design (future direction, not implemented) |
| `SESSION_STATE.md` | This file |

### Code Architecture Quick Reference
- Global state: `G` object (~80 properties)
- Main loop: `gameLoop()` → `update(dt)` + `render()`
- Key functions: `popCircle()`, `updateCircles()`, `renderCircles()`, `getMadnessAmount()`
- Render pipeline: 15+ layers from background to HUD
- Dev buttons: ABYSS (toggle biome), MADNESS (force 100% WT), AUTO LVL, INFINITE TAPS
- Dev madness override: `devMadnessOverride` variable (set via console or MADNESS button)

### Progression Plan Status
| Phase | Feature | Status |
|-------|---------|--------|
| Phase 1 | B1 Madness Color Fix | **Partially done** (Session 5 made curve gentler) |
| Phase 2 | Nexus Shield Tethers | Done (Session 4) |
| Phase 3 | Leviathan Bloat/Burst | Done (Session 4) |
| Phase 4 | Rift Walls | Done (Session 4) + Visual overhaul (Session 5) |
| Phase 5 | Archon Tether Rework | Done (Session 4) |
| Phase 6 | Gravity Bridges | Done (Session 4) |
| Phase 7 | Peak Boss Polish | Not started |
| Phase 8 | Lore & Polish Pass | Not started |
