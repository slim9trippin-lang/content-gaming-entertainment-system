# STREETS OF FIRE — Big3 Fusion Basketball
### Working title GDD v1 · Unity target

> **One-line identity:** A half-court 3v3 streetball brawler where *style is currency, momentum is fuel, and disrespect has consequences.* Arcade flair sitting on a 2K-grade input floor, wrapped in the Big3 ruleset.

---

## 0. The Fusion Map (what each reference actually contributes)

| Source | Layer it owns | Why it's load-bearing |
|---|---|---|
| **Big3** | The *frame* — half court, 3v3, target score, 4-pt circles | Makes gamebreakers/4pt shots native, not gimmicks |
| **NBA Street** | *Style economy* — trick stick, style meter, **Gamebreaker** | The risk/reward parallel-points game on top of real points |
| **NBA Jam** | *Momentum* — heat check / on-fire | Streak detection that bends the math mid-possession |
| **Blades of Steel** | *Consequence* — fight scene + penalty box | Turns hard fouls into a Guest minigame with real stakes |
| **2K** | *Input fidelity floor* — shot meter, signature anims, stamina | The "realism dip" — every arcade move resolves on sim inputs |

**Litmus (your render vs logic test):** replace all art with gray rectangles. The *basketball* survives (logic-carried). The *identity* — heat flames, gamebreaker cinematic, fight cam — dies (render-carried). That tells us exactly what to greybox vs what to juice. See §9.

---

## 1. The Big3 Ruleset Frame

- **Court:** half-court, single hoop, make-it-take-it OR check-ball variants (toggle per mode).
- **Teams:** 3v3, captain + 2. Substitution from a 5-deep bench.
- **Scoring:** 2s, 3s, and **three 4-point circles** (deep arc).
- **Win condition:** target score (default **21, win by 2**) — selectable up to 50 for long sessions. Target-score format is what makes a single gamebreaker swing a game, which is the whole tension engine.
- **The "Bring the Fire" wrinkle:** a once-per-game challenge a trailing team can call — covered in §4 as a comeback valve.

---

## 2. CORE SYSTEMS

### 2.1 Movement + Trick Stick (NBA Street DNA, 2K floor)
- Left stick = drive. Right stick = **trick stick** (crossovers, hesis, behind-the-back, between-legs, ankle-breaker chains).
- Every successful trick that beats a defender's stance feeds the **Style Meter** (§2.3).
- **2K dip:** dribble moves resolve against real defender footwork/hip orientation, not a coin flip. A crossover only "breaks ankles" if the defender's momentum is committed the wrong way. This is the realism floor under the arcade flair.

### 2.2 Shooting — three-layer stack
1. **Input layer (2K):** shot meter with a green window. Release timing + contest determine base make %.
2. **Heat layer (Jam):** 3 makes in a row → **HEATING UP** → 4th make → **ON FIRE**. On fire = enlarged green window, flame trail on the ball, net scorch FX, auto-swish near range. Cools when opponent scores or you miss twice.
3. **Style layer (Street):** *how* you scored adds Style (contested fadeaway > wide-open layup). Style ≠ points; Style fuels the Gamebreaker.

> Design note: heat and style are **separate meters**. Heat is about *consistency*; style is about *disrespect*. A player can be on fire without a gamebreaker, or sitting on a loaded gamebreaker while ice-cold. Keeping them orthogonal is what gives possessions texture.

### 2.3 The Gamebreaker (Street's crown jewel, Big3-ified)
- Style Meter fills through tricks, dunks-in-traffic, posterizers, and-1s, blocks.
- At full meter → trigger **GAMEBREAKER**: a cinematic shot/dunk worth **bonus points AND it subtracts from the opponent's score** (classic Street rule). In the Big3 frame this resolves from a 4-point circle as a "**FIREBALL**" — a 4-pt make that also docks the opponent.
- **Counter mechanic:** the defending team can spend *their* style meter to attempt a **Gamebreaker Block** — a timed contest. Success negates the steal-back and humiliates the shooter (huge style swing). This keeps it from being unblockable and creates a meter-vs-meter standoff.

### 2.4 Flashpoint → Fight Scene + Penalty Box (Blades of Steel)
This is your **Host/Guest** moment — straight out of the POE mixer rules.

