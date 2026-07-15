# 02 — Interpretation Layer

*Reconstructed from original build session history — recovered flash drive did not contain this file.*

The Interpretation Layer is the **design lock-in point**. It converts the Input Contract's loose creative direction into five binding mechanical decisions. After this contract is set, every downstream contract must obey it — the World Graph can't go vertical if Interpretation says `world_structure: dense`, the Ability System can't include flight if `movement_model: grounded`.

This is where AI typically wants to keep its options open. Don't. The contract's value is in committing.

## Schema

```json
{
  "movement_model": "enum: grounded | aerial | hybrid",
  "combat_model": "enum: projectile | melee | hybrid",
  "world_structure": "enum: dense | vertical | sprawling",
  "progression_style": "enum: ability-gated | key-based | hybrid",
  "risk_profile": "enum: high_damage | attrition | balanced"
}
```

All five fields are required. All five are enums — no free text.

## Mapping rules (Input → Interpretation)

### `movement_model`

| Input signal | → movement_model |
|---|---|
| Protagonist walks/runs/jumps at human scale; setting is grounded | `grounded` |
| Flight, swinging, floating, or aerial traversal is central | `aerial` |
| Protagonist moves in both registers meaningfully | `hybrid` |

Default for ambiguous Input → `grounded` (most Controidvanias are grounded).

### `combat_model`

| Input signal | → combat_model |
|---|---|
| Guns, bows, magic bolts, ranged projectiles dominant | `projectile` |
| Swords, fists, martial arts, melee dominant | `melee` |
| Both matter; choice of approach is a game mechanic | `hybrid` |

### `world_structure`

| Input signal | → world_structure |
|---|---|
| City, urban, dense settlement, layered architecture | `dense` |
| Cave systems, tall ruins, vertical kingdoms, mineshafts | `vertical` |
| Open biomes, alien worlds, ancient continents | `sprawling` |

### `progression_style`

| Input signal | → progression_style |
|---|---|
| Abilities change *how* the protagonist moves/fights | `ability-gated` |
| Keys, badges, clearance levels unlock specific doors | `key-based` |
| Both matter; some abilities change movement, some items unlock doors | `hybrid` |

Default for Controidvania → `ability-gated`. This is the genre's defining feature. Only switch to `key-based` if the Input strongly signals item-keys-over-abilities (e.g., a heist game in a Controidvania shell).

### `risk_profile`

| Input signal | → risk_profile |
|---|---|
| Gritty, lethal, hardcore, Soulslike vibes | `high_damage` |
| Long, exploratory, can take a beating, classic feel | `attrition` |
| Modern indie balance, fair fights, reasonable healing | `balanced` |

Map from `tone` primarily (`gritty`/`noir`/`horror` → `high_damage`; `literary`/`pulpy`/`whimsical` → `attrition` or `balanced`; `balanced` tone → `balanced`), then from `difficulty` secondarily (`hard` tilts `high_damage`, `easy` tilts `balanced`).

## Example

Input: `{ "theme": "survival and power", "setting": "hostile modern city", "protagonist_archetype": "street tactician", "tone": "gritty", "core_ability_style": "mobility + resource control" }`

```json
{
  "movement_model": "grounded",
  "combat_model": "hybrid",
  "world_structure": "dense",
  "progression_style": "ability-gated",
  "risk_profile": "high_damage"
}
```

## Per-contract validation

- [ ] All five fields present
- [ ] Each field's value is one of the enum's allowed values (no free text)
- [ ] No contradictions: e.g., `movement_model: aerial` with `world_structure: dense` is suspicious (aerial wants vertical or sprawling) — flag for review but don't block
- [ ] If Input's `protagonist_archetype` strongly implied a specific movement type (e.g., "wall-crawler") and `movement_model` didn't capture it, regenerate

## Common mistakes

- **Defaulting to `hybrid` on everything.** Hybrid is the cowardly choice. Commit to `grounded` or `aerial`, `projectile` or `melee`, unless the source genuinely demands both. Hybrid creates a moveset that's twice as expensive to design and tune.
- **Letting `tone` override `movement_model`.** "Gritty" doesn't mean grounded; "whimsical" doesn't mean aerial. These are independent axes.
- **Skipping `progression_style`.** For a Controidvania, this almost always lands on `ability-gated` — but write it explicitly. Don't assume.
- **Over-mapping `risk_profile` from `difficulty`.** Difficulty is a slider in settings; risk_profile is a design pillar. They're related but not identical.
