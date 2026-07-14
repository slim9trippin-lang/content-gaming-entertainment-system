---
name: retro-pixel-art
description: Produce hardware-accurate constraint profiles and generate spec-compliant pixel art for vintage and modern indie game systems. Use this whenever the user mentions pixel art, sprites, tilesets, or palettes for a specific console or era — NES, SNES, Game Boy/GBC/GBA, NDS, Genesis/Mega Drive, Master System, Game Gear, Saturn, Dreamcast, PS1, PS2, PS3, PS4, or modern indie palettes like PICO-8, Endesga 32, DB16/32, Sweetie 16. Also use this when the user wants to generate retro-style sprites or SVG pixel art, build a palette that matches a specific era, validate art against hardware constraints (palette limits, sprite-per-scanline limits, tile grid alignment), or write authentic-feeling generation prompts for image tools. Trigger this even if the user only says "8-bit sprite," "16-bit style," "Mega Drive vibe," or "make it look like Game Boy" — the skill exists to ground vague aesthetic requests in real hardware specs and the era-appropriate techniques (dithering, sprite flickering, parallax, palette swapping) that define those looks.
---

# Retro Pixel Art Constraint Engine

Pixel art for a specific system isn't a style — it's a set of constraints. A "Genesis sprite" that uses 200 colors and a smooth gradient isn't a Genesis sprite; it just looks vaguely retro. This skill exists to swap vibes for specs. Given a target system, it loads the real hardware profile (resolution, palette structure, sprite/tile rules, era-specific techniques) and uses it to either describe what authentic art looks like or to actually generate art that follows those rules.

The user's real goal is almost always "make this look right for the era." That requires honoring not just the obvious limits (color count, resolution) but the *techniques* artists used to work around those limits — dithering as fake transparency on Genesis, sprite flickering to fake more on-screen objects on the Master System, palette cycling for animation, the reserved transparency index. Get those right and the result feels authentic. Skip them and it just looks like a low-res image.

## Two modes

Most requests fall into one of two modes. Figure out which one before doing anything else.

**Profile mode** — the user wants to *understand* the constraints. They asked something like "what are the specs for SNES pixel art" or "give me the rules for Game Boy Color." Output a structured profile card. Don't generate art.

**Generate mode** — the user wants *output* that obeys the constraints. They asked for a sprite, a tileset, a palette, a Genesis-style boss, a SNES-style mockup. Load the profile, then produce art that respects it. Use the visualizer for SVG output (each game pixel is a `<rect>`); for image-generation tools or external editors, produce a constraint-locked spec/prompt instead.

If the request is ambiguous ("can you do a Genesis sprite") lean toward generate mode but offer the profile alongside if useful.

## Workflow

### Step 1 — Identify the target system

The user might name it directly (NES, Genesis, PICO-8) or describe it by era ("8-bit Sega," "16-bit Nintendo," "early CD-era PlayStation"). If the target is genuinely unclear, ask a single short clarifying question — but only if it matters. "Game Boy" is unambiguous enough; "retro" alone is not.

Map the named system to a reference file:

| Family       | Systems covered                                            | File                            |
|--------------|------------------------------------------------------------|---------------------------------|
| Nintendo     | NES, SNES, Game Boy, GBC, GBA, NDS                         | `references/nintendo.md`        |
| Sega         | Master System, Game Gear, Genesis/Mega Drive, Saturn, DC   | `references/sega.md`            |
| Sony         | PS1, PS2, PS3, PS4                                         | `references/sony.md`            |
| Modern indie | PICO-8, Endesga 32, DB16/32, Sweetie 16, Apollo, AxulArt   | `references/indie_palettes.md`  |

### Step 2 — Read the relevant reference file

Use the `view` tool. The reference files are the source of truth — never recall specs from memory because there are too many subtle differences (Genesis has 4×16 sub-palettes; SNES has 8×16; Master System has 2×16; the GB has 4 shades total). Reading is cheap, getting it wrong is what makes pixel art look fake.

If the user asks about a system *family* ("Sega 16-bit era") or wants a comparison across systems, you may need to read multiple reference files.

### Step 3 — Produce output

For **profile mode**, fill out the profile schema below using data from the reference file and present it.

For **generate mode**, follow the generation rules further down.

## The constraint profile schema

Use this exact structure when outputting a profile card. Keep it scannable — this is reference material the user will come back to.

```
# [System name] — Constraint Profile

**Era:** [release year, generation]

## Display
- Native resolution: [W×H]
- Alt resolutions: [if any]
- Aspect ratio: [4:3, 16:9, etc.]

## Tile / Sprite
- Tile base size: [usually 8×8]
- Sprite base size: [varies]
- Max sprites total: [if hardware-limited]
- Max sprites per scanline: [if hardware-limited]

## Palette
- Master palette: [bit depth → color count]
- On-screen max: [simultaneous color limit]
- Organization: [e.g. "4 sub-palettes × 16 colors"]
- Transparency: [reserved index, if any]

## Defining techniques
[2–5 era-specific tricks artists relied on, each with one sentence on what it is and why it matters]

## Common pitfalls
[2–4 things that break the authentic look]

## Modern palette substitutes
[Curated palettes from Lospec or similar that emulate this system]
```

