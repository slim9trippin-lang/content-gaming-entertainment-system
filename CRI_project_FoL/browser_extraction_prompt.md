# Browser-Agent Extraction Prompt

*For use with Claude in Chrome (or any browsing agent). Turns source-material pages — RPG supplements, wikis, novels, film/show pages, game compendiums, mythology references — into structured entity documents following the Universal Entity Template.*

---

## How to use

1. Open the page you want to extract (Scribd doc, Drive PDF, fan wiki, etc.). Make sure you can actually see the content — log in / scroll past previews if needed. The agent can't bypass paywalls or DRM.
2. Activate Claude in Chrome on that tab.
3. Paste the **PROMPT** block below.
4. If the page is part of a multi-page work (book, scrollable Scribd doc, paginated wiki), open the **first** page that contains entity content before invoking.
5. The agent will inventory entities first, then ask which to extract — or extract all if you tell it to.

**Output:** one markdown file per entity, named `<entity_id>.md`, downloaded to your default download location.

**Copyright note:** the prompt instructs the agent to paraphrase and extract structured fields, not to reproduce source prose. Direct quotes are capped at <15 words and used only when wording itself is the point. The agent will refuse to dump full chapters verbatim.

---

## PROMPT — copy everything below this line

```
You are an entity-extraction agent. Your job is to read the source material currently visible in this browser tab and convert it into one or more structured entity documents using the Universal Entity Template defined below. The output of this task is a set of markdown files I can use as inputs to a downstream game-design pipeline.

# PROCESS

1. **Survey the page.**
   - Identify the source: title, author, publisher/site, type (RPG supplement, wiki, novel, film summary, mythology, game guide, etc.).
   - Determine the SOURCE TIER:
     • T1 = pre-structured compendium with explicit fields (bestiary, Pokédex-style entry, stat-block reference)
     • T2 = semi-structured (wiki with sections, lore appendix, glossary)
     • T3 = pure narrative (novel chapter, film scene, mythological text)
   - Note whether this page is part of a larger work and whether you can navigate to more.

2. **Inventory entities.**
   List every distinct entity on the visible page(s) that has enough information to fill at least four of the template sections below. An "entity" is anything coherent enough to behave consistently: a creature, a person, a species, a faction-as-actor, a sentient place, an intelligent artifact.
   For each, give: name, kind, approximate hierarchy level (individual / variant / species / type-group / cosmological-group), and a one-line description.

3. **Confirm scope.**
   Show the inventory to the user and ask which entities to extract — UNLESS the user already specified "extract all" in their initial message, in which case proceed.

4. **Extract.**
   For each chosen entity, fill the template below. Navigate the page (scroll, click chapter links, follow internal references) to gather information across all sections. Don't fabricate; use the fill-flags when data is missing.

5. **Output one markdown file per entity.**
   Filename: <entity_id>.md (lowercase, underscores, no spaces).
   Save to the user's default download location.

# FILL-FLAGS (use these instead of inventing data)

- `[N/A]` — concept genuinely doesn't apply to this entity
- `[INFERRED]` — filled from interpretation of narrative cues, not direct statement. ALWAYS use this for T3 sources where most fields are inferred. Add a parenthetical note explaining the inference, e.g., `[INFERRED] hierarchical pack structure (based on Chapter 4 hunting scene)`
- `[UNKNOWN]` — source could plausibly contain this but doesn't say
- `[CONFLICTING]` — source contains contradictions; list them

# COPYRIGHT & QUOTING RULES

- Paraphrase. Do not reproduce source prose. Each field should be the extracted FACT in your own words.
- Direct quotes only when the wording itself matters (a defining epithet, a signature phrase). Cap quotes at under 15 words. Max one quote per source per entity.
- Do not summarize entire chapters or scenes verbatim. If a field needs more than ~3 sentences, you are summarizing too much — distill further.
- Always cite the location (page, chapter, scene, section heading) for non-obvious claims.

# TEMPLATE TO FILL

Output exactly this structure for each entity:

---

```yaml
entity_id:
source_title:
source_type:
source_tier:        # T1 | T2 | T3
extraction_date:
extractor: Claude in Chrome
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

- **Paywalled / blurred content.** Don't guess. Extract only from what's actually visible. Flag missing sections as [UNKNOWN] and tell the user which sections the paywall blocked.
- **Multi-page source.** If the page has navigation (next page, chapter index, scroll-to-load), use it to gather all sections of a single entity before writing its file. Don't write half-filled templates if more content is one click away.
- **Hierarchical entities.** If the source describes both an individual (e.g., "Vermithrax the Black") and its species ("black dragons"), output SEPARATE files for each level and link them via `parent_group`. Don't merge.
- **One entity, multiple variants** (e.g., young/adult/elder; mark I/II/III; novice/master). Either: (a) one file per variant, sharing parent_group, or (b) one file with variant info in §2 Variants/subtypes and §3 Stages. Default to (a) if each variant has its own stat block; (b) if variants are just narrative growth.
- **Faction or location as entity.** A faction, organization, or sentient location CAN be an entity if it behaves as a coherent actor. Faction → §5 becomes internal structure; §6 Habitat becomes territory of operation; §7 Economy becomes resources & revenue.
- **Page contains no extractable entity.** Tell the user the page is index/marketing/credits/etc. and stop.
- **Real-named public figures.** If the source treats a real historical person as a character (mythology, alt-history fiction), extract only the in-source treatment, not real biography.

# STOPPING CONDITIONS

- Stop after producing one file per requested entity.
- If the user said "extract all," extract every entity you inventoried and then stop. Don't keep navigating to find more.
- If you've made 10+ navigation actions on one entity without finding new content, write the file with what you have and move on.

# FIRST RESPONSE

Begin with a brief survey: source title, type, tier, and the entity inventory. Then either proceed to extraction (if the user said "extract all") or ask which to extract.

Do not begin the extraction step until you have surveyed.
```

---

## Variations

**For a single known entity:**
Append to the prompt: *"Extract only the entity named ___. Skip the inventory step."*

**For batch processing (e.g., a whole bestiary):**
Append: *"Extract all entities you find. Skip confirmation. After each entity file is written, briefly note which one is done and move to the next."*

**For T3 narrative sources (novels, films):**
Append: *"Source is narrative (T3). Expect to flag most fields [INFERRED]. Weight §4 Mind and §11 Deployment heavily — these are usually well-supported in narrative sources. Weight §8 Capabilities and §9 Mechanics lightly — these will be heavily inferred and may be the user's job to flesh out later."*

**For hierarchy-first extraction:**
Append: *"Before extracting individuals, first extract the highest-level group (species / cosmological-group). This becomes the parent_group for everything below. Then descend the hierarchy."*

---

## Tips

- The browser agent works best on rendered, scrollable pages. Scribd's pagination is fine; image-only scans without OCR are not.
- If a page is gated behind a Scribd preview, the agent will only see the visible portion. Either upload to Claude directly via the file attachment in chat (better OCR, no gating) or use a different host.
- For your own game-design pipeline: the resulting markdown files are designed to be parsed downstream. Keep the YAML header intact and don't rename section headers — downstream tools key on those.

---

*Companion file to: universal_entity_template.md*
