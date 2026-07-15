# 06 — Enemy System Contract

Enemies are the World Graph's difficulty made concrete. Every node with `type: combat` or `type: traversal` needs population here, and every enemy's numbers have to make sense against the Player Controller's `damage`, `health`, and `iframes` — an enemy that out-damages the iframe window isn't a difficulty choice, it's a bug.

## Schema

```json
{
  "enemies": [
    {
      "id": "string (snake_case, e.g. 'rooftop_grunt_01') — unique",
      "type": "enum: grunt | flying | turret | ambush | swarm",
      "behavior_state_machine": "enum: patrol_only | patrol_aggro | stationary_aggro | ambush_trigger",
      "health": "integer",
      "damage": "integer — per-hit damage to player",
      "attack_rate": "float — seconds between attacks",
      "detection_range": "float — units at which patrol transitions to aggro",
      "movement_pattern": "enum: horizontal | vertical | stationary | flight_path | none",
      "spawn_nodes": "array of node IDs from World Graph — where this enemy appears",
      "gated_by": "string or null — ability ID; if set, this enemy only spawns/activates once player lacks OR has this ability (see gating_type)",
      "gating_type": "enum: blocks_until_ability | active_until_ability | null — required if gated_by is set",
      "contact_rule": "enum: symmetric | asymmetric — symmetric = player and enemy exchange damage identically on contact; asymmetric = touching does not always mean mutual damage (e.g. spikes-only enemy, or player attack has priority)"
    }
  ]
}
```

## Generation rules

### Health/damage scaling by node difficulty

World Graph nodes carry a `difficulty` field (1–5). Use this table as the fixed default — same rationale as Player Controller: known starting point, not tuned.

| Node difficulty | Enemy health | Enemy damage | Detection range |
|---|---|---|---|
| 1 | 15 | 5 | 4.0 |
| 2 | 25 | 8 | 5.0 |
| 3 | 40 | 12 | 6.0 |
| 4 | 60 | 16 | 7.0 |
| 5 | 85 | 20 | 8.0 |

**Hard validation check:** `enemy.damage` must never exceed `(player.health / 4)` at difficulty 1, scaling proportionally — an enemy that can kill the player in 2–3 hits at the start of the game is a design failure, not difficulty. This is a gate, not a suggestion.

### Behavior state machine, kept minimal on purpose

Four states cover the genre without over-engineering:
- `patrol_only` — moves on `movement_pattern`, never attacks, exists as environmental pressure
- `patrol_aggro` — patrols until `detection_range` triggers pursuit + attack
- `stationary_aggro` — doesn't move, attacks when player is in range (turrets)
- `ambush_trigger` — dormant until a proximity or scripted trigger, then activates as `patrol_aggro`

Anything more granular than this belongs in a Boss contract, not here — regular enemies should be cheap to reason about in bulk.

### spawn_nodes coupling

Every node in World Graph with `type: combat` needs at least one enemy with that node in `spawn_nodes`. Every node with `type: traversal` may optionally have enemies (environmental hazard enemies), but `type: boss`, `type: hub`, and `type: start` must NOT appear in any enemy's `spawn_nodes` — hubs and start nodes are player-safe by genre convention, and boss nodes are populated by the Boss contract instead.

### gated_by — ability-reactive enemies

Two patterns, both real in the genre:
- `blocks_until_ability` — enemy is an obstacle that becomes passable/defeatable only once the player has a specific ability (e.g., an armored enemy that requires a charged attack unlocked later). Until then, the node should route around it or the enemy should be a hard stop.
- `active_until_ability` — enemy disappears/deactivates permanently once the player acquires a specific ability (e.g., a security drone disabled once the player gets a hacking tool). Represents world-state progression, not player skill.

If `gated_by` is set, `gating_type` is required — an ungated ability reference is a contract error.

## Common pitfalls

- **Damage that outpaces iframes.** Cross-check every enemy's `attack_rate` against Player Controller's `iframes` — if an enemy can land two hits within the iframe window through overlapping attackers rather than raw speed, that's a swarm design problem, not an individual enemy stat problem.
- **Populating boss/hub/start nodes.** These are reserved. If you find yourself wanting an enemy there, it's a Boss contract, not this one.
- **Vague `movement_pattern: none` on a non-stationary type.** `none` is only valid for `type: turret` or `stationary_aggro` behavior — a `grunt` with no movement pattern is a modeling error.
- **Skipping the difficulty-scaling table.** Ad hoc numbers per enemy break the readable difficulty curve the whole World Graph was built to encode.
