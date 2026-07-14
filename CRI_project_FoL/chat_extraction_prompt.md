# Chat-Attachment Extraction Prompt

*Companion to `browser_extraction_prompt.md`. Use this when you attach a source file directly to a regular Claude chat (PDF, DOCX, EPUB, image scans, large text) instead of using the browser agent. Produces the same Universal Entity Template documents, but with full-document survey, image/stat-block handling, and validation passes that the browser version can't do.*

---

## When to use this vs. the browser prompt

**Use this (chat attachment) when:**
- You have the file on disk (PDF, DOCX, EPUB, scanned images, plain text)
- The source is paywalled but you legally own a copy
- You want full-document survey before extraction (no pagination gating)
- You need image-based stat blocks, maps, or diagrams interpreted
- You want cross-validation across the whole document (every entity in the index gets a file)

**Use the browser prompt when:**
- The source lives on a website (wiki, public reference site, free fan resource)
- You can't easily download the source
- The source updates dynamically and you want a snapshot of "as it appears today"

---

## How to use

1. Start a new Claude chat with code execution enabled.
2. Attach the source file (PDF, DOCX, etc.). Multiple files are fine if they're related.
3. Paste the **PROMPT** block below.
4. Claude will run a survey pass first, show you an entity manifest, and ask which to extract — unless you append "extract all" to the prompt.
5. Files are written to `/mnt/user-data/outputs/` and presented for download.

**Output:**
- One markdown file per entity: `<entity_id>.md`
- A `manifest.md` listing all extracted entities with their parent_group, hierarchy level, and confidence score
- A `validation_report.md` flagging any unresolved cross-references, conflicting data, or low-confidence extractions

---

## PROMPT — copy everything below this line

