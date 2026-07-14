# The Retro Reverse-Engineering & Data Capture Template

A complete framework for extracting everything an engine needs to emulate, recreate, or translate a retro console game. The template runs in two halves:

- **Sections I–IV — The Hardware Ceiling.** What the console allowed: architecture, rendering limits, the quirks that defined the game's identity. This is the box the game lived inside.
- **Sections V–XI — The Content Census.** What this specific game actually did with that box: every sprite, palette, animation, movement value, entity, and state rule. This is what you hand to the engine to translate.

The distinction is the whole point. Constraints alone can't reproduce a game — you need its contents *and* its feel. Capture both.

**Convention:** Where a value needs ROM/RAM inspection you haven't done yet, write `[MEASURE]` rather than guessing. A blank is recoverable; a wrong number quietly corrupts the translation downstream.

---

## PART 1 — The Hardware Ceiling

### I. Hardware Architecture & Memory
* **Target Console:** (e.g., NES, SNES, Genesis)
* **CPU / Processing Speed:** (clock speed, instruction set limits)
* **Memory Mapper / Cartridge Type:** (how the game handles memory banks — crucial for NES/SNES)
* **Total ROM Size:** (PRG-ROM for code, CHR-ROM/RAM for graphics)

### II. Graphics & Rendering Constraints
* **Resolution & Aspect Ratio:** (native rendering resolution)
* **Palette Limitations:** (global colors available, colors allowed per sprite/background tile)
* **Sprite Constraints:** (maximum sprites on screen, maximum sprites per horizontal scanline — this causes sprite flicker)
* **Background & Scrolling:** (single-axis vs. multi-axis scrolling, attribute tables, parallax techniques)
* **Special Rendering Tricks:** (raster interrupts, pseudo-3D scaling, palette swapping)

### III. Logic, Mechanics & Game State
* **Object Handling:** (how the game manages entities, collision hitboxes, memory addresses for health/stats)
* **RNG (Random Number Generation):** (how randomness is calculated — often tied to frame counters or player input)
* **State Machines:** (how enemy AI transitions between behaviors)

### IV. Key Innovations & Design Quirks
* **Notable Technical Achievements:** (how the developers bypassed standard hardware limits)
* **Known Exploits / Frame Data:** (mechanical quirks, i-frames, or glitches that define high-level play)

---

## PART 2 — The Content Census

### V. Visual Asset Inventory
A census of every distinct graphic — the "what exists" pass, not the limits.

* **Sprite roster:** (every distinct sprite — player, enemies, items, projectiles, FX — with pixel dimensions and distinct frame count each)
* **Tileset inventory:** (background tile banks, count of unique 8×8 tiles, reused vs. unique)
* **Composite objects:** (large objects built from multiple sprite cells or tiles — bosses, vehicles, UI panels — and how the cells assemble)
* **Asset reuse / mirroring:** (which assets are flipped, recolored, or reused to save memory)
* **Resolution footprint:** (how much screen each asset class occupies; sprite vs. background-tile usage)

