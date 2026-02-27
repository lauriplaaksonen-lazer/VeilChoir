# Layered Game Music — WAV Stems Approach

## Concept
Play multiple WAV files simultaneously, all identical in length and tempo. Fade layers in/out based on game state to create dynamic music without audible transitions.

## Preparing the Stems

- Compose all layers in **one DAW session** to guarantee sync
- Export each layer as a separate WAV (same sample rate, bit depth, length)
- All layers must share: **BPM, time signature, key, exact sample count**
- Ensure loop points are clean (no silence padding, no clicks at boundaries)
- Normalize each layer independently so you have mixing headroom in-engine

## Example Layer Structure

| Layer | Content | When active |
|-------|---------|-------------|
| Base | Ambient pads, light melody | Always playing |
| Mid | Rhythmic elements, arpeggios | Tension / approaching danger |
| High | Full drums, intense lead | Combat / climax |

## Playback Rules

1. **Start all layers simultaneously**, even if some are at volume 0
2. Fade layers in/out with a crossfade time of ~1–2 seconds
3. Quantize transitions to **bar boundaries** when possible (sounds more musical)
4. Keep the base layer always audible to maintain continuity

## File Naming Convention

```
track01_base.wav
track01_mid.wav
track01_high.wav
```

## Common Pitfalls

- **Length mismatch**: Even 1 sample off will cause drift over loops — always export from the same session, same render settings
- **Phase issues**: Avoid identical frequencies in multiple layers; give each layer its own frequency space
- **Muddy mix**: High-pass filter upper layers so they don't compete with the base
- **Loud stacking**: When all layers play together, the mix gets louder — plan for this in mastering; leave headroom

## Minimal Implementation (Pseudocode)

```
// On scene load
base = loadAndLoop("track01_base.wav")
mid  = loadAndLoop("track01_mid.wav")
high = loadAndLoop("track01_high.wav")

// Start all at once
playAll(base, mid, high)
mid.volume  = 0
high.volume = 0

// On game state change
if danger > 0.3:
    fadeTo(mid, targetVolume=1.0, duration=1.5s)
if danger > 0.7:
    fadeTo(high, targetVolume=1.0, duration=1.5s)
if danger < 0.3:
    fadeTo(mid, targetVolume=0.0, duration=2.0s)
    fadeTo(high, targetVolume=0.0, duration=2.0s)
```

## Tips

- **2–3 layers is enough.** More adds complexity without much payoff.
- Test the mix with all layers active first, then strip back.
- Short loops (16–32 bars) keep memory usage low.
- Consider separate loop sets for different areas/biomes and crossfade between sets at area transitions.
