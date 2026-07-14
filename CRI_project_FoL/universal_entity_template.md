# Universal Entity Extraction Template

*Source-agnostic. Compendium-architecture. For extracting structured data from any source material — RPG supplements, novels, films, comics, games, mythology — to feed into game-design pipelines.*

**Status:** DRAFT v0.1 — for review before finalization
**Generalized from:** *Draconomicon: Chromatic Dragons* (D&D 4e) five-layer pattern, with universal-entity abstractions.
**Designed to fit:** humans, creatures, slimes, robots, demons, vampire slayers, chthonians, gods, AIs, factions-as-entities, sentient locations, intelligent artifacts.

---

## How to Use

This template extracts a single **entity** from any source. An entity is anything coherent enough to behave consistently — a person, a species, a faction treated as a single actor, a place that acts on the world, a thinking object.

**Fill what the source supports.** For unsupported fields, use the flags:
- `[N/A]` — concept genuinely doesn't apply (e.g., "reproduction" for a unique cosmic horror)
- `[INFERRED]` — you filled it from interpretation, not direct source
- `[UNKNOWN]` — source could support it but doesn't say
- `[CONFLICTING]` — source contains contradictions; note them

**Source tier** (declared in §0):
- **T1 — Pre-structured.** Compendium with explicit fields (Draconomicon, Pokédex, Bestiary, game wiki). Direct copy.
- **T2 — Semi-structured.** Source has scattered structured info (Tolkien appendices, Dune glossary, mythology with scholarship). Mixed copy + inference.
- **T3 — Narrative.** Novels, films, comics, oral myth. Full inference; the template acts as an interview framework against the source.

**Hierarchy.** A single filled template represents ONE level of a chain:
`individual → variant → species → type-group → cosmological-group`
e.g., Smaug → Ancient Red Dragon → Red Dragon → Chromatic Dragons → Dragons. Use §1 to declare the level and link to the parent.

---

## 0. META

```yaml
entity_id:           # short slug, e.g., red_dragon, chthonian, slayer_buffy, hal_9000
source_title:        # canonical source
source_type:         # compendium | wiki | novel | film | comic | game | mythology | other
source_tier:         # T1 | T2 | T3
extraction_date:
extractor:
confidence_overall:  # 0.0–1.0
notes:
```

---

## 1. IDENTITY

- **Canonical name:**
- **Aliases / titles / epithets:**
- **Hierarchy level:** *(individual | variant | species | type-group | cosmological-group)*
- **Parent group:** *(entity_id of the level above, if any)*
- **Classification / kind:** *(species, race, type, model, order — the category this fits)*
- **Domain / register:** *(mortal | undead | divine | infernal | fey | mechanical | elemental | cosmic | aberrant | other)*
- **Singular vs. plural:** *(is this a one-of-a-kind, or one example of many?)*
- **Source citation:** *(page, chapter, scene, episode)*

---

## 2. FORM (Physiology / Morphology)

- **Body schema:** *(humanoid, serpentine, amorphous, polyhedral, swarm, energy-pattern, mechanical, distributed, hybrid…)*
- **Material composition:** *(flesh, metal, ichor, slime, plasma, stone, ectoplasm, code, dream-substance…)*
- **Sensory apparatus:** *(sight, scent, sonar, telepathy, electromagnetic, soul-sight, none…)*
- **Locomotion:** *(walk, flight, burrow, swim, teleport, phase, network-jump, possession…)*
- **Size by stage:** *(only if size varies across lifecycle)*
- **Distinguishing markers:** *(scars, signature features, anomalies)*
- **Variants / subtypes:** *(known sub-categories — chromatic colors, model numbers, breeds, sects, generations)*

---

## 3. LIFECYCLE / ONTOGENY

- **Origin / genesis:** *(birth, manufacture, summoning, emergence, infection, spontaneous, divine fiat, narrative inevitability)*
- **Stages:** *(named progression — wyrmling/young/adult/elder/ancient, or initiate/journeyman/master, or prototype/release/legacy)*
- **Maturation triggers:** *(time, kills, ritual, trauma, ascension, software update, planar event)*
- **End-state:** *(death, transcendence, dissolution, reset, reincarnation, sealing)*
- **Reproduction / propagation:** *(sexual, asexual, viral, ritual, manufactured, narrative-only, none)*
- **Lifespan:** *(or "indefinite", "until task complete", "until killed by named agent")*

---

## 4. MIND (Cognition / Psychology / Drives)

- **Intelligence model:** *(animal, sapient, hive, distributed, divine, alien, emergent, none)*
- **Core motivations:** *(top 1–3 drives — survival, dominion, knowledge, vengeance, hunger, duty, beauty)*
- **Values:** *(what it protects, exalts, considers sacred)*
- **Aversions / fears:** *(what it flees, despises, refuses)*
- **Personality range:** *(individual variation across the kind: from ___ to ___)*
- **Behavioral defaults:** *(what it does when nothing is provoking it)*
- **Triggers:** *(stimuli that switch its behavior — territory invasion, blood, prayer, loud noise, true name spoken)*
- **Moral register:** *(if applicable — alignment, ethical stance, code, "beyond morality")*

---

## 5. SOCIETY (Relationships / Communication / Faith)

- **Internal structure:** *(solitary, pair-bonded, pack, hive, hierarchy, network, faction, atomic)*
- **Hierarchy / rank:** *(name the ranks if any)*
- **Communication:** *(spoken language, pheromone, song, telepathy, code, gesture, none)*
- **External relationships:**
  - Allies / kin / patrons:
  - Rivals / enemies / prey:
  - Neutral / transactional:
- **Faction or organizational ties:** *(orders, cults, cabals, guilds, governments, gods served)*
- **Faith / ideology:** *(deities, doctrines, rituals — or its absence)*
- **Servitors / dependents:** *(minions, familiars, summoned beings, supporting cast)*

