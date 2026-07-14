# Arena Brawler (Beat-em-up / Fighter) — Skeleton Reference

**One-line vibe:** hyper-focused combat in confined spaces — combos, juggles, wave survival, frame data. The story is in the violence.

**Lineage:** *Streets of Rage*, *Devil May Cry*, *Bayonetta*, *Sifu*, *Hades* (combat layer), *Def Jam: Fight for NY*, *God Hand*, *Yakuza* (brawler installments).

## Core mechanical DNA

- **Movement model:** confined to the arena. Movement exists but is short-range — dashing between enemies, repositioning, wall-running. Not traversal.
- **Combat model:** real-time, frame-data-driven. Light/heavy attacks, combo chains, juggle states, hit-stun, parry/dodge windows, special meter. The skill ceiling is in *execution*.
- **Progression model:** typically lateral (new moves, weapons, styles) rather than vertical (stat growth). Mastery is the progression.
- **World structure:** arenas. Discrete fight scenarios — sometimes wave-based, sometimes single-encounter. Hubs for upgrades exist but are minimal.
- **Failure state:** death restarts the encounter or sequence. Run-based pressure is common but not universal.

## The translation job

1. **What does the protagonist's violence *feel* like?** (precise, brutal, elegant, tactical, desperate) → becomes the combat archetype's animation language.
2. **What's the protagonist's signature move?** → becomes the special / finisher move.
3. **Who are the typical antagonists?** (street thugs, soldiers, monsters, demons, white-collar enforcers) → becomes the enemy roster, tiered by difficulty.
4. **What are the iconic settings for violence in the source?** → becomes the arena set.
5. **What weapons / tools does the protagonist use?** → becomes the moveset variety.

## Suits source material when

- Combat *is* the source's identity, not a side product (John Wick, *300*, most martial arts manga, action heroes).
- The protagonist is unambiguously skilled at violence.
- Settings are repeatable, contained spaces (clubs, alleys, warehouses, arenas).
- Story beats are punctuated by fight setpieces.

## Strains when

- The protagonist is *not* a fighter (most literary fiction, mystery, romance).
- Stealth or strategy matters more than direct confrontation.
- The story is about avoiding violence (Survival Horror territory).
- The supporting cast is critical — the Brawler is fundamentally a solo experience.

## Mixer affinity

- **Excellent guest** for Top-Down RPG hosts (Hades-style — explore + fight rooms).
- Reasonable host for Roguelike runs (Hades again, *Returnal*).
- Poor pairing with Turn-Based — opposite combat philosophies clash.
- Poor pairing with Survival Horror — Brawler implies confidence, Horror implies vulnerability.

## Mapping checklist

- [ ] Combat archetype (precise/brutal/elegant/desperate — pick one)
- [ ] Signature move (the source's iconic violence translated)
- [ ] At least 3 enemy tiers with distinct behaviors
- [ ] Arena variety (4+ distinct settings)
- [ ] Weapon / style variety (3–6 options the player can switch)
- [ ] Failure pressure model (lives? checkpoint? run reset?)
- [ ] Score/style system (does the source's tone support a "rank your fight" mechanic?)
