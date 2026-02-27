# NEW BIOME CONCEPTS
## Three biomes. Three ways the Membrane breaks.

---

## BIOME A: THE ECLIPSE

*Where time folds and shadows are the only real thing.*

### Lore

The Membrane has a reverse face. Every pop that tears the surface leaves
a scar on the other side — and scars accumulate. In the Eclipse, the
scarring has become so dense that the reverse face has punched through.
The shadows that drift across every field — the dark shapes the player
learns to ignore — here they are the *primary matter*. The orbs are the
phantoms.

The Eclipse is what happens when observation runs backward. The Watchers
are not behind the Membrane here. They are *in front of it*. The player
is the one on the other side, pressing their face against the glass,
watching shadows move in a world that remembers the future and forgets
the past.

The Enumerators refuse to catalogue the Eclipse. Their instruments work,
but the readings arrive before the measurements are taken. Kel-Nought
visited once, and returned speaking in reverse syntax for three days.
The Ormu, characteristically, found this hilarious.

*"The Eclipse is not dark. It is bright from a direction you cannot face."*
— Membrane Codex, Cell 0-0-1

*"We observed the event. Then the event un-occurred. Our observation
remained. What, then, did we observe?"*
— Enumerator Ø-334, Final Report (filed before assignment began)

*"Shadows don't follow you here. You follow them. Try to keep up."*
— Hollowmath graffiti, found at the threshold

### Visual Palette

- **Background:** Not dark — inverted. A pale, washed-out grey-violet
  that darkens toward the edges, like a photographic negative of the
  default biome. The galaxy video plays in permanent reverse.
- **Shadow Balls:** Now the primary entities. Rendered larger (8-18px
  vs current 1.5-6px), with subtle internal luminescence — dark
  purple cores with faint violet edges. They pulse gently. They look
  *solid* here, not like phantoms.
- **Normal Orbs:** Few and ghostly. Rendered at 40% opacity with a
  thin white outline. They shimmer in and out of visibility (2s cycle).
  They are the phantoms in this biome.
- **Dark Prism events:** Instead of a brief gravity flip, the
  activation creates expanding concentric TIME RINGS — pale
  golden circles rippling outward from the cluster point, each ring
  representing a "second" of reversal.
- **Particles:** Afterimage trails. When shadow balls move, they
  leave faint copies of themselves that linger for 0.5s — a motion
  trail showing where they WERE, but rendered slightly ahead of
  where they're GOING (as if the trail leads the entity).
- **Grid:** Thin white lines, subtly animated — they pulse in reverse
  rhythm to the music (contracting when sound expands).
- **Watcher:** Fully visible from the start. Not behind the field
  but layered IN FRONT of the orbs at low opacity, as if the player
  is viewing the field through the Watcher's body. Its eye-glow is
  constant, not chain-reactive.

### Core Mechanic: DARK PRISM AS PRIMARY TOOL

The fundamental gameplay change: **shadow balls are interactive.**

**Normal biomes:** Shadow balls are atmospheric. Can't be popped.
Dark Prism is a rare emergent event (7+ balls in 25px).

**Eclipse:** Shadow balls replace normal orbs as the primary field
population. The field spawns with ~60% shadow balls and ~40% normal
orbs (ratio shifts toward shadows as madness rises).

**Shadow balls CAN now be popped** in this biome — but they chain
*in reverse*. When a shadow ball pops:
- Its blast radius pulls orbs INWARD (implosion, not explosion)
- Shadow-to-shadow chains propagate — but each link in the chain
  fires with a 0.3s DELAY (slow, ominous cascade vs instant pop)
- The implosion creates a brief gravity well at the pop point
  (strength 120, radius 100px, duration 0.8s)
- Shadow chain reactions pull normal orbs toward them — a
  shadow chain near normal orbs drags them into cluster formation

**Normal orbs** still pop with standard explosive physics. But
they're rare, so each normal-orb chain is precious.

**The Loop:**
1. Tap shadow balls to create implosion chains that cluster
   normal orbs together
