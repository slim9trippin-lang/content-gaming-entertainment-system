# PS4 Pixel Art — Constraint Profile

**Era:** 1080p HD standard, 2013

**Important framing:** the PS4 has **no hardware pixel-art constraints**. It supports full 32-bit RGBA and arbitrary resolution. That means authenticity comes from *artistic discipline* — constraints you impose on yourself — not from the hardware. The list below is what you should lock in voluntarily; the hardware will not enforce any of it.

## Display
- Native resolution: 1920×1080
- Aspect ratio: 16:9
- No hardware tile or sprite structure

## Palette
- Hardware: full 32-bit RGBA, unlimited
- **Recommended self-imposed budget:** pick *one* fixed master palette for the entire game and never deviate

## The four constraints to lock in

These are the choices that separate "intentional pixel art" from "low-res image."

### 1. Pick a base resolution that integer-scales to 1080p
The single most important decision. Pick one of:

| Base resolution | Scales to 1080p | Scales to 4K (2160p) | Use case                              |
|-----------------|------------------|----------------------|---------------------------------------|
| **320×180**     | 6×               | 12×                  | Modern indie default — Celeste-tier   |
| 480×270         | 4×               | 8×                   | More detail per sprite                |
| 640×360         | 3×               | 6×                   | Detailed, almost SNES-resolution feel |

Design *at* the base resolution. The engine scales up with **nearest-neighbor filtering** (never bilinear/linear — that blurs the pixels into mush).

### 2. Pick one master palette and lock it
The most common choices:

- **Endesga 32** — saturated, clean ramps for character art; popular for RPGs and action games
- **DB32 (Dawnbringer 32)** — versatile 16-bit-Amiga-feel
- **Resurrect 64** — broader range for SNES/PS1-era detail levels and painterly backgrounds
- **PICO-8 (16)** — the modern indie shorthand; tight, vibrant, instantly recognizable
- **Sweetie 16** — warmer/cozier alternative to PICO-8
- **Custom 32-color** — built for your game's specific mood

Every sprite, tile, UI element, and effect must pull from this list. No exceptions.

### 3. Apply sub-palette discipline per object
Even though the hardware doesn't require it, divide your master palette into 16-color "sub-palettes" and use only one per sprite. This is what makes art read as 16-bit-era rather than vaguely retro. A character that pulls from 24 colors at once never quite looks right; a character built from a coherent 16-color ramp does.

### 4. Reserve a transparent index
Pick one color in your palette as "transparent" and never use it as a visible color anywhere. This keeps your art portable to any retro-export target and enforces the discipline that defines the look.

## What breaks the look

- **Bilinear / linear filtering.** Turns crisp pixels into blurry mush. Always nearest-neighbor.
- **Designing directly at 1080p.** You're making low-resolution-looking HD art instead of pixel art. Design at the base res.
- **Dynamic lighting / smooth alpha particles / post-processing.** All three introduce off-palette colors into the grid. If you must use them, accept the look is no longer "pure" pixel art and the sub-palette discipline is broken.
- **Mid-game palette additions.** Adding "just one more color" for a special effect cascades — once the palette is unlocked, it stops being a palette.

## Modern palette substitutes
- See "pick one and lock it" above. Endesga 32 and DB32 are the safest defaults; Resurrect 64 if you need more range; PICO-8 or Sweetie 16 if you want a tighter signature look.

## Reference points

These shipped on PS4 and demonstrate the patterns:

- **Celeste** — custom vibrant palette, 320×180-ish base, atmospheric effects within disciplined color choices
- **Dead Cells** — high-detail rotoscoped sprites at a higher base resolution (~640-wide), demonstrates that "indie pixel art" can mean different scales
- **Shovel Knight** — NES 54-color palette with a few "forbidden" additions for skin tones and depth — a pragmatic example of "augmented historical palette"

---

**Bottom line:** on PS4 you're not honoring hardware. You're enforcing rules to make the choice of pixel art feel intentional. Pick base resolution, palette, sub-palette discipline, and nearest-neighbor scaling — lock all four — and the rest follows.
