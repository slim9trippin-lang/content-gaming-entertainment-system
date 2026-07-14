# Controidvania POE Contracts — Schemas 1–4

Scoped subset of the full 9-contract schema. For Controidvania (Kinesthetic Explorer) games only.

Contracts 5–9 are excluded by design — they require greybox playtesting and must not be generated from source material alone. See `SKILL.md §The Design-Fingerprint Half` for rationale.

For the full 9-contract schema, see `narrative-structure/references/contracts.md`.

---

## §C1 Input Contract

```json
{
  "theme": "string — emotional/moral engine (e.g., 'reclaiming lost identity', 'nature vs. industry')",
  "setting": "string — world description (e.g., 'crumbling post-industrial underground city')",
  "protagonist_archetype": "string — who they are in the world (e.g., 'amnesiac knight', 'spider-blessed rogue')",
  "tone": "gritty | grounded | stylized | fantastical | surreal",
  "core_ability_style": "string — SIGNATURE MOVEMENT first, then combat verb (e.g., 'wall-clinging + nail attacks', 'web-swinging + improvised brawling')",
  "reference_inspirations": [
    "string — source IP",
    "string — comparable Controidvania title for calibration"
  ],
  "difficulty": "easy | normal | hard",
  "session_length": "short | medium | long"
}
```

**Notes:**
- `core_ability_style` must name the traversal verb before the combat verb — traversal is primary in this skeleton
- `reference_inspirations[0]` is the source IP; remaining entries are calibration references (existing games with similar feel)

---

## §C2 Interpretation Layer

```json
{
  "movement_model": "grounded | hybrid",
  "combat_model": "melee | projectile | hybrid",
  "world_structure": "dense | vertical",
  "progression_style": "ability-gated",
  "risk_profile": "high_damage | attrition",
  "session_length_fit": "short | medium | long",
  "tone_register": "gritty | grounded | stylized | fantastical | surreal"
}
```

**Locked fields — never alter for this skeleton:**
- `progression_style` must be `"ability-gated"` — this is what makes it a Controidvania
- `world_structure` must be `"dense"` or `"vertical"` — linear maps break backtracking
- `combat_model` is never `"none"` — combat is real-time twitch even when minimal
- `movement_model` is never `"aerial"` as base — start at `"grounded"`, upgrade to `"hybrid"` only if the protagonist has persistent flight or levitation at game start

---

## §C3 World Graph Contract

```json
{
  "nodes": [
    {
      "id": "string — snake_case identifier (e.g., 'forgotten_aqueduct')",
      "type": "start | traversal | combat | boss | hub",
      "region": "string — display name for the area",
      "connections": ["node_id — adjacent nodes (open or gated)"],
      "gates": ["ability_id — required to enter this node; empty array if open"],
      "rewards": ["ability_id — awarded here (boss drop or exploration pickup); empty if none"]
    }
  ],
  "edges": [
    {
      "from": "node_id",
      "to": "node_id",
      "requirement": "ability_id | none",
      "direction": "one-way | two-way",
      "shortcut": false
    }
  ]
}
```

**Validation rules — must pass before output:**
- Exactly 1 node with `type: "start"`
- No dead-end nodes except `type: "boss"` (boss connects forward after defeat) and terminal hub nodes
- Every `requirement` value in edges must match an `id` in C4.abilities
- All nodes reachable from start via open + gated edges
- At least 2 edges with `shortcut: true` (late-game traversal shortcuts that open on backtrack)
- 1 boss node per major narrative region; each boss node has at least 1 entry in `rewards[]`

**Node type guide:**
- `start` — single entry point, player begins here
- `traversal` — platforming/navigation challenge room, no major combat
- `combat` — encounter-dense room, no ability reward
- `boss` — boss arena; always rewards an ability on first clear
- `hub` — safe room / fast-travel anchor; no gates, connects to multiple regions

---

## §C4 Ability System Contract

```json
{
  "abilities": [
    {
      "id": "string — snake_case, matches references in C3 gates/edges/rewards",
      "name": "string — display name shown to player",
      "type": "movement | combat | utility",
      "description": "string — one sentence: what it lets the player DO physically",
      "unlock_node": "node_id — where it is awarded (must exist in C3)",
      "gates_unlocked": [
        "node_id or edge descriptor — what becomes accessible after gaining this ability"
      ],
      "narrative_order": 1,
      "scaling": false
    }
  ]
}
```

**Validation rules — must pass before output:**
- Minimum 4 abilities
- `narrative_order` integers are unique and sequential from 1
- Every ability has `gates_unlocked` with ≥1 entry — no decoration-only abilities at this stage
- `unlock_node` references a node that exists in C3
- Every gated edge in C3 has a corresponding ability_id here
- The protagonist's signature movement (from C1.core_ability_style) appears at `narrative_order: 1` or `2`

**Type guidance:**
- `movement` — changes how the player traverses space (double jump, dash, wall-cling, grapple)
- `combat` — changes how the player fights (must ALSO gate a spatial path to belong in C4; otherwise belongs in C5)
- `utility` — changes interaction with the world (map reveal, item sense, environmental manipulation)

`scaling: false` is the default at the fingerprint stage. Scaling behavior is a C5 (Player Controller) concern.

---

*For contracts 5–9 schemas, see `narrative-structure/references/contracts.md`.*