### VI. Palette & Color Data
The *actual* palettes the game used, not the hardware maximum (that's Section II).

* **Master palette in use:** (the specific colors pulled from the hardware's available set)
* **Sub-palette assignments:** (which sprite/background sub-palette each object class is bound to)
* **Per-object color ramps:** (the ramp used per material/character — highlight, mid, shadow)
* **Palette-swap events:** (runtime swaps — day/night, damage flash, room transition, alarm states)
* **Transparency handling:** (what index 0 is reserved as; any layering tricks depending on it)

### VII. Animation Data
Motion is data, not vibe. Capture frame counts and timing per action.

* **Action set per actor:** (idle, walk, run, jump, attack, hurt, death — what each entity actually has)
* **Frame counts & order:** (frames per action and sequence; ping-pong vs. loop vs. one-shot)
* **Timing:** (frame duration / playback speed; whether tied to 60/50 Hz refresh)
* **Motion type:** (whole-pixel movement vs. sub-pixel shading; pixel shift per frame)
* **Silhouette consistency:** (does the readable outline hold across frames — matters when re-rendering at a different resolution)

### VIII. World & Level Structure
The spatial dataset the engine needs to lay out a translated version.

* **Level / screen count:** (number of stages, screens, or rooms; total navigable space)
* **Connection map:** (how spaces link — linear, hub-and-spoke, interconnected/non-linear)
* **Scroll behavior:** (single-axis vs. multi-axis, scroll speed, screen-flip vs. continuous)
* **Layout logic:** (static geometry vs. procedural; what's fixed and what's generated per run)
* **Transition rules:** (loading/streaming behavior, what triggers a change, what persists across it)

### IX. Player Control & Game-Feel Metrics
The precise tunable numbers. This is where "feel" lives, and what breaks most if estimated.

* **Movement:** (walk speed, run speed, acceleration, deceleration/friction, max velocity)
* **Vertical:** (jump force/height, gravity, fall speed cap, air control, variable-height rules)
* **Combat timing:** (attack startup / active / recovery frames, attack rate, hit-stun, knockback)
* **Defense / survivability:** (health pool, invincibility frames after hit, damage values taken)
* **Per-character variance:** (if multiple playable characters, the stat matrix differentiating them)

### X. Enemy, Boss & Entity Catalog
A per-entity datasheet — one row per entity.

* **Identity & role:** (name/type, where it appears, threat tier)
* **Stats:** (health, damage, speed)
* **Behavior / AI:** (movement pattern, state transitions, aggro/detection rules)
* **Attack data:** (attack types, telegraphs/startup, hitbox behavior, projectile patterns)
* **Spawn & death rules:** (where/when it spawns, how many at once, on-death behavior or drops)
* **Boss-specific:** (phases, health thresholds per phase, phase-change patterns, end reward)

### XI. Game State, Progression & Economy
The variables and rules governing a playthrough.

* **Tracked state variables:** (global memory — counters, flags, timers, multi-actor tracking)
* **Progression model:** (key-based, ability-gated, score-based, survival; what unlocks what)
* **Economy / resources:** (currency, ammo, consumables — how earned and spent)
* **Win / lose conditions:** (explicit success and failure states)
* **Difficulty scaling:** (how challenge escalates — time, depth, loops, stat multipliers)
* **Feedback cues (optional):** (screen shake, flash, sound triggers — note if the engine should preserve them)

---

## WORKED CASE STUDY: Friday the 13th (NES, 1989)

The full intake on Atlus/LJN's notoriously cryptic survival horror game. `[MEASURE]` marks values needing ROM inspection before translation.

### I. Hardware Architecture & Memory
* **Target Console:** Nintendo Entertainment System (NES).
* **CPU:** Ricoh 2A03 (8-bit, 1.79 MHz).
* **Memory Mapper:** CNROM (Mapper 3) — a very basic, early-era mapper.
* **ROM Size:** 64 KB total — 1 bank of 32KB PRG-ROM (code/logic) and 4 banks of 8KB CHR-ROM (graphics). Achieving its level of macro-tracking on only 64KB is a massive technical feat.

### II. Graphics & Rendering Constraints
* **Resolution:** 256×240 pixels.
* **Palette Limits:** 4 palettes for sprites (3 colors + transparent) and 4 palettes for backgrounds.
* **Sprite Constraints:** 64 sprites max on screen, only 8 per scanline. When Jason, a counselor, and weapons overlap horizontally, the engine cycles which sprites draw on alternating frames — the classic NES flicker.
* **Background & Scrolling:** overworld uses horizontal scrolling. The map pathing uses a clever trick — the small green paths to caves are drawn as *sprites* instead of background tiles, bypassing background palette limits and blending with the grass.

### III. Logic, Mechanics & Game State
* **The Counselor Stat Matrix:** six counselors have invisible stats tied to memory addresses governing Speed and Jump Height. Top-tier (Mark, Crissy) have frame-data advantages to clear hazards seamlessly; bottom-tier (George, Paul) have abysmal movement frames.
* **Global State Tracking:** the game tracks 15 children and 6 counselors simultaneously. The Proximity Alarm is a global interrupt — neon UI flash and siren when AI Jason attacks a cabin elsewhere in memory, forcing the player to abandon their coordinates.
* **The 3-Day State Machine:** the day cycle resets every time Jason's health hits zero. Each day, Jason's memory variables for speed, damage, and pathing aggression are multiplied.

### IV. Key Innovations & Design Quirks
* **Pseudo-3D Cabin Combat:** entering a cabin Jason is attacking shifts the engine from 2D side-scroller to pseudo-3D first-person, heavily inspired by *Punch-Out!!*.
* **Micro-second Frame Dodging:** in the cabin, Jason's AI moves in a lateral 3-point grid (Left, Center, Right). Skill isn't dictated by stats here but by reading visual startup frames — the player micro-dodges the weapon hitbox, then counters during Jason's recovery frames.
* **Procedural Item Spawning:** high-tier weapons (like the Torch) spawn at randomized memory addresses. Overworld geometry is static; resource placement is freshly generated every run.

### V. Visual Asset Inventory
* **Sprite roster:** 6 counselors (8×16, distinct walk/attack/hurt frames each); enemies — wolves, crows, zombies; Jason (overworld form + larger cabin form); items — torch, knife, machete, sweater, flashlight, keys. Frame counts per actor `[MEASURE]`.
* **Tileset inventory:** forest, lakeside, cabin-exterior, cabin-interior, cave banks; unique-tile count `[MEASURE]`. Heavy reuse expected on a 64KB cart.
* **Composite objects:** cabin-interior pseudo-3D scene assembled from background tiles + Jason sprite; HUD map panel.
* **Asset reuse / mirroring:** directional sprites almost certainly horizontal-flipped rather than redrawn; path graphics reused across the overworld.
* **Resolution footprint:** sprite budget is tight — paths offloaded to the sprite layer (see VI), so sprite usage runs high near cabins → flicker.

### VI. Palette & Color Data
* **Master palette in use:** subset of the NES 54-color set `[MEASURE]` — muddy greens/browns for forest, blues for night/lake.
* **Sub-palette assignments:** counselors and Jason on dedicated sprite sub-palettes; overworld terrain on the 4 background sub-palettes (16×16 attribute granularity).
* **Per-object color ramps:** 3 visible + transparent per sprite (NES ceiling); exact ramps `[MEASURE]`.
* **Palette-swap events:** day↔night cycle; Proximity Alarm flash (UI goes neon/siren); likely damage flash `[MEASURE]`.
* **Transparency handling:** index 0 transparent. **Key quirk:** green cave-path markers are drawn as *sprites*, not background tiles, to escape the background palette — any translation must preserve which layer they live on.

### VII. Animation Data
* **Action set per actor:** counselors — walk, attack, hurt, climb/enter; Jason — walk (overworld), attack (cabin, 3-point lateral grid); enemies — patrol, attack.
* **Frame counts & order:** `[MEASURE]` per action; expect short loops on an 8-bit budget.
* **Timing:** tied to ~60 Hz NTSC refresh; exact durations `[MEASURE]`.
* **Motion type:** whole-pixel movement; counselor speed differences are frame-data driven (top-tier clear hazards seamlessly, bottom-tier stutter).
* **Silhouette consistency:** rely on silhouette — internal detail is minimal at this resolution; outlines must stay readable if re-rendered larger.

### VIII. World & Level Structure
* **Level / screen count:** single persistent overworld (forest/lake/cabins/caves) + instanced cabin interiors. Screen count `[MEASURE]`.
* **Connection map:** non-linear hub — the overworld map is freely traversed; cabins and caves are entered from it.
* **Scroll behavior:** horizontal scrolling overworld; single-axis.
* **Layout logic:** geometry is **static**; resource placement is **procedural** (weapon spawn addresses randomized per run).
* **Transition rules:** entering a cabin under attack switches to pseudo-3D combat; the Proximity Alarm interrupt can force the player to abandon current coordinates and travel.

### IX. Player Control & Game-Feel Metrics
* **Movement:** per-counselor walk speed `[MEASURE]`; six-character stat matrix governs Speed and Jump Height (Mark/Crissy high, George/Paul low).
* **Vertical:** jump height per counselor `[MEASURE]`; the high/low split is the defining feel difference.
* **Combat timing:** cabin combat is read-and-react — Jason's attack startup frames are the skill check; player micro-dodges, then counters during his recovery frames. Frame values `[MEASURE]`.
* **Defense / survivability:** counselor health pool `[MEASURE]`; i-frames after hit `[MEASURE]`.
* **Per-character variance:** the 6-counselor Speed/Jump matrix is the central variance system — capture all six.

### X. Enemy, Boss & Entity Catalog
* **Wolves / crows / zombies:** patrol behavior, horizontal movement, low health `[MEASURE]`; spawn on overworld traversal; act as attrition hazards.
* **Jason (boss):** roaming global threat, not node-bound. Behavior escalates via the day cycle. Cabin form moves on a 3-point lateral grid (Left/Center/Right). Health per encounter `[MEASURE]`.
* **Spawn & death rules:** Jason respawns/relocates after being driven off; weapons spawn at randomized addresses.
* **Boss-specific phases:** governed by the **3-day state machine** — each time Jason's health hits zero, the day advances and his speed/damage/pathing-aggression are multiplied. Reward = survival/endgame progression rather than an item.

### XI. Game State, Progression & Economy
* **Tracked state variables:** 6 counselors + 15 children tracked simultaneously in memory; day counter; Jason's escalating stat variables; Proximity Alarm flag.
* **Progression model:** key-based — the torch gates the final Jason confrontation; survival across days is the spine.
* **Economy / resources:** weapons and healing items are the consumables; procedurally placed each run.
* **Win / lose conditions:** survive/defeat Jason across the day cycle; lose when counselors/children are killed out.
* **Difficulty scaling:** the 3-day machine multiplies Jason's threat each cycle — non-linear escalation.
* **Feedback cues:** Proximity Alarm (neon flash + siren) is a defining feedback element; preserve it on translation.

---

## Handoff Notes

A fully filled I–XI gives the engine what constraints alone can't: the **content census** (V–VIII, X) to regenerate assets and layouts, and the **feel/state data** (IX, XI) to keep the translated version playing like the original. Sections I–IV bound the work; V–XI fill it in.

Every `[MEASURE]` left blank is a spot where the engine will approximate — so the completeness of this pass determines how faithful the translation can be. Run a quick self-check before handoff: are all sprite/enemy/boss rows populated, are the game-feel numbers measured rather than estimated, and is every palette-swap and technique-defining quirk recorded?
