# POP FIELD -- Balance Guide & Tuning Reference

## Design Philosophy

The balance targets three experiences at different play stages:

1. **Early game (Levels 1-4):** Approachable. Player learns tap mechanics, earns enough taps to survive, chain reactions feel rewarding but not overwhelming.
2. **Mid game (Levels 5-9):** Strategic. Upgrades matter. Chains grow longer. The player feels power growth but isn't clearing the field in one tap.
3. **Late game (Levels 10+):** Spectacle. Massive chains, field clears, Archons, black holes. Balance is secondary to experience -- the game becomes a firework show.

Madness adds a perpendicular progression: the game transforms from puzzle to cosmic horror regardless of level.

---

## Critical Balance Values

### Chain Reaction Power

The chain reaction is the core mechanic. If too powerful, every tap clears the field (boring). If too weak, chains fizzle and the player runs out of taps (frustrating).

```
Blast radius formula:   60 + 75 * ((level-1)/16)^0.6
Range:                  60px (level 1) to 135px (level 17+)
Growth curve:           t^0.6 (gentler than sqrt, prevents mid-game spike)
Level span:             16 levels to reach max (slow ramp)
```

**Why t^0.6?** Earlier versions used `sqrt(t)` which spiked too fast at levels 5-7, causing premature field clears. The 0.6 exponent spreads growth more evenly.

**Why 16-level span?** Previous values of /9 and /14 gave too much power too early. At /16, the player needs to be level 9 before blast radius exceeds 100px.

### Fracture Progression

Fractures (spawned fragments) are the chain amplifier. Too early = exponential cascades. Too late = chains feel flat.

```
Level 1-2:  No fractures at all (learn basic popping)
Level 3-4:  1 fragment per pop (gentle chains)
Level 5-7:  Single chain depth (gen-0->gen-1->gen-2, 1 each)
Level 8-9:  Branching begins (gen-0 spawns 2 fragments)
Level 10+:  Full fracture tree ramps in over 6 more levels
```

**Key decision:** Branching delayed to level 8 (was 7). This prevents the "level 6-7 overpowered" problem where chains would cascade exponentially.

### Fission Balance

Fission is the most dangerous upgrade for balance. Recursive fission would create infinite chains.

**Anti-cascade safeguards:**
1. `fissionSpawn = true` flag prevents fission orbs from spawning more fission
2. Max 2 orbs per pop (was 4, then 3)
3. Gen-3 minimum (minimal XP, tiny blast contribution)
4. 0.6s blast immunity (prevents instant chain from parent blast)
5. Radius: 0.4x base (tiny, less likely to hit things)
6. Speed: 150-230 px/s (fast, fly out of blast zone quickly)

### Echo Burst Balance

Echo bursts create a second blast at 1.3x range. At max level 6:
- 60% chance per pop means ~60% of all chain pops double-blast
- This is intentionally strong at max level (reward for investing)
- The 1.3x range means echoes can catch orbs just outside normal range
- Current 10% per level (was 12%) prevents early-game dominance

---

## Tap Economy

The tap system is the fail state. If players can't earn taps faster than they spend them, the game ends. If taps are trivially easy to earn, there's no tension.

### Starting State
```
Starting taps:    10 (was 8, increased for accessibility)
Tap timer:        12 seconds base (was 10)
Timer growth:     +0.5s per level (caps at 16s)
```

### Tap Earnings
```
Level 1-2:  Chain ≥ 3 pops: 60% chance +1 tap
            Chain ≥ 6 pops: +1 tap guaranteed
            Chain ≥ 12 pops: +2 taps
Level 5-6:  Chain ≥ 8 pops: 40% chance +1 tap
            Chain ≥ 15 pops: +1 tap
            Chain ≥ 25 pops: +2 taps
```

**Chain Bounty multiplier:** At max level 6 = 2.8x, meaning a +2 tap chain gives +5 taps.

**Black hole collapses:** Free +1 tap (or +2 at mass ≥ 8) without costing a player tap.

**Field clear:** +3 bonus taps.

### Target Metrics
- Player should average 2-3 taps earned per tap spent in early game
- Mid game: 3-5 taps earned per tap spent (upgrades help)
- Late game: taps become nearly irrelevant (chains are massive, black holes give free taps)

---

## XP Curve

```
Level:  XP Required   Cumulative
2       12            12
3       20            32
4       35            67
5       59            126
6       100           226
7       170           396
8       290           686
9       493           1179
10      838           2017
```

**Growth factor 1.7** (was 1.5). The steeper curve ensures:
- Level 2: reachable after 2-3 decent chains (feels accessible)
- Level 5: requires several minutes of play
- Level 10+: genuine achievement, many chains needed

### XP Per Chain (Approximate)
```
10-pop chain:   ~6 XP (0.6 base * 10, accounting for gen scaling)
20-pop chain:   ~10 XP (diminishing returns kick in at 15 pops)
40-pop chain:   ~14 XP (heavy diminishing, but still meaningful)
```

---

## Orb Count Scaling

The screen should never feel empty OR impossibly cluttered.

```
Base:           35 orbs
Level bonus:    +1.5 per level (caps at +20 = level 14)
Density upgrade: +6 per level (max +36)
Zoom factor:    Fewer orbs when zoomed in (compensates for smaller visible area)
Madness:        1x at 0%, ramps to 3.5x at 100%
```

### Target Orb Counts
| Scenario | Count |
|----------|-------|
| Level 1, 0% madness | 35 |
| Level 5, 0% madness | 41 |
| Level 10, 50% madness | ~82 |
| Level 10, 80% madness | ~144 |
| Level 10, 100% madness | ~168 |

At full madness + density upgrade maxed: potentially 200+ orbs on screen.