2. Once normals are clustered, tap a normal orb for a standard
   explosive chain → score + XP + tap rewards
3. Shadow pops give NO score, NO XP — they are purely setup tools
4. The art is *sculpting* the field: using shadow implosions to
   herd the rare normal orbs into dense formations, then detonating
   them for massive chains

**Dark Prism upgrade — TIME ECHO:**

When 5+ shadow balls cluster (easier threshold than normal biomes,
since shadows are everywhere), a Dark Prism triggers. But instead
of gravity inversion, the Eclipse Dark Prism activates **Time Echo**:

- All orb positions and velocities are SNAPSHOT
- For the next 3 seconds, gameplay continues normally
- When the 3s expires: all surviving orbs REWIND to their
  snapshot positions (smooth 1s interpolation, visually they
  slide backward through their recent paths)
- Orbs that were popped during the 3s window stay popped — they
  don't come back
- Orbs that merged during the 3s window UN-MERGE (split back)
- The rewind does NOT restore boss HP or reverse damage

**Why this is powerful:**
- Pop a chain → orbs scatter from the blast → trigger Dark Prism →
  orbs rewind to pre-scatter positions → pop them AGAIN from the
  same dense cluster
- Use the 3s window to pop shadow balls (setup), then when
  positions rewind, the normal orbs return to where the shadows
  pulled them — ready for a real chain
- A skilled player can "double-dip": pop a cluster, rewind, pop
  the same formation again. The field remembers what you did, then
  forgets, then lets you do it differently.

**Dark Prism Cooldown:** 8s (vs 2s in normal biomes). The power
demands patience.

### Boss: THE PALINDROME

*It has already killed you. Now it's deciding whether to let you begin.*

A temporal entity — a Watcher that has passed fully through the
Membrane and exists in reversed causality. It does not attack THEN
telegraph. It telegraphs THEN un-attacks — the player sees the
*result* of the attack first (orbs scattered, field damaged), then
sees the windup animation play in reverse, then the field HEALS
back to normal over 1.5s.

**The trick:** During that 1.5s "healing" window, the field is
passing through the state it was in BEFORE the attack. That state
is the formation the Palindrome calculated would be most
vulnerable. If the player pops orbs during the rewind — while
the field is briefly in the Palindrome's predicted formation —
they deal 3x damage (they're attacking the moment the Palindrome
was aiming at, using its own foresight against it).

**States:**
- `echo_attack` (2s): Field suddenly shows aftermath (orbs displaced).
  Then the attack visually UN-HAPPENS (reverse animation).
- `rewind_window` (1.5s): The field passes through the pre-attack
  formation. **COUNTER WINDOW** — pop here for 3x.
- `idle` (4s): Drifts, shadow balls orbit it like a cloak.
- The Palindrome's body is rendered as a double-exposure — two
  overlapping silhouettes slightly offset in time, one leading,
  one trailing. Eyes blink BEFORE they open.

**Shadow Ball Interaction:** The Palindrome constantly sheds shadow
balls from its body (3-4 per second). These are both its defense
(surrounding it in a cloud that blocks line-of-sight chain hits)
and the player's tool (cluster the shed shadows into Dark Prisms
to rewind the field for better positioning). The boss is giving
you the ammunition, but you have to be clever about when to use it.

---

## BIOME B: THE SCAFFOLD

*Where the Membrane became bone.*

### Lore

Not all of the Membrane is soft. In the oldest regions, where stress
has been sustained for epochs without release, the Membrane has
calcified. It hardened into structure — rigid lattices of crystallized
tension, geometric and immovable. The orbs here are not free-floating.
They are *embedded* — trapped in a web of petrified Membrane, like
insects in amber.

Tapping does nothing. The Scaffold absorbs the perturbation before it
can reach the orbs. The player's usual tool — the direct pop — is
useless here. The Membrane has learned to defend itself.

