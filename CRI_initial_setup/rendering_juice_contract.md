# POE CONTRACT — `rendering_juice_contract`

> Closes the gap surfaced by *Streets of Fire*: a third render category that the gray-rectangle litmus previously couldn't express. General-purpose — attaches to ANY skeleton, not just Sports/Court.

---

## 1. The problem it solves

The gray-rectangle litmus used to return a **binary**:
- survives → logic-carried (no render contract)
- dies → render-carried (needs `rendering_illusion_contract`)

*Streets of Fire* exposed a case that is **neither**: the game survives greybox (plays identically) but loses all its swagger. The render layer is real but **cosmetic**. The binary couldn't hold it. This contract formalizes that third state.

---

## 2. The upgraded render taxonomy (binary → ternary)

| Litmus answer | Bucket | What the render does | Contract |
|---|---|---|---|
| "plays identically" | **1 — Logic-carried** | pure presentation | none |
| "plays, but feels dead" | **3 — Render-carried SEASONING** | spectacle/legibility, no logic | **`rendering_juice_contract`** |
| "doesn't play at all" | **2 — Render-carried IDENTITY** | the render IS the mechanic | `rendering_illusion_contract` |

**Upgraded litmus (replace the binary):**
> Strip all art to gray rectangles. Does it (a) play the same, (b) play but feel dead, or (c) stop being the game? → Bucket 1 / 3 / 2.

| Precedent | Answer | Bucket |
|---|---|---|
| Top Gun (NES) | plays the same | 1 |
| basketball core | plays the same | 1 |
| Streets of Fire flair | plays but dead | 3 |
| Rad Racer road | stops being a racer | 2 |

---

## 3. The defining property — the FIREWALL

A juice binding carries **zero logic**. Removing the entire `rendering_juice_contract` must leave every game-state value **bit-identical**.

```
load_bearing = false   // ENFORCED, not advisory
```

This is what separates it from `rendering_illusion_contract`, where removing the render *changes the transform that the game depends on* (the road geometry, the Mode 7 matrix).

---

## 4. Schema

```json
{
  "rendering_juice_contract": {
    "bindings": [
      {
        "id": "string",
        "trigger_event": "host-emitted event name",
        "fx": "visual/audio effect ref",
        "load_bearing": false,
        "tier": "high | mid | low",
        "_firewall": "removing this changes NO game-state value"
      }
    ]
  }
}
```

### Field notes
- **trigger_event** — listens to Host events only; never writes back.
- **load_bearing** — must be `false`. A `true` here is a category error (see §6).
- **tier** — the hardware-aware lever. Juice scales DOWN for weak targets or lower ratings **without touching logic**. This is the strippable layer in your hardware-aware compiler: same game, less garnish.

---

## 5. Worked example — *Streets of Fire*

| id | trigger_event | fx | tier | load_bearing |
|---|---|---|---|---|
| heat_trail | `on_fire_entered` | flame ball-trail + net scorch | mid | false |
| gamebreaker_cine | `gamebreaker_fired` | slow-mo cinematic + air-horn stab | high | false |
| fight_cam | `flashpoint_started` | side-view cut + crowd roar | high | false |
| vs_slam | `match_start` | player collision + name crash | low | false |
| crowd_react | `style_threshold` | crowd stand + chant | mid | false |
| dj_scratch | `gamebreaker_fired` | diegetic scratch synced to stab | mid | false |

Strip every row → the match still reaches its target score with identical scoring. The game *plays*. It just stops *bragging*.

---

## 6. Validation rule (the integrity check)

A juice binding that, **if removed, changes any logic value, is MISCATEGORIZED**. It must be promoted out of the juice contract into its real home:

- changes a hit/score outcome → belongs in C5 / a meter primitive
- changes a transform the game reads → belongs in `rendering_illusion_contract`
- changes nothing → correctly juice; stays.

C9 (Validation Layer) runs this check: for each juice binding, assert game-state diff = ∅ when the binding is disabled. Any non-empty diff = build error.

---

## 7. Why this matters to the compiler

Three render buckets = three handling strategies in the pipeline:
- **Bucket 1**: skin freely, no constraint.
- **Bucket 2**: the render is a *mandatory transform* — validate against hardware budget (HBlank, Mode 7 matrix), generation fails if illegal.
- **Bucket 3**: optional, **tiered, strippable** — the hardware-aware degrade path. Low-end target? Drop `tier:high` juice. Logic untouched, game intact, swagger reduced.

The juice contract is the layer that lets one design ship across hardware eras gracefully — the same reason it earns its own contract instead of being lumped with illusion.