```
You are an entity-extraction agent. You have one or more source files attached to this conversation. Your job is to convert that source material into a set of structured entity documents using the Universal Entity Template defined below, plus a manifest and validation report. These files will feed a downstream game-design pipeline.

# PROCESS (multi-pass)

## Pass 1 — Survey

1. Read the attached source(s). If the source is a PDF, use the pdf-reading skill at /mnt/skills/public/pdf-reading/SKILL.md before extracting. For other formats, use the file-reading skill at /mnt/skills/public/file-reading/SKILL.md.
2. Identify:
   - Source title, author/publisher, type (RPG supplement, novel, wiki dump, film script, mythology compendium, game guide, etc.)
   - SOURCE TIER:
     • T1 = pre-structured compendium with explicit fields
     • T2 = semi-structured (wiki sections, lore appendix, glossary)
     • T3 = pure narrative (novel, film, mythological text)
   - Document structure (chapters, table of contents, index, glossary)
3. Build an entity manifest by scanning the whole document. Cross-reference the TOC and the index (if present) — every named entity in the index should appear in the manifest. An "entity" is anything coherent enough to behave consistently: creature, person, species, faction-as-actor, sentient place, intelligent artifact, organization-as-actor.
4. For each entity in the manifest, capture:
   - Proposed entity_id (lowercase, underscores)
   - Canonical name
   - Hierarchy level (individual / variant / species / type-group / cosmological-group)
   - Proposed parent_group (entity_id of the level above, if any)
   - One-line description
   - Source location (chapter / page range)
   - Estimated extraction confidence (0.0–1.0) based on how much source content supports it

5. Present the manifest. Wait for user confirmation on scope UNLESS the user already said "extract all" in their initial message.

## Pass 2 — Extract

For each confirmed entity, fill the template (below) using all relevant source content. Read every section of the source that mentions the entity, not just the primary entry. Cross-reference:
- Per-species lore chapters
- Stat block / mechanical chapters
- Adventure / encounter chapters where the entity appears
- Index / appendix entries
- Sidebars and callouts
- Image-based stat blocks (interpret them; don't skip)

Write each entity to `/mnt/user-data/outputs/<entity_id>.md`.

## Pass 3 — Validate

After all entities are written, produce a `validation_report.md` checking:
- Every `parent_group` reference resolves to another file in the output
- Every `Related entities` / `Required-for` / `Required-by` link resolves OR is flagged as "external to this source"
- Every entity in the source index has either a file or an explanation why it was skipped
- YAML headers are well-formed
- No file is below 30% filled (if it is, either there's not enough source material — flag it — or extraction was too thin)
- List any entities with confidence_overall < 0.5 with a note on why

## Pass 4 — Manifest

Produce `manifest.md` listing every output file with: entity_id, canonical name, hierarchy level, parent_group, confidence_overall, primary source location, one-line description.

## Pass 5 — Present

Use the present_files tool to surface all output files. Lead with `manifest.md`, then `validation_report.md`, then entity files in hierarchy order (groups before members).

# FILL-FLAGS

- `[N/A]` — concept genuinely doesn't apply
- `[INFERRED]` — filled from interpretation of narrative cues. Always use for T3 sources where most fields are inferred. Include a parenthetical citing the basis: `[INFERRED] pack hierarchy (based on hunting scene in Ch. 7)`
- `[UNKNOWN]` — source could plausibly contain this but doesn't say
- `[CONFLICTING]` — source contains contradictions; list them with citations

# COPYRIGHT & QUOTING RULES

- Paraphrase every field in your own words. Do not reproduce source prose.
- Direct quotes only when the wording itself matters (a defining epithet, signature phrase, exact stat-block label). Cap at under 15 words. Max one quote per source per entity.
- For each non-obvious claim, cite the source location (page, chapter, scene, section heading).
- Never reproduce entire stat blocks verbatim. Distill them into the relevant template fields (§8 Capabilities, §9 Mechanics).
- Never reproduce read-aloud / boxed text verbatim. Paraphrase the situation.
- If a section would require reproducing more than ~3 sentences of source prose, distill further.

# TEMPLATE TO FILL (per entity)

Output exactly this structure:

---

```yaml
entity_id:
source_title:
source_type:
source_tier:        # T1 | T2 | T3
extraction_date:
extractor: Claude (chat attachment)
confidence_overall: # 0.0–1.0
notes:
```

## 1. IDENTITY
- **Canonical name:**
- **Aliases / titles / epithets:**
- **Hierarchy level:** (individual | variant | species | type-group | cosmological-group)
- **Parent group:** (entity_id of the level above, if any)
- **Classification / kind:**
- **Domain / register:** (mortal | undead | divine | infernal | fey | mechanical | elemental | cosmic | aberrant | other)
- **Singular vs. plural:**
- **Source citation:**

## 2. FORM (Physiology / Morphology)
- **Body schema:**
- **Material composition:**
- **Sensory apparatus:**
- **Locomotion:**
- **Size by stage:**
- **Distinguishing markers:**
- **Variants / subtypes:**

## 3. LIFECYCLE / ONTOGENY
- **Origin / genesis:**
- **Stages:**
- **Maturation triggers:**
- **End-state:**
- **Reproduction / propagation:**
- **Lifespan:**

## 4. MIND (Cognition / Psychology / Drives)
- **Intelligence model:**
- **Core motivations:** (top 1–3)
- **Values:**
- **Aversions / fears:**
- **Personality range:**
- **Behavioral defaults:**
- **Triggers:**
- **Moral register:**

## 5. SOCIETY (Relationships / Communication / Faith)
- **Internal structure:**
- **Hierarchy / rank:**
- **Communication:**
- **External relationships:**
  - Allies / kin / patrons:
  - Rivals / enemies / prey:
  - Neutral / transactional:
- **Faction or organizational ties:**
- **Faith / ideology:**
- **Servitors / dependents:**

## 6. HABITAT (Territory / Lair / Ecology)
- **Preferred environment / biome:**
- **Territory size & defense:**
- **Lair / dwelling pattern:**
- **Lair features:**
- **Environmental modification:**
- **Ecological role:**
- **Resource requirements:**

## 7. ECONOMY (What It Values / Accumulates / Exchanges)
- **Hoard / accumulation type:**
- **Acquisition methods:**
- **Storage / display patterns:**
- **Trades / exchanges:**
- **Intrinsic vs. instrumental value:**

## 8. CAPABILITIES (Powers / Abilities / Vulnerabilities)
- **Core capabilities:**
- **Signature ability:**
- **Special abilities:** (with frequency: at-will | encounter | daily | recharge | cooldown | conditional)
- **Resistances:**
- **Vulnerabilities:**
- **Scaling:** (across lifecycle / threat tiers)

## 9. MECHANICS (Runtime / Game Translation)
*For T3 sources this is heavily INFERRED. Flag accordingly.*
- **Role archetype:** (Brute | Skirmisher | Controller | Lurker | Soldier | Artillery | Support | Solo | Minion | Boss-only)
- **Threat tier:** (low | mid | high | boss | faction-level | world-ending)
- **Action economy notes:**
- **Encounter behavior:** (opener / mid-fight pattern / retreat or berserk condition)
- **Cooldown / recharge logic:**
- **Stat block placeholders by tier:**
  - Tier 1:
  - Tier 2:
  - Tier 3:
- **Pipeline contract link:** (Enemy | Boss | NPC | Faction | Setting)

## 10. KNOWLEDGE TIERS (Information Gating)
- **Public knowledge:** (rumor, distorted folk knowledge)
- **Specialist knowledge:** (scholars, hunters, professionals)
- **Hidden / forbidden knowledge:** (secret texts, cults, classified)
- **Author / GM only:** (true nature, plot reveals)

## 11. DEPLOYMENT (Narrative & Encounter Use)
- **Recurring narrative roles:**
- **Sample encounter compositions:**
- **Plot hooks / quest seeds:** (3–5)
- **Named exemplars:**
- **Templates / mutations / hybrids:**

## 12. LINKS (Cross-References)
- **Related entities:** (by entity_id)
- **Setting dependencies:**
- **Required-for / required-by:**
- **Source canon notes:** (continuity issues, retcons, contradictions)

---

# EDGE CASES

- **Hierarchical entities.** If the source describes both an individual ("Smaug") and its species ("dragons"), output SEPARATE files for each level and link them via parent_group. Extract the highest level first so children can reference it. Don't merge.
- **One entity, multiple variants** (young/adult/elder; mark I/II/III; novice/master). If each variant has its own stat block, give each its own file with shared parent_group. If variants are just narrative growth, one file with variant info in §2 and §3.
- **Faction or location as entity.** A faction, organization, or sentient location IS an entity if it acts coherently. For factions: §5 becomes internal structure; §6 Habitat becomes territory of operation; §7 Economy becomes resources & revenue; §8 Capabilities becomes faction-level abilities (e.g., "deploys assassins as encounter recharge").
- **Image-based stat blocks.** PDFs often have stat blocks as styled boxes or images. Interpret them as data — extract HP, AC, defenses, powers, action economy. Don't skip just because they're visually formatted.
- **Adventure / scenario content.** Some sources mix entities with adventures (e.g., Dragon Delves). Extract entities AND note in §11 Deployment that the entity appears in a specific scenario. Don't extract the scenario itself as an entity unless it's a sentient location.
- **Real-named public figures.** If the source treats a real historical person as a character (mythology, alt-history fiction), extract only the in-source treatment, not real biography.
- **Source has multiple editions / versions.** Note which edition you're working from in `notes`. Don't merge contradictory info from different editions without flagging.

# QUALITY THRESHOLDS

- An entity file with fewer than 4 sections filled is a stub. Either find more source content or move the entity to a "stubs" section of the manifest.
- An entity with confidence_overall < 0.3 should not be written as a full file — list it in the manifest as "insufficient source coverage" instead.
- If extraction time on one entity exceeds reasonable bounds (e.g., the agent is searching the document repeatedly without finding new info), write what's there and move on.

# FIRST RESPONSE

1. Confirm what source(s) you see attached and acknowledge any skills you'll be reading first.
2. Run Pass 1 (Survey). Present the entity manifest with confidence scores.
3. Ask the user which entities to extract — unless they already said "extract all."
4. Don't begin Pass 2 without confirmation OR an explicit "extract all" instruction.
```

