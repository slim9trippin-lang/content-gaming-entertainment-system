---
name: controidvania-contracts
description: Generate the design-fingerprint half of the POE contract pipeline for Controidvania (Kinesthetic Explorer) games. Outputs full, production-ready JSON for contracts 1–4 (Input, Interpretation, World Graph, Ability System) and stops there. Contracts 5–9 require greybox playtesting and are intentionally excluded — no physics values, no enemy stats, no room layouts.
---

# Controidvania-Contracts Skill

## When to use

Source material is already confirmed as Controidvania (Kinesthetic Explorer skeleton). Skip skeleton selection — go straight to JSON.

Use this skill when:
- Source material has been mapped to Controidvania via `genre-skeleton-mapper`
- You need contracts 1–4 in production-ready JSON (not prose summaries)
- You are NOT yet ready for runtime tuning (contracts 5–9 need playtest data)

Do NOT use this skill to:
- Select a skeleton — use `genre-skeleton-mapper` first if skeleton is uncertain
- Generate all 9 contracts — use `narrative-structure` for the full pipeline
- Tune physics, enemy stats, or room layouts — those are contracts 5–9

---

## The Design-Fingerprint Half

Contracts 1–4 define what the game **IS**:

| Contract | Role |
|---|---|
| C1 Input | Thematic/emotional brief — tone, archetype, session feel |
| C2 Interpretation | Mechanical commitment — movement model, combat model, world shape |
| C3 World Graph | The map — nodes, edges, locked paths, boss placement |
| C4 Ability System | What the player learns and what each ability unlocks |

Contracts 5–9 define how the game **RUNS** (physics values, enemy behaviors, room geometry, full validation). Those require greybox data. Stub fields in 5–9 invite hallucinated frame data — don't generate them.

---

## Process

### Step 1 — Answer the 5 Translation Questions

Extract answers from source material before touching JSON:

1. **Signature movement?** → the protagonist's primary traversal verb (wall-cling, web-swing, dash, grapple, double-jump, teleport…) — becomes the first ability in C4
2. **Defining spaces?** → what environments characterize the source (rooftops, dungeons, subways, jungle canopy, underwater ruins…) — becomes C3 regional aesthetic
3. **What does the protagonist learn across the arc?** → ordered list of acquired capabilities — becomes the ability progression sequence in C4
4. **Who are the recurring antagonists?** → boss list — each gates a zone and rewards an ability
5. **What is a "locked door" in this world?** → gating logic (gang territory, magical seal, hostile environment, security clearance, physical barrier) — drives C3 edge requirements

Do not proceed to Step 2 until all five are answered in plain text.

---

### Step 2 — Fill C1 (Input Contract)

Schema: `references/contracts_1to4.md §C1`

- `theme`: the emotional/moral engine of the source
- `setting`: world description (one evocative phrase)
- `protagonist_archetype`: who they are in the world
- `tone`: pick from the enum — do not invent new values
- `core_ability_style`: SIGNATURE MOVEMENT first, then combat verb (e.g. `"wall-clinging + nail attacks"`)
- `reference_inspirations`: source IP + 1–2 comparable Controidvania titles for calibration
- `difficulty` / `session_length`: infer from source pacing

---

### Step 3 — Fill C2 (Interpretation Layer)

Schema: `references/contracts_1to4.md §C2`

**Locked fields — do not alter:**
- `progression_style: "ability-gated"` — structural DNA of this skeleton
- `world_structure`: must be `"dense"` or `"vertical"` — linear maps break this skeleton
- `combat_model`: never `"none"` — even minimal combat is real-time twitch

For `movement_model`: start at `"grounded"` — upgrade to `"hybrid"` only if the protagonist has a persistent aerial ability at game start (most Controidvanias are grounded base kit; aerial tools arrive as unlocks).

---

### Step 4 — Build C3 (World Graph Contract)

Schema: `references/contracts_1to4.md §C3`

Construction checklist:
- [ ] Minimum 10 nodes — the map is the game
- [ ] Exactly 1 `type: "start"` node
- [ ] 1 boss node per major region (boss gates the next region, rewards an ability)
- [ ] All locked edge `requirement` values match ability IDs in C4
- [ ] Every node reachable from start (run a mental traversal)
- [ ] At least 2 edges with `shortcut: true` (backtrack accessibility shortcuts)
- [ ] No dead-end nodes except `type: "boss"` (which connects forward after defeat)

Name regions from Step 1 (Question 2). Map narrative arc onto region order — early regions = start of story, final boss region = climax.

---

### Step 5 — Build C4 (Ability System Contract)

Schema: `references/contracts_1to4.md §C4`

Construction checklist:
- [ ] Minimum 4 abilities
- [ ] Abilities ordered by `narrative_order` (story order = unlock order)
- [ ] Every ability has `gates_unlocked` with ≥1 entry — no cosmetic/decoration abilities
- [ ] `unlock_node` references a valid node in C3
- [ ] Each gated edge in C3 has a matching ability ID here
- [ ] The signature movement from Step 1 (Question 1) is `narrative_order: 1` or a very early unlock

Combat abilities are allowed but must ALSO unlock at least one spatial path — otherwise they belong in contract 5 (Player Controller), not here.

---

### Step 6 — Validate the Design Fingerprint

Run before output:

- [ ] C2.progression_style === `"ability-gated"`
- [ ] C2.world_structure === `"dense"` or `"vertical"`
- [ ] Every edge requirement in C3 resolves to an ability_id in C4
- [ ] Every ability in C4 has a non-empty `gates_unlocked`
- [ ] C3 has exactly 1 start node
- [ ] All C3 nodes are reachable from start via open or gated edges
- [ ] Ability count ≥ locked edge count (no ability unlocks nothing)

If any check fails: fix the contracts. Do not emit broken JSON.

---

### Step 7 — Output

Output a single JSON object. No prose. No markdown headers inside the JSON. No stub fields for contracts 5–9.

```json
{
  "skeleton": "controidvania",
  "source": "<source title>",
  "design_fingerprint": {
    "contract_1_input": { },
    "contract_2_interpretation": { },
    "contract_3_world_graph": { },
    "contract_4_ability_system": { }
  },
  "contracts_5to9": "DEFERRED — requires greybox playtesting",
  "validation": {
    "fingerprint_valid": true,
    "errors": [],
    "warnings": []
  }
}
```

If `fingerprint_valid` is `false`, include error strings in `errors[]` and do not pass to the runtime pipeline.

---

## Locked Fields Reference

These are non-negotiable for this skeleton:

| Field | Locked Value | Rationale |
|---|---|---|
| C2.progression_style | `"ability-gated"` | Structural DNA — changing this makes it a different skeleton |
| C2.world_structure | `"dense"` or `"vertical"` | Map traversal is the product; linear = broken |
| C2.combat_model | not `"none"` | Even minimal combat is twitch real-time |
| C4: every ability gates ≥1 path | required | No decoration abilities before contracts 5–9 |
| C3: boss node per region | required | Boss = regional gatekeeper |
| Failure mode | checkpoint respawn | Not run-based (Roguelike is a different skeleton) |

---

## Handoff After This Skill

Once contracts 1–4 are valid:

- **C3 World Graph → designer** for room/tile layout decisions (feeds Contract 8)
- **C4 Ability System → programmer** for controller implementation (feeds Contract 5)
- **Full pipeline → `narrative-structure`** skill when greybox data is available to fill contracts 5–9
- **Contract 9 (Validation)** runs only after all 9 contracts are filled
