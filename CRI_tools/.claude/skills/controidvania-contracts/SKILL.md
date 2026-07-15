---
name: controidvania-contracts
description: Generate all nine POE/PLS contracts (Input, Interpretation, World Graph, Ability System, Player Controller, Enemy System, Boss, Room/Tile, Validation Layer) for a Controidvania-style ability-gated 2D exploration game. Use this whenever the user wants to design a Metroidvania, build a non-linear world graph, generate an ability progression tree, produce room geometry and enemy/boss placement, or needs deterministic, cross-validated JSON output that a downstream engine could consume.
---

# Controidvania Contract Generator

*Originally scoped to the first 4 contracts only (the "design-fingerprint half") — deliberately stopping short of runtime tuning because that's where AI is least reliable. Extended to the full 9 on explicit request. The honest scope flag below reflects the extended version; read it before treating this skill's output as more finished than it is.*

## Workflow

### Step 1 — Detect input mode

Three modes the user might come in on:

| Mode | What the user gave you | What to do |
|---|---|---|
| Casual / prose | "Design a Controidvania about urban survival" | Generate all 9 contracts in sequence; default reasonably |
| Structured input | A complete Input Contract JSON | Skip to Interpretation; generate the remaining 8 |
| Partial state | Input + Interpretation, or Input + some downstream contracts | Fill what's missing; regenerate downstream contracts as needed |

If the user's prose is too vague to extract an Input Contract from ("make me a game"), ask one short clarifying question before generating. Don't fabricate a theme out of nothing. See `references/01_input_contract.md` for the four fields that specifically cannot be silently defaulted.

### Step 2 — Read the reference files in order

Read them as you need them — don't pre-load all nine if you're only generating Input.

**Front half — design fingerprint (Contracts 1–4):**
- `references/01_input_contract.md` — schema for Input, extraction rules from prose
- `references/02_interpretation.md` — mapping rules Input → Interpretation
- `references/03_world_graph.md` — graph generation rules, structural validation
- `references/04_ability_system.md` — ability design rules, graph-cross-validation

**Back half — runtime tuning (Contracts 5–9):**
- `references/05_player_controller.md` — physics defaults, ability_modifiers coupling, `tuning_status: default_untested` flag
- `references/06_enemy_system.md` — health/damage scaling table, behavior FSM, `gated_by` pattern
- `references/07_boss.md` — three-way coupling (World Graph node ↔ Ability System reward ↔ Boss entry), phase/stat table, one-shot floor
- `references/08_room_tile.md` — gap geometry, layout-by-node-type, `mcp_target` hook for hardware binding
- `references/09_validation_layer.md` — G1–G19 gates that check every coupling promise made across all 8 prior contracts

### Step 3 — Generate each contract, in order

Each contract depends on the ones above it — don't generate the World Graph before Interpretation locks in, don't generate Room/Tile before Player Controller's physics values exist to validate gaps against.

For each contract:
1. Apply the generation rules from the reference file
2. Validate the contract against its own internal rules (per-contract validation, documented in each reference file)
3. Note any choices made by inference (defaults, gap-filling) so the user can correct them

**On the back half specifically:** Contracts 5–7 use fixed default values for physics and stats (per the `tuning_status: default_untested` convention established in Contract 5) rather than tuned "game feel" numbers. Surface this plainly when presenting output — these are documented starting points for a human tuning pass, not finished values.

### Step 4 — Run Validation Layer (Contract 9)

This replaces ad hoc manual checking. Contract 9's G1–G19 gates cover every cross-contract coupling introduced across Contracts 1–8:

- Ability ↔ World Graph load-bearing and edge-existence checks (G1–G4)
- Player Controller ↔ Interpretation ↔ Ability System model consistency (G5–G6)
- Enemy System damage ceilings and node coverage (G7–G9)
- Boss three-way coupling and the one-shot floor (G10–G13)
- Room/Tile coverage and bidirectional enemy placement (G14–G17)
- Physics reachability against Player Controller's actual stats (G18–G19) — uses a documented simplified projectile-motion model, not a real physics engine; treat this as a design-generation approximation

Run every gate, don't stop at the first failure — collect everything so the user gets one complete error report, not a slow drip of individually-discovered problems. If `valid: false`, generation has failed; don't present partial output as if it were usable.

### Step 5 — Handling the genre-skeleton-mapper bridge

If the user is coming from a `genre-skeleton-mapper` output (a "[Source] mapped to Kinesthetic Explorer" breakdown), you have everything you need. Mine the breakdown:

- Vibe paragraph → tone + reference_inspirations
- Translation table → theme, setting, protagonist_archetype, core_ability_style
- Movement model → feeds directly into the Interpretation contract's `movement_model`
- Combat verb → feeds the Interpretation contract's `combat_model`
- World structure → feeds `world_structure`
- Boss-ability pairings → seed the Ability System contract, and now also the Boss contract's `reward_ability_id` coupling directly

When this is the case, surface that you're using the mapper's output and note any choices that needed inference beyond what the mapper provided.

## Honest scope flag

This skill now produces the full 9-contract design + geometry package: narrative-to-mechanics translation, a validated world graph, an ability progression tree, physics defaults, enemy/boss population, and room-level geometry with gap distances checked against those physics defaults.

**It still does not produce a playable game.** Two things remain genuinely unsolved by this skill:

1. **Tuned game feel.** Contracts 5–7's numbers are documented defaults, explicitly flagged as needing a human playtesting pass. Treat them as a starting point, not a finished spec.
2. **Actual rendering and runtime.** Room/Tile geometry is tile-count abstractions, not rendered assets — it hands off to the retro-pixel-art skill and MCP hardware profiles for that, and to an actual engine (Unity, etc.) for anything playable.

If the user expects to take this output and run it in an engine tomorrow, surface that gap. The path from here to playable is real engineering, not a missing JSON block.
