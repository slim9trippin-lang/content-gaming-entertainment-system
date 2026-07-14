# A Boy and His Blob (Horror/Sci-Fi) Controidvania — Contracts

User prompt: *"Boy-and-his-blob-style horror/sci-fi metroidvania."*

This is a structurally unusual case worth flagging up front: the player controls a **pair** (boy + symbiotic companion), and the abilities live on the companion rather than the protagonist. Each ability is a **transformation triggered by a consumable** the player has found — mechanically ability-gated, but acquired via key-like consumables. The Interpretation contract captures this as `progression_style: hybrid`. The Ability System contract names each transformation as an ability with the triggering substance noted in the description.

Horror/sci-fi reframes the original's whimsical jellybeans as biological samples found in an abandoned research facility, and reframes "feed the blob" as "expose the symbiote to contaminant." Tonal references: *Signalis* (sci-fi survival horror with a companion), *Alien* (claustrophobic facility horror), the *Blob* template kept structurally intact.

---

## 1. Input Contract

```json
{
  "theme": "symbiosis as survival in a hostile facility",
  "setting": "abandoned bio-research station on a derelict orbital",
  "protagonist_archetype": "boy with symbiotic companion (paired unit)",
  "tone": "uneasy",
  "core_ability_style": "consumable-triggered companion transformations",
  "reference_inspirations": ["A Boy and His Blob", "Signalis", "Alien"],
  "difficulty": "hard",
  "session_length": "medium"
}
```

## 2. Interpretation Layer

The horror/sci-fi tone pushes `risk_profile` to `high_damage` (a hit or two ends a run). The descent through a derelict facility lands cleanly on `world_structure: vertical`. The combat model is the awkward field: the boy doesn't fight directly — his combat options are *all* mediated through the symbiote's transformations (claw form, acid form), which span melee and projectile. Captured as `hybrid`. The progression-style hybrid reflects the consumable-but-also-ability nature of the unlock system.

```json
{
  "movement_model": "grounded",
  "combat_model": "hybrid",
  "world_structure": "vertical",
  "progression_style": "hybrid",
  "risk_profile": "high_damage"
}
```

## 3. World Graph Contract

12 nodes descending through the facility from the docking ring at the top to the contamination core at the bottom. Biomes: airlocks → med bay → research wings → cryo storage → contamination zone → core. One-way shortcut from a service shaft back up to the docking ring; loop through the early hub. Difficulty increases with depth.

```json
{
  "nodes": [
    {"id": "node_01", "type": "start", "biome": "docking ring airlock", "difficulty": 1, "connections": ["node_02"], "gates": [], "rewards": []},
    {"id": "node_02", "type": "hub", "biome": "decompression chamber", "difficulty": 1, "connections": ["node_01", "node_03", "node_04"], "gates": [], "rewards": ["save_terminal", "blob_companion_intro"]},
    {"id": "node_03", "type": "traversal", "biome": "upper corridors", "difficulty": 2, "connections": ["node_02", "node_05"], "gates": [], "rewards": ["ladder_sample"]},
    {"id": "node_04", "type": "combat", "biome": "abandoned mess hall", "difficulty": 2, "connections": ["node_02", "node_06"], "gates": [], "rewards": ["audio_log_01"]},
    {"id": "node_05", "type": "boss", "biome": "infected med bay", "difficulty": 3, "connections": ["node_03", "node_07"], "gates": ["ladder_form"], "rewards": ["bubble_sample"]},
    {"id": "node_06", "type": "traversal", "biome": "vertical maintenance shaft", "difficulty": 3, "connections": ["node_04", "node_07"], "gates": ["ladder_form"], "rewards": []},
    {"id": "node_07", "type": "hub", "biome": "central elevator hub", "difficulty": 3, "connections": ["node_05", "node_06", "node_08"], "gates": [], "rewards": ["save_terminal", "claw_sample"]},
    {"id": "node_08", "type": "traversal", "biome": "flooded research wing", "difficulty": 4, "connections": ["node_07", "node_09"], "gates": ["bubble_form"], "rewards": []},
    {"id": "node_09", "type": "boss", "biome": "cryo storage", "difficulty": 4, "connections": ["node_08", "node_10", "node_11"], "gates": ["claw_form"], "rewards": ["spike_sample"]},
    {"id": "node_10", "type": "secret", "biome": "researcher quarters", "difficulty": 3, "connections": ["node_09", "node_02"], "gates": ["spike_form"], "rewards": ["true_ending_key", "audio_log_final"]},
    {"id": "node_11", "type": "combat", "biome": "contamination corridor", "difficulty": 5, "connections": ["node_09", "node_12"], "gates": ["spike_form"], "rewards": []},
    {"id": "node_12", "type": "boss", "biome": "contamination core", "difficulty": 5, "connections": ["node_11"], "gates": [], "rewards": ["ending"]}
  ],
  "edges": [
    {"from": "node_01", "to": "node_02", "requirement": "none", "bidirectional": true},
    {"from": "node_02", "to": "node_03", "requirement": "none", "bidirectional": true},
    {"from": "node_02", "to": "node_04", "requirement": "none", "bidirectional": true},
    {"from": "node_03", "to": "node_05", "requirement": "ladder_form", "bidirectional": true},
    {"from": "node_04", "to": "node_06", "requirement": "none", "bidirectional": true},
    {"from": "node_06", "to": "node_07", "requirement": "ladder_form", "bidirectional": true},
    {"from": "node_05", "to": "node_07", "requirement": "none", "bidirectional": true},
    {"from": "node_07", "to": "node_08", "requirement": "bubble_form", "bidirectional": true},
    {"from": "node_08", "to": "node_09", "requirement": "claw_form", "bidirectional": true},
    {"from": "node_09", "to": "node_10", "requirement": "spike_form", "bidirectional": true},
    {"from": "node_10", "to": "node_02", "requirement": "none", "bidirectional": false},
    {"from": "node_09", "to": "node_11", "requirement": "spike_form", "bidirectional": true},
    {"from": "node_11", "to": "node_12", "requirement": "none", "bidirectional": true}
  ],
  "start_node": "node_01",
  "end_node": "node_12"
}
```

