# Urban Survival Controidvania — Contracts

User prose: *"Design a Controidvania about urban survival in a hostile modern city."*

The prose locks in theme and setting clearly. Two required fields needed inference: `protagonist_archetype` (extracted as "urban survivor" from the survival framing — adjust if you want something more specific like "fugitive," "hustler," or "courier") and `core_ability_style` (inferred as "mobility + resource control" from the urban-survival genre's typical moveset). All four contracts below; if the inferences are off, tell me which one and I'll regenerate downstream.

---

## 1. Input Contract

Locks in the loose creative direction. This is the filter that constrains every contract below.

```json
{
  "theme": "survival and power",
  "setting": "hostile modern city",
  "protagonist_archetype": "urban survivor",
  "tone": "gritty",
  "core_ability_style": "mobility + resource control",
  "reference_inspirations": [],
  "difficulty": "normal",
  "session_length": "medium"
}
```

## 2. Interpretation Layer

The design lock-in. Five binding decisions every downstream contract must obey.

```json
{
  "movement_model": "grounded",
  "combat_model": "hybrid",
  "world_structure": "dense",
  "progression_style": "ability-gated",
  "risk_profile": "high_damage"
}
```

## 3. World Graph Contract

The structural backbone. 12 nodes across a layered urban geography: subway underground → ground-level alleys → rooftop circuit → vertical skyscrapers → kingpin's penthouse. Two loops (backtracks through the early hub, plus a one-way shortcut from the hidden vault). Difficulty climbs roughly with vertical altitude.

```json
{
  "nodes": [
    {"id": "node_01", "type": "start", "biome": "abandoned subway platform", "difficulty": 1, "connections": ["node_02"], "gates": [], "rewards": []},
    {"id": "node_02", "type": "hub", "biome": "subway concourse", "difficulty": 1, "connections": ["node_01", "node_03", "node_04"], "gates": [], "rewards": ["save_point", "black_market_vendor"]},
    {"id": "node_03", "type": "traversal", "biome": "service tunnels", "difficulty": 2, "connections": ["node_02", "node_05"], "gates": [], "rewards": ["parkour_vault"]},
    {"id": "node_04", "type": "combat", "biome": "ground-level alley", "difficulty": 2, "connections": ["node_02", "node_06"], "gates": [], "rewards": ["scrap_currency"]},
    {"id": "node_05", "type": "boss", "biome": "rooftop standoff", "difficulty": 3, "connections": ["node_03", "node_07"], "gates": ["parkour_vault"], "rewards": ["double_jump"]},
    {"id": "node_06", "type": "traversal", "biome": "fire escape network", "difficulty": 2, "connections": ["node_04", "node_07"], "gates": ["parkour_vault"], "rewards": []},
    {"id": "node_07", "type": "hub", "biome": "penthouse safe house", "difficulty": 3, "connections": ["node_05", "node_06", "node_08"], "gates": [], "rewards": ["save_point", "wall_kick"]},
    {"id": "node_08", "type": "traversal", "biome": "skyscraper interior", "difficulty": 3, "connections": ["node_07", "node_09"], "gates": ["double_jump"], "rewards": []},
    {"id": "node_09", "type": "boss", "biome": "skyscraper rooftop", "difficulty": 4, "connections": ["node_08", "node_10", "node_11"], "gates": [], "rewards": ["grapple_hook"]},
    {"id": "node_10", "type": "secret", "biome": "underground armory vault", "difficulty": 3, "connections": ["node_09", "node_02"], "gates": ["wall_kick"], "rewards": ["weapon_cache", "lore_fragment"]},
    {"id": "node_11", "type": "combat", "biome": "rival crew territory", "difficulty": 4, "connections": ["node_09", "node_12"], "gates": [], "rewards": []},
    {"id": "node_12", "type": "boss", "biome": "kingpin penthouse", "difficulty": 5, "connections": ["node_11"], "gates": ["grapple_hook"], "rewards": ["ending"]}
  ],
  "edges": [
    {"from": "node_01", "to": "node_02", "requirement": "none", "bidirectional": true},
    {"from": "node_02", "to": "node_03", "requirement": "none", "bidirectional": true},
    {"from": "node_02", "to": "node_04", "requirement": "none", "bidirectional": true},
    {"from": "node_03", "to": "node_05", "requirement": "parkour_vault", "bidirectional": true},
    {"from": "node_04", "to": "node_06", "requirement": "none", "bidirectional": true},
    {"from": "node_06", "to": "node_07", "requirement": "parkour_vault", "bidirectional": true},
    {"from": "node_05", "to": "node_07", "requirement": "none", "bidirectional": true},
    {"from": "node_07", "to": "node_08", "requirement": "double_jump", "bidirectional": true},
    {"from": "node_08", "to": "node_09", "requirement": "none", "bidirectional": true},
    {"from": "node_09", "to": "node_10", "requirement": "wall_kick", "bidirectional": true},
    {"from": "node_10", "to": "node_02", "requirement": "none", "bidirectional": false},
    {"from": "node_09", "to": "node_11", "requirement": "none", "bidirectional": true},
    {"from": "node_11", "to": "node_12", "requirement": "grapple_hook", "bidirectional": true}
  ],
  "start_node": "node_01",
  "end_node": "node_12"
}
```

## 4. Ability System Contract

Four abilities, all `movement` type — appropriate for a mobility-focused urban game. Acquisition order: `parkour_vault` (node_03, no prereqs) → `double_jump` (node_05, gated by parkour_vault) → `wall_kick` (node_07, reached after double_jump) → `grapple_hook` (node_09, reached after wall_kick).

```json
{
  "abilities": [
    {
      "id": "parkour_vault",
      "type": "movement",
      "description": "Chain handholds up fire escapes, brick walls, and drainpipes to scale low-to-mid-height vertical surfaces. The signature urban-traversal verb.",
      "unlock_node": "node_03",
      "gates_unlocked": ["fire_escape_routes", "rooftop_access", "low_ledge_climbs"],
      "scaling": false
    },
    {
      "id": "double_jump",
      "type": "movement",
      "description": "Catch a second wind mid-air to clear gaps between rooftops. Pure horizontal-range extension.",
      "unlock_node": "node_05",
      "gates_unlocked": ["wide_rooftop_gaps", "skyscraper_traversal"],
      "scaling": false
    },
    {
      "id": "wall_kick",
      "type": "movement",
      "description": "Push off vertical surfaces to gain altitude, alternating between two walls in tight shafts to climb past parkour_vault's height ceiling.",
      "unlock_node": "node_07",
      "gates_unlocked": ["narrow_shafts", "high_ledges", "vault_access"],
      "scaling": false
    },
    {
      "id": "grapple_hook",
      "type": "movement",
      "description": "Anchor a line to designated points and swing or pull across long distances. Late-game traversal range.",
      "unlock_node": "node_09",
      "gates_unlocked": ["long_distance_swings", "kingpin_tower_access", "fast_travel_anchors"],
      "scaling": false
    }
  ]
}
```

---

## Cross-contract validation

- ✅ All gated edges reference matching abilities (4 gated edges, all valid)
- ✅ All abilities are load-bearing (each gates at least one edge)
- ✅ No orphan nodes (12 nodes, all connected via edges)
- ✅ Graph reachable in ability-unlock order (start → node_03 → node_05 → node_07 → node_08 → node_09 → node_11 → node_12)
- ✅ No circular lockouts (every unlock_node reachable with prior abilities only)
- ✅ At least 2 loops (node_05 ↔ node_07 backtrack ring, plus node_10 → node_02 one-way shortcut)
- ✅ One-way edge present (node_10 → node_02)

⚠️ **Note:** `combat_model: hybrid` is locked in but the four abilities are all `movement`. The back-five contracts (out of scope here) would specify the protagonist's actual combat verbs — for a `hybrid` model, that's typically a melee weapon plus a ranged option, both designed in the Player Controller contract.