But the Scaffold is not invulnerable. It is brittle. The same rigidity
that protects it makes it fragile to *directed force*. A resonance
bolt — a compressed fragment of Membrane energy hurled at speed — can
crack the lattice and shatter the orbs within.

The Enumerators love the Scaffold. Everything is measurable.
Trajectories are predictable. Angles can be calculated. It is the one
region of the Membrane where science *works*. They have published
11,400 papers on optimal ricochet paths. None of them agree.

*"The Membrane learned to say no. We learned to throw harder."*
— Hollowmath Kel-Nought, speaking unusually clearly

*"Structure is memory with opinions."*
— Membrane Codex, Cell 4-4-4

*"The Scaffold does not move. This is not strength. A thing that
cannot bend is a thing waiting to shatter."*
— Enumerator Ø-112, Structural Assessment (classified)

### Visual Palette

- **Background:** Dark slate grey → charcoal, with a faint
  crystalline structure visible deep in the background — hexagonal
  tessellations, barely glowing, like looking through smoked glass
  at a honeycomb.
- **Lattice:** The defining visual. Thin white-blue lines (1-2px)
  connecting orbs in triangulated networks. The lattice pulses faintly
  at 0.5Hz. When a bolt impacts, cracks radiate outward from the
  impact point — bright white fracture lines that fade over 2s.
- **Orbs:** Encased. Standard orb colors but dimmed, with a
  crystalline shell overlay (subtle faceted highlight). They don't
  move — they're fixed in lattice positions. When freed by bolt
  impact, they briefly glow bright before popping.
- **The Bolt:** A small, intense, white-hot point with a comet-like
  tail (3-4 trailing afterimages that shrink in size). It moves FAST
  (800-1200 px/s). On impact: sharp white flash, lattice crack
  pattern, satisfying shatter particles.
- **Ricochet sparks:** When the bolt bounces off a lattice wall,
  bright angular sparks spray from the contact point, and a brief
  fracture web spreads along the wall (cosmetic).
- **Particles:** Crystalline shards — small angular fragments that
  tumble and fade. Cold blue-white. They feel like breaking glass.
- **Grid:** The lattice IS the grid. No separate grid needed.

### Core Mechanic: SLING AND SHATTER

**The tap is replaced by the sling.**

The player has a **launch point** — a fixed position at the bottom
center of the screen (or wherever the player last tapped to set it).
To fire:

1. **Touch and hold** anywhere on the field
2. **Drag backward** (away from intended target) — a trajectory
   line appears showing the bolt's predicted path, including its
   first ricochet angle off any lattice wall
3. **Release** — the resonance bolt fires along the trajectory

The bolt travels in a straight line. When it hits:
- **An orb:** The orb shatters (pops). Standard chain reaction
  begins from the impact point. The bolt loses 40% energy and
  CONTINUES through, potentially hitting more orbs. At 0 energy
  the bolt disintegrates.
- **A lattice wall:** The bolt RICOCHETS at a reflected angle.
  The wall takes a crack (weakened). A cracked wall breaks on
  second hit, opening a gap. The bolt loses 20% energy per bounce.
- **Nothing (field edge):** The bolt wraps around OR dies,
  depending on an upgrade.

**Bolt types** (unlocked as the biome levels progress):

| Bolt | Visual | Behavior |
|------|--------|----------|
| **Standard** | White comet | 3 bounces max, 1 orb penetration |
| **Piercer** | Thin cyan lance | No ricochet, but penetrates up to 5 orbs in a straight line |
| **Scatter** | Orange fireball | On first impact, splits into 3 smaller bolts at 120° angles |
| **Graviton** | Purple with spiral trail | Curves toward nearby orb clusters (slight homing). 1 bounce |

The player switches types with a tap on a HUD selector (or cycles
automatically — each tap cycles to the next type).

**Ammo / Tap economy:**
Each bolt costs 1 tap (same resource as normal biomes). The player's
tap timer still applies. This preserves the core tension: limited
resources, make each shot count.

