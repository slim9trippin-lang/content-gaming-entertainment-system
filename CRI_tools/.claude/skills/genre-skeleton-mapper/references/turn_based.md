# Turn-Based Strategist (JRPG / Tactics) — Skeleton Reference

**One-line vibe:** methodical, math-driven combat where the protagonist's *crew* matters as much as the protagonist; story carries the weight that twitch reflexes don't.

**Lineage:** *Final Fantasy*, *Persona*, *Dragon Quest*, *Fire Emblem*, *XCOM*, *Yakuza: Like a Dragon*, *Disco Elysium* (loose).

## Core mechanical DNA

- **Movement model:** typically minimal — overworld or grid movement between encounters. Combat is the primary state, not movement.
- **Combat model:** turn-based, math-resolved. Initiative queues, damage formulas (attack vs. defense, with RNG variance), elemental/typing systems, status effects, party synergies. The skill ceiling is in *planning* and *party composition*, not execution.
- **Progression model:** experience points → levels → stat increases + new abilities. Equipment trees. Often a "job" or "class" system.
- **World structure:** chapter-based or region-based, with discrete encounters. Story beats gate progression, not abilities.
- **Failure state:** game over → reload save. Combat death has weight; story death is permanent unless reloaded.

## The translation job

This is the skeleton where *relationships* become mechanics. The AI's job is heavy here:

1. **Who is the protagonist's circle?** (best friend, mentor, rival, romantic interest, betrayer) → each becomes a party member with a role (DPS, support, tank, debuffer).
2. **What are the recurring tensions in those relationships?** → become *synergy combos* (when two specific characters act consecutively, bonus effect) and *betrayal mechanics* (party member leaves under conditions).
3. **What are the source's elemental forces?** (magic schools, weapon types, ideologies, factions) → becomes the elemental/type chart.
4. **What is the protagonist's weakness?** → becomes their stat profile (low HP / high speed = "glass cannon"; high charisma / low strength = "support").
5. **What's the source's central narrative arc?** → becomes the chapter structure, with party composition shifting at key beats.

## Suits source material when

- The protagonist has a *named, mattering* supporting cast (not just sidekicks).
- Conflict is large-scale or politically charged (kingdoms, organizations, factions).
- The story has multiple character arcs running in parallel.
- Pacing is methodical/literary rather than action-driven.
- Tone supports a slower, contemplative experience.

## Strains when

- The protagonist is fundamentally alone (Cormac McCarthy, most westerns).
- Combat is the source's primary aesthetic identity (John Wick, *300*).
- The story moves at thriller pace — turn-based feels wrong for urgency.
- The supporting cast exists but is shallow or interchangeable.
- The source's tone is comedic-arcade (better suited to Brawler).

## Mixer affinity

- **Excellent guest** when paired with Controidvania (touch enemy → tactical battle).
- **Excellent guest** when paired with Top-Down RPG (Persona-style: explore world, fight in turn-based).
- Poor as host for action-genre guests — host turn-based + guest brawler whiplashes too hard.
- Pairs surprisingly well with Roguelike for run-based tactical games (*Slay the Spire*, *Into the Breach*).

## Mapping checklist

- [ ] Party of 4–6 named characters with distinct roles
- [ ] Stat profile per party member (HP, MP, Speed, Attack, Defense, plus 1–2 source-specific stats)
- [ ] At least 3 synergy combos (X + Y character produces special effect)
- [ ] Elemental/type chart (4–6 categories with strengths/weaknesses)
- [ ] Status effect list (stun, poison, etc., with source-specific renaming)
- [ ] Chapter structure mapped to source's narrative arc
- [ ] At least one named "betrayal mechanic" (party member changes sides under specific conditions)