---

## 6. HABITAT (Territory / Lair / Ecology)

- **Preferred environment / biome:**
- **Territory size & defense:**
- **Lair / dwelling pattern:** *(natural cave, constructed fortress, dimension, body of host, distributed network, none)*
- **Lair features:** *(traps, hazards, signature elements, internal layout type)*
- **Environmental modification:** *(how its presence alters surroundings — corruption aura, frost halo, terraforming, decay, signal interference)*
- **Ecological role:** *(apex predator, parasite, decomposer, keystone, invader, alien-to-ecosystem)*
- **Resource requirements:** *(food, fuel, sacrifice, belief, electricity, light, darkness, attention)*

---

## 7. ECONOMY (What It Values / Accumulates / Exchanges)

- **Hoard / accumulation type:** *(gold, knowledge, souls, data, biomass, memories, weapons, prisoners, names, none)*
- **Acquisition methods:** *(theft, conquest, gift, trade, harvest, manufacture, summoning)*
- **Storage / display patterns:** *(piled vs. distributed, hidden vs. flaunted)*
- **Trades / exchanges:** *(what it'll give up, for what)*
- **Intrinsic vs. instrumental value:** *(what it loves for its own sake vs. as a means)*

---

## 8. CAPABILITIES (Powers / Abilities / Vulnerabilities)

- **Core capabilities:** *(what it can always do)*
- **Signature ability:** *(the one defining power — breath weapon, dream-walking, possession, perfect mimicry, hacking)*
- **Special abilities:** *(list with frequency: at-will, encounter, daily, recharge, cooldown, conditional)*
- **Resistances:** *(damage types, conditions, magical effects)*
- **Vulnerabilities:** *(damage types, materials, conditions — silver, iron, sunlight, true name, sacred song)*
- **Scaling:** *(how capabilities shift across lifecycle stages or threat tiers)*

---

## 9. MECHANICS (Runtime / Game Translation)

*Use this layer to translate the entity into game-runtime behavior. Heavily inferred for T3 sources.*

- **Role archetype:** *(Brute | Skirmisher | Controller | Lurker | Soldier | Artillery | Support | Solo | Minion | Boss-only)*
- **Threat tier:** *(low | mid | high | boss | faction-level | world-ending)*
- **Action economy notes:** *(single-target, multi-target, AoE, actions per turn)*
- **Encounter behavior:** *(opener, mid-fight pattern, retreat / berserk condition)*
- **Cooldown / recharge logic:** *(rhythms of its big moves)*
- **Stat block placeholders by tier:**
  - Tier 1 (e.g., young / weak / common): ___
  - Tier 2 (e.g., adult / standard): ___
  - Tier 3 (e.g., elder / ancient / unique): ___
- **Pipeline contract link:** *(which downstream game-engine contract this feeds — Enemy Contract, Boss Contract, NPC Contract, Faction Contract)*

---

## 10. KNOWLEDGE TIERS (Information Gating)

*Replaces D&D's DC-gated lore. Defines what different observers know about this entity. Useful for in-game reveals, NPC dialogue, lore drops, mystery design.*

- **Public knowledge:** *(what commoners, kids, ambient rumor say — often distorted)*
- **Specialist knowledge:** *(what scholars, hunters, professionals, veterans know)*
- **Hidden / forbidden knowledge:** *(secret texts, cults, classified files, ancient orders)*
- **Author / GM only:** *(the true nature, retconnable secrets, plot reveals)*

---

## 11. DEPLOYMENT (Narrative & Encounter Use)

- **Recurring narrative roles:** *(patron, antagonist, ally, mystery, obstacle, mentor, monster-of-the-week, final boss, twist reveal)*
- **Sample encounter compositions:** *(typical group sizes, allied entity types, terrain pairings)*
- **Plot hooks / quest seeds:** *(3–5 ways this entity drives a story)*
- **Named exemplars:** *(legendary or unique individuals of this kind — Smaug, Dracula, HAL 9000, Buffy Summers)*
- **Templates / mutations / hybrids:** *(how this kind crossbreeds, mutates, or hybridizes with others)*

---

## 12. LINKS (Cross-References)

- **Related entities:** *(parents, predators, prey, kin, rivals — link by entity_id)*
- **Setting dependencies:** *(cosmologies, magic systems, tech bases that must exist for this entity to exist)*
- **Required-for / required-by:** *(what other entities depend on this one, and vice versa)*
- **Source canon notes:** *(continuity issues, contradictions across the source, retcons, expanded-universe additions)*

---

## Field Mapping Reference

How this template covers the Draconomicon's chapter contents:

| Draconomicon concept | This template's section |
|---|---|
| Physiology | §2 Form |
| Psychology / Outlook | §4 Mind |
| Life Cycle | §3 Lifecycle |
| Combat Tactics | §8 Capabilities + §9 Mechanics |
| Lair Construction | §6 Habitat |
| Ecology | §6 Habitat |
| Servitor Systems | §5 Society |
| Treasure Logic | §7 Economy |
| Encounter Design | §9 Mechanics + §11 Deployment |
| World Integration | §6 Habitat + §12 Links |
| Campaign Hooks | §11 Deployment |
| Societies | §5 Society |
| Behavioral Patterns | §4 Mind |
| Environmental Interaction | §6 Habitat |
| Monster Variants | §2 Form + §11 Deployment |
| Hierarchical Systems | §1 Identity (level) + §3 Lifecycle |
| Progression Scaling | §3 Lifecycle + §8 Capabilities + §9 Mechanics |
| Origins | §3 Lifecycle |
| Language | §5 Society |
| Religion | §5 Society |

---

*End of template draft.*