- **Trigger (Host = basketball sim):** a hard foul, a posterizer dunk-on, or a trash-talk-stacked play raises a hidden **Tension** value. Cross the threshold → **FLASHPOINT**.
- **Handshake:** sim freezes, emits a JSON payload `{aggressor, target, tension, crowd_heat}` → Fight system instances.
- **Guest = Fight minigame (Arena Brawler skeleton):** side-view, Blades-of-Steel-style timed brawl. Block / jab / haymaker, short and snappy (≤6 seconds). 
- **Resolution → handshake back:** loser is sent to the **PENALTY BOX** for one defensive possession → the offended team plays **3-on-2** (your power-play equivalent). Winner gets a Style spike; the crowd-heat carries into music intensity.
- **Logic culling:** while the fight runs, the sim's economy/AI pause (your Rule 5). Clean isolation, no physics bleed.

> Why this works: it imports the *consequence* layer basketball games usually lack, and it reuses your existing Host/Guest contract instead of inventing new plumbing.

### 2.5 The Arcade ↔ Sim Slider ("dipped in 2K")
A single global tuning axis exposed in settings and weighted per mode:
- **Arcade end:** generous green windows, exaggerated dunk physics, frequent heat, fast tricks.
- **Sim end:** tight windows, stamina drain matters, contests punish, tricks demand real reads.
- Modes preset the slider (Dunk Contest = full arcade; Season ranked = sim-leaning). One slider, many presets — same engine, different feel.

---

## 3. PLAYER ARCHETYPES (the "Jordan vs Bird" duel, IP-safe)

The *Jordan vs Bird* magic was an **archetype clash** — aerial finisher vs. cerebral marksman. Build the roster as archetype slots so the system is identical whether you ship original or licensed players:

| Archetype | Identity | Signature lever |
|---|---|---|
| **The Aerial** ("Jordan" slot) | dunk-heavy finisher | fastest Gamebreaker fill off posterizers |
| **The Marksman** ("Bird" slot) | range shooter | fastest Heat ignition, deepest 4pt range |
| **The Handle** | trick-stick savant | style-per-trick multiplier |
| **The Wall** | defensive anchor | best Gamebreaker-Block + fight stats |
| **The Engine** | tempo/playmaker | feeds teammates' meters via assists |
| **The Bruiser** | enforcer | wins Flashpoints, raises team Tension threshold |

Each archetype = a ScriptableObject stat block + one signature animation set + one special. **No likeness required.** Real names become a *skin/license layer* on top, decided later — keeps your Lane-1 exposure at zero until you choose.

---

## 4. GAME MODES (your Jordan-vs-Bird list, defined)

1. **1v1** — iso duel to a low target (11). Pure archetype clash. Tension/fights ON by default; this is where rivalries are born.
2. **3v3 (core)** — full Big3 ruleset. The main event.
3. **Dunk Contest** — trick-stick + timing combo scorer, judge panel, full-arcade slider. Style meter = your score.
4. **3pt / 4pt Contest** — rack-based shootout. 4pt mode uses the deep circles + a "money ball" rack. Heat mechanic active (rhythm shooting rewards streaks).
5. **Season — "World Tour"** — the international map hub (§6). Travel city to city, build a squad, manage stamina/morale across a schedule.
6. **1-Up-1-Down (King of the Court)** — ladder mode: **win and you climb, lose and you drop.** Winner stays on, challenger rotates in. Run the gauntlet from local court → city champ → continental → world. This is your endless-replay arcade hook.

**"Bring the Fire" comeback valve (all competitive modes):** a team trailing by a set margin can call it once — next possession, all their made baskets are worth +1 and gamebreaker fills double. Big risk: if they get stopped, opponent gets a style bonus. Keeps blowouts alive and crowds loud.

---

## 5. PLAYER SELECT — Street Fighter Style

- **Grid roster screen:** portrait cells, archetype icon, 3-stat radar peek on hover, signature-special preview clip.
- **VS slam:** chosen players collide center-screen, names crash in, announcer call-out ("AERIAL vs MARKSMAN — *BRING IT*").
- **Stance pick:** before tip, choose a team *style stance* (Run-and-Gun / Lockdown / Showtime) that nudges the arcade-sim slider and meter rates — your equivalent of a fighting-game character's playstyle.
- Mobile-friendly: grid collapses to a swipe carousel; VS slam still hits.

