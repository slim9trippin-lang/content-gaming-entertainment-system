# 03 — World Graph Contract

*Reconstructed from original build session history — recovered flash drive did not contain this file.*

The World Graph is the **structural backbone** of the entire game. Nodes are areas, edges are paths between them, and some edges are *gated* by ability requirements that won't be defined until the Ability System contract. This is the contract where most generation bugs live, so internal validation is critical.

The graph is generated *after* the Interpretation Layer is locked because its shape depends on Interpretation's `world_structure` field.

## Schema

```json
{
  "nodes": [
    {
      "id": "string (e.g. node_01) — unique",
      "type": "enum: start | traversal | combat | boss | hub | secret",
      "biome": "string — thematic descriptor (e.g. 'rooftops', 'sewers', 'cathedral')",
      "difficulty": "integer 1–5",
      "connections": "array of node IDs (informational; the source of truth is edges[])",
      "gates": "array of ability IDs that gate this node's incoming paths (informational)",
      "rewards": "array of strings — what the player gets here (ability ID, lore, currency, etc.)"
    }
  ],
  "edges": [
    {
      "from": "string (node ID)",
      "to": "string (node ID)",
      "requirement": "string — either 'none' or an ability ID from the Ability System contract",
      "bidirectional": "boolean (default true) — false for one-way shortcuts"
    }
  ],
  "start_node": "string (node ID)",
  "end_node": "string (node ID)"
}
```

## Generation rules

### Node count

Target 10–15 nodes. Fewer than 10 makes the game feel cramped; more than 15 makes the design hard to balance and the graph hard to validate. Sweet spot: 12.

### Gate density

Too few gates → linear game. Too many → backtracking hell. Balance gated edges against total edge count — most nodes should have at least one un-gated path in, even if other paths are gated.

### Loops and shortcuts

Include at least 2 loops (paths that return the player to a previously-visited area) and at least 1 one-way shortcut (`bidirectional: false`) that activates later for fast travel.

### `start_node` and `end_node`

- `start_node`: must be a `start`-typed node, must have at least one outgoing edge with `requirement: "none"`.
- `end_node`: typically a `boss`-typed node. Must be reachable from `start_node` via some ability-acquisition order.

## Example fragment — dense urban world

```json
{
  "nodes": [
    {"id": "node_01", "type": "start", "biome": "abandoned subway platform", "difficulty": 1, "connections": ["node_02"], "gates": [], "rewards": []},
    {"id": "node_02", "type": "hub", "biome": "subway concourse", "difficulty": 1, "connections": ["node_01", "node_03", "node_04"], "gates": [], "rewards": ["save_point", "merchant"]},
    {"id": "node_03", "type": "traversal", "biome": "service tunnels", "difficulty": 2, "connections": ["node_02", "node_05"], "gates": [], "rewards": []},
    {"id": "node_04", "type": "combat", "biome": "ground-level alley", "difficulty": 2, "connections": ["node_02", "node_06"], "gates": [], "rewards": []},
    {"id": "node_05", "type": "boss", "biome": "rooftop", "difficulty": 3, "connections": ["node_03", "node_07"], "gates": ["wall_climb"], "rewards": ["double_jump"]},
    {"id": "node_06", "type": "traversal", "biome": "fire escape network", "difficulty": 2, "connections": ["node_04", "node_07"], "gates": ["wall_climb"], "rewards": []},
    {"id": "node_07", "type": "hub", "biome": "penthouse safe house", "difficulty": 3, "connections": ["node_05", "node_06", "node_08"], "gates": [], "rewards": ["save_point", "wall_climb"]},
    {"id": "node_08", "type": "traversal", "biome": "skyscraper interior", "difficulty": 3, "connections": ["node_07", "node_09"], "gates": ["double_jump"], "rewards": []},
    {"id": "node_09", "type": "boss", "biome": "skyscraper rooftop", "difficulty": 4, "connections": ["node_08", "node_10"], "gates": [], "rewards": ["grapple"]},
    {"id": "node_10", "type": "secret", "biome": "underground vault", "difficulty": 3, "connections": ["node_02"], "gates": ["grapple"], "rewards": ["lore_fragment", "currency_cache"]},
    {"id": "node_11", "type": "traversal", "biome": "rival territory", "difficulty": 4, "connections": ["node_09", "node_12"], "gates": [], "rewards": []},
    {"id": "node_12", "type": "boss", "biome": "kingpin penthouse", "difficulty": 5, "connections": ["node_11"], "gates": ["grapple"], "rewards": ["ending"]}
  ],
  "edges": [
    {"from": "node_01", "to": "node_02", "requirement": "none", "bidirectional": true},
    {"from": "node_02", "to": "node_03", "requirement": "none", "bidirectional": true},
    {"from": "node_02", "to": "node_04", "requirement": "none", "bidirectional": true},
    {"from": "node_03", "to": "node_05", "requirement": "wall_climb", "bidirectional": true},
    {"from": "node_04", "to": "node_06", "requirement": "none", "bidirectional": true},
    {"from": "node_06", "to": "node_07", "requirement": "wall_climb", "bidirectional": true},
    {"from": "node_05", "to": "node_07", "requirement": "none", "bidirectional": true},
    {"from": "node_07", "to": "node_08", "requirement": "double_jump", "bidirectional": true},
    {"from": "node_08", "to": "node_09", "requirement": "none", "bidirectional": true},
    {"from": "node_09", "to": "node_10", "requirement": "grapple", "bidirectional": false},
    {"from": "node_10", "to": "node_02", "requirement": "none", "bidirectional": false},
    {"from": "node_09", "to": "node_11", "requirement": "none", "bidirectional": true},
    {"from": "node_11", "to": "node_12", "requirement": "grapple", "bidirectional": true}
  ],
  "start_node": "node_01",
  "end_node": "node_12"
}
```

Note: this example references `wall_climb`, `double_jump`, and `grapple` as edge requirements. These ability IDs **must exist** in the Ability System contract. Cross-validation enforces this — see SKILL.md's cross-contract validation step.

## Per-contract validation

- [ ] At least 10 nodes, at most 15
- [ ] Exactly one `start` node, at least one `boss` node
- [ ] `start_node` and `end_node` both reference real node IDs
- [ ] `start_node` has at least one outgoing edge with `requirement: "none"`
- [ ] No orphan nodes (every node ID appears in at least one edge)
- [ ] No duplicate node IDs
- [ ] All edge `from` and `to` reference real node IDs
- [ ] At least 2 loops in the graph
- [ ] At least 1 one-way edge (`bidirectional: false`) for shortcut
- [ ] Difficulty progression broadly increases with distance from start

## Common mistakes

- **Forgetting to include unlocks at boss nodes.** Every boss should drop an ability via `rewards`. Otherwise bosses are just obstacles, not progression.
- **Over-gating early areas.** Players need at least 2–3 nodes of mobility before the first gated path or the game feels claustrophobic.
- **Generating gates that lock the player out of the start area.** Verify that backtracking to `start_node` is always possible. (Save points and merchants often live in or near `start_node`'s cluster.)
- **Naming edge requirements that won't exist in the Ability contract.** If you use `requirement: "wall_climb"`, the Ability System contract must include an ability with `id: "wall_climb"`. This is what cross-validation catches.
- **Creating `secret` nodes with no reward.** If a secret has nothing in `rewards[]`, it's a dead end. Either populate the reward or remove the node.
