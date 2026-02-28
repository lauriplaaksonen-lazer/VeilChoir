# MUSIC CONCEPTS — Per-Biome Audio Design
## Tied to Lore. Built on Existing Synth Engine.

---

## THE MUSICAL COSMOLOGY

The Membrane vibrates. That vibration — at the frequency of meaning
— is the Codex. The orbs are standing waves. Every pop is a
puncture that rings. Every chain is a tear that sings.

The Choir has been singing one chord for eleven billion years. It
is every frequency that has ever existed, insisting on being heard
at once. The Hollowmaths call it "the democracy of sound."

The game's music IS the Membrane's vibration. It is not
accompaniment. It is the thing itself.

### Musical Principles (Apply Everywhere)

1. **Chains are ascending chromatic scales.** Each pop is one
   semitone higher (1.055× frequency). This is already implemented
   and should never change — it is the Membrane tearing upward.

2. **The Choir's chord is the destination.** Every biome's ambient
   soundscape is a fragment of the Choir's chord — a subset of
   harmonics from the infinite superposition. The deeper into the
   game, the more harmonics are present, the closer to the full
   chord.

3. **One semitone higher.** The recurring lore motif. When
   something transcendent happens (boss death, biome transition,
   proof collapse), the base tone shifts up one semitone. The
   Membrane has advanced. The Choir sings the next note.

4. **Oscillator type = perception state.** Sine = innocence/purity.
   Triangle = depth/pressure. Sawtooth = corruption/awareness.
   Square = fracture/pain. The oscillator IS the Membrane's health.

5. **Silence is a sound.** The absence of the drone after a boss
   dies, the pause before a chain starts, the gap between the
   Bloom's piano notes — these are structural. The Membrane between
   sounds is as meaningful as the sounds themselves.

---

## THE EXISTING ENGINE (What We Have)

All sound is procedural Web Audio API. No samples, no WAVs.

| Component | Oscillators | Notes |
|-----------|------------|-------|
| Pop sound | 1× biome oscType | Ascending chromatic per chain step |
| Archon pop | sine (60Hz) + square (90Hz) | Deep boom + crunch |
| Void Crack | sine (400→80Hz) + triangle (50→30Hz) | Eerie descending |
| Boss spawn | sine (40→200Hz) + sine (25Hz sub) | Rising + rumble |
| Boss hit | square (80→40Hz) | Punchy |
| Boss death | sine (50→20Hz) + saw (200→60Hz) + sine (800→2000Hz) | Boom + crunch + shimmer |
| BPGE ambient | noise + sine(32Hz) + triangle(65Hz) + LFO(0.15Hz) | Breathing drone, 40%+ madness |
| Bonus tap | sine (880→1400Hz) | Reward chime |

**What's missing:** Per-biome ambient drones, harmonic layering,
reverb/delay, melodic fragments, rhythmic elements, reactive
harmony, boss-specific themes, transition sounds.

---

## PHASE I — THE AWAKENING
*"The field awakens. Orbs drift in primordial silence."*

**baseNote: 240 Hz (≈B3) | oscType: sine**

### Ambient Drone: THE FIRST HUM

The simplest sound in the game. A single sine wave at 240 Hz,
barely audible (volume 0.03), with a slow LFO tremolo at 0.08 Hz
(one breath every 12 seconds). This is the Membrane's resting
state — a standing wave that has existed since before observers.

**Lore connection:** The orbs condensed from resonance. This drone
IS that resonance. It was here before the player arrived. It will
be here after.

**Chain interaction:** During chains, the drone gains a gentle
fifth (360 Hz, volume 0.015) that fades in proportional to chain
length and fades out 2 seconds after the chain ends. The
consonance rewards chaining — the Membrane LIKES being disturbed,
harmonically.

**Madness response:** As WT rises, the drone's LFO speeds up
(0.08 → 0.2 Hz at 50% WT) and a faint octave below (120 Hz)
fades in. The breathing quickens. Something is waking up.

### Pop Character
Pure sine. Clean, bell-like. Each pop rings clearly. No
distortion, no grit. The sounds of a world that hasn't been
hurt yet.

### Lore Audio Moment
At level 1, the very first tap produces a special sound: the
base note held for 1 full second instead of the usual 180ms
decay. The Membrane responding to its first observer. This never
happens again.

---

## PHASE II — THE STIRRING
*"Warmth bleeds through. The orbs grow restless."*

**baseNote: 262 Hz (≈C4, middle C) | oscType: sine**