*(I can mock this screen up as an interactive widget on request — say the word.)*

---

## 6. INTERNATIONAL MAP / SEASON HUB

- **Node-based world map** (Risk/Master-League feel). Teams seeded into real cities/countries; tapping a node shows the home squad, court skin, and crowd identity.
- Each city = a **court archetype** (rooftop, beachfront cage, arena, alley) that subtly tweaks rules/visuals — e.g., a windy rooftop nudges the slider toward chaos.
- **Progression:** start regional → win to unlock continental brackets → World Tour finals. Logos live *in their cities*, so the map doubles as the standings board.
- Ties cleanly to **1-Up-1-Down**: the map IS the ladder when you play it in King-of-the-Court mode.

---

## 7. AUDIO — Dynamic Hip Hop Layer

- **Stem-based adaptive music:** a base beat that *adds layers* as state escalates — heat check drops a hi-hat run, on-fire kicks the full beat, gamebreaker hits an air-horn/sample stab, flashpoint cuts to a tense filtered loop, crowd-heat raises the mix.
- Court-specific crate (regional flavor per city node).
- Announcer + crowd reactive to style and disrespect (Jam-style call-outs, modernized).
- **Hook:** a "DJ booth" on the sideline whose scratches sync to gamebreaker triggers — diegetic music that *is* a feedback channel.

---

## 8. UNITY ARCHITECTURE SKETCH

```
GameDirector (mode manager / global FSM)
│
├── MatchHost  ── basketball sim FSM  (the Host)
│     ├── PossessionSM      (offense/defense/loose-ball states)
│     ├── ShotSystem        (meter + contest + heat resolution)
│     ├── StyleSystem       (trick scoring → Style Meter)
│     ├── GamebreakerSystem (trigger / steal-back / block standoff)
│     ├── TensionSystem     (foul/disrespect accumulator → Flashpoint)
│     └── SliderTuner       (arcade↔sim weights, per-mode preset)
│
├── FightGuest ── brawl FSM   (the Guest, instanced on Flashpoint)
│     └── handshake: JSON in {aggressor,target,tension} → result out {loser→PenaltyBox}
│
├── Data (ScriptableObjects)
│     ├── ArchetypeSO  (stats, sig anim set, special)
│     ├── CourtSO      (rules tweak, visual skin, crowd)
│     ├── ModeRulesSO  (target score, slider preset, fights on/off)
│     └── MusicStateSO (stem map per game state)
│
└── AudioDirector (adaptive stem mixer, listens to MatchHost events)
```

- **Host/Guest handshake** = your POE Rule 2 (clean JSON segregation), so fights can never corrupt sim state.
- **Universal Variable Dictionary** (Rule 3): `Style`, `Heat`, `Tension`, `Stamina` are shared currencies read by every system — one stat lexicon, no parallel leveling.
- **Input flagging** (Rule 4): `InputContext = {Dribble | Shoot | Fight | Menu}` swaps the control map so the trick stick doesn't fire during a brawl.

---

## 9. PROTOTYPE PRIORITY (render vs logic guidance)

**Greybox first (logic-carried — survives gray rectangles):**
- Possession FSM, shot meter + heat math, style/gamebreaker meter economy, target-score win logic, Host/Guest handshake, penalty-box 3-on-2 state.

**Juice last (render-carried — these ARE the identity):**
- On-fire flames, gamebreaker cinematic, fight cam + hit FX, VS slam, adaptive music, crowd reactions.

Build a 1v1 greybox that proves *style → gamebreaker → steal-back* feels good with capsules and no art. If that loop is fun gray, the game is real. Then layer juice.

---

## 10. OPEN FORKS (your call before v2)

1. **Roster:** original archetypes only, or pursue licensed likeness layer? (Affects nothing mechanically — pure Lane-1 risk decision.)
2. **Make-it-take-it vs check-ball** as the default 3v3 possession rule.
3. **Fights:** always-on, toggle, or unlock? (Some markets/ratings care.)
4. **Express this as POE 9-Contract JSON?** The fight Guest already fits your mixer; the rest maps to a new **Sports/Court skeleton** with a `style_economy_contract` and `momentum_contract` — two primitives your NES conversions didn't surface.
