# Kinesthetic Explorer (Controidvania) — Skeleton Reference

**One-line vibe:** side-scrolling traversal of an interconnected map, where new abilities unlock previously inaccessible space.

**Lineage:** *Metroid*, *Castlevania: Symphony of the Night*, *Hollow Knight*, *Ori and the Blind Forest*, *Guacamelee*, *Dead Cells* (loose).

## Core mechanical DNA

- **Movement model:** grounded with vertical mobility tools. Gravity, jump arc, and air control are the primary verbs. Traversal mechanics (double jump, dash, wall-cling, grapple, glide) are the protagonist's main vocabulary.
- **Combat model:** real-time, twitch-skill-based, but secondary to traversal. Combat is something to *survive* between rooms, not the central loop.
- **Progression model:** ability-gated. The protagonist starts able to do roughly 30% of what they'll be able to do at the end. Each ability unlocks new map regions; backtracking with new abilities is the structural rhythm.
- **World structure:** interconnected node graph (typically 10–20 major regions), with shortcuts that activate on backtrack. The map *is* the game.
- **Failure state:** death respawns at last checkpoint, often with minor resource loss. Not run-based.

## The translation job

The AI's task when receiving source material is to answer five questions:

1. **What is the protagonist's signature movement?** (web-swinging, parkour, climbing, flying, teleporting, etc.) → becomes a traversal ability.
2. **What spaces define the source?** (rooftops, dungeons, subways, jungles, cities) → becomes the map's regional aesthetic.
3. **What does the protagonist learn over the source's arc?** → becomes the ability progression order.
4. **Who are the recurring antagonists?** → becomes the boss list (each gates an ability).
5. **What's a "locked door" in this world?** (a gang's territory, a magical seal, a hostile environment, a security clearance) → becomes the gating logic.

## Suits source material when

- The protagonist has a distinctive way of moving (Spider-Man, Daredevil, Kratos, Aragorn).
- The story has a meaningful map — places matter, return is significant.
- The protagonist gains specific, namable powers over time, not just generic stat increases.
- Combat exists but isn't the *whole* point.

## Strains when

- The story is dialogue-driven or interior-focused (Sally Rooney, Knausgård).
- The protagonist's growth is moral or social rather than physical/mechanical.
- The world is one location with no spatial significance (single-room thrillers).
- Combat is the protagonist's defining trait (John Wick — that's an Arena Brawler, not a Controidvania).

## Mixer affinity

- **Excellent host** for Turn-Based Strategist as guest (touch enemy → tactical battle, like *South Park: TFBW*).
- Reasonable host for Roguelike runs (each "run" is a portion of the map; *Dead Cells* does this).
- Poor pairing with Survival Horror — the kinesthetic confidence of traversal undercuts dread.
- Poor pairing with Stealth as host — stealth wants slowness; Controidvania wants flow.

## Mapping checklist

When producing a full breakdown, make sure each of these is concretely answered:

- [ ] Starting ability set (what can the protagonist do at minute 0?)
- [ ] At least 4 distinct unlock abilities, each gating a region
- [ ] Map structure (number of regions, naming, connections)
- [ ] Boss-ability pairings (each boss drops a specific unlock)
- [ ] Combat verb (sword, gun, web, magic, etc. — singular signature, not a kit)
- [ ] Failure consequence (respawn cost, save point system)