### Ambient Drone: THE SECOND VOICE

Two sine oscillators: 262 Hz (fundamental) and 393 Hz (perfect
fifth above). The fifth is quieter (0.6× the fundamental volume).
Together they form the simplest harmony — the interval the
Membrane discovers when it first interacts with itself.

**Lore connection:** "Patterns emerge from chaos." The Membrane
has found its first harmonic relationship. Two frequencies that
agree.

**LFO:** Slightly faster (0.1 Hz). The fifth has its own LFO at
0.07 Hz (different phase), creating gentle beating — the
frequencies almost agree, but not quite. A warmth. An unease.

**Chain interaction:** Chains longer than 8 add a brief major
third (330 Hz) that pulses with the chain. The harmony thickens.
The Membrane is learning to sing.

**Madness response:** Above 30% WT, the fifth detunes slightly
(393 → 390 Hz), creating audible beating. The harmony is
starting to disagree with itself. A sawtooth undertone at 131 Hz
(sub-octave) fades in at 0.01 volume — the first hint of
corruption.

### Pop Character
Still sine, but with a slightly longer tail (200ms vs 180ms).
Pops overlap more. The field is getting denser, and the sounds
are starting to layer.

---

## PHASE III — THE FRACTURING
*"Fractures spread. Something watches from the edges."*

**baseNote: 294 Hz (≈D4) | oscType: sawtooth**

### Ambient Drone: THE CRACK

This is where the music breaks. The sawtooth oscillator replaces
sine — harmonically rich, buzzing with overtones. The fundamental
(294 Hz) is joined by a detuned copy (291 Hz), creating a slow
3 Hz beating that sounds like something vibrating wrong.

**Lore connection:** "The orbs pulse with stolen light. Gravity
bends wrong." The Membrane's clean resonance has fractured. The
harmonics are all present now (sawtooth = all integer harmonics),
but they're fighting each other.

**New element: the Archon undertone.** When an Archon forms on
screen, a sub-sine at 73.5 Hz (D2, two octaves below the
fundamental) fades in over the Archon's 2-5 second lifespan.
When it pops (or decays), the tone cuts abruptly. The Archon is
trying to sing its own note. It remembers wholeness.