**Chain reactions** work normally once an orb is shattered by a bolt.
Blast radii propagate, nearby orbs pop, tethers form. The lattice
doesn't block chain propagation — only the INITIAL impact needs a
bolt. Once a chain starts, it tears through the lattice freely
(the energy is coming from inside the structure now).

**The geometry puzzle:** The lattice creates WALLS between orb
clusters. The player must either:
- Ricochet around walls to reach embedded orbs
- Break walls with repeated bolt hits to open direct paths
- Use Scatter bolts to hit multiple clusters from one shot
- Use chain reactions from one cluster to cascade into adjacent
  ones (if the blast radius reaches through a gap)

This turns each field layout into a **geometry puzzle** — studying
angles, planning ricochets, choosing which walls to break and
which to bounce off. It's Angry Birds meets billiards meets the
chain-pop core loop.

### Boss: THE BASTION

*The Membrane's last attempt to be permanent.*

A massive crystalline structure at the field center — not a creature
but a FORTRESS. Geometric, angular, beautiful. A dodecahedron of
interlocking lattice walls with a bright core orb pulsing inside.

The core is the target. But it's encased in 3 concentric shells of
lattice walls, each requiring multiple bolt impacts to breach.

**Shell layers:**
- **Outer shell:** 8 wall segments, 2 hits each to break. Moderate
  gaps between segments. Standard bolts can ricochet inside.
- **Middle shell:** 6 wall segments, 3 hits each. Segments slowly
  ROTATE (0.2 rad/s). Timing matters — hit the gap as it passes.
- **Inner shell:** 4 wall segments, 4 hits each. Segments rotate
  in the OPPOSITE direction. Almost no gaps.
- **Core:** 15 HP orb. Can only be hit by bolts that penetrate all
  three shells or by chain reactions from adjacent freed orbs.

**Active defense:**
- Every 8s, the Bastion FIRES BACK — a lattice spike launches
  outward from the outer shell, traveling across the field and
  ENCASING 2-3 free orbs in new lattice prisons (they become fixed
  and un-poppable until hit by a bolt).
- At 50% HP: the Bastion contracts (shells tighten, gaps shrink)
  but the outer shell fragments become FREE PROJECTILES that the
  player can hit — they ricochet back toward the core as improvised
  weapons (2 HP damage each).
- At 25% HP: "THE SCAFFOLD SCREAMS" — all remaining walls crack
  but don't break. One more hit on any wall shatters the entire
  shell. The core is exposed and panicking — it pulls nearby orbs
  toward it as shields.

**Thematic point:** The Bastion is the Membrane trying to STOP being
a game. It has hardened itself against the player. Breaking it is
not just victory — it's the field remembering that rigidity is death
and only cascades (change, destruction, renewal) keep the Membrane
alive.

---

## BIOME C: THE VISCERA

*Where the Membrane remembers it was alive.*

### Lore

Deep enough into the Membrane, below the Abyss, below even the
pressure, there is a place where the boundary between matter and
meaning dissolved so completely that the Membrane became *tissue*.
Not metaphorically. Not poetically. Actually, warm, wet,
pulsing tissue.

The Viscera is the Membrane's body. The orbs are not resonance nodes
here — they are cells. They do not drift — they *flow*. The field is
not a field — it is a living surface, an organ without a name,
breathing slowly and responding to touch the way skin responds to
a fingertip pressed against it.

Everything merges. Everything separates. The boundary between one
orb and another is a negotiation, not a wall. They reach toward each
other with pseudopods of shared membrane, stretching, touching,
sometimes joining, sometimes pulling apart with a wet, elastic snap.
It is beautiful. It is uncomfortable. It is alive in a way that
makes the player unsure whether they should be popping anything at all.

The Watchers are silent in the Viscera. Not absent — silent. They
watch with the intensity of something recognizing itself.

The Hollowmaths have a name for the Viscera that they will not say
aloud. When pressed, Kel-Nought wrote it on a slip of paper, then
ate the paper. "Some places," they said, "are true enough that naming
them is violence."