## 4. Ability System Contract

Four symbiote transformations, each triggered by a biological sample the player finds. Acquisition order: `ladder_form` (sample at node_03) → `bubble_form` (sample at node_05) → `claw_form` (sample at node_07) → `spike_form` (sample at node_09). Three movement-typed transformations, one combat-typed (`claw_form` is the closest the pair gets to a direct attack option).

```json
{
  "abilities": [
    {
      "id": "ladder_form",
      "type": "movement",
      "description": "Expose the symbiote to the ladder_sample (a calcified spinal fragment) and it extrudes rigid rungs against vertical surfaces, letting the boy climb. Triggered by sample, persists while symbiote is fed.",
      "unlock_node": "node_03",
      "gates_unlocked": ["vertical_shafts", "med_bay_access"],
      "scaling": false
    },
    {
      "id": "bubble_form",
      "type": "movement",
      "description": "The bubble_sample (a viscous polymer harvested from a cryo-tank) makes the symbiote inflate into a sealed sac the boy crawls inside — survives contaminated air, drowns flooded passages.",
      "unlock_node": "node_05",
      "gates_unlocked": ["flooded_zones", "toxic_air_corridors"],
      "scaling": false
    },
    {
      "id": "claw_form",
      "type": "combat",
      "description": "The claw_sample (chitin scraped from a dead specimen) drives the symbiote into a brief aggressive shape — extends as a chitinous limb the boy swings to break bio-barriers or strike enemies. The pair's only direct-violence option.",
      "unlock_node": "node_07",
      "gates_unlocked": ["bio_barriers", "cryo_access"],
      "scaling": false
    },
    {
      "id": "spike_form",
      "type": "utility",
      "description": "The spike_sample (corrosion-resistant filament from the contamination core's edge) hardens the symbiote into a defensive shell — the boy walks safely through acid-flooded corridors and triggers heavy pressure plates.",
      "unlock_node": "node_09",
      "gates_unlocked": ["acid_corridors", "pressure_plate_routes", "researcher_quarters"],
      "scaling": false
    }
  ]
}
```

---

## Cross-contract validation

- ✅ All gated edges reference matching abilities (5 gated edges, all valid)
- ✅ All abilities load-bearing (each gates at least one edge)
- ✅ No orphan nodes (12 nodes, all connected)
- ✅ Graph reachable in ability-unlock order
- ✅ No circular lockouts (each unlock_node reachable with prior abilities only)
- ✅ Two loops (node_05 ↔ node_07 ring; node_10 → node_02 one-way shortcut to docking ring)
- ✅ One-way edge present (node_10 → node_02)

⚠️ **Strain flag — schema-vs-source:** The standard Ability System schema assumes the player-character holds the abilities. Here the *symbiote* holds them, triggered by the player's consumables. The contract represents this faithfully (each ability description names its trigger sample), but a back-five Player Controller contract would need an unusual structure — the player's actual inputs are "feed sample" and "command symbiote," not "jump" and "attack." Flagging because a downstream runtime would need to handle the indirection.

⚠️ **Strain flag — combat thinness:** `combat_model: hybrid` is locked but only one ability (`claw_form`) is combat-typed. The pair fundamentally avoids direct violence — most encounters are about *escape* or *environmental solutions* rather than fights. The horror tone supports this (vulnerability is the point), but if the back-five contracts try to build a full combat moveset, they'll be working against the source. Consider re-tagging `combat_model: hybrid` → `melee` with the design note that combat is rare and deliberately disempowering.
