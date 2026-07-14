---
name: genre-skeleton-mapper
description: Translate source material (books, comics, manga, novels, films, TV, established fiction) into specific game-mechanical decisions by mapping it onto a fixed library of seven genre skeletons. Use this whenever the user wants to know what kind of game a piece of fiction would make, how a story's elements translate into gameplay systems, or how to design a game inspired by an existing work. Triggers include "what kind of game would [book/comic/manga] make", "map [series] onto [genre]", "design a game based on [source]", "what would a [Sister Souljah / Spider-Man / Dune / John Wick] game look like", "translate [story] into a Metroidvania/RPG/fighter/etc.", and any request to convert narrative DNA into mechanical decisions. Covers Kinesthetic Explorer (Controidvania), Top-Down Tactician (action-RPG), Turn-Based Strategist (JRPG/tactics), Arena Brawler, Roguelike, Survival Horror, and Stealth. Resists creative drift by forcing source onto established skeletons rather than inventing new genres.
---

# Genre Skeleton Mapper

The job here is mechanical translation, not creative invention. A user shows up with a book, comic, manga, film, or series and wants to know what kind of game it would be. The instinct is to invent a custom genre that perfectly matches their source. Resist that. The whole point of skeletons is that they're proven, finite, and tractable — every novel mechanic the AI invents is a system that nobody has ever built and nobody knows how to balance. Mapping onto an existing skeleton means the gameplay math is already solved; only the *theming* has to be invented.

This skill exists for one specific kind of question: "this story's DNA — which skeleton receives it cleanly, and how does the translation actually work?"

## The seven skeletons

| Skeleton                | Vibe                                          | Core question it answers                          |
|-------------------------|-----------------------------------------------|---------------------------------------------------|
| Kinesthetic Explorer    | Side-scrolling traversal, ability-gating      | "How does this protagonist move through space?"   |
| Top-Down Tactician      | Omnidirectional, dungeons, stat progression   | "What's this protagonist's economy of power?"     |
| Turn-Based Strategist   | Methodical pacing, party synergy, math combat | "Who's in this protagonist's circle, and how?"    |
| Arena Brawler           | Combo combat, wave survival                   | "What does this protagonist's violence feel like?"|
| Roguelike               | Run-based, procedural, meta-progression       | "What does this protagonist learn between deaths?"|
| Survival Horror         | Resource scarcity, vulnerability, dread       | "What does this protagonist *avoid*?"             |
| Stealth                 | Detection systems, non-combat resolution      | "Who is this protagonist hiding from?"            |

Each skeleton has a reference file in `references/`. Read the matching file before writing a full breakdown — the file contains the specific mechanical DNA (movement model, combat model, progression structure, what blends well, what strains).

## Two workflows

### Workflow A — User specifies source + skeleton

This is the common case. "Map Sister Souljah's *The Coldest Winter Ever* onto the Top-Down Tactician." Or "What would a Spider-Man Controidvania look like?"

Steps:
1. **Identify the source's mechanical DNA.** Not its plot — its mechanical DNA. What does the protagonist *do*? How do they win? What's their relationship to space, to violence, to other characters? Pull these out as discrete elements before mapping.
2. **Read the matching skeleton's reference file.** Use the `view` tool on `references/[skeleton].md`.
3. **Produce both outputs** (the user asked for both):
   - **Translation table** — narrative elements in the left column, the mechanic each becomes in the right column. Short, scannable.
   - **Full breakdown** — apply the skeleton's reference structure (movement model, combat model, progression, world structure, etc.) with the source's elements filled in.
4. **Note the strain points.** If something in the source doesn't fit the skeleton cleanly, say so. Don't paper over it. The user benefits more from "the betrayal subplot doesn't translate to combo combat — you'd lose it" than from a forced metaphor.

### Workflow B — User specifies source, asks for recommendation

"What kind of game would *Dune* make?" The user hasn't picked a skeleton; they want guidance.

Steps:
1. Identify mechanical DNA as above.
2. **Score the source against multiple skeletons** — at least three, ideally four. For each, give a one-sentence verdict: clean fit / strained fit / wrong tool. Be honest. Most sources fit two or three skeletons; almost none fit all seven.
3. Recommend the strongest 1–2 fits with one-line reasoning each.
4. Offer to do the full Workflow A breakdown on the user's choice. Don't produce all seven full breakdowns unsolicited — that's wall-of-text territory and most of them won't matter.

