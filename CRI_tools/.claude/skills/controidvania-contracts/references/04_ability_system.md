# 04 — Ability System Contract

*Reconstructed from original build session history — recovered flash drive did not contain this file.*

The Ability System defines the **keys** that unlock the World Graph's gated edges. Every ability must be load-bearing — it has to unlock at least one previously-locked path. "Dead abilities" that look cool but don't change what the player can reach are the most common Controidvania design failure.

This contract is the most tightly coupled to the World Graph. They must agree on every ability ID. The skill's cross-contract validation step is what enforces this.

## Schema

```json
{
  "abilities": [
    {
      "id": "string (snake_case, e.g. 'double_jump') — unique, referenced by World Graph edges",
      "type": "enum: movement | combat | utility",
      "description": "string — what the ability does (1–2 sentences)",
      "unlock_node": "string — node ID where the player acquires this ability",
      "gates_unlocked": "array of strings — short tags describing what the ability opens up (e.g. ['vertical_paths', 'rooftop_access'])",
      "scaling": "boolean — true if the ability has upgrade tiers, false if binary"
    }
  ]
}
```

## Generation rules

### Ability count

Target **4–8 abilities**. Fewer than 4 makes the progression curve too sparse; more than 8 creates cognitive overload and tuning hell. Sweet spot: 5–6.

### Type distribution

For a typical Controidvania:
- 3–5 `movement` abilities (the defining type — double jump, dash, wall-climb, grapple, glide)
- 0–2 `combat` abilities (charged attacks, projectile types, parry — only if combat is a meaningful design pillar, not just a verb)
- 0–2 `utility` abilities (lantern that reveals secrets, scanner, etc. — situational)

Bias toward `movement` heavily. Movement abilities are the genre's defining feature; the others are flavor.

### Theming from Input + Interpretation

Pull ability names and concepts from the Input Contract's `core_ability_style` and the Interpretation's `movement_model`. Don't generate generic abilities for every game — a "double jump" in a noir city should be called something else (parkour vault?) even if mechanically identical.

## Example — tracing acquisition order against the World Graph, and an open discrepancy

Using the World Graph example from Contract 03: abilities are `wall_climb` (unlock_node: node_07), `double_jump` (unlock_node: node_05), `grapple` (unlock_node: node_09).

Trace: the only edge into node_05 is `node_03 → node_05, requirement: wall_climb`. But `wall_climb` unlocks at node_07 — and the only edges into node_07 are `node_05 → node_07, requirement: none` and `node_06 → node_07, requirement: wall_climb`. Reaching node_07 through node_05 requires no ability, but reaching node_05 in the first place requires `wall_climb`, which you can only get by first reaching node_07. **As recovered, this is a genuine circular lockout** — G3 (full solvability) in the Validation Layer contract would catch exactly this.

**This is flagged rather than silently resolved.** The original session's transcript asserted this acquisition order was valid, but re-tracing it against the recovered edge list doesn't support that. Two possibilities: either the recovered World Graph example is missing an edge that broke the cycle, or the original assertion was itself wrong and went unvalidated. Either way, don't treat this specific example as a clean reference — the fix pattern is still correct (move `wall_climb`'s `unlock_node` to a node reachable without it, or add an un-gated incoming edge to node_05), but the worked example needs a real re-trace, not an inherited conclusion.

## Per-contract validation

- [ ] At least 4 abilities, at most 8
- [ ] All `id` values unique
- [ ] All `id` values in snake_case
- [ ] Each ability has a `type` from the enum
- [ ] Each ability has a non-empty `description`
- [ ] Each `unlock_node` references a real node in the World Graph
- [ ] No two abilities share the same `unlock_node`
- [ ] At least 60% of abilities are `type: movement`

## Cross-validation against World Graph (mandatory)

- [ ] Every `abilities[i].id` appears as the `requirement` of at least one edge in the World Graph
- [ ] No "dead abilities" (acquiring it changes nothing reachable)
- [ ] Every edge `requirement` value (other than `"none"`) matches an `abilities[i].id` — no phantom requirements
- [ ] No circular lockouts: every `unlock_node` is reachable using only abilities acquired *before* it in unlock order
- [ ] `end_node` is reachable from `start_node` once all abilities are acquired in their unlock order

If any cross-validation fails, the first fix is usually to either move an ability's `unlock_node` earlier, or to add an un-gated incoming edge to the problem node.

## Common mistakes

- **Generating an ability with no matching gate.** "Time slow" sounds cool. If no edge in the World Graph requires `time_slow`, it does nothing structural. Either gate something with it or remove it.
- **Circular lockouts.** The ability you need to reach the node where you acquire the ability. Always trace acquisition order against the graph before shipping.
- **Generic ability naming.** "double_jump" is fine as an ID; the description should not also say "the player can jump twice." Theme it from the Input.
- **Too many combat abilities.** Combat abilities are easy to over-generate because they're fun to imagine. Stay disciplined: 0–2 max for a typical Controidvania.
- **Abilities with no structural role pretending to be cosmetic.** If an ability doesn't gate anything *and* doesn't change moment-to-moment play, it's clutter. Remove it.
