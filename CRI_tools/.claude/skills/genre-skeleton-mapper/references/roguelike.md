# Roguelike — Skeleton Reference

**One-line vibe:** runs, not stories. Death resets the protagonist's body but not their *knowledge* — both the player's and the world's.

**Lineage:** *Rogue*, *Spelunky*, *The Binding of Isaac*, *Hades*, *Slay the Spire*, *Returnal*, *Dead Cells*, *FTL*.

## Core mechanical DNA

- **Movement / combat model:** *inherits* from another skeleton. Roguelike is a structural overlay, not a combat system. It pairs with Brawler (Hades), Top-Down RPG (Isaac), Turn-Based (Slay the Spire), or Controidvania (Dead Cells).
- **Run structure:** procedurally arranged sequence of encounters/rooms/floors. Each run is fresh; the world rebuilds. Run length: typically 30–90 minutes.
- **Progression model:** *dual-layer*. (1) Per-run progression — items, weapons, abilities accumulated within a run, lost on death. (2) Meta-progression — currency, unlocks, story revelations that persist across runs.
- **World structure:** procedural, layered. Often "biomes" or "floors" with escalating difficulty. A boss caps each layer.
- **Failure state:** **death is the central mechanic, not a punishment.** Each death pushes meta-progression forward. The protagonist's knowledge (and the player's) compounds.

## The translation job

This is the skeleton with the most demanding translation, because most source material isn't structured around death-as-progression. The AI's job:

1. **What does the protagonist *learn* that survives death?** (knowledge of enemy patterns? lore? a relationship? a curse?) → becomes the meta-progression currency.
2. **Why does the protagonist keep coming back?** (compulsion, duty, vengeance, curse, contract) → becomes the run-loop framing.
3. **What's the structural unit of a run?** (a heist, a dive, a journey, an attempt, a night, a mission) → becomes the run's narrative wrapper.
4. **Who is the patron / sender?** (someone who keeps issuing the runs) → becomes the hub character.
5. **What's the *final* run that ends the meta-arc?** → becomes the "true ending" condition.

## Suits source material when

- The source already has run-based or attempt-based logic (heist movies, *Edge of Tomorrow*, *Russian Doll*, mythological underworld journeys, Sisyphean stories).
- The protagonist's growth happens through *iterated failure*.
- The source's world is fundamentally hostile and the protagonist is fundamentally outmatched.
- There's a meaningful "between attempts" downtime (*Hades*' Pool of Styx, the heist crew's planning room).

## Strains when

- The story has a single, irreversible arc — death undoes its meaning.
- The protagonist's growth is purely emotional/internal (no mechanical learning to translate).
- The world is small enough that procedural generation feels artificial.
- The source's pleasure is in *narrative inevitability*, which procedural generation undermines.

## Mixer affinity

- Roguelike is *itself* a mixer — it's almost always paired with another skeleton's combat layer.
- Best paired with Arena Brawler (Hades), Top-Down RPG (Isaac, Gungeon), or Turn-Based (Slay the Spire).
- Poor pairing with Survival Horror — repetition undermines dread.
- Decent pairing with Stealth (*Disco Elysium* doesn't go this far, but in principle).

## Mapping checklist

- [ ] Which combat skeleton does this Roguelike inherit? (Brawler / Top-Down / Turn-Based / Controidvania)
- [ ] Run wrapper (heist? dive? mission? night?)
- [ ] Run length target (in minutes)
- [ ] Layer/biome list (3–5 layers with thematic identity)
- [ ] Per-run reward types (items, abilities, currency)
- [ ] Meta-progression currency (and what it unlocks)
- [ ] Hub character / sender (who frames the runs?)
- [ ] "True ending" condition (what does the meta-arc resolve into?)
