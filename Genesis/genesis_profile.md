# Sega Genesis / Mega Drive — Constraint Profile

**Era:** 16-bit, 1988–1989

## Display
- Native resolution: 320×224 (most common) or 256×224
- Aspect ratio: 4:3
- Tile base size: 8×8

## Tile / Sprite
- Tile base size: 8×8
- Sprite sizes: 8×8 up to 32×32 (built from 8-pixel cells)
- Max sprites total: 80
- Max sprites per scanline: 20 (high throughput vs SNES's 32, but no scaling/rotation hardware)

## Palette
- Master palette: 9-bit → 512 colors
- On-screen max: 64 colors (extendable past 100 via shadow/highlight mode)
- Organization: **4 sub-palettes × 16 colors**
- Transparency: **color 0 of every sub-palette is reserved** — using it as a visible color shows the layer behind the sprite

## Defining techniques
- **Dithering for fake transparency** — Genesis has no alpha blending. Checkerboard patterns smear on CRT to simulate translucency. Famous in Sonic 1 waterfalls and Vectorman effects. *This is the single most Genesis-defining technique.*
- **Parallax scrolling** — two hardware BG layers (A and B) scroll at different speeds for extreme depth. Defines the "fast" Sega brand.
- **Shadow & Highlight mode** — VDP can darken or brighten colors on the fly, expanding effective on-screen count past 100.
- **DMA tile streaming** — tiles swap in and out of VRAM mid-animation rather than holding all frames at once; enables larger-feeling characters.
- **Crunchy palette aesthetic** — the 9-bit master is famously narrower than SNES's 15-bit; many devs leaned into bold primaries and high contrast rather than fight it.

## Common pitfalls
- **Using palette index 0 as a visible color.** The single most common Genesis mistake — that slot is transparency, period.
- Treating dithering as ugly noise instead of as the system's hallmark technique.
- Putting more than 16 colors on one sprite (sprites use one of the 4 sub-palettes).
- Soft pastel palettes — Genesis CRTs and the VDP's color space don't render them well; they wash out.

## Modern palette substitutes
- **"Genesis / Mega Drive 512"** — the full hardware master palette, used by SGDK homebrew
- **"Dharm32"** — a 15-bit gaming palette emulating SNES/Genesis-era discrete-channel feel

---

**Quick gut check before designing for Genesis:** pick one of the four sub-palettes for your sprite, lock to its 16 colors, never touch index 0, and lean into dithering rather than smooth gradients. That's 80% of the authentic look.
