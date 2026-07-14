---
name: narrative-structure
description: >
  Convert narrative input — any length, any format — into structured game design data
  for the POE/PLS pipeline. Use this skill whenever the user provides a story, book synopsis,
  theme prompt, character description, IP concept, or any narrative source material and wants
  to extract game-relevant structure from it. Also triggers when the user says things like
  "turn this into a game", "what skeleton fits this story", "extract game elements from",
  "analyze this narrative", "map this to a genre", or asks about translating narrative
  content into mechanics, contracts, GDDs, or genre skeletons. Always use this skill before
  generating any POE contract JSON or selecting a genre skeleton — narrative analysis
  must come first.
---

# Narrative Structure Skill

Converts raw narrative input into structured game design data. This is the **Interpretation Layer**
of the POE pipeline — the step between "story" and "mechanics".

---

## Pipeline Overview

```
INPUT (any length/format)
  ↓
STEP 1: Input Classification
  ↓
STEP 2: Narrative Extraction
  ↓
STEP 3: Mechanical Translation
  ↓
STEP 4: Skeleton Selection + Reasoning
  ↓
STEP 5: [Optional] Contract Generation
```

Each step's output feeds the next. Never skip to contracts without completing the analysis.

---

## STEP 1: Input Classification

Before extracting anything, classify the input to set expectations:

| Type | Signal | Strategy |
|------|--------|----------|
| **Theme prompt** | <50 words, abstract ("survival in a dystopian city") | Infer; fill gaps with archetype defaults |
| **Character brief** | Protagonist-focused, lists abilities/traits | Lead with archetype mapping |
| **Synopsis** | Plot summary, named characters, defined world | Full extraction |
| **Story bible / long-form** | Multiple factions, locations, lore depth | Summarize first, then extract |
| **IP reference** | Named existing property ("like Dune") | Extract known structural patterns |

State the classification at the start of your response. For long-form input, produce a
2–3 sentence compression before proceeding — this anchors the rest of the analysis.

---

## STEP 2: Narrative Extraction

Extract these 7 elements. For sparse inputs, infer reasonable defaults and mark them `[inferred]`.

### 2.1 Core Theme
The emotional/moral engine of the story. Not genre — the *why*.
- Examples: "survival vs. loyalty", "power corrupts systems", "found family against institutions"
- → Maps to: tone parameters, NPC motivation systems, progression stakes

### 2.2 World Architecture
How the world is physically and socially structured.
- Dimensions: scale (intimate/city/world), density (sparse/layered/labyrinthine), verticality (flat/vertical/multi-plane)
- → Maps to: world graph structure, room/tile layout types, traversal design

### 2.3 Protagonist Archetype
Not class — the *role* the protagonist plays in the world.
- Reference archetypes: Tactician, Survivor, Outcast, Seeker, Enforcer, Trickster, Protector
- → Maps to: movement model, core ability style, combat approach

### 2.4 Conflict Model
How the story generates tension.
- Types: external (enemy/environment), internal (resource/moral), systemic (faction/economy)
- → Maps to: enemy design, progression gates, risk profile

### 2.5 Pacing Signature
The rhythm of the narrative.
- Types: escalating (slow build → explosion), episodic (self-contained arcs), sustained tension (constant pressure)
- → Maps to: session structure, difficulty curve, boss placement

### 2.6 Tone Register
- Scale: gritty / grounded / stylized / fantastical / surreal
- → Maps to: palette constraints (see retro-pixel-art skill), audio direction, UI language

### 2.7 Mechanical Seeds
Moments in the narrative that suggest specific mechanics.
- Look for: chase sequences, power asymmetry, resource scarcity, territory control, transformation, social manipulation
- → Maps directly to: ability systems, inventory design, enemy behaviors

**Output format for Step 2:**
```
NARRATIVE PROFILE
─────────────────
Theme:         [value]
World:         [value]
Protagonist:   [value]
Conflict:      [value]
Pacing:        [value]
Tone:          [value]
Mechanical seeds: [list]
```

---

## STEP 3: Mechanical Translation

Translate the narrative profile into the POE Interpretation Layer format.
This is the bridge between story language and system language.

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

Justify each field with 1 sentence tied back to the narrative profile.
**Do not guess mechanically attractive choices** — ground every decision in the story.

---

## STEP 4: Skeleton Selection + Reasoning

Match the mechanical translation to one or two of the four genre skeletons.

### The Four Skeletons

| Skeleton | Core Loop | Best For |
|----------|-----------|----------|
| **Controidvania** | Traverse → gate → ability → unlock | High-mobility protagonists, spatial worlds, power escalation |
| **Top-Down Tactician** | Explore → manage → interact → quest | Complex economies, faction systems, NPC-driven worlds |
| **Turn-Based Strategist** | Plan → execute → adapt → progress | Relationship-heavy narratives, methodical pacing, party dynamics |
| **Arena Brawler** | Enter → survive → escalate → master | Combat-dense stories, short sessions, skill expression |

### Selection Rules
1. **Primary skeleton**: The one whose core loop most directly mirrors how the story generates tension.
2. **Secondary skeleton** (optional): Only recommend a blend if the narrative has a genuine dual structure (e.g., exploration world + tactical combat). Do not blend just for variety.
3. If recommending a blend: specify Host vs. Guest explicitly and explain which skeleton owns the macro state.

**Output format:**
```
SKELETON RECOMMENDATION
───────────────────────
Primary:   [Skeleton name]
Reasoning: [1–2 sentences]

Blend:     [Yes/No]
If yes →
  Host:    [Skeleton]
  Guest:   [Skeleton]
  Trigger: [What narrative moment switches context?]
  Rationale: [Why this blend serves the story]
```

---

## STEP 5: Contract Generation (Optional)

Only proceed to contracts when the user explicitly requests them, or when they've confirmed
the skeleton recommendation in Step 4.

When generating contracts, start with the two that are always required:

**Contract 1 — Input Contract** (what the user/system feeds in)
**Contract 2 — Interpretation Layer** (Step 3 output, formalized)

Then generate additional contracts based on skeleton type:
- Controidvania: add World Graph + Ability System + Player Controller
- Top-Down Tactician: add World Graph + NPC/Quest + Inventory
- Turn-Based Strategist: add Party System + Combat Matrix + Progression
- Arena Brawler: add Enemy Waves + Boss Contract + Frame Data

See `references/contracts.md` for full JSON schemas for each contract type.

**Validation reminder**: Before handing contracts to the runtime, flag any of these:
- Theme has no mechanical expression (purely aesthetic)
- Conflict model doesn't map to any progression gate
- Pacing signature contradicts chosen skeleton's natural rhythm

---

## Handling Edge Cases

**Overly sparse input** ("make a game about revenge"):
→ Extract what you can, mark the rest `[inferred]`, present the profile, and ask one clarifying question before proceeding to skeleton selection.

**Overly rich input** (full novel synopsis):
→ Compress to 2–3 sentences first. Extract from the compression, not the raw text. This prevents mechanical noise from subplot details.

**IP reference** ("something like Bloodborne"):
→ Extract structural patterns from the IP (not aesthetics), then apply to the user's actual input. Do not design a clone.

**Conflicting signals** (gritty tone + fantastical world):
→ Flag the tension. Ask which axis is dominant before resolving.

---

## Output Style

- Always show your work — each step should be visible, not collapsed into a final answer
- Use the structured output formats defined above (NARRATIVE PROFILE, SKELETON RECOMMENDATION)
- Keep reasoning tight: 1–2 sentences per decision, not paragraphs
- If a step produces no actionable output, say so and explain why before continuing
