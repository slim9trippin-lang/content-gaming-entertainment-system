# POE Contract Schemas Reference

Full JSON schemas for each contract type used in Step 5 of the narrative structure pipeline.

## Table of Contents
1. [Input Contract](#1-input-contract)
2. [Interpretation Layer](#2-interpretation-layer)
3. [World Graph Contract](#3-world-graph-contract)
4. [Ability System Contract](#4-ability-system-contract)
5. [Player Controller Contract](#5-player-controller-contract)
6. [Enemy System Contract](#6-enemy-system-contract)
7. [Boss Contract](#7-boss-contract)
8. [Room / Tile Contract](#8-room--tile-contract)
9. [Validation Layer](#9-validation-layer)

---

## 1. Input Contract
```json
{
  "theme": "string — emotional/moral engine",
  "setting": "string — world description",
  "protagonist_archetype": "string — role in world",
  "tone": "gritty | grounded | stylized | fantastical | surreal",
  "core_ability_style": "string — movement + combat summary",
  "reference_inspirations": ["string"],
  "difficulty": "easy | normal | hard",
  "session_length": "short | medium | long"
}
```

## 2. Interpretation Layer
```json
{
  "movement_model": "grounded | aerial | hybrid | grid",
  "combat_model": "projectile | melee | hybrid | none",
  "world_structure": "dense | vertical | sprawling | linear",
  "progression_style": "ability-gated | key-based | stat-based | hybrid",
  "risk_profile": "high_damage | attrition | balanced | puzzle",
  "session_length_fit": "short | medium | long",
  "tone_register": "gritty | grounded | stylized | fantastical"
}
```

## 3. World Graph Contract
```json
{
  "nodes": [
    {
      "id": "node_01",
      "type": "start | traversal | combat | boss | hub",
      "connections": ["node_02"],
      "gates": [],
      "rewards": []
    }
  ],
  "edges": [
    {
      "from": "node_01",
      "to": "node_02",
      "requirement": "ability_id | none"
    }
  ]
}
```
**Validation rules:**
- Every locked edge must reference a valid ability_id
- No dead ends unless `type: "boss"` or `type: "start"`
- Must have exactly one `type: "start"` node

## 4. Ability System Contract
```json
{
  "abilities": [
    {
      "id": "string",
      "type": "movement | combat | utility",
      "unlock_node": "node_id",
      "gates_unlocked": ["edge_ids or node_ids"],
      "scaling": false
    }
  ]
}
```
**Rule:** Every ability must gate ≥1 path and be immediately usable after unlock.

## 5. Player Controller Contract
```json
{
  "movement": {
    "speed": 5.0,
    "jump_force": 12.0,
    "gravity": 0.8,
    "air_control": 0.6
  },
  "combat": {
    "attack_type": "projectile | melee | ranged",
    "attack_rate": 0.5,
    "damage": 10
  },
  "health": 100,
  "iframes": 0.3
}
```
**Note:** These values must be tunable. Game feel lives here — do not auto-generate final values.

## 6. Enemy System Contract
```json
{
  "enemies": [
    {
      "id": "string",
      "behavior": "patrol | chase | ranged | stationary | swarm",
      "health": 30,
      "movement_pattern": "horizontal | vertical | random | scripted",
      "spawn_nodes": ["node_ids"]
    }
  ]
}
```

## 7. Boss Contract
```json
{
  "bosses": [
    {
      "id": "string",
      "phases": [
        {
          "phase": 1,
          "patterns": ["string"],
          "health_threshold": 0.7
        }
      ],
      "reward": "ability_id",
      "gate_node": "node_id"
    }
  ]
}
```
**Rule:** Every boss must reward an ability and gate a previously unreachable area.

## 8. Room / Tile Contract
```json
{
  "rooms": [
    {
      "node_id": "string",
      "layout_type": "platforming | arena | vertical | hub | corridor",
      "hazards": ["string"],
      "platform_density": "low | medium | high"
    }
  ]
}
```

## 9. Validation Layer
```json
{
  "valid": true,
  "errors": [],
  "warnings": [],
  "checks": {
    "all_nodes_reachable": true,
    "all_abilities_obtainable": true,
    "all_gates_solvable": true,
    "difficulty_curve_valid": true,
    "no_softlocks": true
  }
}
```
**If `valid: false`** → generation pipeline halts. Do not pass broken contracts to runtime.