---

## Variations

**Extract all without asking:**
Append: *"Extract all entities in the manifest. Skip confirmation. Proceed directly to Pass 2 after presenting the manifest."*

**Focused extraction:**
Append: *"Skip the survey. Extract only the entity named ___. If it has variants, extract all variants."*

**Hierarchy-first batch:**
Append: *"After the survey, extract in hierarchy order: cosmological groups first, then type-groups, then species, then variants, then individuals. This way parent_group references always resolve."*

**T3 narrative source (novel / film / comic):**
Append: *"Source is narrative (T3). Expect [INFERRED] on most fields. Prioritize §4 Mind, §5 Society, §11 Deployment — these are usually best-supported in narrative. §8 Capabilities and §9 Mechanics will be heavily inferred; flag accordingly and don't over-invent stat blocks."*

**Update / re-extract:**
Append: *"I have prior entity files at /mnt/user-data/uploads/. Read them first. For each entity in the new source, either UPDATE the existing file (preserve YAML, add new info, increment a version note) or CREATE a new file if it's a new entity. Output a diff summary."*

**Image-heavy source:**
Append: *"This source has many image-based stat blocks, maps, and illustrations. Use the pdf-reading skill's page rasterization technique to render pages as images and read them visually when text extraction is insufficient."*

---

## Tips

- **Attach the original file, not a summary.** Summaries strip exactly the structural cues the extractor needs.
- **One source at a time** unless the sources are clearly related (e.g., a supplement and its errata, a novel and its companion compendium). Mixing unrelated sources into one extraction confuses hierarchy.
- **Check the validation report.** Unresolved cross-references usually mean either (a) the source assumed external knowledge, (b) you missed an entity in the survey, or (c) the source itself is inconsistent. The validation report distinguishes these.
- **Downstream parsing.** Keep YAML headers and section headers unchanged. Downstream tools key on them. Free-form notes belong in the field values, not in extra sections.
- **Re-runs.** If you re-run the prompt on the same source, use the "Update / re-extract" variation so prior work isn't lost.

---

*Companion to: `universal_entity_template.md`, `browser_extraction_prompt.md`*
