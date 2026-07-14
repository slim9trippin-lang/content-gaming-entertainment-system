# Retro Reverse-Engineering Template — Project Extension (Sections V–X)

These sections append directly after **Section IV** of the existing template. Sections I–IV capture *what the game is* for human analysis. Sections V–X convert that analysis into the machine-executable artifacts this project actually runs: the **MCP constraint profile**, the **pixel engine's validation rules**, the **asset generation manifest**, **cross-system translation**, and the **POE game-logic contracts**.

The rule of thumb: I–IV is description; V–X is compilation. If a field in V–X can't be filled from I–IV, the analysis isn't finished yet.

---

## 3. The Extended Template (Blank Framework)

### V. MCP Constraint Profile (Compiler Input)
Convert the hardware facts from Sections I–II into the machine-readable profile the engine loads at runtime. This is the "static knowledge → runtime constraint engine" step.

```json
{
  "system": "",
  "era": "",
  "display": { "resolution": "", "visible_area": "", "aspect_ratio": "" },
  "tile": { "size": "8x8" },
  "sprite": { "sizes": [], "max_total": 0, "max_per_scanline": 0 },
  "palette": {
    "master_count": 0,
    "onscreen_max": 0,
    "organization": "",
    "transparency_index": 0
  },
  "memory_context": { "mapper": "", "prg_rom": "", "chr_rom": "" }
}
```

### VI. Validation Rules & Fail Conditions (Engine Enforcement)
Derive the hard checks the pixel engine runs on any generated asset. These are pass/fail gates, not suggestions — phrase each as an automatic action.

* **Palette legality:** IF a color is outside the master palette → quantize to nearest legal entry.
* **Sub-palette ceiling:** IF a single sprite uses more than [N] visible colors → reject.
* **Tile alignment:** IF asset is not on the [8×8] grid → reject.
* **Scanline budget:** IF more than [N] sprites occupy one horizontal line → apply flicker handling or reject.
* **Transparency reservation:** IF index 0 is used as a visible color → reject.
* [Add game-specific constraints discovered in Section II, e.g. attribute-grid coloring.]

### VII. Asset Reconstruction Manifest (Generator Targets)
Inventory every asset class the generator must produce to rebuild the game, each tagged with the technique flag from Section X that governs its authentic look.

| Asset class | Count / spec | Grid | Sub-palette | Technique flags |
|---|---|---|---|---|
| Player sprite(s) | | | | |
| Enemy sprites | | | | |
| Boss sprite(s) | | | | |
| Background tilesets | | | | |
| UI / HUD | | | | |
| FX (smear, particle) | | | | |

### VIII. Cross-System Translation Notes (Re-Render Targets)
For each target console you'd port the asset to, find the **intersection of constraints** — the output must obey the stricter rule on every axis. State what tightens and what loosens.

| Axis | Source system | Target system | Governing (stricter) value | Action required |
|---|---|---|---|---|
| Resolution | | | | |
| Colors per sprite | | | | |
| On-screen colors | | | | |
| Sprites per scanline | | | | |
| Tile/sprite size | | | | |

### IX. Game-Logic Contracts (POE Pipeline Bridge)
Map the mechanics from Section III into the POE contract schema so the reverse-engineered game can drive the generation pipeline, not just the art pipeline.

* **World Graph Contract:** nodes (start / traversal / combat / boss / hub), connections, gates, rewards.
* **Ability / Progression Contract:** keys or abilities, what each unlocks, scaling on/off.
* **Enemy System Contract:** behaviors, health, movement patterns, spawn nodes.
* **Boss Contract:** phases, patterns, health thresholds, reward.
* **Room / Tile Contract:** per-node layout type, hazards, density.
* **Validation Layer:** reachability, ability obtainability, gate solvability, difficulty curve increases — `valid: true/false`.

### X. Authenticity Technique Flags (Generator Switches)
Booleans the engine reads to decide which era-specific tricks to apply. A flag set wrong is what makes output "look vaguely retro" instead of authentic.

```json
{
  "sprite_flicker": false,
  "attribute_grid_coloring": false,
  "palette_swap_per_room": false,
  "checkerboard_dithering": false,
  "parallax_layers": 0,
  "tile_sprite_bosses": false
}
```

---

## 4. Case Study Extension: Friday the 13th (NES, 1989)

Picking up where the existing case study (Sections I–IV) left off.

### V. MCP Constraint Profile

```json
{
  "system": "NES",
  "era": "8-bit, 1983–1985",
  "display": { "resolution": "256x240", "visible_area": "256x224", "aspect_ratio": "8:7 native, 4:3 displayed" },
  "tile": { "size": "8x8" },
  "sprite": { "sizes": ["8x8", "8x16"], "max_total": 64, "max_per_scanline": 8 },
  "palette": {
    "master_count": 54,
    "onscreen_max": 25,
    "organization": "4 background sub-palettes + 4 sprite sub-palettes, each 3 visible colors + shared transparent; backgrounds colored in 16x16 attribute chunks",
    "transparency_index": 0
  },
  "memory_context": { "mapper": "CNROM (Mapper 3)", "prg_rom": "32KB (1 bank)", "chr_rom": "32KB (4 x 8KB banks)" }
}
```

