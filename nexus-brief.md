# NEXUS BOSS UPGRADE BRIEF
## Features: Void Beam (Direct Threat) + Singularity Pulse (Big Move w/ Counter)

---

## OVERVIEW

Two new attack states added to the Nexus state machine. Both follow a
"telegraph → danger → counter-play" loop inspired by Dark Souls design:
the player reads the tell, then decides whether to play safe or go aggressive
for a reward.

---

## FEATURE 1 — VOID BEAM

### Concept
The Nexus targets the player's last tap location and fires a devastating beam
that destroys orbs in its path. Punishes mindless tapping near the boss and
forces spatial awareness — "where I tap matters, not just when."

### New State: `charging_beam` → `firing_beam`

**Trigger:**
- Replaces one `absorbing` cycle. After `idle` ends, 30% chance to enter
  `charging_beam` instead of `absorbing` (if `bossKillCount >= 1`, so the
  very first Nexus fight stays simple).
- Erratic Nexus: added to the random state pool with equal weight.

**Phase 1 — `charging_beam` (2.0s):**
- All eyes on the Nexus rotate to converge on a single focal point on the
  body's edge (the "muzzle" — the point on the body perimeter closest to
  `G.lastTapX, G.lastTapY`).
- Visual: pulsing purple energy gathers at the muzzle point. A thin dashed
  "laser sight" line extends from muzzle toward the target position, alpha
  ramping 0 → 0.6 over the 2s. Screen-shake intensity 1, rising to 3.
- Audio cue: increasing hum (if sound exists), or intensifying screen-shake.
- The target position is **locked** 0.5s before firing (the dashed line
  freezes). This gives the player the final read — "don't tap there."

**Phase 2 — `firing_beam` (0.6s):**
- A wide beam (width = `b.radius * 0.6`, length = `900px`) fires from
  muzzle through the locked target direction.
- Any **non-tethered** orb whose center falls within the beam rectangle is
  **destroyed** (removed, no pop, no chain — just vaporized with a brief
  white flash particle).
- The beam does NOT damage the player's taps directly — it destroys field
  orbs, reducing chaining potential. Losing 5-10 orbs *hurts*.