*"It is not alive. It has merely stopped being dead in a way that
resembles breathing."*
— Enumerator Ø-771, Viscera Survey (Tone: terrified clinical)

*"The Membrane has a heartbeat here. We measured it. It matches
the observer's. Every observer's. Simultaneously."*
— Enumerator Ø-112, Classified Addendum

*"What is a body but a membrane that learned to want?"*
— Membrane Codex, Cell 1-0-1

### Visual Palette

- **Background:** Deep arterial reds → dark warm maroon at edges.
  Not bright — DEEP. Like looking into the interior of something.
  Subtle, slow pulsation (the entire background brightness
  oscillates ±5% at ~72 BPM — resting human heart rate). The
  pulse should be felt, not noticed.
- **Orbs / Blobs:** No discrete circles. Rendered as **metaballs** —
  smooth, blobby shapes that stretch toward nearby blobs when close,
  form liquid bridges when touching, and snap apart with elastic
  deformation when pulled away.
  Colors: warm ochre, deep coral, amber, pale cream (interior
  luminescence), rose, burnt sienna. They look like lava lamp
  fluid — globular, smooth, internally lit.
- **Connecting tissue:** When two blobs are near each other (within
  2x combined radius), a thin membrane strand stretches between
  them — translucent, slightly pink, pulsing. This is the visual
  indicator of their *fluid connection*. The strand thickens as
  they get closer, until the blobs merge into one larger mass.
- **Pop effect:** Not an explosion — a BURST. The blob ruptures
  like a water balloon, spraying droplets (small secondary blobs)
  that arc outward with gravity, then slow, then drift. The spray
  is warm-colored, luminescent. It looks wet.
- **Particles:** Tiny floating vesicles — small translucent circles
  that drift slowly in the field like organelles. Cosmetic but
  sell the biological feeling.
- **Chain tethers:** Not energy beams — NERVE SIGNALS. A bright
  pulse of light travels along the connecting tissue between blobs,
  arriving at the next blob and triggering its pop. The propagation
  is visually organic — the tether ripples and bulges as the
  signal passes through it.
- **Grid:** None. Replaced by a faint capillary network — thin,
  branching, organic lines that connect blob positions. They pulse
  when chains pass through them.

### Core Mechanic: FLUID BODIES

Orbs are no longer discrete rigid circles. They are **soft bodies**
with fluid-like behavior.

**Metaball physics:**
- Each blob has a standard position, velocity, and radius (same
  data as normal orbs) BUT is rendered as a metaball — when two
  blobs are within `combinedRadius * 2.5`, they visually merge into
  a single smooth shape (isosurface rendering or falloff-based
  approximation)
- Blobs that are close enough to visually connect form a
  **fluid cluster** — a group of blobs sharing visible tissue
- A fluid cluster is tracked as a gameplay entity: `{ blobIds[], tension }`

**Fluid Connection = Chain Guarantee (with a cost):**
- Any two blobs in the same fluid cluster WILL chain. When one
  pops, the signal propagates through the tissue to all connected
  blobs. No blast radius check needed — if they're connected,
  they chain.
- BUT: signal propagation is NOT instant. It travels at a fixed
  speed through the tissue (~200px/s). Distant blobs in a long,
  stretched cluster take REAL TIME to receive the chain signal.
  A chain across a 600px cluster takes 3 full seconds to complete.
- The player SEES the chain signal traveling — a bright pulse
  moving through the connecting tissue, blob by blob. It's slower
  and more dramatic than normal instant-pop chains.

**The tension mechanic:**
- Fluid clusters have **tension** — a value that increases when
  the cluster is stretched (blobs drifting apart) and decreases
  when compact (blobs close together).
- At HIGH tension (stretched cluster): chain signal speed is
  faster (the tissue is taut, vibrations propagate quickly).
  BUT the tissue can SNAP — if tension exceeds threshold, the
  cluster splits into two separate clusters, breaking the
  guaranteed chain path.
