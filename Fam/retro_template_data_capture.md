# Retro Reverse-Engineering Template — Data Capture Extension (Sections V–XI)

These sections append after **Section IV** of the existing template and stay on the *intake* side. Sections I–IV record the **hardware ceiling and notable quirks** — the limits the game lived inside. Sections V–XI record the **actual contents of the game** — the full census of assets, colors, motion, layout, feel, and entities that the engine needs to reproduce or translate the title.

The distinction matters: I–IV tell you *what the console allowed*; V–XI tell you *what this specific game actually did with it*. You can't translate a game from its constraints alone — you need its contents. Fill these per game, then hand the completed sheet to the engine.

Where a value requires ROM/RAM inspection you haven't done yet, record it as `[MEASURE]` rather than guessing — a missing number is recoverable, a wrong one corrupts the translation.

---

## 3. The Extended Template (Blank Framework)

### V. Visual Asset Inventory
A census of every distinct graphic in the game. This is the raw "what exists" pass — not the limits, the actual count.

* **Sprite roster:** (every distinct sprite — player, enemies, items, projectiles, FX — with pixel dimensions and how many distinct frames each has)
* **Tileset inventory:** (background tile banks, how many unique 8×8 tiles, reused vs. unique)
* **Composite objects:** (large objects built from multiple sprite cells or tiles — bosses, vehicles, UI panels — and how the cells assemble)
* **Asset reuse / mirroring:** (which assets are flipped, recolored, or reused to save memory — critical for honest reproduction)
* **Resolution footprint:** (how much of the screen each asset class occupies; sprite vs. background-tile usage)

