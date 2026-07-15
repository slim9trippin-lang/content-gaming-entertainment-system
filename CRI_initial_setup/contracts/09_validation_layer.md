# 09 — Validation Layer Contract

This is the contract that makes every coupling promise made in Contracts 1–8 enforceable rather than aspirational. It runs after all other contracts are generated, checks all of them against each other, and produces a single pass/fail verdict with actionable errors. If `valid: false`, generation has failed and nothing downstream should be treated as usable — consistent with the project's "hard FAIL conditions over affirmative instructions" principle.

**Runs all gates, doesn't stop at first failure.** Collecting every violation in one pass is more useful for iteration than fail-fast — matches the precedent set by the combat system's G1–G7 validator.

## Schema

```json
{
  "valid": "boolean",
  "errors": [
    {
      "gate": "string — gate ID (e.g. 'G7')",
      "message": "string — human-readable description of the violation",
      "location": "string — the specific id/node/entity where the violation occurs"
    }
  ]
}
```

## The gates

### Ability System ↔ World Graph

- **G1 — Load-bearing abilities.** Every ability in the Ability System contract must have a non-empty `gates_unlocked`. Dead abilities fail.
- **G2 — Edge-ability existence.** Every World Graph edge with a `requirement` other than `"none"` must reference an ability `id` that actually exists in the Ability System contract.
- **G3 — Full solvability.** Simulating a playthrough from `start_node`, acquiring abilities in the order their `unlock_node`s become reachable, every node in the graph must be reachable by `end_node`. No node may require an ability that can only be obtained by first passing through that same node (circular gating).
- **G4 — Secret reward presence.** Every `type: secret` node must have a non-empty `rewards` array.

### Player Controller ↔ Interpretation ↔ Ability System

- **G5 — Model consistency.** Player Controller's `movement.model` must equal Interpretation's `movement_model`; `combat.model` must equal `combat_model`.
- **G6 — Ability mechanical realism.** Every `movement`-type ability in the Ability System contract must have at least one corresponding entry in Player Controller's `ability_modifiers`.

### Enemy System

- **G7 — Damage ceiling.** Every enemy's `damage` must not exceed the difficulty-scaled ceiling from Contract 6's table (`player.health / 4` at difficulty 1, scaling proportionally).
- **G8 — Combat node coverage.** Every `type: combat` World Graph node must have at least one enemy whose `spawn_nodes` includes it. No enemy's `spawn_nodes` may include a `boss`, `hub`, or `start` node.
- **G9 — Gating consistency.** If `gated_by` is set on an enemy, `gating_type` must also be set.

### Boss

- **G10 — Boss-node uniqueness.** Every `type: boss` World Graph node has exactly one boss entry with matching `node_id`; no node has more than one.
- **G11 — Three-way reward coupling.** Each boss's `reward_ability_id` must exist in the Ability System contract, and that ability's `unlock_node` must equal the boss's `node_id`.
- **G12 — Boss ability load-bearing.** The reward ability's `gates_unlocked` must be non-empty (inherits G1's logic, checked again in context).
- **G13 — One-shot floor.** No boss phase's `damage` may exceed Player Controller's `health`, regardless of the boss tier's exemption from the Enemy gate.

### Room/Tile

- **G14 — Room coverage.** Every World Graph node has exactly one room in the Room/Tile contract, no more, no fewer.
- **G15 — Enemy placement bidirectionality.** Every `enemy_placements` entry's `enemy_id` must exist in the Enemy contract with this room's `node_id` in its `spawn_nodes` — and every enemy whose `spawn_nodes` includes this node must have at least one placement here.
- **G16 — Boss room exclusivity.** Rooms on `type: boss` nodes must have zero `enemy_placements`.
- **G17 — Gap-edge consistency.** Any gap with a non-null `requires_ability` must correspond to a real World Graph edge carrying the same `requirement` value.

### Physics Reachability (the deferred check from Contract 8)

Uses a simplified projectile-motion model — **1 unit = 1 tile**, this is a design-generation approximation, not a physics engine. For a gap with `horizontal_distance_tiles` (h) and `vertical_distance_tiles` (v, positive = drop, negative = rise), using Player Controller's `speed` (s), `jump_force` (j), `gravity` (g):

- **Same height (v = 0):** time of flight `t = 2j/g`; max range `= s × t`
- **Falling (v > 0):** `t = [j + √(j² + 2gv)] / g`; max range `= s × t`
- **Rising (v < 0):** conservative bound using time-to-peak `t = j/g`; max range `= s × t`, and only valid if `|v| ≤ j²/(2g)` (max jump height)

- **G18 — Baseline clearance.** For every gap with `requires_ability: null`, the max range computed from Player Controller's *base* stats must be ≥ `horizontal_distance_tiles`. If it isn't, the level requires no ability but is physically uncrossable — a generation failure.
- **G19 — Gate validity.** For every gap with `requires_ability` set, base stats must **fail** to clear it (otherwise the ability isn't actually load-bearing for this specific gap — it's decorative), AND stats with that ability's `ability_modifiers` applied must clear it.

## Error format example

```json
{
  "valid": false,
  "errors": [
    {
      "gate": "G6",
      "message": "Ability 'grapple_hook' has no corresponding ability_modifiers entry in Player Controller",
      "location": "abilities.grapple_hook"
    },
    {
      "gate": "G19",
      "message": "Gap 'gap_04' in node_07 requires ability 'double_jump' but baseline stats already clear it (decorative gate)",
      "location": "rooms.node_07.gaps.gap_04"
    }
  ]
}
```

## Common pitfalls

- **Treating this as advisory.** `valid: false` means the design generation failed — this isn't a lint warning, it's the terminal gate for the whole 9-contract pipeline.
- **Stopping at the first error.** Run every gate, every time — partial validation reports hide downstream problems.
- **Skipping G19's decorative-gate check.** It's easy to validate that a gated gap is *eventually* crossable and forget to check it *wasn't already* crossable — that's the same "dead ability" failure mode as G1, just discovered later and more expensively.
