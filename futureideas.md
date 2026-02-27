# Future Ideas

## Lighting Engine Research (2026-02-27)

### Top 3 Options (all free, all Canvas2D compatible)

| Option | Approach | Lines of Code | Shadows? | Performance | Deps |
|--------|----------|--------------|----------|-------------|------|
| **A) illuminated.js** | Library, MIT, inline-able | ~1000 lines (paste into script tag) | Real geometric shadows from circles (DiscObject) | Great - cached gradients, no raycasting | Zero |
| **B) DIY Tangent Shadow** | Custom code - circle tangent projection + composite ops | ~100-150 lines | Real shadows (same math as illuminated.js) | Best - minimal overhead | Zero |
| **C) Composite-only fake** | globalCompositeOperation tricks | ~40-60 lines | Fake (offset dark ellipses) | Fastest | Zero |

### Recommendation: Option B (DIY Tangent Shadow)

illuminated.js is 1000 lines of someone else's code from 2012. The actual shadow math for circles is only ~80 lines. Writing the minimal version gives full control and zero bloat.

### Sources & References

- **illuminated.js** - https://github.com/gre/illuminated.js/ (MIT, ~420 stars, archived but stable)
  - Demo: http://gre.github.io/illuminated.js/
  - Blog: https://greweb.me/2012/05/illuminated-js-2d-lights-and-shadows-rendering-engine-for-html5-applications
  - ~1000 lines vanilla JS, zero deps, has DiscObject for circles, Lamp for point lights, DarkMask for darkness overlay
  - Shadows via tangent line projection from light to circle edges, composited with destination-out

- **Nicky Case "Sight & Light"** - https://ncase.me/sight-and-light/ (GitHub: https://github.com/ncase/sight-and-light)
  - ~1100 stars, ~100-150 lines core algorithm
  - Raycasting approach - casts rays toward line segment endpoints
  - Only works with line segments, NOT circles (would need polygon approximation)
  - Better for wall/obstacle games, not ideal for circular orbs

- **Red Blob Games 2D Visibility** - https://www.redblobgames.com/articles/visibility/
  - JS Port: https://github.com/Silverwolf90/2d-visibility (MIT, 170 stars)
  - Angular sweep algorithm, excellent reference material
  - Also line-segment based, not circle-native

- **Slembcke's 2D Lighting Techniques** (GPU/WebGL reference)
  - Overview: https://www.slembcke.net/blog/2DLightingTechniques/
  - Hard shadows: https://www.slembcke.net/blog/SuperFastHardShadows/
  - Soft shadows: https://www.slembcke.net/blog/SuperFastSoftShadows/
  - Gold-standard GPU techniques, requires WebGL shaders

- **Jaloko LightingEngine** - https://github.com/Jaloko/LightingEngine (MIT, 52 stars, WebGL-based, needs npm build)

- **MDN globalCompositeOperation** - https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/globalCompositeOperation

---

### Implementation Plan - Test in TESTLEVEL Biome

#### Phase 1: Ambient Light Layer (Fake lighting, no shadows)
Goal: Dark overlay with a radial light at cursor position

1. Create an offscreen canvas (lightCanvas) at game resolution
2. Each frame, fill it with darkness (rgba(0,0,0,0.7))
3. Use globalCompositeOperation = 'destination-out' to punch a radial gradient hole at cursor position
4. Draw lightCanvas over the game using multiply composite mode
5. Only activate when TESTLEVEL biome is active - zero impact on normal gameplay
6. Adjustable: light radius, intensity, falloff curve

#### Phase 2: Orb Shadow Casting (Real geometric shadows)
Goal: Orbs block the cursor light and cast shadows onto the dust/BG

1. For each visible orb, compute tangent lines from cursor (light source) to circle edge:
   - theta = asin(radius / distance_to_light)
   - Two tangent directions = atan2(dy, dx) +/- theta
