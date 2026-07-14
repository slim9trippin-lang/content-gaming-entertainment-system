# POE GENRE SKELETON #5 — SPORTS / COURT (Host)

> Status: formalized from the *Streets of Fire* conversion. Joins the original four (Kinesthetic Explorer, Top-Down Tactician, Turn-Based Strategist, Arena Brawler) as the first **non-spatial-progression Host**.

---

## 1. Identity

A real-time rules-contest host whose win condition is a **target score**, not a spatial exit. The "world" is not a traversable map — it is a **self-contained ruleset loop (a match)** that can repeat infinitely, sitting under a **meta-progression layer (ladder / season / map)**.

**One-line:** *the scoreboard is the level exit.*

---

## 2. Why this is a NEW skeleton (not a reskin)

Every original skeleton has a **spatial** win condition:

| Skeleton | Win condition | Progression unit |
|---|---|---|
| Kinesthetic Explorer | reach the exit | place (node) |
| Top-Down Tactician | clear the dungeon | place (room) |
| Turn-Based Strategist | survive the encounter | encounter |
| Arena Brawler | clear the wave | wave |
| **Sports / Court** | **hit the target number** | **a repeatable rules loop** |

Sports/Court breaks the **World Graph contract's core assumption** — that nodes are *places you traverse*. Here, nodes are *rungs you re-fight*. That break is the signature. It is why this is the 5th skeleton and not Arena Brawler in a jersey: an Arena Brawler ends when the room is clear; a Sports match has no room — it ends when a *number* is reached, and the same loop replays as the unit of a ladder.

---

## 3. Core mechanics (the host loop)

```
TIP/CHECK -> POSSESSION SM (offense <-> defense) -> SCORE EVENT -> 
  check target -> [win] | [continue] ...repeats until target met
                  ^                                          
                  └── two parallel meter economies run live (see §5)
```

Mandatory:
- **Possession/turn state machine** (who has initiative)
- **Scoreboard with a target** (the actual win condition — NOT a boss gate)
- **Real-time physics contest on a sim-input floor** (the "feel" floor)
- **Meta-progression layer ABOVE the match** (ladder/season/map — the thing matches feed)

---

## 4. Contract polymorphism map (the spine)

How the standard 9 reinterpret under this host:

| Contract | Spatial-host meaning | Sports/Court reinterpretation |
|---|---|---|
| C1 Input | level/mode select | match config + **rating_target** (drives fight mode) |
| C2 Interpretation | mechanical lock-in | **match ruleset** (possession model, scoring zones, target) |
| C3 World Graph | traversable places | **POLYMORPHED → progression graph** (ladder rungs / map cities) |
| C4 Ability | path-gating keys | **POLYMORPHED → meter-gated specials** (no spatial gate) |
| C5 Player Controller | platformer physics | sport physics (dribble/shoot/defend) + arcade↔sim slider |
| C6 Enemy | bespoke enemies | **POLYMORPHED → opposing archetype AI** (reuses C4 archetypes) |
| C7 Boss | encounter gate | **CLEAN 1:1 → ladder-apex rival** (best-fitting contract) |
| C8 Room/Tile | physical layout | **POLYMORPHED → court archetypes** (rule-tweaking skins) |
| C9 Validation | reachability/solvability | match-termination + meter-resolvability + ladder-completability |

**Finding:** 5 of 9 contracts polymorph (C3/C4/C6/C8 + the Input rating wrinkle). Boss is the single clean fit. This is the 4th independent confirmation that **C4 player_controller/ability are dialects, not fixed schemas** — they must be polymorphically typed at the engine level.

---

## 5. Required primitives

| Primitive | Role here | Mandatory? |
|---|---|---|
| `style_economy_contract` | flair-as-currency → special | **yes** (defines the genre's risk/reward) |
| `momentum_contract` | streak detector → buffed state | **yes** (the hot-hand layer) |
| `economy_contract` | stamina (depleting resource) | yes |
| `stat_progression_contract` | season growth / morale | optional (match-only modes skip it) |

The first two were **surfaced by this skeleton**; the latter two **carried over from the NES conversions** — confirming all four are schema-level.

> **Keystone rule:** `style_economy` and `momentum` MUST be orthogonal meters. Collapsing "consistency" and "flair" into one bar is the failure mode that makes sports games feel flat. Two meters = two independent stories per possession.

---

## 6. Mixer role — a new pattern: "event-guest"

Sports/Court is a **Host** that embeds **Arena Brawler as an event-triggered Guest** (the Flashpoint fight). This is distinct from the co-host blends documented earlier (Controidvania+Turn-Based, etc.):

- **Co-host blend:** two skeletons share the session, one Host one Guest, persistent.
- **Event-guest (NEW):** the Guest is *dormant* and instances only on a Host event (Tension threshold), runs <6s, hands back, dissolves.

Log this as a second legal mixer topology. It reuses Mixer Rules 2 (JSON handshake), 4 (input-context flag), 5 (logic culling) — no new plumbing.

---

## 7. Render classification

**Logic-carried CORE + render-carried SEASONING.** The basketball survives gray rectangles; the swagger (heat flames, gamebreaker cinematic, fight cam) does not. This is **Bucket 3** in the upgraded render taxonomy → attach `rendering_juice_contract` (see companion spec), NOT a `rendering_illusion_contract`. The juice layer is strippable for weak hardware/lower ratings without touching logic.

---

## 8. AI's job (deterministic translation only)

Given a sport + a narrative/IP, the AI maps:
- characters/players → **archetype stat blocks** (C4)
- the sport's rules → **ruleset params** (C2)
- the competitive structure → **ladder/season graph** (C3)
- the "feel" intent → **slider preset + meter tuning** (C2/C5)

The AI does **not** invent game feel. Feel lives in C5 numbers + the juice layer, both handcrafted/tuned.

---

## 9. Validation rules (skeleton-specific)

1. Every match reaches a win condition (no infinite games).
2. Every meter has a spend path AND a cool/decay path (no permanent-buff lock).
3. The meter standoff (style-spend vs counter-spend) is resolvable (no soft-lock).
4. The ladder is completable from the bottom rung.
5. `rating_target` legally maps to a flashpoint resolution mode.

---

## 10. Example applications

- **Streets of Fire** (worked case — Big3 half-court 3v3).
- Arcade soccer / futsal (target = goals, style = skill moves, momentum = "in form").
- Dodgeball / arena sports (target = eliminations, flashpoint = collision scrum).
- Any **scoreboard-target contest with a hot-hand and a flair layer**.

> Boundary: position-scored *racing* is NOT this skeleton (race is render-carried per the Rad Racer precedent). Sports/Court = **number-race on a court**, not a track.