**Chain interaction:** Chains add dissonance: a minor second
interval (311 Hz, ≈D#4) fades in at chain count > 5, creating
a harsh rub against the fundamental. Longer chains = more
tension. The Membrane is tearing and the sound reflects it.

**Madness response:** Above 40%, a filtered noise layer (bandpass
at 500 Hz, Q=4) breathes in sync with the LFO. It sounds like
wind through cracks. Above 60%, the detuning widens (291→288 Hz,
6 Hz beating). The fracturing accelerates.

### Pop Character
Sawtooth. Each pop buzzes. The overtones make chains sound denser,
grittier. The ascending chromatic scale now has harmonics — it
sounds like a distorted synthesizer climbing.

### Lore Audio Moment
The first time the Watcher becomes visible (around 40% WT), a
single bass note — 36.75 Hz (D1, four octaves below fundamental)
— pulses once, lasting 3 seconds. Felt more than heard. It does
not repeat at this exact pitch. It has announced itself.

---

## PHASE IV — THE HUNGER
*"Chains echo longer than they should. Time thickens."*

**baseNote: 330 Hz (≈E4) | oscType: sawtooth**

### Ambient Drone: THE APPETITE

Three oscillators. The harmony is a power chord — bare, heavy,
ambiguous:
- 330 Hz sawtooth (fundamental)
- 495 Hz sawtooth (fifth, detuned by -2 Hz for beating)
- 165 Hz sine (sub-octave, the stomach)

**Lore connection:** "The void between orbs is not empty. It
breathes." The sub-octave is the void's voice — always present,
always hungry. The power chord provides no emotional resolution.
It just *is*. Heavy.

**New element: the Echo.** "Chains echo longer than they should."
After every chain of 5+ pops, the final pop's frequency is held
as a ghost tone — a sine wave at the chain's peak frequency that
decays over 3 seconds (instead of the normal 180ms). The field
remembers the chain. Time thickens because the sound won't let go.

**Chain interaction:** The echo effect means chains CREATE the
ambient soundscape. A long chain leaves behind a sustained high
tone. Another chain starts while the echo lingers, adding another
sustained tone. Multiple echoes layer into an accidental chord —
sometimes consonant, sometimes clashing. The player is composing
without knowing it.

**Madness response:** Above 60%, the echoes lengthen to 5 seconds.
Above 80%, they gain a slow pitch-bend downward (dropping a whole
tone over their sustain). The echoes are sagging. Time is heavy.
The filtered noise from Phase III is continuous now, no longer
breathing — a constant wind.

### Pop Character
Sawtooth at higher base pitch. The ascending chromatic scale
reaches into piercing territory faster. At chain 30+, the sound
is aggressive, almost painful. The Membrane is screaming.

---

## PHASE V — THE DISSOLUTION
*"Boundaries dissolve. Their gaze is all that remains."*

**baseNote: 349 Hz (≈F4) | oscType: sine**

### Ambient Drone: THE CHOIR (fragment)

The return to sine is deliberate. After the sawtooth corruption
of Phases III-IV, the pure tone returns — but it is not
innocence. It is transcendence. The Membrane has passed through
corruption and come out the other side as something clearer and
more terrifying.

Four oscillators forming a major seventh chord:
- 349 Hz sine (F4 — fundamental)
- 440 Hz sine (A4 — major third)
- 523 Hz sine (C5 — fifth)
- 659 Hz sine (E5 — major seventh)

This is not a comfortable chord. The major seventh creates a
shimmering, unresolved beauty — like looking at something too
bright. The Membrane at maximum transparency sounds like glass
about to ring.

**Lore connection:** "Every pop tears the membrane further. The
Watchers drink." The chord is a fragment of the Choir's infinite
superposition — four harmonics from the democracy of sound.
Enough to glimpse the whole.

**Chain interaction:** Chains above 10 add the ninth (392 Hz, G4)
and the eleventh (466 Hz, Bb4). The chord becomes a stack of
thirds climbing upward. At chain 30+, the chord is so dense it
approaches the Choir — multiple simultaneous harmonics,
shimmering and vast. The player is, momentarily, singing with the
Choir.

**Madness response:** At 90%+ WT, the chord gains micro-detuning
on each voice (±1-3 Hz), creating a chorus/shimmer effect. At
100%, a very faint white noise layer (volume 0.01) is added to
everything — the Membrane's surface tension vibrating at all
frequencies. The Choir's democracy, at the threshold of hearing.

### Pop Character
Sine again, but at 349 Hz base — the highest starting pitch. Each
pop is a clear, high bell tone. Long chains sound like wind chimes
ascending into ultrasonic. Beautiful and slightly wrong.

### Lore Audio Moment
At 100% World Transparency, the BPGE ambient drone and the Phase
V chord merge into a single sustained tone. For 3 seconds, all
oscillators align to the same phase — a brief moment of perfect
coherence. Then the phases drift apart again. The Membrane almost
said something. The player will never know what.

---

## THE ABYSS
*"You sink below. A different eye opens in the deep."*

**baseNote: 180 Hz (≈F#3) | oscType: triangle**

### Ambient Drone: THE PRESSURE

The Abyss sounds like depth. Like weight. Like the ocean floor
in a world where the ocean is made of mathematics.

Two triangle oscillators:
- 180 Hz (fundamental) — heavy, present, the pressure
- 90 Hz sine (sub-octave) — the void beneath the void

**Triangle wave** is the defining timbre. It has odd harmonics
only (like a clarinet, like a closed pipe), which creates a
hollow, rounded tone. It sounds like something resonating inside
a confined space. Perfect for deep water. Perfect for pressure.

**New element: the Sonar.** Every 8-14 seconds (matching the
pressure wave cycle), a single sine ping at 720 Hz (two octaves
above fundamental) rings out — bright, brief (150ms), with a long
decay (2 seconds, volume envelope shaped like sonar). It echoes.
It bounces off something. Something out there.

**Chain interaction:** Chains in the Abyss sound like a
"distant marimba rising from underwater" (per the design doc).
The triangle wave gives each pop a rounder, woodier tone than
sine or sawtooth. The ascending chromatic scale sounds like
bamboo chimes sinking deeper and deeper — each note clear but
muffled, as if heard through water.

Chains above 15 add a sub-harmonic pulse: the sub-octave (90 Hz)
thumps once per pop in sync with the chain, creating a heartbeat
underneath the chime ascent. The Leviathan's pulse.

**Pressure wave sound:** The existing `playAbyssWooshSound()` (35
Hz → 20 Hz sweep) is already excellent. Layer on top: a brief
reverse-envelope triangle tone at 360 Hz (octave above
fundamental) that SWELLS just before the whoosh hits — a warning.
The pressure announces itself one beat early.

**Madness response:** As WT rises, the sub-octave gains volume
and drops in pitch (90 → 60 Hz at full madness). The pressure
increases. The sonar ping detuning widens (720 → 740 Hz) — the
echo is coming back *wrong*. Something between the ping and the
return is changing the frequency. The Abyss Watcher breathes
between the notes.

**Bioluminescent vivid mode:** Above 70% WT (when the ABYSS
flips from greyscale to vivid bioluminescence), the triangle
oscillators gain a companion: a quiet sine at 540 Hz (the major
third) with slow vibrato (±5 Hz at 0.3 Hz rate). The light has
a sound. It hums.

### Boss (Leviathan) Theme

The Leviathan is not scored with melody. It is scored with
RHYTHM.

When the Leviathan spawns, a low pulse begins: sine at 45 Hz,
pulsing at 0.8 Hz (48 BPM — a sleeping heartbeat). This is the
Leviathan's breath. It is slow, steady, and relentless.

**Grabbing state:** The pulse speeds to 1.2 Hz (72 BPM). Each
tentacle grab adds a quiet click (square wave burst, 200 Hz,
5ms duration) — the sound of suction cups attaching. Multiple
grabs create a polyrhythmic clicking pattern. Alien. Hungry.

**Bloat state:** The pulse STOPS. Silence for 0.5 seconds
(the Leviathan holds its breath). Then a rising pressure tone:
triangle at 90 Hz climbing to 180 Hz over the 3-second bloat
window. The pitch communicates urgency — hit it NOW.

**If countered (HUNGER RUPTURES):** The rising tone cuts to
silence, replaced by a burst: the Leviathan's 45 Hz sine
displaced up by two octaves (180 Hz) for 0.5 seconds, then
crashing back down. The creature's internal pressure
released violently.

**If uncountered (IT FEEDS):** The rising tone resolves DOWN
to 30 Hz — a sub-bass boom that shakes the speaker. The
Leviathan exhales. Satisfied.

---

## THE ECLIPSE
*"Where time folds and shadows are the only real thing."*

**baseNote: 220 Hz (A3) | oscType: sine (reversed envelope)**

### Ambient Drone: THE MIRROR

The Eclipse sounds like music played backward. Not literally
reversed audio — but reversed ENVELOPES. Every tone swells IN
instead of decaying out. Sounds build to their peak and then
cut off. The Membrane's time runs backward here.

Two sine oscillators:
- 220 Hz (A3 — the tuning standard, the universal reference
  pitch, chosen because the Eclipse is where all reference
  points invert)
- 330 Hz (E4 — perfect fifth)

Both have **reverse envelopes:** volume starts at 0, swells to
peak over 2 seconds, then cuts to 0 instantly. Repeat. The
effect is deeply unsettling — sounds that approach instead of
recede. Things getting closer.

**New element: the Echo-Before.** A pre-echo system. 0.5 seconds
BEFORE a pop sound plays, a ghost of that sound plays at 20%
volume and reversed pitch direction (descending instead of
ascending). The player hears the echo of the pop before the pop
happens. Effect is subtle but creates a persistent feeling of
deja vu.

**Dark Prism / Time Echo sound:** When a Dark Prism triggers
and the Time Echo snapshot activates, ALL ambient oscillators
simultaneously pitch-shift down one semitone (220 → 207.6 Hz)
over 0.3 seconds. They hold for the 3-second echo duration.
When the rewind completes, they shift back up. The entire
soundscape sags and recovers. Time displacement as pitch
displacement.

**Chain interaction:** Shadow ball implosion chains have INVERTED
pitch direction. Instead of ascending chromatic (each pop higher),
shadow chains DESCEND — each pop is one semitone LOWER. The
chains sink instead of climb. Normal orb chains still ascend.
When both fire simultaneously (shadow setup → normal payoff),
the descending and ascending scales cross — creating momentary
consonances and dissonances as the falling and rising notes
intersect. This is musically extraordinary.

**Madness response:** As WT rises, the reverse-envelope cycle
shortens (2s → 0.8s at full madness). The swells come faster.
The cuts are more abrupt. Time is folding tighter.

### Boss (Palindrome) Theme

The Palindrome's music is, of course, a palindrome.

A 4-note motif: A3 - C4 - E4 - C4 (220 - 262 - 330 - 262 Hz).
It ascends and descends identically. It plays forward. Then it
plays backward. They sound the same. The Palindrome IS its own
reflection.

**Echo attack state:** The motif plays at 2× speed (compressed
into 1 second). During the rewind, it plays at 0.5× speed
(stretched over 3 seconds). Same notes. Different time.

**Counter window:** During the 1.5s counter window, only the
middle note sustains (C4 — 262 Hz) as a held drone. This is the
FULCRUM — the center of the palindrome, the point of symmetry,
the moment to strike.

**On counter-hit:** The held C4 leaps up one octave (524 Hz) —
bright, triumphant, cutting. The palindrome is broken. Its
symmetry shattered for 2.5 seconds.

---

## THE SCAFFOLD
*"Where the Membrane became bone."*

**baseNote: 196 Hz (G3) | oscType: square (filtered)**

### Ambient Drone: THE LATTICE

Square wave — all harmonics, even and odd, the most "structured"
waveform. But filtered through a low-pass at 600 Hz, softening
the buzz into a warm, woody, hollow tone. It sounds like
resonating stone. Like a cathedral built from crystallized math.

Two filtered-square oscillators:
- 196 Hz (G3 — fundamental, the "column")
- 294 Hz (D4 — fifth above, the "arch")

Together: a bare fifth. The most structurally stable interval in
music. No warmth, no sadness, no joy — just structure. Two notes
that agree because geometry says they must.

**Lattice resonance:** The lattice walls HUM. Each wall segment
is assigned a frequency based on its length (shorter walls =
higher pitch, proportional to 196 / wallLength). When a bolt
hits a wall, that wall's frequency rings briefly (100ms, sine,
volume 0.04). A ricochet through multiple walls plays a rapid
ascending or descending scale of wall tones — the lattice
playing itself like a xylophone.

**Chain interaction:** Once a bolt triggers a chain inside the
lattice, the standard ascending chromatic pops play — but
through the square oscillator, they sound harder, more
percussive. Each pop has a sharper attack (10ms vs 30ms rise)
and shorter tail (120ms vs 180ms). The chains SNAP instead of
RING. Glass breaking, not bells chiming.

**Bolt sound:** A bright, percussive attack. Sine at 1200 Hz,
10ms duration, volume 0.1. On ricochet: same but at 1500 Hz.
On orb impact: sine sweep 1200 → 400 Hz in 50ms (the bolt
transferring its energy). Each bolt type has a harmonic variant:
- Standard: 1200 Hz sine
- Piercer: 1600 Hz (brighter, thinner)
- Scatter: 800 Hz, splitting into 3 tones at 800/1000/1200
  (the triad dispersing)
- Graviton: 600 Hz with 4 Hz vibrato (the curve in audible form)

**Madness response:** As WT rises, the low-pass filter opens
(600 → 1200 Hz), revealing more harmonics. The warm stone sound
becomes harsher, more metallic. The Scaffold's calcification is
being stressed. At 80%+, the fifth detunes (294 → 290 Hz). The
structure disagrees with itself. Cracks are forming in the sound.

### Boss (Bastion) Theme

The Bastion is scored with GEOMETRY.

Each concentric shell produces a tone based on its polygon count:
- Outer (8 segments): 196 × 8/4 = 392 Hz (G4, octave)
- Middle (6 segments): 196 × 6/4 = 294 Hz (D4, fifth)
- Inner (4 segments): 196 × 4/4 = 196 Hz (G3, unison)

These three tones form a G power chord stack (G3-D4-G4).
As shells are damaged, their tone wavers (gains vibrato
proportional to damage: 0 Hz at full HP → ±8 Hz near death).
When a shell breaks entirely, its tone cuts out. The chord
thins. The structure audibly collapses.

**At 25% HP — "THE SCAFFOLD SCREAMS":** All remaining tones
pitch-bend UP one semitone over 2 seconds (G3→Ab3, D4→Eb4,
G4→Ab4). The chord becomes dissonant — a diminished shape
screaming against its own geometry. Then silence when the shell
shatters.

---

## THE VISCERA
*"Where the Membrane remembers it was alive."*

**baseNote: 147 Hz (D3) | oscType: sine (modulated)**

### Ambient Drone: THE PULSE

A heartbeat. 72 BPM. The most human rhythm in a game about
inhuman mathematics.

The drone is built from the heart:
- 147 Hz sine (D3) — the lub (30ms attack, 200ms decay)
- 73.5 Hz sine (D2, sub-octave) — the dub (50ms after the lub,
  20ms attack, 150ms decay)

The "lub-dub" repeats every 833ms (72 BPM). Between beats: near-
silence. Just the faintest sustained 147 Hz at volume 0.005 —
the tissue at rest, not silent but waiting.

**Lore connection:** "The Membrane has a heartbeat here. We
measured it. It matches the observer's. Every observer's.
Simultaneously." The drone synchronizes to human resting heart
rate because the Viscera IS the observer's body reflected in the
Membrane. You are hearing yourself.

**Fluid connection tone:** When two blobs form a tissue
connection (metaball bridge), a quiet sine tone at the geometric
mean of their sizes sounds for 200ms. Larger connections =
lower pitch. Small connections = higher. The tissue network
sings as it forms — a constantly shifting chord of connection
tones that rises and falls as blobs drift together and apart.

**Chain interaction:** Chains do not ascend chromatically here.
Instead, the chain signal (traveling visually through tissue at
200px/s) produces a sustained, SLIDING tone — a portamento from
the first blob's pitch to the last blob's pitch, gliding
continuously as the signal travels. The pitch is mapped to
blob radius: small blob = high (600 Hz), large blob = low
(100 Hz). A chain through a cluster of mixed-size blobs creates
a swooping, organic melody — rising, dipping, rising again as
the signal passes through different-sized tissue nodes.

This is the most melodic biome. The player doesn't choose the
melody — the field's topology does. But the player chose WHERE
to start the chain, which determines the path and therefore the
tune. You are conducting an organ.

**Madness response:** Above 50% WT, the heartbeat gains an
arrhythmia — occasional skipped beats (5% chance per cycle) or
double-beats (3% chance). Each irregularity is brief but deeply
unsettling. Above 80%, a continuous mid-frequency hum (220 Hz,
triangle) fades in — the tissue is inflamed. Feverish. The
connection tones become unstable, each gaining ±3 Hz vibrato.
The body is sick.

### Boss (Heart) Theme

The Heart IS the ambient heartbeat. When it spawns, the drone
doesn't change — because the drone was always the Heart's voice.
The player realizes they've been hearing the boss since they
entered the biome.

**Arterial severance:** Each severed artery drops one component
of the heartbeat. The first severance cuts the sub-octave "dub"
— now only the "lub" remains. Second severance: the lub gains
an irregular rhythm (±50ms timing jitter). Third: volume drops
to half. Fourth: the heartbeat becomes a faint, thready whisper.

**Phase 3 — THE HEART REMEMBERS:** The heartbeat SLAMS back to
full volume and perfect rhythm — 72 BPM, loud, defiant. Plus a
new tone: a screaming sine at 588 Hz (D5, two octaves above
fundamental) — the Heart's pain, high and piercing, sustained
for the entire phase. The bass says "I'm alive." The treble
says "I'm dying."

**Death:** One final beat. The loudest sound in the game. 147 Hz
sine at volume 0.4 (2× normal boss death volume), 3 second
sustain, no decay curve — just a hard cut at 3 seconds. Then:
silence. Real silence. The BPGE ambient cuts, the connection
tones cut, the fluid network stops humming. For 10 seconds, the
game is mute. The Membrane has forgotten it was alive.

Then, at the 10-second mark, the normal biome drone fades back
in. But one semitone higher. 147 → 155.6 Hz. D3 → Eb3.
Something has changed. The Heart is gone but the Membrane
remembers the pitch.

---

## THE DELIRIUM
*"Where the counting counts itself."*

**baseNote: 220 Hz (A3) | oscType: sine**

### Ambient Drone: THE HARMONIC SERIES

The Delirium's drone is the harmonic series ITSELF — the
mathematical foundation of all music, made literal.

Active oscillators change with symmetry order:
- **2-fold:** 220 Hz + 440 Hz (octave, ratio 2:1)
- **3-fold:** + 330 Hz (fifth, ratio 3:2)
- **4-fold:** + 293 Hz (fourth, ratio 4:3)
- **6-fold:** + 352 Hz (major third, ratio 5:4) + 264 Hz
  (minor third, ratio 6:5)

As symmetry increases, the chord builds. At 6-fold, five
simultaneous tones ring: a dense cluster of just-intonation
intervals. It sounds ancient — like a bell foundry, like
Pythagorean tuning, like mathematics singing about itself.

**Lore connection:** The harmonic series is not a metaphor. It is
the mathematical relationship between whole numbers expressed as
sound. 2:1, 3:2, 4:3, 5:4, 6:5 — each symmetry order adds the
next ratio. The Delirium IS the harmonic series made spatial.

**Symmetry transition sounds:** When symmetry order changes, the
new interval fades in over 1 second while the old one (if losing
a fold) fades out. The transition chord — two overlapping
harmonics — creates brief, complex beating patterns. The
2→3 transition adds the fifth; the 3→4 transition adds the
fourth (which beats against the fifth at 330 vs 293 Hz = 37 Hz
beating). The player hears the geometry reorganizing.

**Pattern completion sounds:** When a geometric formation
completes and glows gold, an ascending arpeggio plays: N notes
for an N-sided pattern, each note a harmonic of 220 Hz.
- Triangle (3): 220 - 440 - 660 Hz (harmonics 1, 2, 3)
- Square (4): 220 - 440 - 660 - 880 Hz
- Pentagon (5): 220 - 440 - 660 - 880 - 1100 Hz
- Hexagon (6): 220 - 440 - 660 - 880 - 1100 - 1320 Hz
- Star (10): the full harmonic series up to the 10th partial

Each note rings for 150ms, overlapping the next by 50ms. The
arpeggio IS the shape — its sonic fingerprint.

**Fever mutation sound:** The ambient drone pitch-bends down one
semitone (all oscillators: 220 → 207.6 Hz, etc.) over 0.5
seconds. Holds for the mutation duration. Then bends back up.
"The Choir's chord, displaced by one semitone. The Delirium is
playing the next chord early."

**Chain interaction:** Mirror pops create spatial audio cues.
The original pop plays center. Mirror pops are panned based on
their angular position relative to center: a 3-fold mirror at
120° plays panned right, 240° panned left. At 6-fold, the pops
create a spinning stereo field — pops appear to rotate around
the listener. The symmetry is audible as spatial position.

**Madness response:** Above 50% WT, the harmonic tones gain
micro-detuning (each ±1-2 Hz from just intonation). The perfect
ratios waver. Mathematics is dreaming, and dreams are imprecise.
Above 80%, an additional oscillator appears: the 7th harmonic
(7:4 ratio, 385 Hz) — the "blue note," the harmonic that
doesn't fit the Western scale. It is the Delirium acknowledging
that its own system is incomplete.

### Boss (Theorem) Theme

The Theorem's mandala hums. Each ring contributes a tone:
- Triangle ring (3 slots): 660 Hz (3rd harmonic)
- Hexagon ring (6 slots): 1320 Hz (6th harmonic)
- Dodecagon ring (12 slots): 2640 Hz (12th harmonic)

Empty slots silence their contribution. As the Theorem captures
orbs and fills slots, each ring's tone fades in proportionally
(1/3 volume per slot for the triangle, 1/6 per slot for the
hexagon, etc.). A complete ring rings at full volume.

**The critical tension:** A nearly-complete Theorem hums a
near-perfect chord (harmonics 3, 6, 12 — all octave-related,
pure consonance). It sounds BEAUTIFUL. The player must destroy
something that sounds perfect. This is the musical expression of
the lore: "preserving the contradiction means preventing
resolution."

**Contradiction Orb:** When the red Contradiction Orb pulses,
it emits a brief tritone (311 Hz, the "devil's interval" against
the 220 Hz base). The tritone is the most dissonant interval in
Western music — it signals "this is the flaw." The player learns
to listen for it.

**Death:** Each simplification step removes a harmonic:
- Dodecagon dissolves: 2640 Hz fades out
- Hexagon dissolves: 1320 Hz fades out
- Triangle dissolves: 660 Hz fades out
- Line: only 220 Hz remains (the fundamental, alone)
- Point: 220 Hz fades to silence over 2 seconds

Then: a single tone. 233 Hz. A3 + one semitone = Bb3.

The Choir has advanced.

---

## CROSS-BIOME MUSICAL ARCHITECTURE

### The Ascending Scale Across All Biomes

| Biome | Base Note | Note Name | Interval from Phase I |
|-------|-----------|-----------|----------------------|
| Expanse | 120 Hz | B2 | Sub-octave (ground) |
| Viscera | 147 Hz | D3 | Minor third |
| Abyss | 180 Hz | F#3 | Tritone |
| Scaffold | 196 Hz | G3 | Fifth |
| Eclipse | 220 Hz | A3 | Major sixth |
| Delirium | 220 Hz | A3 | Major sixth (shared) |
| Phase I | 240 Hz | B3 | Unison (home) |
| Phase II | 262 Hz | C4 | Minor second |
| Phase III | 294 Hz | D4 | Minor third |
| Phase IV | 330 Hz | E4 | Perfect fourth |
| Phase V | 349 Hz | F4 | Tritone |

The main Phase progression (I→V) ascends B3-C4-D4-E4-F4 — a
whole-tone scale fragment, harmonically ambiguous, neither major
nor minor. The Membrane doesn't choose. It just climbs.

The special biomes sit BELOW the main phases, forming a bass
foundation. The deepest (Expanse at 120 Hz) is the oldest
region. The highest (Phase V at 349 Hz) is the most dissolved.

### The Choir's Chord (Full Reconstruction)

If a player could hear ALL biome drones simultaneously, they
would hear: B2, D3, F#3, G3, A3, B3, C4, D4, E4, F4 — plus
all their associated fifths, thirds, and harmonic additions.

This is a dense chromatic cluster spanning two octaves. It
approaches the Choir's chord — "every frequency that has ever
existed insisting on being heard at once." Each biome is a
fragment of this superposition. The game's progression is a
journey through the Choir's chord, one note at a time.

### The Semitone Shift (Lore Events)

Every boss death shifts the ambient drone up one semitone.
This means:
- After first boss: Phase I drone = 240 → 254 Hz
- After second boss: 254 → 269 Hz
- After the fifth boss kill, Phase I's drone has climbed to
  approximately Phase II's starting point.

The game AGES harmonically. A late-game Phase I sounds different
from an early-game Phase I. The Choir has been singing, and the
player has been advancing its chord. This is the musical
expression of the lore: each pop tears the Membrane, each boss
death shifts reality. You can hear the scar.

---

## IMPLEMENTATION NOTES

### What Can Ship Immediately (Modify Existing Functions)

1. **Chain echo (Phase IV+):** After `playPopSound`, schedule a
   second oscillator at the final chain frequency, 50ms after
   the chain ends, with 3s decay. ~15 lines added to
   `playPopSound`.

2. **Reverse envelopes (Eclipse):** In `playPopSound`, check
   biome. If Eclipse: swap the gain envelope (ramp UP to peak,
   hard cut). ~5 lines.

3. **Descending shadow chains (Eclipse):** New function
   `playShadowPopSound(chainIndex)` — same as `playPopSound`
   but frequency = `baseNote * (1/1.055)^chainIndex`. ~15 lines.

4. **Heartbeat drone (Viscera):** Add to `updateBPGEAmbient()` —
   when biome is Viscera, replace the noise/LFO drone with a
   timed lub-dub sine pulse at 72 BPM. ~30 lines.

5. **Portamento chains (Viscera):** Modify `playPopSound` for
   Viscera biome: instead of discrete pops, use
   `osc.frequency.linearRampToValueAtTime` to glide between
   pop frequencies over the signal travel time. ~20 lines.

### What Requires New Systems

6. **Per-biome ambient drones:** Replace or extend
   `initBPGEAmbient()` with biome-specific oscillator
   configurations that activate/deactivate on biome change.
   ~100-150 lines.

7. **Harmonic series drone (Delirium):** Dynamic oscillator
   bank that adds/removes partials based on symmetry order.
   ~60 lines.

8. **Boss themes:** Per-boss-type ambient oscillator setup in
   `trySpawnBoss`, per-boss-state modulation in `updateBoss`.
   ~80 lines per boss type.

9. **Lattice resonance (Scaffold):** Frequency assignment per
   wall segment, ring-on-impact in bolt collision handler.
   ~40 lines.

10. **Semitone shift system:** Global pitch multiplier
    (`G.choirShift = 1.0`, incremented by `* 1.0595` on boss
    death) applied to all `baseNote` lookups. ~5 lines.

### Performance

All proposed sounds use OscillatorNode and GainNode — the same
primitives already in use. Web Audio API handles mixing in a
dedicated audio thread. No performance impact on the Canvas2D
render budget.

Maximum simultaneous oscillators at any point:
- Ambient drone: 2-6 oscillators (biome dependent)
- Active chain: 1 oscillator (recycled per pop)
- Chain echo: 1-3 sustained oscillators (decaying)
- Boss theme: 1-4 oscillators
- Total peak: ~12-15 simultaneous oscillators

Modern browsers handle 50+ simultaneous oscillators without
issue. We are well within budget.