### VI. Palette & Color Data
The *actual* palettes the game used, not the hardware maximum (that's in Section II). Extract the real tables.

* **Master palette in use:** (the specific colors the game pulled from the hardware's available set)
* **Sub-palette assignments:** (which sprite/background sub-palette each object class is bound to)
* **Per-object color ramps:** (the 3–16 color ramp used per material/character — highlight, mid, shadow)
* **Palette-swap events:** (where the game swaps palettes at runtime — day/night, damage flash, room transition, alarm states)
* **Transparency handling:** (what index 0 is reserved as; any layering tricks that depend on it)

### VII. Animation Data
Motion is data, not vibe. Capture frame counts and timing per action so the engine can rebuild or re-time animations under a new target.

* **Action set per actor:** (idle, walk, run, jump, attack, hurt, death — list what each entity actually has)
* **Frame counts & order:** (frames per action and their sequence; ping-pong vs. loop vs. one-shot)
* **Timing:** (frame duration / playback speed; whether it's tied to the 60/50 Hz refresh)
* **Motion type:** (whole-pixel movement vs. sub-pixel shading; how much pixel shift per frame)
* **Silhouette consistency:** (does the readable outline hold across frames — important when re-rendering at a different resolution)

### VIII. World & Level Structure
How the game's space is built and connected. This is the spatial dataset the engine needs to lay out a translated version.

* **Level / screen count:** (number of stages, screens, or rooms; total navigable space)
* **Connection map:** (how spaces link — linear, hub-and-spoke, interconnected/non-linear)
* **Scroll behavior:** (single-axis vs. multi-axis, scroll speed, screen-flip vs. continuous scroll)
* **Layout logic:** (static geometry vs. procedural; what's fixed and what's generated per run)
* **Transition rules:** (loading/streaming behavior, what triggers a room/screen change, what persists across it)

### IX. Player Control & Game-Feel Metrics
The precise tunable numbers. This is where "feel" lives, and the part that breaks most if estimated instead of measured.

* **Movement:** (walk speed, run speed, acceleration, deceleration/friction, max velocity)
* **Vertical:** (jump force/height, gravity, fall speed cap, air control, double-jump or variable-height rules)
* **Combat timing:** (attack startup / active / recovery frames, attack rate, hit-stun, knockback)
* **Defense / survivability:** (health pool, invincibility frames after hit, damage values taken)
* **Per-character variance:** (if multiple playable characters, the stat matrix differentiating them)

### X. Enemy, Boss & Entity Catalog
A per-entity datasheet for everything the player interacts with. One row per entity.

* **Identity & role:** (name/type, where it appears, threat tier)
* **Stats:** (health, damage, speed)
* **Behavior / AI:** (movement pattern, state transitions, aggro/detection rules)
* **Attack data:** (attack types, telegraphs/startup, hitbox behavior, projectile patterns)
* **Spawn & death rules:** (where/when it spawns, how many at once, on-death behavior or drops)
* **Boss-specific:** (phases, health thresholds per phase, phase-change patterns, end reward)

### XI. Game State, Progression & Economy
The variables and rules that govern a playthrough — the systemic data behind the moment-to-moment.

* **Tracked state variables:** (what the game holds in memory globally — counters, flags, timers, multi-actor tracking)
* **Progression model:** (key-based, ability-gated, score-based, survival; what unlocks what)
* **Economy / resources:** (currency, ammo, consumables — how they're earned and spent)
* **Win / lose conditions:** (explicit success and failure states)
* **Difficulty scaling:** (how challenge escalates — time, depth, loops, stat multipliers)
* **Feedback cues (optional):** (screen shake, flash, sound triggers — note if the engine should preserve them)

---

## 4. Case Study Capture: Friday the 13th (NES, 1989)

Demonstrating the intake on the same title. `[MEASURE]` marks values that need ROM inspection before translation.

### V. Visual Asset Inventory
* **Sprite roster:** 6 counselors (8×16, distinct walk/attack/hurt frames each); enemies — wolves, crows, zombies; Jason (overworld form + larger cabin form); items — torch, knife, machete, sweater, flashlight, keys. Frame counts per actor `[MEASURE]`.
* **Tileset inventory:** forest, lakeside, cabin-exterior, cabin-interior, cave banks; unique-tile count `[MEASURE]`. Heavy reuse expected on 64KB cart.
* **Composite objects:** cabin-interior pseudo-3D scene assembled from background tiles + Jason sprite; HUD map panel.
* **Asset reuse / mirroring:** directional sprites almost certainly horizontal-flipped rather than redrawn; path graphics reused across overworld.
* **Resolution footprint:** sprite budget is tight — paths offloaded to the *sprite* layer (see VI), so sprite usage runs high near cabins → flicker.

### VI. Palette & Color Data
* **Master palette in use:** subset of the NES 54-color set `[MEASURE]` — muddy greens/browns for forest, blues for night/lake.
* **Sub-palette assignments:** counselors and Jason on dedicated sprite sub-palettes; overworld terrain on the 4 background sub-palettes (16×16 attribute granularity).
* **Per-object color ramps:** 3 visible + transparent per sprite (NES ceiling); ramps `[MEASURE]`.
* **Palette-swap events:** day↔night cycle; Proximity Alarm flash (UI goes neon/siren); likely damage flash `[MEASURE]`.
* **Transparency handling:** index 0 transparent. **Key quirk:** the green cave-path markers are drawn as *sprites*, not background tiles, to escape the background palette — any translation must preserve which layer they live on.

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
* **Transition rules:** entering a cabin under attack switches to pseudo-3D combat; Proximity Alarm interrupt can force the player to abandon current coordinates and travel.

### IX. Player Control & Game-Feel Metrics
* **Movement:** per-counselor walk speed `[MEASURE]`; six-character stat matrix governs Speed and Jump Height (Mark/Crissy high, George/Paul low).
* **Vertical:** jump height per counselor `[MEASURE]`; the high/low split is the defining feel difference.
* **Combat timing:** cabin combat is read-and-react — Jason's attack startup frames are the skill check; player micro-dodges then counters during his recovery frames. Frame values `[MEASURE]`.
* **Defense / survivability:** counselor health pool `[MEASURE]`; i-frames after hit `[MEASURE]`.
* **Per-character variance:** the 6-counselor Speed/Jump matrix is the central variance system — capture all six.

### X. Enemy, Boss & Entity Catalog
* **Wolves / crows / zombies:** patrol behavior, horizontal movement, low health `[MEASURE]`; spawn on overworld traversal; act as attrition hazards.
* **Jason (boss):** roaming global threat, not node-bound. Behavior escalates via the day cycle. Cabin form moves on a 3-point lateral grid (Left/Center/Right). Health per encounter `[MEASURE]`.
* **Spawn & death rules:** Jason respawns/relocates after being driven off; weapons spawn at randomized addresses.
* **Boss-specific phases:** governed by the **3-day state machine** — each time Jason's health hits zero the day advances and his speed/damage/pathing-aggression are multiplied. Reward = survival/endgame progression rather than an item.

### XI. Game State, Progression & Economy
* **Tracked state variables:** 6 counselors + 15 children tracked simultaneously in memory; day counter; Jason's escalating stat variables; Proximity Alarm flag.
* **Progression model:** key-based — the torch gates the final Jason confrontation; survival across days is the spine.
* **Economy / resources:** weapons and healing items are the consumables; procedurally placed each run.
* **Win / lose conditions:** survive/defeat Jason across the day cycle; lose when counselors/children are killed out.
* **Difficulty scaling:** the 3-day machine multiplies Jason's threat each cycle — non-linear escalation.
* **Feedback cues:** Proximity Alarm (neon flash + siren) is a defining feedback element; preserve it on translation.

---

## How to use this sheet with the engine

A fully filled I–XI gives the engine two things it can't infer from constraints alone: the **content census** (V–VIII, X) it needs to regenerate assets and layouts, and the **feel/state data** (IX, XI) it needs to keep the translated version playing like the original. Every `[MEASURE]` left blank is a spot where the engine will have to approximate — so the completeness of this capture pass is what determines how faithful the translation can be.
