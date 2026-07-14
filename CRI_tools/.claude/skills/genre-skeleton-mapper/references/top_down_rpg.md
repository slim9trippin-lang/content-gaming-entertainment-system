# Top-Down Tactician (Action-RPG / Zelda-lite) — Skeleton Reference

**One-line vibe:** omnidirectional movement, dungeon crawling, stat progression, and resource management — the protagonist's empire of capabilities grows over time.

**Lineage:** *The Legend of Zelda*, *Diablo*, *Hades* (with brawler hybrid), *Stardew Valley* (loose), *GTA: Chinatown Wars*, *Hyper Light Drifter*.

## Core mechanical DNA

- **Movement model:** omnidirectional (8-way or free-vector) on a top-down or isometric plane. Movement is functional rather than expressive — getting from A to B, not performing.
- **Combat model:** real-time but positional. AoE hitboxes, dodge rolls, ranged vs. melee tradeoffs. Less twitch-intensive than a Brawler but more active than a Strategist.
- **Progression model:** stat + inventory growth. The protagonist accumulates equipment, levels, currency, and territory. Quantitative power growth is the loop.
- **World structure:** hub-and-spoke OR open overworld with discrete dungeons. NPCs in town, danger in dungeons. Quest flags drive narrative gating.
- **Failure state:** death respawns with cost (currency loss, inventory drop, time loss). Persistent character.

## The translation job

1. **What's the protagonist's economy of power?** (wands, weapons, allies, territory, money, knowledge) → becomes the inventory + skill tree structure.
2. **What's the home base?** (a town, a hideout, a stash house, Hogwarts common room) → becomes the hub.
3. **What's *out there*?** (dungeons, forests, rival territory, hostile districts) → becomes the dungeon set.
4. **Who are the NPCs?** → becomes the dialogue tree + quest-giver network.
5. **What's the "currency" of the source's world?** (gold, respect, House Points, info, drugs, favors) → becomes the central economic resource.

## Suits source material when

- The protagonist has a clear collection-and-growth loop (RPG protagonists, hustle narratives, wizard-school stories).
- The world has discrete locations with distinct dangers.
- Supporting cast matters but operates around the protagonist (vs. *with* them in a JRPG party).
- Resource management is part of the source's tension (ammo, money, magical components, social capital).

## Strains when

- The protagonist's power is fixed and the story is about *applying* it (John Wick — Arena Brawler).
- The story is purely a journey through space without acquisition (Cormac McCarthy's *The Road* — closer to Survival Horror).
- The conflict is interpersonal/diplomatic with no spatial component.
- The economy of power is purely social/relational (better suited to Turn-Based Strategist where party dynamics are mechanical).

## Mixer affinity

- **Excellent host** for Arena Brawler in combat rooms (*Hades* does exactly this).
- Reasonable host for Turn-Based combat encounters (older JRPGs).
- Poor pairing with Controidvania (similar exploration loop, redundant).
- Decent pairing with Roguelike for run structure (Hades again, *Enter the Gungeon*).

## Mapping checklist

- [ ] Hub location (where does the protagonist return to?)
- [ ] At least 4 distinct dungeons / hostile zones with thematic identity
- [ ] Inventory category list (weapons, consumables, key items, currency)
- [ ] Stat progression (HP, attack, defense, plus 1–2 source-specific stats)
- [ ] NPC roster (5+ named NPCs with quest hooks)
- [ ] Central economic resource (what gets spent and earned)
- [ ] Combat archetype the protagonist defaults to (melee/ranged/magic/hybrid)
