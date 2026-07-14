# Stealth — Skeleton Reference

**One-line vibe:** the protagonist is hunted, hiding, or infiltrating. Detection systems are the central mechanic; combat is a failure state or a precision tool.

**Lineage:** *Metal Gear Solid*, *Thief*, *Splinter Cell*, *Dishonored*, *Hitman*, *Mark of the Ninja*, *Assassin's Creed* (early titles).

## Core mechanical DNA

- **Movement model:** quiet vs. loud. Crouch/walk/run states with different noise + visibility profiles. Cover systems, vertical infiltration (vents, rooftops, shadows). Movement is *expressive* but in a different register from Controidvania — the goal is *not being seen*, not flow.
- **Combat model:** asymmetric. The protagonist is usually weak in direct combat but devastating from surprise. Takedowns, body hiding, distractions, lures.
- **Progression model:** new tools (gadgets, weapons, abilities) rather than stat growth. Each tool opens new approaches, not new spaces.
- **World structure:** discrete missions or levels, each with clear objectives. High replayability — most missions support multiple approaches (lethal/non-lethal, ghost/loud).
- **Failure state:** detection. Once seen, the mission either fails or escalates dramatically (alarm states, reinforcements, lockdowns).

## The translation job

The detection system is the load-bearing mechanic. Every translation lives or dies on whether you can answer "who is hunting whom, and how do they see?"

1. **Who is hunting the protagonist?** (police, a syndicate, supernatural pursuers, paparazzi, a single relentless figure) → becomes the antagonist patrol/AI.
2. **How does the source's antagonist *detect*?** (sight, sound, smell, magic, surveillance cameras, social inquiry) → becomes the detection vector mix.
3. **What does the protagonist's signature stealth tool *do*?** (smoke, shadow, distraction, mimicry, technology) → becomes the player's defining gadget.
4. **What's the mission template?** (assassination, infiltration, theft, rescue, sabotage, escape) → becomes the objective set.
5. **What's the protagonist's "I've been spotted" panic move?** → becomes the escape/combat fallback.

## Suits source material when

- The protagonist is a spy, assassin, thief, fugitive, or infiltrator by trade or necessity.
- The story's tension is in *not being caught*, not in winning fights.
- The world has clear power asymmetries (the protagonist is outnumbered or outgunned).
- Replayable scenarios are plausible (a heist, a hit, a break-in).
- The supporting cast includes a handler / quartermaster / tech support figure.

## Strains when

- The protagonist's identity is *to be seen* (most superheroes, most action heroes).
- The story is open-world freeform without discrete objectives.
- Combat is the protagonist's preferred tool (Brawler).
- The threat is environmental rather than agent-driven (better suited to Survival Horror).

## Mixer affinity

- Reasonable pairing with Survival Horror — both share outmatched-protagonist premises, though tonal blend is tricky (Stealth implies competence, Horror implies vulnerability).
- Decent pairing with Top-Down RPG (mission hub + stealth missions).
- Possible Roguelike pairing if the missions are procedural.
- Poor pairing with Brawler, Controidvania, or Turn-Based as direct hosts.

## Mapping checklist

- [ ] Antagonist faction with patrol behavior
- [ ] Detection vector mix (sight cones, sound radii, traces, social suspicion)
- [ ] Protagonist's signature stealth tool (one defining gadget/ability)
- [ ] Mission template (assassination / infiltration / theft / rescue / sabotage / escape)
- [ ] At least 3 mission-level set pieces with multiple solutions
- [ ] Loud-fallback combat moveset (less developed than a Brawler's, but functional)
- [ ] Handler / quartermaster character (between-mission hub)
- [ ] Detection state model (clear / suspicious / alarm / lockdown)
