# POP FIELD — Progression Rework Plan
## Phased Implementation (testable after each phase)

---

## PHASE 1: B1 Madness Color Fix (cosmetic, no mechanics)
**Goal:** Fix the "B1 gets boring at high madness" problem.
**Change:** Replace desaturation-to-grey with chromatic intensification.
- Low madness: warm natural palette (current Phase I-II)
- Mid madness: colors shift toward neon/electric — oversaturated, hue-rotated
- High madness: chromatic splitting, reality-breaking colors. Horror comes from SHAPES (eyes, Watcher, breathing), not color death
- Keep the flesh/bile tint for the Watcher and eye overlays specifically, but the orbs and explosions should look MORE spectacular, not less

**Test:** Play B1 to high madness. Chains at 80%+ madness should look more dramatic than at 20%, not washed out.

---

## PHASE 2: Boss Mechanic — Nexus Shield Tethers
**Goal:** Give the Nexus boss an interactive mechanic (not just "pop near it").
**Change:** Nexus tethers to 3-4 nearby orbs with energy beams.
- Tethered orbs are shielded (can't be chain-popped, only direct-tapped)
- Breaking all tethers drops the shield for 4s — boss is vulnerable
- Shield regenerates after 4s (new tethers form)
- Visual: glowing lines from boss eyes to tethered orbs

**Test:** First boss fight at ~45% madness should feel like a puzzle. Player must target specific orbs.

---

## PHASE 3: Boss Mechanic — Leviathan Bloat/Burst
**Goal:** Give Leviathan a risk/reward timing window.
**Change:** After eating 6+ orbs, Leviathan enters BLOAT state (3s window).
- Bloated: swollen, slower, pulsing — takes 3x damage
- If player damages during bloat: boss takes massive hit + drops all eaten orbs back as free pops
- If player misses the window: boss releases shockwave (scatters all orbs to edges) and heals 30% HP
- Visual: boss visibly inflates, color shifts to stressed red/green, countdown pulse

**Test:** Leviathan fight should have clear "attack now!" moments. Missing the window should feel punishing but recoverable.

---

## PHASE 4: Rift Walls (mid-game spatial puzzle)
**Goal:** Fill the empty post-first-boss phase with spatial challenge.
**Change:** At madness >55%, semi-transparent barrier lines appear on the field.
- 2-3 walls per set, positioned randomly
- Walls BLOCK blast propagation (chain explosions stop at wall)
- Orbs drift THROUGH walls freely (walls are energy barriers, not physical)
- Walls have HP (3-4 hits from blast contact to destroy)
- Wall destruction: satisfying shatter effect + all orbs near wall get popped
- New walls spawn every 30-45 seconds while madness >55%
- B1 walls: crackling purple energy lines
- B2 walls: bioluminescent membrane sheets

**Test:** Mid-game chains require thinking about wall placement. Destroying walls feels like a mini-victory. Chains that punch through a wall by destroying it feel epic.

---

## PHASE 5: Archon Tether Rework (mid-game event)
**Goal:** Make Archon appearances feel like special events worth setting up.
**Change:** When an Archon mutates, it creates energy tethers to 2-4 nearby orbs.
- Tethered orbs pulse in sync with the Archon
- Popping the Archon triggers IMPLOSION: all tethered orbs rush inward and auto-pop
- The implosion creates a concentrated gravity well (stronger than current)
- If tethered orbs are popped first, the Archon loses tethers (weaker implosion)
- Player choice: pop Archon for big implosion, or pick off tethers for safer chain

**Test:** Archon appearances at level 5+ create visible "constellations" of connected orbs. Popping the center Archon feels like detonating a cluster bomb.

---

## PHASE 6: Gravity Bridges (late-game connection mechanic)
**Goal:** Add the "connection-based" mechanic for pre-peak gameplay.
**Change:** At madness >70%, same-color orbs that stay near each other form visible energy bridges.
- Bridge forms after 2s of proximity (within 3x radius of each other)
- When one bridged orb pops, blast TRAVELS ALONG BRIDGE to connected orb (instant, ignores distance)
- Bridge chains can cascade: A→B→C if B and C are also bridged
- Visual: thin glowing line between bridged orbs, pulses with color
- Cursor herding becomes strategic: push same-color orbs together to build bridge networks
- Bridge network detonation = screen-filling chain reactions

**Test:** At high madness, the field should develop visible "constellations" of bridged orbs. One good tap into a bridge network should cascade across the entire field. This is the late-game mastery moment.

---

## PHASE 7: Peak Boss Polish
**Goal:** Make the final boss encounters feel unique and beatable.
**Changes:**
- **Peak Nexus:** Periodically creates gravity INVERSION zones (3s duration, pushes all orbs outward in a radius). Player must time taps between inversions. Visual: reality-distortion sphere expanding from boss.
- **Peak Leviathan:** Bloat mechanic intensified — eats faster, bloat window shorter (2s), but reward is bigger (5x damage window). On shockwave release: creates temporary whirlpool that sucks orbs back in (sets up next cycle).
- Both peak bosses: reduce regen rate during vulnerable states
- Floating text hints: "SHIELD DOWN!" / "IT BLOATS!" to teach mechanics

**Test:** Peak bosses should feel hard but learnable. Each death should teach the player something. Victory should feel earned.

---

## PHASE 8: Lore & Polish Pass
**Goal:** Integrate all new mechanics into the game's lore/naming.
- Rift Walls: "Membrane Fractures" (B1) / "Pressure Walls" (B2)
- Gravity Bridges: "Resonance Threads" (B1) / "Symbiotic Links" (B2)
- Bloat state: "Gorged" / "Engorged"
- Shield tethers: "Soul Tethers" (B1) / "Neural Threads" (B2)
- Update BIOME_LORE with phase-appropriate mentions
- Update LORE_SPLASHES with new mechanic lore
- Update changelog

---

## Implementation Order & Dependencies
```
PHASE 1 (cosmetic) ─── can ship alone, no deps
PHASE 2 (Nexus)    ─── can ship alone
PHASE 3 (Leviathan)─── can ship alone
PHASE 4 (Walls)    ─── can ship alone, biggest gameplay impact
PHASE 5 (Archons)  ─── can ship alone
PHASE 6 (Bridges)  ─── needs Phase 4 walls to avoid visual clutter
PHASE 7 (Peak Boss)─── needs Phase 2+3 base mechanics
PHASE 8 (Lore)     ─── after all mechanics are final
```

Each phase is independently testable. Ship and playtest each one before moving to the next.