Adapt for systems with "soft" limits (PS3, PS4, Switch) — those don't have hardware palette caps, so the relevant constraints are *artistic* (a chosen master palette, an integer-scaling base resolution like 320×180). Note this explicitly so the user understands the difference.

## Generation rules (when producing art)

The point of generation is fidelity to the profile. Before producing anything, ask yourself: does this respect the resolution, the palette structure, the tile grid, and the era's techniques?

### When generating SVG (preferred default in chat)

Use the visualizer. Each game pixel is a `<rect>` of uniform size. This works because SVG scales cleanly and the user sees pixel-perfect output inline.

Concrete rules:

- **One rect per pixel.** Don't use gradients, smooth shapes, or path curves. The whole point is the visible grid.
- **Color discipline.** Every `fill` value must come from the system's palette. For systems with sub-palette structure (Genesis, SNES, GBC), pick *one* sub-palette and stick to it for a single sprite. Don't pull from multiple at once.
- **Resolution discipline.** Match the system's sprite or tile size when relevant. A "Genesis-style enemy" should be built on an 8-pixel grid; a Game Boy sprite is 8×8 or 8×16. Don't draw a 47×31 sprite and call it Genesis.
- **Apply the era's signature technique.** A Genesis waterfall without dithering isn't Genesis. A Master System scene with 9 enemies on one row should show the flicker (or just keep it to 8). A GBC sprite shouldn't use 32 colors. The reference file lists the relevant techniques per system — use them.
- **Reserve transparency correctly.** On most retro systems, the first palette index is transparent. In SVG this just means: don't fill the background; let it show through. But if the user is going to export the art for an actual ROM or sprite sheet, mention that the transparent color must not be used as a visible color anywhere on the sprite.

### When the user wants a prompt for an external image generator

Don't pretend image generators understand "Genesis sprite." Instead, write a constraint-locked prompt that names the specifics: target resolution, exact palette (list the hex codes from the profile), tile grid, technique to apply ("use ordered dithering for shading; do not use gradients or anti-aliasing"). Be concrete. Negative constraints matter as much as positive ones.

### When the user is making art themselves

Provide the profile card plus a short workflow note tailored to their tool (Aseprite, Photoshop, etc.) — for example, "in Aseprite, load the SGDK Genesis 512 palette as indexed, then lock yourself to one 16-color sub-palette per sprite. Reserve color 0 for transparency."

## Cross-system principles

A few rules apply almost everywhere in retro pixel art. Internalize these regardless of which system is targeted:

- **The first palette color is reserved for transparency** on virtually every tile-based retro system (NES, SMS, GB, Genesis, SNES, GBA). Using it as a visible color shows the layer behind the sprite.
- **Tiles are 8×8.** Almost every pre-Saturn console builds backgrounds from 8×8 tiles. Sprites are usually 8×8 or composed from multiple 8×8 cells. Aligning to this grid is what makes the art read as authentic instead of vaguely "low-res."
- **Sub-palette discipline.** A single sprite on a 16-bit system uses *one* sub-palette of 16 colors, not the full on-screen color count. Using 64 colors on one Genesis sprite is impossible on real hardware.
- **Hardware limits drove the techniques.** Dithering, sprite flickering, palette swapping, and parallax weren't stylistic choices — they were workarounds. Honoring them is what creates the authentic feel.
- **Modern HD systems (PS4, Switch, PS3, Xbox 360, Wii) have no hardware pixel-art limits.** Authenticity there comes from artistic discipline: pick a fixed palette (often 16, 32, or 64 colors), pick an integer-scalable base resolution (320×180 is the modern indie standard), and use nearest-neighbor scaling. Mention this whenever the user targets a modern system, because the constraints are different in kind.

## When the user mixes eras

If the user asks for something that spans systems ("a sprite that works on both Genesis and SNES" or "translate this Game Boy art to GBC") — the right move is to find the *intersection* of constraints. The output must obey the stricter rule on each axis: lower color count, smaller resolution, more restrictive palette structure. State this explicitly so the user sees what's being tightened.

## What this skill is not for

- Generating photorealistic art, modern 3D, or non-pixel illustration — the skill assumes a pixel grid.
- Reproducing copyrighted characters or sprites (Mario, Sonic, Pokémon, etc.). Generate original characters in the target style instead.
- Detailed emulator/hardware programming questions (cycle timing, DMA setup, etc.) — those are out of scope. The skill handles the artist-facing side: what the art should look like and why.