---

## Gravity Scaling

All gravity systems use `getMadnessPhysicsScale()`:

```
0-50% madness:   1.0 -> 1.15  (barely noticeable)
50-100% madness: 1.15 -> 1.7  (dramatic, aggressive)
```

This creates two distinct feels:
- **Sub-50% madness:** Physics feel "normal" with subtle enhancement
- **Above 50%:** Everything accelerates. Whirlwinds become storms. Black holes become inescapable. Cursor pull becomes a vortex.

### Specific Gravity Values at 100% Madness

| System | Base | At Full Madness |
|--------|------|-----------------|
| Inter-orb gravity | 15 | 25.5 (1.7x) |
| BH strength | 1100 | 1870 |
| BH gravity radius | 420px | 714px |
| Whirlwind swirl | 1x | 3x (separate multiplier) |
| ABYSS pressure | 1x | 3x (separate multiplier) |
| Cursor reach | 1x | 2x (separate multiplier) |
| Blast radius | base | base * 1.7 |

---

## Size-Based Mechanics

Bigger orbs are worth more and explode harder. This rewards strategic play (letting orbs merge before popping).

### XP Scaling
```
sizeXpScale = max(0.15, (radius / 18) * 0.6)

Normal (18px):     0.6 XP (scaled from 1.0 base)
Merged 2x (36px):  1.2 XP
Merged 2.5x (45px):1.5 XP
Shrapnel (7px):    0.24 -> clamped to 0.15 XP
Fission (7px):     0.15 XP (shrapnel flag)
```

### Blast Scaling
```
sizeScale = radius / BASE_CIRCLE_RADIUS
blastRadius *= (0.7 + 0.3 * sizeScale)

Shrapnel (0.4x):  blast * 0.82 (18% reduction)
Normal (1.0x):    blast * 1.0
Merged (2.5x):    blast * 1.45 (45% bigger)
```

### Gravity Well Scaling
```
pullStrength = gravityWellLevel * 22 * sizeScale * madnessPhysicsScale
```
Merged orbs create stronger gravity pulls when popped.

---

## Balance History (Key Changes)

### Chain Reaction Nerfs
1. BASE_BLAST_RADIUS: 100 -> 65 -> 50 -> 60 (settled)
2. MAX_BLAST_RADIUS: 150 -> 135
3. Growth curve: sqrt(t) -> t^0.6
4. Growth span: /9 -> /14 -> /16 levels
5. Nova upgrade: +12px -> +8px -> +6px per level
6. Echo burst: 18% -> 12% -> 10% per level

### Fission Nerfs
1. Max orbs: 4 -> 3 -> 2
2. Size: 0.6x -> 0.5x -> 0.4x
3. Generation: gen-0 -> gen-1 -> gen-2 -> gen-3
4. Immunity: 0.2s -> 0.4s -> 0.6s
5. Anti-recursion flag added

### Starting Difficulty
1. BASE_TAPS: 8 -> 10
2. BASE_CIRCLE_COUNT: 45 -> 30 -> 35
3. BASE_BLAST_RADIUS: 100 -> 50 -> 60
4. XP_BASE: 15 -> 12

### Fracture Timing
1. Branching at level 6 -> 7 -> 8
2. Single fragments through level 5 -> 6 -> 7

---

## Known Balance Considerations

### Level 1-2 Accessibility
The first few chains need to give enough taps to survive. Current bonus tap thresholds (chain ≥ 3 at 60% chance) ensure most players can chain 3-5 orbs and earn at least 1 tap back.

### Mid-Game Power Spike
Levels 5-7 are where upgrades start stacking. Nova Radius + Echo Burst + Gravity Well creates a potent combination. Fracture branching starting at level 8 (not 7) prevents this from going exponential too early.

### Late-Game Spectacle vs. Balance
Above level 10, balance becomes secondary. The game is designed to become a visual spectacle at this point. Massive chains, field clears, Archon gravity combos, black hole collapses -- these are the reward for surviving this long. The tap economy is generous enough that skilled players can play indefinitely.

### Madness Balance
Madness is intentionally unbalanced. At 100% madness, the game is chaos -- 3.5x orbs, 1.7x physics, triple gravity effects, supermassive black holes. This is the intended experience. Balance concerns at high madness are secondary to the "cosmic horror" aesthetic goal.

### Fission Remains Contentious
Fission is the most-nerfed upgrade and still potentially the strongest. The anti-recursion flag (fissionSpawn) is the critical safeguard. Without it, fission orbs spawning fission orbs creates infinite cascade. Current balance: 2 tiny gen-3 orbs with 0.6s immunity is weak individually but meaningful over many pops.

---

## Tuning Levers

If chains are too powerful:
- Reduce BASE_BLAST_RADIUS or MAX_BLAST_RADIUS
- Increase growth exponent (0.6 -> 0.7 = slower mid-game growth)
- Increase growth span (/16 -> /20 = takes more levels to reach max)
- Reduce Nova upgrade per-level bonus

If chains are too weak:
- Increase BASE_BLAST_RADIUS
- Reduce growth exponent (0.6 -> 0.5 = faster early growth)
- Add more starting orbs (BASE_CIRCLE_COUNT)
- Increase bonus tap chances

If game is too hard early:
- Increase BASE_TAPS
- Lower XP_BASE
- Increase bonus tap small-chain chance

If game is too easy late:
- Increase XP_SCALE (steeper level requirements)
- Reduce fracture counts at each level bracket
- Increase cascade burst thresholds

If madness is too intense:
- Reduce madnessOrbMult peak (3.5x -> 2.5x)
- Reduce madness physics scale cap (1.7 -> 1.5)
- Increase SBH spawn interval
- Reduce gravity multiplier caps