### VI. Validation Rules & Fail Conditions

* IF a sprite uses more than 3 visible colors + transparent → reject (NES sub-palette ceiling).
* IF a background tile ignores the 16×16 attribute grid → reject (most NES-defining constraint).
* IF more than 8 sprites land on one scanline → apply flicker (F13 depends on this when Jason, a counselor, and weapons overlap).
* IF a color falls outside the 54-color master palette → quantize to nearest NES entry.
* IF the overworld "cave path" is drawn as a background tile → flag: F13 deliberately renders these as *sprites* to bypass the background palette, so the generator must place them on the sprite layer.

### VII. Asset Reconstruction Manifest

| Asset class | Count / spec | Grid | Sub-palette | Technique flags |
|---|---|---|---|---|
| Counselor sprites | 6 variants, 8×16 | 8px | 1 sprite sub-pal each | flicker |
| Enemy sprites | wolves, crows, zombies | 8px | shared sprite sub-pal | flicker |
| Jason (boss) | overworld + cabin form | 8px | dedicated sprite sub-pal | flicker, palette-swap |
| Overworld tilesets | forest, lake, cabins | 8px tiles, 16×16 attr | 4 BG sub-pals | attribute-grid coloring |
| Cave-path markers | green path bits | 8px | sprite sub-pal | rendered as sprites (not BG) |
| Cabin interior (pseudo-3D) | Punch-Out-style arena | 8px | BG + sprite split | palette-swap on entry |
| HUD / Proximity Alarm | siren flash, map | 8px | dedicated sub-pal | palette-swap (alarm flash) |

### VIII. Cross-System Translation Notes — NES → Game Boy Color

| Axis | NES (source) | GBC (target) | Governing value | Action required |
|---|---|---|---|---|
| Resolution | 256×224 | 160×144 | GBC (smaller) | Crop/redesign overworld; the horizontal map must be re-paneled into a smaller frame |
| Colors per sprite | 3 + transparent | 3 + transparent | tie | No change; ramps transfer directly |
| On-screen colors | 25 | 56 | NES (fewer) — but GBC *loosens* | Free headroom; can color paths normally instead of the sprite-layer hack |
| Sprites per scanline | 8 | 10 | NES (stricter) — GBC loosens | Slight relief, but keep flicker logic since source design assumes 8 |
| Tile/sprite size | 8×8 / 8×16 | 8×8 / 8×16 | tie | Direct transfer |

Net: the GBC port *gains* color budget (the cave-path-as-sprite hack becomes unnecessary) but *loses* screen real estate, so the dominant translation work is spatial re-layout, not recoloring.

### IX. Game-Logic Contracts (POE Bridge)

* **World Graph:** hub = overworld map; traversal nodes = forest/lake paths; combat nodes = cabin interiors; boss nodes = Jason encounters. Cabins under attack become time-pressured nodes via the Proximity Alarm interrupt.
* **Ability / Progression:** key-based, not ability-gated. Items (torch, knife, machete, sweater) act as keys; the torch gates the cave/cJason confrontation.
* **Enemy System:** wolves/crows = patrol, horizontal movement, low health, spawn on traversal nodes. Jason = roaming global threat, not bound to one node.
* **Boss Contract:** Jason's phases are driven by the **3-day state machine** — each time his health hits zero, the day advances and his speed/damage/pathing-aggression variables are multiplied. Reward = day survived / endgame progression.
* **Room / Tile Contract:** overworld nodes = horizontal-traversal layout; cabin nodes = pseudo-3D `arena` layout (3-point lateral grid, frame-read dodging); cave = boss-arena layout.
* **Validation Layer:** can the player reach Jason without the torch? (should fail → torch is a hard gate). Does difficulty escalate each day? (yes → curve passes). Are all 6 counselors playable from start? (yes → no progression lock). `valid: true`.

### X. Authenticity Technique Flags

```json
{
  "sprite_flicker": true,
  "attribute_grid_coloring": true,
  "palette_swap_per_room": true,
  "checkerboard_dithering": false,
  "parallax_layers": 0,
  "tile_sprite_bosses": false
}
```

*Notes:* F13 is single-layer horizontal scrolling, so `parallax_layers: 0`. It does **not** use tile-sprite bosses (Jason is a real sprite, which is exactly why flicker matters). `palette_swap_per_room` is true because of the day/night cycle and the alarm flash. Setting `parallax` or `dithering` true here would produce a more "16-bit Sega" feel — wrong era, wrong system.

---

## How this plugs into the rest of the project

* **Section V** is the direct input to the MCP loader — one filled profile = one plug-and-play rule set.
* **Section VI + X** are what the validation/correction engine reads; together they're the difference between "AI that makes pixel-looking images" and "AI that produces usable sprites."
* **Section VII** is the work order for generate-mode.
* **Section VIII** is the format-translation feature (re-render one game's assets under a different MCP profile).
* **Section IX** is the handoff to the POE pipeline — a reverse-engineered game becomes a set of generation contracts, so analysis and generation share one schema.