## How to identify a source's mechanical DNA

This is the hard part. Most users will describe their source by *theme* ("it's about loyalty and survival in the streets") which is useless for mapping. You have to extract:

- **Protagonist's relationship to space** — do they traverse, hide, control, conquer, navigate? A Spider-Man story is *traversal*; a Dune story is *control*; a John Wick story is *navigation through hostile spaces*.
- **Protagonist's relationship to conflict** — do they outfight, outwit, outlast, outmaneuver, or outnumber opponents? Conan outfights. Sherlock outwits. Ripley (Alien) outlasts. Solid Snake outmaneuvers.
- **Protagonist's economy of power** — do they grow stronger via stats, abilities, allies, equipment, knowledge, or none? *Harry Potter* grows via knowledge + abilities; Sister Souljah's Winter grows via territory + crew; John Wick is *static* in power and the world adapts to him.
- **The story's pacing rhythm** — is it set-piece-driven (Bond, Indiana Jones), exploration-driven (Tolkien, Le Guin), conversation-driven (Sally Rooney, Sorkin), or attrition-driven (King, McCarthy)?
- **What goes wrong when the protagonist fails** — death, loss of resource, social damage, narrative regression? This determines whether the skeleton is roguelike-shaped, survival-shaped, or arcade-shaped.

These five questions usually narrow the field to two skeletons within a few sentences. If you can't answer them from the source, you don't know the source well enough to map it — say so and ask the user for help.

## Output structure for a full breakdown

When producing the full mapping (Workflow A step 3), follow this structure. Pull the headers from the skeleton's reference file, fill the values from the source.

```
## [Source] mapped to [Skeleton]

### The vibe
[One paragraph. What does this game feel like to play?]

### Translation table
| Source element        | Becomes mechanic                |
|-----------------------|---------------------------------|
| [protagonist trait]   | [movement/combat/progression]   |
| [recurring conflict]  | [system / level structure]      |
| [supporting cast]     | [party / NPC / wave / etc.]     |
| [setting]             | [world structure]               |
| [tone]                | [pacing / risk / aesthetic]     |

### Movement model
[How the protagonist moves, with specifics from the source. Not "they move fast" — "the protagonist's signature is X, which becomes a [mechanical noun] that does Y."]

### Combat model
[Same treatment. Concrete mechanics, not vibes.]

### Progression
[What unlocks what, what's the economy of power, what's the late-game state.]

### World structure
[How is space organized? Connected nodes? Procedural rooms? Hub-and-spoke? Single arena?]

### Strain points
[What in the source doesn't translate. Be honest. If the protagonist's defining moment is a 10-page internal monologue, that's not in any of these skeletons — flag it.]
```

Adapt the structure if the skeleton's reference file uses different categories (Roguelike has a "run structure" section that replaces "world structure," for example).

## What this skill is *not* for

- **Inventing new genres.** If the user wants a custom genre, decline gently and offer the closest skeleton. The skill's value is constraint.
- **Writing the actual game design document.** That's a downstream skill (the Multi-Agent Pipeline / Master Prompt territory). This skill stops at "here's how the source maps."
- **Generating world graphs, ability lists, or contract JSON.** Those belong to the Controidvania Contract Generator. This skill produces *mappings*, not artifacts.
- **Recommending source material.** "What book should I make a game from?" is the wrong direction for this skill — it works source-first, not skeleton-first.

## A note on tone-fit

Some source material is *too gritty* or *too literary* for some skeletons even when the mechanical DNA fits. A turn-based JRPG version of *No Country for Old Men* technically maps (initiative, party synergy from Llewelyn/Chigurh/Bell, attrition combat) but the cheerful menu-driven combat aesthetic fights the source's tone hard. Surface this when relevant. The user can override; you're not gatekeeping, you're flagging.

Conversely, some skeletons can stretch surprisingly far if the implementation is restrained — *Disco Elysium* is technically a stat-based RPG with dialogue trees, which mechanically should be incompatible with literary noir, but the implementation makes it work. Don't be doctrinaire. Note the risk; trust the user's judgment.
