# 07 — Boss Contract

Bosses are the only place in the pipeline where World Graph, Ability System, and Enemy-tier design intersect at once: a boss occupies a `type: boss` node, and defeating it is always how the player receives a new ability. This is a three-way coupling — get any leg wrong and either the graph has an unreachable ability, the ability contract has a phantom `unlock_node`, or the boss node has no payoff.

## Resolved design decisions

- Every boss awards an ability. No lore-only/non-gating bosses in this contract.
- Bosses have their own escalated stat tier, exempt from the Enemy contract's `damage ≤ player.health/4` gate. Bosses are allowed to hit harder than regular enemies at the same difficulty number.
- Phase count scales with node difficulty rather than a fixed count.

## Schema

```json
{
  "bosses": [
    {
      "id": "string (snake_case, e.g. 'rooftop_enforcer_boss')",
      "node_id": "string — must match a World Graph node with type: boss, exactly one boss per boss node",
      "total_health": "integer",
      "phases": [
        {
          "phase": "integer, starts at 1",
          "health_threshold": "float 0.0–1.0 — phase ends when boss HP drops below this fraction of total_health (phase 1 always starts at 1.0)",
          "patterns": "array of strings — attack pattern tags, unique to this boss (e.g. ['spread_shot', 'dash_slam'])",
          "damage": "integer — this phase's per-hit damage to player",
          "ability_showcase": "boolean — true if this phase demonstrates the reward ability being used against the player (see Generation rules)"
        }
      ],
      "reward_ability_id": "string — must match an id in Ability System contract, and that ability's unlock_node must equal this boss's node_id",
      "player_gap_feedback": "string — on defeat, what the player was missing (e.g. 'You needed a way to close distance fast — that's what you're about to get.')"
    }
  ]
}
```

## Generation rules

### Phase count and stat scaling by node difficulty

Bosses get their own table — deliberately steeper than the Enemy contract's, and exempt from its hard gate.

| Node difficulty | Phase count | total_health | Phase damage progression |
|---|---|---|---|
| 1 | 1 | 60 | 10 |
| 2 | 2 | 100 | 8 → 12 |
| 3 | 2 | 150 | 10 → 15 |
| 4 | 3 | 220 | 10 → 14 → 18 |
| 5 | 4 | 300 | 12 → 16 → 20 → 25 |

`health_threshold` values divide `total_health` evenly across phases unless a specific boss narratively justifies an uneven split (e.g., a long opening phase, short desperate final phase).

### The one hard floor that still applies

Even though bosses are exempt from the Enemy contract's formula, no phase's `damage` may exceed the Player Controller's `health` value. A boss that can one-shot the player from full health isn't difficulty, it's a design failure regardless of which tier system is in play.

### ability_showcase — the gap-analysis pattern

Exactly one phase (typically the second-to-last) should set `ability_showcase: true` and include a pattern that uses the *reward ability's* verb against the player — a boss who's about to grant `double_jump` might display an aerial evasive maneuver in that phase. This does two things: it telegraphs the mechanical solution the player is about to receive, and it makes `player_gap_feedback` concrete rather than generic.

### The three-way coupling, explicitly

For every boss:
1. `node_id` must exist in World Graph as `type: boss`, and no other boss may share that node.
2. `reward_ability_id` must exist in the Ability System contract.
3. That ability's `unlock_node` (Ability System field) must equal this boss's `node_id`.

If any of these three don't line up, the pipeline has a boss that either can't be found, can't reward anything, or rewards an ability the player could've gotten elsewhere — breaking the "boss defeat = gate-key" rule this contract is built on.

## Common pitfalls

- **Uneven phase splits with no narrative reason.** Default to even `health_threshold` division; only deviate deliberately.
- **Reward ability with empty `gates_unlocked`.** Same dead-ability problem as the Ability System contract — a boss-granted ability still has to open something.
- **Skipping `ability_showcase`.** Without it, boss fights lose the gap-analysis payoff that replaces a generic loss screen.
- **Forgetting the one-shot floor.** This is the single check that survives even though bosses are exempt from the Enemy gate — don't drop it by association.