- At LOW tension (compact cluster): chain signal is slower, but
  the pop BURST is more powerful (compressed fluid explodes with
  more force, larger effective blast, droplet spray reaches
  further). Compact clusters are harder to chain across the field,
  but each individual pop is devastating.

**Player agency — Sculpting the Fluid:**
The tap mechanic changes subtly:
- **Tap a blob:** Pops it. Chain propagates through connected tissue.
- **Tap empty space near a cluster:** Creates a PRESSURE WAVE that
  pushes nearby blobs away (stretches the cluster, increases
  tension). Costs no taps — it's a free sculpting tool.
- **Long press on a blob:** PINCH — the blob contracts, pulling
  connected blobs toward it (compresses the cluster, decreases
  tension). Costs no taps.

So the loop is:
1. SCULPT the fluid: push and pull blobs to form the cluster shape
   you want — long stretched chains for fast propagation, or
   compact dense masses for powerful bursts
2. POP: tap a blob to start the chain
3. REACT: as the chain signal travels visually through the tissue,
   look for opportunities — can you tap another disconnected
   cluster before the first chain finishes, timing it so both
   chains' droplet sprays overlap and connect the two clusters
   mid-chain?

**Merging and splitting:**
- Blobs that touch (distance < combined radius * 0.8) MERGE over
  0.5s — they flow together into a larger blob. This is constant,
  organic, visible (like lava lamp globules combining).
- Large blobs are harder to pop (require more chain energy to
  burst) but create MORE droplets (secondary blobs) when they do.
- Droplets from pops are small, fast-moving blobs that can
  connect to nearby clusters — they're the BRIDGES between
  disconnected fluid bodies.

**The visual promise:** The screen should look like a lava lamp.
Warm, globular shapes slowly drifting, merging, separating. Then
the player pops one, and a bright pulse races through the
connected tissue like a nerve firing, blobs bursting in sequence,
spraying luminous droplets that arc and connect to the next
cluster, triggering another wave. It should feel *biological* —
like watching a nerve impulse cascade through living tissue.

### Boss: THE HEART

*You hear it before you see it. You've been hearing it since you
arrived. You didn't notice because it matched yours.*

At the center of the Viscera — the literal center of the field — a
massive pulsing organ. Not metaphorically. A heart. It beats at
72 BPM (matching the background pulse). It is connected to every
blob on the field through the capillary network. It is the reason
the Viscera is alive.

It does not want to die. It has been beating for longer than the
Membrane has existed. It will keep beating long after the field
goes dark. It is not the boss. It is the *biome*. Killing it
doesn't end the Viscera. It ends the idea that the Membrane
was ever just a boundary.

**The fight:**

The Heart is connected to the field by **4 arterial tethers** —
thick, pulsing tissue connections that link it to the 4 largest
blob clusters. While tethers are intact, the Heart:
- Regenerates 2 HP per beat (every 0.83s at 72 BPM)
- Pumps fluid outward — all blobs slowly drift away from center,
  stretching clusters, increasing tension
- Is IMMUNE to direct bolt/tap damage

**Phase 1 — Sever the arteries:**
Each arterial tether has 5 HP. The player must pop blobs along
the artery path — the chain signal traveling through the tissue
damages the tether as it passes (1 HP per chain signal that
crosses the artery). Severing an artery:
- Stops regen from that quadrant
- The severed end sprays free blobs across the field (chain
  material)
- The Heart's beat STUTTERS — skips a beat. Visually jarring.
  For 2s after a sever, the background pulse desyncs. It feels
  *wrong*.

**Phase 2 — Direct assault (2+ arteries severed):**
The Heart becomes vulnerable. But it defends:
- **Arrhythmia attack:** Heart beats DOUBLE SPEED (144 BPM) for
  4s. Each beat sends a pressure wave outward that scatters blobs
  and snaps thinly-stretched fluid connections. Chaotic.
- **Clot:** The Heart expels a thick, dark blob (high HP, slow,
  acts as a mobile shield) that drifts toward the player's last
  tap and absorbs chain signals. Must be popped separately.