2. Project tangent rays outward to light radius -> forms a trapezoid shadow polygon
3. Draw shadow trapezoids onto the light canvas using destination-out (shadows block light)
4. Skip orbs outside light radius (early cull)
5. Skip orbs too close to light (< radius - they'd be fully inside the light)

#### Phase 3: Dust Layer + Shadow Receiving
Goal: Subtle particle dust layer that receives shadows, giving depth

1. Pre-generate a static dust texture (offscreen canvas with scattered tiny dots)
2. Render dust layer between BG and orbs
3. Dust is only visible in lit areas (multiply with light map)
4. Creates visible shadow shapes on the "floor" - orbs appear to float above it

#### Phase 4: Polish & Performance
- Cache light gradient (redraw only when radius changes)
- LOD: skip shadow computation for tiny/distant orbs
- Optional: second light source at boss position
- Optional: colored light tinting (warm cursor light, cool boss light)
- FPS counter to validate 60fps with 30-50 orbs

---

## Nexus Boss Upgrade — "Dark Souls" Attacks (2026-02-27)

Two new attack states for the Nexus boss following a "telegraph → danger → counter-play" loop.
Both drop/bypass the shield — boss commits to attacks, creating vulnerability through aggression.

### Feature 1: Void Beam (Direct Threat)

**Concept:** Nexus targets the player's last tap location and fires a beam that destroys orbs in its path. Forces spatial awareness — "where I tap matters."

**States:** `charging_beam` (2.0s) → `firing_beam` (0.6s)
- 30% chance to replace an `absorbing` cycle (only after first boss kill)
- Charge: all eyes converge on muzzle point, dashed laser-sight tracks last tap, locks aim 0.5s before fire
- Fire: beam width `b.radius * 0.6`, length 900px. Orbs in path destroyed (no pop, no chain, no heal — just vaporized)
- After fire: longer idle (4-5s recovery window)
- Erratic: 1.3s charge, wider beam (0.8x radius), can refire (15% chance), sight jitters ±15°

### Feature 2: Singularity Pulse (Big Move + "Parry" Counter)

**Concept:** Massive gravity implosion. All eyes close (the tell), orbs yanked inward. Player can counter-hit during the pull for 3× damage, or miss and get punished.

**States:** `singularity_telegraph` (2.0s) → `singularity_pull` (1.5s) → `singularity_recover`
- Fires every 18-22s, interrupts current state (deferred during beam)
- Telegraph: ALL eyes close simultaneously (unique tell), body shrinks to 80%, vortex spiral, "CONVERGENCE" text
- Pull: strength 350, radius 400px. Orbs at `radius * 0.4` devoured for +1 HP each
- **Counter-hit:** Pop an orb during pull that explosion-overlaps boss → 3× damage, "RUPTURED", boss stunned 2.5s with shield down, pulled orbs scatter outward as chain material
- **Miss:** Boss heals +2 HP, shockwave scatters field, shield restores free, "IT FEEDS"
- Erratic: 12-16s cooldown, 1.3s telegraph, 1.0s window, strength 450, heals +4 on miss

**Updated state machine:** Normal cycle + beam branch + singularity interrupt. See `nexus-brief.md` for full flow diagram, constants, implementation hooks with line numbers, and rendering specs.

---

## New Biome Concepts (2026-02-27)

Three new biomes, each breaking the Membrane in a different way. Full lore, visual specs, mechanics, and boss designs in `biome-concepts.md`.

### Biome A: THE ECLIPSE — *Where time folds and shadows are the only real thing*

**Core twist:** Shadow balls become the primary interactive entity. The Dark Prism mechanic becomes the central gameplay tool instead of a rare emergent event.

**Gameplay:**
- Field is ~60% shadow balls, ~40% normal orbs (normals are ghostly, rare)
- Shadow balls are now poppable — but chain in REVERSE (implosion, not explosion, with 0.3s delayed propagation)
- Shadow implosion chains pull normal orbs inward, clustering them for real explosive chains
- Shadow pops give no score/XP — they are purely setup tools for sculpting the field
- **Dark Prism upgrade → Time Echo:** 5+ shadows cluster → snapshot all positions → 3s normal play → all surviving orbs REWIND to snapshot positions. Enables "double-dip" — pop a cluster, rewind, pop the same formation again

**Boss: THE PALINDROME** — Temporal entity existing in reversed causality. Shows attack results FIRST, then un-attacks (reverse animation). Counter window during the rewind when field passes through the predicted vulnerable formation (3× damage). Sheds shadow balls from its body — both its shield cloud and the player's Dark Prism fuel.

**Lore:** The Membrane's reverse face has punched through. Watchers are in FRONT of the Membrane. The player is on the other side. Observation runs backward.

**Builds on:** Shadow ball system (lines 3232-3630), Dark Prism detection (lines 3523-3583), gravity inversion timer.

---

### Biome B: THE SCAFFOLD — *Where the Membrane became bone*

**Core twist:** Tap-to-pop replaced by sling-to-shatter. The Membrane has calcified into rigid lattice — tapping does nothing. Player launches resonance bolts via drag-and-release (slingshot).

**Gameplay:**
- Touch-hold-drag-release fires a bolt along predicted trajectory (including ricochet preview)
- Bolts ricochet off lattice walls (20% energy loss), penetrate orbs (40% energy loss), continue until energy = 0
- Orbs are embedded in lattice, fixed positions — once hit by bolt, standard chain propagation begins
- Each field is a geometry puzzle: plan angles, choose which walls to break vs bounce off
- 4 bolt types: Standard (3 bounces), Piercer (no bounce, 5 orb penetration), Scatter (splits into 3), Graviton (slight homing)
- Bolts cost 1 tap each — preserves core resource tension

**Boss: THE BASTION** — Crystalline fortress, not a creature. 3 concentric rotating shell layers protecting a core orb. Outer = 8 segments × 2 HP, middle = 6 × 3 HP (rotating), inner = 4 × 4 HP (counter-rotating). Fires lattice spikes that encase free orbs. At 50% HP shell fragments become redirectable projectiles. At 25% "THE SCAFFOLD SCREAMS" — one hit shatters entire remaining shell.

**Lore:** The Membrane learned to defend itself. Rigidity as resistance. Breaking it is the field remembering that only cascades keep it alive.

**Builds on:** Tap resource economy, chain propagation, blast radius system. New: sling input, bolt entity, lattice structural layer.

---

### Biome C: THE VISCERA — *Where the Membrane remembers it was alive*

**Core twist:** Orbs are soft-body metaballs — lava-lamp fluid blobs that stretch, merge, flow, and connect through visible living tissue. Chains propagate as visible nerve signals traveling through the tissue at ~200px/s.

**Gameplay:**
- Blobs within `combinedRadius * 2.5` visually merge (metaball rendering) and form fluid clusters
- Connected blobs chain GUARANTEED — but propagation takes real time through tissue (not instant)
- **Tension mechanic:** Stretched clusters = fast signal but can SNAP (breaking chain path). Compact clusters = slow signal but devastating burst power
- **Free sculpting:** Tap empty space = pressure wave (push blobs apart, increase tension). Long-press blob = pinch (pull connected blobs together, decrease tension)
- Droplets from pops bridge disconnected clusters — timing multiple chains to overlap via droplet spray

**Boss: THE HEART** — Literal pulsing organ at field center, 72 BPM (matching background). Connected by 4 arterial tethers (5 HP each). Phase 1: sever arteries by routing chain signals through them. Phase 2: direct assault while defending against arrhythmia attacks, clots, and artery regrowth. Phase 3 (<30% HP): massive inward pull — converging mass is either consumed (heal) or chained mid-flow for the biggest cascade in the game. Death: one final beat, then silence. "THE MEMBRANE FORGETS IT WAS ALIVE."

**Lore:** Below the Abyss, the Membrane became tissue. Watchers are silent — watching with the intensity of something recognizing itself. The heartbeat matches every observer's. Simultaneously.

**Builds on:** Orb merging system, gravity wells, chain propagation. New: metaball rendering, fluid cluster tracking, tension value, signal speed propagation.

---

### Thematic Arc of the Three Biomes

| Biome | What Breaks | Player Role | Feel |
|-------|-------------|-------------|------|
| Eclipse | Causality | Sculptor of time | Cerebral — think two steps ahead |
| Scaffold | Structure | Siege engineer | Kinetic — aim, fire, shatter |
| Viscera | Life itself | Surgeon / mercy killer | Emotional — kill something alive |

---

---

## Pseudo-3D / Real 3D Exploration (2026-02-27)

How to add depth and dimensionality to the game without tanking performance.
Current renderer: single Canvas2D, DPR capped at 2×, up to 300 orbs + 800 particles + 600 shadow balls. No WebGL. Existing pseudo-3D: depth-of-field fade on generation orbs, parallax Watcher/galaxy layers, velocity stretch deformation, zoom-as-camera-distance, ABYSS depth fog, 4-tier LOD system.

### What's Already Working As Pseudo-3D

These existing features already establish a depth language:

| Feature | Where | Depth Cue |
|---------|-------|-----------|
| Zoom level (2.0→0.5) | Lines 7671-7676 | Camera pulling back as madness grows |
| Watcher behind galaxy behind field | Layers 1c, 1d, 6 | 3 discrete parallax planes |
| Generation-based DoF fade | Lines 9298-9304 | Smaller/later orbs fade = "further away" |
| Velocity stretch (squash & stretch) | Lines 9341-9352 | Motion implies volume |
| ABYSS depth fog gradient | Lines 11001-11008 | Bottom = deeper |
| LOD tier system | Lines 9219-9226 | Distant orbs simplify (fewer details) |

These are free — already in the renderer. New 3D work should build on this vocabulary.

---

### TIER 1: Free / Near-Free Enhancements (0-1ms, no new canvases)

#### A. Depth-Sorted Orb Rendering with Z-Layers

Assign every orb a `z` value (0.0 = screen plane, -1.0 = deep background, +1.0 = foreground popping toward camera). Sort `G.circles` by `z` before rendering. Scale each orb by `1 + z * 0.3` and offset alpha by `1 - abs(z) * 0.3`.

**When orbs pop:** z briefly spikes to +0.5 (orb "jumps toward camera" during the pop flash), then the fragments settle at z = -0.2 (they fall back). Chain links between different z-planes draw with slight perspective convergence.

**Cost:** One `Array.sort()` per frame on up to 300 elements (~0.02ms). Per-orb scale change is a single multiply already happening in the deformation code. Effectively free.

**Visual payoff:** Orbs stop feeling like they're on a flat sticker. The field has genuine forward/back layering. Pops feel like they "come at you."

#### B. Parallax Response to Cursor

Background layers (galaxy video, Watcher, nebula clouds, field stars) shift position based on cursor offset from center — a "looking around" effect:

```
parallaxOffsetX = (cursorX - centerX) * layerDepthFactor
parallaxOffsetY = (cursorY - centerY) * layerDepthFactor
```

Layer depth factors: Watcher 0.02, galaxy 0.04, nebula clouds 0.06, field stars 0.08, orbs 0 (anchored). Moving the cursor left makes the deep background drift right, implying a camera parallax through a 3D volume.

**Cost:** 4 extra multiplications per frame. Truly zero.

**Visual payoff:** The field suddenly feels like a window into a deep space, not a flat image. This is the single highest-impact-to-effort ratio enhancement.

#### C. Drop Shadows Under Orbs

For every orb, draw a dark ellipse offset below it (+4px y, 0.6x width, 0.3x height, alpha 0.15). The shadow implies the orb is floating above a surface.

**Cost:** One extra `ctx.ellipse()` + fill per orb. At 300 orbs: ~0.3ms. Could batch into a single `beginPath()` with multiple `ellipse()` calls before one `fill()`.

**Visual payoff:** Immediate "floating in space" feeling. Combined with the existing dust layer from the TESTLEVEL lighting engine, shadows land ON something, giving the depth a ground plane.

---

### TIER 2: Moderate Cost (1-3ms, uses existing offscreen canvases)

#### D. Depth-of-Field Blur Layers (Offscreen Canvas Compositing)

Split entities into 3 depth buckets: `far`, `mid`, `near`. Render `far` to an offscreen canvas, apply `ctx.filter = 'blur(3px)'` once, then composite to main canvas at reduced alpha. Render `mid` directly. Render `near` with slight scale-up and full alpha.

The TESTLEVEL already has `_lightCanvas` and `_dustCanvas` offscreen — add a `_depthCanvas` for the far layer.

**Cost breakdown:**
- Extra offscreen canvas at game resolution: ~0.5ms per frame for drawImage blit
- `blur(3px)` filter on composite: ~1-2ms (GPU-accelerated in Chrome/Firefox)
- Sort into 3 buckets: ~0.01ms
- Total: ~1.5-2.5ms

**Visual payoff:** High. Real depth-of-field makes background orbs feel genuinely distant. Focus on the mid-plane (where the action is) with blurred fore/background creates a cinematic "tilt-shift" feel.

**Best used for:** Boss fights. When a boss spawns, push all non-combat orbs to the `far` bucket (blurred, dimmed) while the boss and nearby orbs stay sharp in `mid`. Creates dramatic focus.

#### E. Perspective Floor Grid

Render a perspective grid "below" the orbs — converging lines toward a vanishing point at ~40% screen height. Use `ctx.setTransform()` per horizontal strip to simulate perspective foreshortening (the segment approach, not pixel-level Mode 7):

```
For each strip y from horizon to bottom:
  scale = cameraHeight / (y - horizonY)
  ctx.setTransform(scale, 0, 0, 1, cx*(1-scale), y)
  ctx.strokeRect(...)  // one horizontal grid line at this depth
```

20-30 strips at ~0.05ms each = 1-1.5ms total. The grid drifts with cursor parallax (from enhancement B), reinforcing the 3D volume.

**Visual payoff:** Gives the floating orbs a spatial context — they exist ABOVE something. Especially powerful in the TESTLEVEL lighting biome where shadows can land on the grid plane.

**Best used for:** Specific biomes. The Scaffold biome concept already calls for a lattice — that lattice could BE a perspective floor grid.

#### F. Particle Depth System

Particles already have a `layer` field ('bg' or 'fg'). Add a `z` value and sort within each layer. Background particles at z < -0.3 are rendered smaller and more transparent. Foreground particles at z > 0.3 are larger and draw on top of orbs.

Pop explosions spawn particles across a z-range (-0.5 to +0.5), creating a volumetric burst that "comes through" the screen plane. The existing particle pool of 800 handles this — it's just a per-particle multiply on size and alpha.

**Cost:** Per-particle z-multiply: negligible. Sort within 800-pool: ~0.05ms. Total: <0.1ms.

**Visual payoff:** Explosions and ambient particles gain volume. Combined with velocity stretch (already in place for orbs but not particles), particles feel like debris flying through 3D space.

---

### TIER 3: Significant Investment (3-6ms, new systems)

#### G. CSS Perspective Tilt on the Canvas

Apply `transform: perspective(900px) rotateX(12deg)` to the canvas element via CSS. The GPU composites this for free. The entire game tilts slightly "into" the screen — a top-down view becomes a 3/4 view.

```css
#c {
  transform: perspective(900px) rotateX(12deg);
  transform-origin: center 60%;
}
```

**The catch:** Input coordinates must be remapped. Every `touchstart`/`mousedown` event arrives in CSS space (after the tilt). You must inverse-transform to get canvas coordinates:

```js
function remapInput(cssX, cssY) {
  const rect = canvas.getBoundingClientRect();
  const nx = (cssX - rect.left) / rect.width;
  const ny = (cssY - rect.top) / rect.height;
  // Inverse perspective+rotateX:
  const cosA = Math.cos(12 * Math.PI/180);
  const sinA = Math.sin(12 * Math.PI/180);
  const persp = 900;
  const factor = persp / (persp + (ny - 0.6) * rect.height * sinA);
  const canvasX = ((nx - 0.5) / factor + 0.5) * G.W;
  const canvasY = ((ny - 0.6) / factor / cosA + 0.6) * G.H;
  return { x: canvasX, y: canvasY };
}
```

**Cost:** Rendering = 0ms (GPU does it). Input remap = trivial math per event. Total runtime cost: effectively zero.

**Complexity cost:** Every `screenToWorld()` call must go through the remap. Touch and mouse handlers need updating. Need thorough testing on mobile (touch precision near screen edges with perspective can feel off).

**Visual payoff:** Very high. The entire game gains a dramatic perspective tilt. Orbs in the upper part of the screen appear further away (smaller due to perspective), those at the bottom are closer. Combined with the existing zoom system, this creates a genuine 3D "looking into a bowl" feeling.

**Best used as:** A biome-specific or boss-specific camera angle. Don't apply globally — it changes the game feel fundamentally. Toggle it on for The Scaffold biome (looking down at a tilted lattice) or during boss encounters (dramatic camera shift when boss spawns).

#### H. WebGL Overlay Canvas for Boss Effects

Add a second `<canvas>` element with `getContext('webgl')`, positioned behind or in front of the main canvas with `pointer-events: none`. Use it ONLY for a specific expensive effect:

- Boss aura / energy field (GPU particle system, 500+ particles at near-zero cost)
- Volumetric light shafts (god rays via radial blur shader)
- Bloom / glow post-process on just the boss layer
- Singularity vortex distortion (displacement shader)

```html
<canvas id="c" style="position:absolute; z-index:2;"></canvas>
<canvas id="fx" style="position:absolute; z-index:1; pointer-events:none;"></canvas>
```

The WebGL canvas is transparent everywhere except the effect area. The browser compositor merges them.

**Cost:** ~0.5-1ms for the compositor merge. The WebGL draw calls themselves are nearly free (0.01ms per call vs ~1ms for equivalent Canvas2D calls). A 500-particle GPU system runs in <0.5ms.

**Complexity cost:** High. Requires writing GLSL shaders, managing a GL context, uploading uniforms per frame. ~200-400 lines of boilerplate for a minimal setup. But once the scaffold exists, adding new shader effects is trivial.

**Visual payoff:** Massive. GPU particles look qualitatively different from Canvas2D particles — they can be blended additively, have per-pixel alpha, and move independently of the CPU frame budget. A boss wreathed in a GPU particle vortex would look like it belongs in a different class of game.

**Best used for:** Boss-only effects. The Nexus singularity pulse, the Palindrome's temporal distortion field, the Heart's arterial glow. Don't use it for regular orbs — keep the game in Canvas2D and let WebGL be the "boss entrance" visual upgrade.

---

### TIER 4: Biome-Specific 3D Modes (Full commitment)

#### I. The Scaffold as a 3D Billiards Table

The Scaffold biome concept (slingshot + ricochet + lattice) is a natural fit for 3D presentation. Render the lattice as a perspective floor grid (Tier 2E), orbs as depth-scaled spheres with specular highlights, and the bolt trajectory as a true 3D arc.

Combine:
- CSS perspective tilt (Tier 3G) at 15-20° for the tilted table view
- Drop shadows (Tier 1C) on the perspective floor
- Bolt rendered as a streak with z-position (rises off the floor, hits the orb at its "height")
- Lattice walls as flat rectangles drawn with perspective scaling per row

This doesn't require WebGL. It's entirely Canvas2D with perspective math applied per-entity (Technique 3 from the research). The perspective is computed in JS, not via CSS, so no input remapping needed.

**Cost:** ~2-3ms for perspective math on 50-100 lattice segments + 30-50 orbs. Fits within budget.

#### J. The Viscera as a Depth-Layered Organ

The Viscera biome concept (metaballs, lava lamp, tissue) benefits from a strong depth illusion. Blobs at different z-depths create a sense of looking INTO living tissue:

- Background capillary network at z = -0.8 (thin, blurred, dim)
- Deep blobs at z = -0.4 (smaller, muted, slow-moving)
- Surface blobs at z = 0 (full size, full color, interactive)
- Foreground droplets from pops at z = +0.3 (large, brief, dramatic)
- The Heart boss at z = -0.2 (slightly recessed, pulsing toward z = 0 on each beat — it "pushes toward you" with every heartbeat)

The depth blur (Tier 2D) is essential here. Far blobs should look like they're seen through layers of translucent tissue.

---

### Recommendation: Build Order

| Priority | Enhancement | Cost | Impact | Biome |
|----------|-------------|------|--------|-------|
| **1** | B. Cursor parallax on backgrounds | 0ms | Very high | All |
| **2** | A. Z-sorted orb rendering | 0.02ms | High | All |
| **3** | C. Drop shadows under orbs | 0.3ms | High | All / TESTLEVEL |
| **4** | F. Particle depth (z on particles) | 0.1ms | Medium | All |
| **5** | D. Depth-of-field blur layers | 2ms | Very high | Boss fights |
| **6** | E. Perspective floor grid | 1.5ms | High | Scaffold / TESTLEVEL |
| **7** | G. CSS perspective tilt | 0ms render | Very high | Scaffold biome |
| **8** | H. WebGL overlay for boss FX | 1ms | Massive | Boss fights |
| **9** | I. Scaffold as 3D table | 3ms | Biome-defining | Scaffold |
| **10** | J. Viscera depth layers | 2ms | Biome-defining | Viscera |

Items 1-4 could ship as a single "depth pass" update to the core renderer — under 0.5ms total, zero new canvases, universal benefit across all biomes. Items 5-10 are biome/boss-specific investments.

---

#### File Changes (all in index.html)
- New function: initLightingEngine() - creates offscreen canvases
- New function: renderLighting(ctx) - computes + draws light/shadow layer
- New function: computeCircleShadow(lx, ly, cx, cy, cr, lightR) - returns shadow polygon points
- Hook into render() - call renderLighting() after orbs, before UI
- Guard: if (G.biomeOverride === 1) - only runs in TESTLEVEL

#### Performance Budget
- 50 orbs x 1 tangent pair computation = 50 asin + 100 cos/sin calls (~0.1ms)
- 50 shadow trapezoid fills (~0.3ms)
- 1 darkness fill + 1 gradient punch + 1 composite drawImage (~0.5ms)
- Total: ~1ms per frame - well within 60fps budget (16.6ms)

#### Shadow Math (Core Algorithm ~80 lines)
```
For each orb (circle at cx,cy with radius r):
  dist = distance(light, circle_center)
  if dist < r: skip (light inside orb)
  if dist > lightRadius + r: skip (orb outside light range)

  theta = asin(r / dist)
  alpha = atan2(cy - ly, cx - lx)

  // Two tangent directions
  dir1 = alpha + theta
  dir2 = alpha - theta

  // Tangent points on circle surface
  t1 = (cx + cos(dir1+PI/2)*r, cy + sin(dir1+PI/2)*r)
  t2 = (cx + cos(dir2-PI/2)*r, cy + sin(dir2-PI/2)*r)

  // Project far points
  farDist = lightRadius * 2
  f1 = (lx + cos(dir1)*farDist, ly + sin(dir1)*farDist)
  f2 = (lx + cos(dir2)*farDist, ly + sin(dir2)*farDist)

  // Draw shadow trapezoid: t1 -> f1 -> f2 -> t2
  fillPolygon(t1, f1, f2, t2)
```
