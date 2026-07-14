# *The Coldest Winter Ever* mapped to Top-Down Tactician

## The vibe
A descending-power-curve action-RPG set in mid-90s Brooklyn. You play Winter Santiaga, daughter of a major dealer, starting at the absolute peak of street privilege — money, name, beauty, untouchable. Every chapter the world *shrinks* on you. Allies turn, money runs out, neighborhoods that used to fear you start ignoring you, and the things you used to wield as currency (looks, family name, expensive clothes) lose their exchange rate. The standard RPG progression curve runs in reverse: you don't get stronger, you watch your stat sheet hollow out and learn what each loss costs.

## Translation table

| Source element                                    | Becomes mechanic                                              |
|---------------------------------------------------|---------------------------------------------------------------|
| Winter's status as a Santiaga                     | Starting "Family Name" stat — high but depleting              |
| Designer clothes / appearance obsession           | Wardrobe inventory; clothes are stat-modifying equipment      |
| Sex appeal as leverage                            | "Looks" stat; gates dialogue branches with male NPCs          |
| Family arrest, asset seizure                      | Inciting event — strips most of starting inventory in act 1   |
| Boyfriends / hustlers / Santiaga associates       | NPC roster; loyalty values that decay over time               |
| Brooklyn brownstone → group home → streets        | Hub locations that change as Winter falls                     |
| Manipulation, seduction, lying                    | "Combat" — but verbal, through dialogue checks                |
| Sister Souljah herself (appears as character)     | Specific NPC quest-giver; foil with opposing value system     |
| Heat from law enforcement                         | "Heat" meter; rises with criminal acts, gates safe locations  |

## Movement model
Functional rather than expressive. Walking, subway, occasional taxi or borrowed car. Movement isn't the point — what matters is *which neighborhoods you can still safely move through*. The map should darken progressively: areas that were green in chapter 1 (the Santiaga estate, certain clubs) become unsafe by mid-game, and Winter loses access not because of a locked door but because the social geometry has changed.

## Combat model
This is the skeleton's weak match for this source — flagged in detail below. Winter is not a fighter. The "combat" verbs are *manipulate, seduce, lie, threaten, leverage.* Mechanically this maps to dialogue-based encounters with success/failure rolls against opponent stats:
- **Persuasion checks** against `Looks + Family Name`
- **Intimidation checks** against `Heat + Reputation`
- **Lie checks** against opponent's `Suspicion`
- **Combat-combat** (the rare physical altercation) is awkward and disadvantageous — Winter loses most fights

## Progression
**This is the unusual part** and probably the game's core hook: Winter does not level up. She levels *down.* Her starting stats are her peak. Each chapter strips equipment, drains stats, and removes NPCs from her network. The "progression" the player drives is *adaptation* — learning to operate at lower stat tiers, finding new (worse) hustles, accepting trades that would have been unthinkable at chapter 1. The win condition isn't power; it's whether Winter survives with her sense of self intact (or not — the book itself doesn't grant this).

A more conventional player might prefer an **alternate ending path** where Winter accepts the Sister-Souljah-character's worldview and the game converts to ascending progression of a totally different kind (community/knowledge/sobriety as the new economy). The skeleton supports this if the developer is willing to fork the game's late chapters.

## World structure
Brooklyn 1990s as a hub-and-spoke where the hubs *move.*
- **Act 1 hub:** the Santiaga brownstone — full services, all NPCs friendly, full inventory access
- **Act 2 hub:** the group home — institutional, restricted hours, NPCs are either threats or surveillance
- **Act 3 hub:** wherever Winter can crash that night — temporary, unstable, may be revoked

Spokes are NYC boroughs, each with distinct danger profiles, gated by Winter's current `Heat`, `Cash`, and `Reputation` rather than by abilities.

## Strain points

The honest list, in order of severity:

1. **Combat doesn't fit the skeleton cleanly.** The Top-Down Tactician wants combat as a core verb. Winter's conflict toolkit is verbal/social, not violent. You can stretch the skeleton with persuasion/seduction/lying as combat-equivalents (and this is creatively interesting), but you're working *against* the skeleton's natural shape. A dialogue-trees-as-combat indie game like *Disco Elysium* is closer to the spirit than *Diablo* is.

2. **The descending progression curve is structurally unusual.** Most action-RPGs assume the player is gaining capability over time. Winter loses capability. This is a real design opportunity (the game could be *about* learning to operate at diminishing power) but it cuts against player expectations, and most action-RPG players want to feel stronger by hour 10.

3. **The first-person voice is a major part of the book's identity, and it doesn't survive any third-person game adaptation.** Winter narrates with a specific, unreliable, justifying voice. A game in third-person top-down loses that. You'd want heavy use of internal-monologue text overlays to preserve it.

4. **The book ends with Winter incarcerated and unrepentant.** Most game players expect agency and triumph. This is solvable (multiple endings, including the unrepentant one as the "true" ending) but it's a design tension to surface early.

5. **A different skeleton might fit better:** **Survival Horror** is a sneaky-good match for the back half of the book. The persistent threat (Heat / former associates / the streets themselves), depleting resources (cash, allies, safe places), and the descending power curve all align. A hybrid — Top-Down Tactician for acts 1–2, Survival Horror for act 3 — is closer to what the book actually does emotionally.