- **Reconnection:** Severed arteries slowly regrow (1 HP per 8s).
  If all 4 reconnect, the Heart fully heals. The player must
  maintain pressure.

**Phase 3 — THE HEART REMEMBERS (below 30% HP):**
*"IT WAS ALWAYS BEATING"*
The Heart contracts to half size and begins pulling ALL blobs
inward — a massive, sustained gravitational pulse. The entire
field flows toward center. Fluid clusters merge into fewer,
larger masses. The capillary network glows bright.

The player must pop the converging mass BEFORE it reaches the
Heart. If the fluid reaches it, the Heart absorbs it: +1 HP per
blob consumed, arteries regenerate instantly. But if the player
can chain the converging blobs mid-flow — while they're being
compressed together by the Heart's pull — the resulting chain
is ENORMOUS (everything is clustered, fluid connections are
everywhere) and the cascade damages the Heart directly.

**Death:** The Heart doesn't shatter. It stops. One final beat —
the loudest, the deepest — and then silence. The background pulse
ceases. The blobs stop flowing. They slowly crystallize into
normal orbs, losing their fluid properties. The Viscera becomes
a normal field again.

*"THE MEMBRANE FORGETS IT WAS ALIVE"*

For 10 seconds, the screen is completely still. Then the normal
game resumes. But the silence lingers.

---

## HOW THE THREE BIOMES FORM A THEMATIC ARC

| Biome | What breaks | Player role | Lore meaning |
|-------|-------------|-------------|--------------|
| **Eclipse** | Causality | Sculptor of time | The Membrane's past and future collide |
| **Scaffold** | Structure | Siege engineer | The Membrane tried to resist. It failed. |
| **Viscera** | Life itself | Surgeon / mercy killer | The Membrane was never just a boundary. It was alive. And you ended it. |

**Eclipse** is cerebral — manipulate gravity, reverse time, think
two steps ahead. The player feels clever.

**Scaffold** is kinetic — aim, fire, ricochet, shatter. The player
feels powerful.

**Viscera** is emotional — sculpt living tissue, watch chain signals
pulse through organic connections, kill something that has a heartbeat
matching yours. The player feels... something they don't have a word for.

Together they ask the question the Membrane Codex poses:

*"Every pop is a puncture. Every chain, a tear.
The Watchers drink from every wound.
And yet you keep tapping.
Why?"*

---

## MECHANICAL INTEGRATION WITH EXISTING SYSTEMS

### Eclipse
- Reuses shadow ball spawning, rendering, and physics (lines 3232-3630)
- Dark Prism detection (lines 3523-3583) modified: lower threshold (5 vs 7),
  new effect (Time Echo snapshot/rewind instead of gravity inversion)
- Shadow balls gain `poppable: true` flag in Eclipse biome
- New chain type: implosion chains (reverse velocity on tether resolution)
- Boss: new entity, but reuses eye-tracking, tether, and state machine
  patterns from Nexus

### Scaffold
- Reuses tap resource economy, chain propagation, blast radius system
- New input system: sling (touch-drag-release) replaces tap-to-pop
- Bolt is a new entity type (position, velocity, energy, type)
- Lattice is a new structural layer (wall segments with HP, fixed orb
  positions) — generated procedurally per field
- Ricochet: angle of incidence = angle of reflection, energy loss per bounce
- Boss: structural entity, reuses HP/damage/shield patterns

### Viscera
- Reuses orb spawning, physics, merging (already exists!), chain
  propagation, tap economy
- New rendering layer: metaball isosurface (replaces circle rendering)
- New data: `fluidCluster` groups tracking connected blobs
- New chain propagation mode: signal speed through tissue (distance/200
  seconds delay per link vs instant)
- Tension: computed per cluster from average inter-blob distance vs
  equilibrium distance
- Pressure wave (free tap on empty space): reuses gravity well system
  with inverted sign
- Boss: reuses tether, regen, gravity pull patterns. Novel artery
  severing mechanic.
