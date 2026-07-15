# 05 — Player Controller Contract

The Player Controller is where narrative and structure stop and pure physics begins. Everything upstream (Input → Interpretation → World Graph → Ability System) is discrete and text-shaped; this contract is continuous and numeric. It has to agree with two upstream contracts and gate two downstream ones — World Graph's traversal difficulty assumes these numbers, and Room/Tile geometry will be built against them.

**Known limitation, stated up front:** these are fixed default values, not playtested "game feel." Treat every number here as a documented starting point for a human tuning pass, not a finished spec. The contract's job is to give downstream systems something concrete to validate against — not to claim the numbers are correct.

## Schema

```json
{
  "movement": {
    "model": "enum: grounded | aerial | hybrid — must match Interpretation Layer's movement_model",
    "speed": "float — horizontal ground speed, units/sec",
    "jump_force": "float — initial upward velocity on jump",
    "gravity": "float — downward acceleration, units/sec²",
    "air_control": "float 0.0–1.0 — fraction of ground speed available mid-air",
    "terminal_velocity": "float — max fall speed"
  },
  "combat": {
    "model": "enum: projectile | melee | hybrid — must match Interpretation Layer's combat_model",
    "attack_rate": "float — seconds between attacks",
    "damage": "integer — base damage per hit",
    "range": "float — melee reach or projectile hitbox size, units"
  },
  "health": "integer — starting max HP",
  "iframes": "float — invulnerability window after taking damage, seconds",
  "ability_modifiers": [
    {
      "ability_id": "string — must match an id in the Ability System contract",
      "modifies": "string — which field above this ability changes (e.g. 'movement.air_control')",
      "modified_value": "value of the same type as the field being modified"
    }
  ],
  "tuning_status": "constant string: 'default_untested' — flags this contract as pending human playtesting"
}
```

## Generation rules

### Baseline defaults (starting point, not gospel)

| Field | Grounded default | Aerial default | Hybrid default |
|---|---|---|---|
| speed | 5.0 | 4.0 | 4.5 |
| jump_force | 10.0 | 14.0 | 12.0 |
| gravity | 0.9 | 0.6 | 0.75 |
| air_control | 0.4 | 0.8 | 0.6 |
| terminal_velocity | 18.0 | 14.0 | 16.0 |

Pick the row matching Interpretation's `movement_model`. These exist so every generated game starts from the same known baseline rather than arbitrary per-game numbers — deviation should be a deliberate later decision, not generation noise.

### Combat model defaults

| Field | Projectile default | Melee default | Hybrid default |
|---|---|---|---|
| attack_rate | 0.5 | 0.35 | 0.4 |
| damage | 8 | 12 | 10 |
| range | 6.0 (projectile travel treated separately) | 1.2 | 2.0 |

### ability_modifiers — this is where World Graph coupling happens

Every `movement`-type ability from the Ability System contract that plausibly changes player capability (double jump, dash, wall-climb, air dash) MUST have a corresponding `ability_modifiers` entry here. This is what lets Room/Tile geometry later validate "is this gap actually crossable once the player has `double_jump`."

If an ability exists in the Ability System contract with no `ability_modifiers` entry here, that's a contract violation — the ability isn't mechanically real, it's just a name.

### iframes and health

Default `iframes: 0.5`, `health: 100`. These interact with the Boss and Enemy contracts' damage values downstream — don't change one without checking the other two.

## Common pitfalls

- **Letting `movement.model` drift from Interpretation's `movement_model`.** These must be the same string. If they disagree, the contract is internally inconsistent and validation should fail it.
- **Forgetting an ability_modifiers entry.** The single most common way this contract silently breaks the pipeline — an ability that exists narratively but does nothing mechanically.
- **Treating the default table as final.** The `tuning_status` field exists specifically so downstream tooling (and humans) know not to trust these numbers as shipped values.
- **Combat `range` for projectiles.** Projectile range is really a travel-distance/lifetime property, not a static reach number — don't reuse the melee interpretation of `range` for projectile-model games without adjusting.