- Orbs destroyed by the beam do NOT heal the boss (unlike eating).
- Visual: bright white core (#ffffff, width * 0.3) inside purple-pink outer
  glow (#d050ff → #8020a0 radial). Flickers at 20Hz. Fade out over the
  last 0.2s.

**After firing:** Transitions to `idle` with a 4-5s timer (longer than normal
idle, giving the player a recovery window).

### Erratic Variant Differences
- Charge time: 1.3s instead of 2.0s (less read time).
- Beam width: `b.radius * 0.8` (wider).
- Can fire twice in a row (state can re-enter `charging_beam` from the
  post-fire idle with a 15% chance, but target re-locks each time).
- The "laser sight" jitters randomly ±15° during charge (unsettling), but
  still locks 0.5s before fire.

### Constants

```
BEAM_CHARGE_TIME        = 2.0       // erratic: 1.3
BEAM_FIRE_DURATION      = 0.6
BEAM_WIDTH              = b.radius * 0.6   // erratic: * 0.8
BEAM_LENGTH             = 900
BEAM_LOCK_TIME          = 0.5       // target freezes this long before fire
BEAM_TRIGGER_CHANCE     = 0.30      // per idle→absorb transition
BEAM_POST_IDLE          = 4.0 + random() * 1.0
BEAM_ERRATIC_REFIRE     = 0.15
```

### Implementation Hooks

**State machine** (in the `stateTimer <= 0` block, ~line 5948):
```
if (b.state === 'idle') {
  if (G.bossKillCount >= 1 && Math.random() < BEAM_TRIGGER_CHANCE) {
    b.state = 'charging_beam';
    b.stateTimer = BEAM_CHARGE_TIME;
    b.beamTargetX = G.lastTapX;  // initial aim
    b.beamTargetY = G.lastTapY;
    b.beamLocked = false;
  } else {
    b.state = 'absorbing';
    ...
  }
}
```

**New update section** (after absorbing logic, before tether logic):
```
if (b.state === 'charging_beam') {
  // Track player tap for first 1.5s, lock at 0.5s remaining
  if (b.stateTimer > BEAM_LOCK_TIME && !b.beamLocked) {
    b.beamTargetX = G.lastTapX;
    b.beamTargetY = G.lastTapY;
  }
  if (b.stateTimer <= BEAM_LOCK_TIME) b.beamLocked = true;
}

if (b.state === 'firing_beam') {
  // On first frame: sweep orbs in beam rect, destroy them
  // (flag to run once: b.beamFired)
}
```

**Rendering** (in `renderNexusBoss`):
- During `charging_beam`: draw dashed line from muzzle → target, eye
  convergence (override `lookStr` to point all eyes at muzzle).
- During `firing_beam`: draw beam rectangle with glow, white flash particles
  at destroyed orb positions.

### Data on Boss Object

```
beamTargetX, beamTargetY   // world coords of aim point
beamLocked: false          // true when target freezes
beamFired: false           // true after orb destruction pass (fire once)
beamAngle: 0               // computed: atan2 from boss to target
```

---

## FEATURE 3 — SINGULARITY PULSE

### Concept
The Nexus performs a massive gravity implosion — all eyes close (the tell),
then all nearby orbs are violently pulled inward. The player has a brief
window to pop an orb *during* the pull to "counter-hit" the Nexus for bonus
damage. Miss the window and the boss feasts.

This is the "parry" mechanic. High risk, high reward, extremely satisfying.

### New State: `singularity_telegraph` → `singularity_pull` → `singularity_recover`

**Trigger:**
- Timed cooldown: fires every 18-22s of boss alive time (tracked via
  `b.singularityCD`, starts at 20s, decremented each frame).
- Overrides whatever state the boss is currently in when the cooldown hits
  zero (interrupts idle/absorbing/shielding).
- During `charging_beam` or `firing_beam`: singularity is deferred until
  beam completes.
- The shield drops temporarily for the singularity sequence regardless of
  tether state (the Nexus commits fully to this attack).

**Phase 1 — `singularity_telegraph` (2.0s):**
- ALL eyes close simultaneously (force `openness = 0`). This is the only
  time every eye is closed — a unique, unmistakable tell.
- The body contracts slightly (radius shrinks to 80% over 2s).
- A deep purple vortex spiral appears around the Nexus, rotating inward,
  alpha ramping 0 → 0.8. Subtle gravity: orbs within 300px drift inward
  at strength 20 (gentle, just enough to signal what's coming).
- Floating text: `'CONVERGENCE'` in #8040c0, slow fade.
- All tethers pulse rapidly (phase speed 12 instead of 4).

**Phase 2 — `singularity_pull` (1.5s) — THE DANGER/COUNTER WINDOW:**
- Massive gravity: all orbs within 400px are yanked toward the boss at
  strength 350 (much stronger than normal absorb's 60).
- Eyes remain closed. Body pulses at 8Hz between 80% and 110% radius.
- Any orb that reaches `b.radius * 0.4` is devoured (boss heals +1, orb
  destroyed, no chain). Without intervention, 3-8 orbs get eaten.

  **COUNTER-HIT MECHANIC:**
  During this 1.5s window, if ANY orb is popped (by player tap) AND the
  resulting explosion overlaps the boss (`d < blastRadius + b.radius`):

  - **Counter-hit triggers!**
  - Damage: `3 × normal hit damage` (stacks with void amplification).
  - Boss enters `singularity_recover` immediately (cancels remaining pull).
  - All currently-being-pulled orbs are released (gravity stops, orbs keep
    their current velocity and scatter outward as a physics impulse —
    potential chain reaction material).
  - Screen flash: bright white, 0.3s.
  - Screen shake: 8 intensity.
  - Floating text: `'RUPTURED'` in #ff3030, large font, slow fade.
  - Boss is **stunned** during recover (takes normal damage with no shield).
  - The released orbs scatter in a ring — a skilled player can immediately
    tap to chain them for massive bonus damage during the stun.

  **If the window expires without a counter-hit:**
  - Boss heals +2 HP (on top of whatever it ate).
  - Floating text: `'IT FEEDS'` in #6020a0.
  - A shockwave (radius 350px, strength 200) scatters all remaining nearby
    orbs outward (field disruption — punishment for not countering).
  - Transitions to `singularity_recover`.

**Phase 3 — `singularity_recover` (2.5s if countered, 1.5s if not):**
- Boss returns to normal size (radius lerps back to base over 0.5s).
- Eyes reopen slowly (openness 0 → 1 over 1.0s).
- If countered: boss is stunned — shield stays DOWN, cannot absorb/eat,
  takes normal damage. This is the reward window.
- If not countered: boss resumes with shield UP (free shield restore),
  enters idle state. The player missed the window and gets punished.
- After recover: reset `singularityCD = 18 + random() * 4`.
- Return to `idle` state with normal timer.

### Erratic Variant Differences
- Cooldown: 12-16s instead of 18-22s (more frequent).
- Telegraph: 1.3s instead of 2.0s (harder to read).
- Pull strength: 450 instead of 350.
- Pull radius: 500px instead of 400px.
- Counter window: 1.0s instead of 1.5s.
- If not countered: heals +4 HP instead of +2.

### Constants

```
SING_COOLDOWN_BASE      = 18.0      // erratic: 12.0
SING_COOLDOWN_RAND      = 4.0       // erratic: 4.0
SING_TELEGRAPH_TIME     = 2.0       // erratic: 1.3
SING_PULL_TIME          = 1.5       // erratic: 1.0
SING_PULL_RADIUS        = 400       // erratic: 500
SING_PULL_STRENGTH      = 350       // erratic: 450
SING_EAT_RADIUS         = b.radius * 0.4
SING_COUNTER_DMG_MULT   = 3
SING_RECOVER_COUNTERED  = 2.5       // stun duration (reward)
SING_RECOVER_NORMAL     = 1.5       // brief recovery (no reward)
SING_FAIL_HEAL          = 2         // erratic: 4
SING_FAIL_SHOCKWAVE_R   = 350
SING_FAIL_SHOCKWAVE_STR = 200
```

### Implementation Hooks

**Cooldown tick** (top of `updateBoss`, after regen, for nexus only):
```
if (b.type === 'nexus' && b.state !== 'dying'
    && b.state !== 'charging_beam' && b.state !== 'firing_beam') {
  b.singularityCD -= dt;
  if (b.singularityCD <= 0) {
    b.state = 'singularity_telegraph';
    b.stateTimer = SING_TELEGRAPH_TIME;
    b.singularityCountered = false;
    // Temporarily drop shield for the sequence
    b.shieldUp = false;
  }
}
```

**Counter-hit detection** (modify `damageBoss`):
```
// At top of damageBoss, before shield check:
if (b.type === 'nexus' && b.state === 'singularity_pull') {
  // Counter-hit! Apply 3× damage
  dmg *= SING_COUNTER_DMG_MULT;
  b.singularityCountered = true;
  b.state = 'singularity_recover';
  b.stateTimer = SING_RECOVER_COUNTERED;
  // Release all pulled orbs (apply outward velocity impulse)
  // Screen flash, shake, floating text
  // Skip normal shield check (shield is already down)
}
```

**Singularity update section** (new block after beam logic):
```
if (b.state === 'singularity_telegraph') {
  // Gentle inward drift, shrink body, close eyes
  // On timer expiry → 'singularity_pull'
}
if (b.state === 'singularity_pull') {
  // Strong gravity pull on all orbs in range
  // Eat orbs that reach eat radius
  // On timer expiry without counter → fail path
}
if (b.state === 'singularity_recover') {
  // Lerp radius back, reopen eyes
  // On timer expiry → reset cooldown, restore idle
}
```

### Rendering

- `singularity_telegraph`: vortex spiral (6 arms, rotating inward, purple
  gradient), body shrink, all eyes forced closed, tethers pulse fast.
- `singularity_pull`: vortex intensifies (white-hot core), body pulsing,
  "gravity lines" drawn from nearby orbs toward boss center (thin, white,
  alpha = 0.3, creates visual suction effect).
- `singularity_recover` (countered): red/white "cracked" overlay on body,
  stunned particles (small stars drifting outward), vulnerability indicator
  ring (same as shield-down indicator but brighter).

### Data on Boss Object

```
singularityCD: 20.0            // countdown to next singularity
singularityCountered: false    // was this one parried?
singularityOrbsEaten: 0        // track orbs consumed during pull
```

---

## STATE MACHINE — COMPLETE UPDATED FLOW

```
                    ┌─────────────────────────┐
                    │                         │
                    ▼                         │
               ┌────────┐   30%         ┌─────────────────┐
               │  IDLE   │─────────────►│ CHARGING_BEAM   │
               │ 3–5s    │              │ 2.0s (1.3s erra)│
               └────┬───┘              └────────┬────────┘
                    │ 70%                        │
                    ▼                            ▼
            ┌────────────┐              ┌──────────────┐
            │ ABSORBING  │              │ FIRING_BEAM  │
            │ 2.0s       │              │ 0.6s         │
            └─────┬──────┘              └──────┬───────┘
                  │                            │
                  ▼                            │
            ┌────────────┐                     │
            │ SHIELDING  │◄────────────────────┘
            │ 3.0s       │       (post-fire idle 4-5s,
            └─────┬──────┘        then normal cycle)
                  │
                  └───► back to IDLE


  ══════ SINGULARITY (interrupt, on cooldown) ══════

  ANY state* ──────► SINGULARITY_TELEGRAPH (2.0s)
  (*except beam)            │
                            ▼
                     SINGULARITY_PULL (1.5s)
                       /            \
                 countered        timeout
                    /                \
                   ▼                  ▼
           SING_RECOVER(2.5s)   SING_RECOVER(1.5s)
           (stunned, no         (shield restores,
            shield = reward)     heal = punishment)
                    \                /
                     └──► IDLE ◄────┘
```

---

## VISUAL LANGUAGE SUMMARY

| State | Player Should Feel | Key Visual Tell |
|-------|-------------------|-----------------|
| `charging_beam` | "Something's aiming at me" | Eyes converge, dashed sight line |
| `firing_beam` | "Get clear!" | White-purple beam, orbs vaporize |
| `singularity_telegraph` | "It's powering up..." | ALL eyes close, body shrinks, vortex |
| `singularity_pull` | "Now or never!" | Orbs sucked in, pulsing body |
| Counter-hit success | "GOT IT!" | White flash, scatter burst, RUPTURED |
| Counter-hit missed | "Damn." | Shockwave, IT FEEDS, boss heals |
| `singularity_recover` (stunned) | "Punish it!" | Cracked body, vulnerability ring |

---

## INTERACTION BETWEEN FEATURES

- Void Beam and Singularity Pulse never overlap (singularity defers during
  beam states).
- Singularity cooldown does NOT tick during beam states (so beam doesn't
  "eat" singularity timing).
- If a beam destroys orbs near the boss, it reduces the orbs available for
  the next singularity to eat — indirect synergy. A well-aimed beam from
  the boss followed by a singularity is the most dangerous combo.
- Both features drop/bypass the shield — they're inherently "the boss is
  committing to an attack" moments, creating vulnerability through
  aggression. This mirrors Dark Souls design: boss attack windows ARE the
  player's damage windows.
