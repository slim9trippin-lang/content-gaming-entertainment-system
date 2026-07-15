# 08 — Room/Tile Contract

This is where the World Graph's abstract nodes become actual traversable space. It's also where Player Controller's numbers stop being decorative — gap distances and platform heights recorded here are what Contract 9 (Validation) will check against `jump_force`, `gravity`, and `speed` to confirm the game is actually beatable.

## Two interpretive calls made here (flag if wrong)

1. **This contract does not self-validate reachability.** It records raw geometry — gap distances, platform heights — but does not compute whether the player can cross them. That check belongs to Contract 9. This follows your existing principle that validation is a separate layer, not folded into generation.
2. **Geometry is hardware-agnostic by default.** Dimensions are in tile counts, not hardware-specific pixel values. An optional `mcp_target` field exists for later binding to a specific retro MCP profile, but this contract doesn't hardcode SNES/Genesis/NES assumptions into the geometry itself — consistent with your intake/output separation principle (hardware constraint capture is a distinct pass from design generation).

## Schema

```json
{
  "rooms": [
    {
      "node_id": "string — must match a World Graph node id, exactly one room per node",
      "layout_type": "enum: platforming | vertical | arena | hub | secret",
      "dimensions": {
        "width_tiles": "integer",
        "height_tiles": "integer"
      },
      "platform_density": "enum: sparse | medium | dense",
      "gaps": [
        {
          "gap_id": "string, unique within this room",
          "horizontal_distance_tiles": "float",
          "vertical_distance_tiles": "float — positive = drop below start height, negative = rise above start height",
          "requires_ability": "string or null — must match a World Graph edge requirement if this gap is the physical form of a gated edge"
        }
      ],
      "hazards": [
        {
          "type": "enum: spikes | pit | crusher | projectile_turret_zone",
          "position_tiles": { "x": "integer", "y": "integer" },
          "damage": "integer"
        }
      ],
      "enemy_placements": [
        {
          "enemy_id": "string — must match an id in Enemy contract whose spawn_nodes includes this node_id",
          "position_tiles": { "x": "integer", "y": "integer" }
        }
      ],
      "mcp_target": "string or null — optional hardware profile binding (e.g. 'SNES'); null means geometry stays generic until a later translation pass"
    }
  ]
}
```

## Generation rules

### layout_type by node type

| World Graph node.type | Default layout_type | Notes |
|---|---|---|
| start | hub | Safe, low platform_density, no hazards or enemy_placements |
| hub | hub | Same safety rules as start |
| traversal | platforming or vertical | Pick vertical if the node's role is height-gain (matches Interpretation's `world_structure: vertical`); platforming otherwise |
| combat | arena | Open geometry, sparse platforms, prioritizes enemy_placements over gaps |
| boss | arena | Larger dimensions than combat arenas; MUST have zero enemy_placements — the boss (defined in Contract 7) occupies this room alone |
| secret | secret | Small, dense platform_density, always contains at least one hazard or a tight gap gating the reward |

### Gap-to-edge coupling

If a World Graph edge has `requirement: <ability_id>` and that edge's traversal is physically represented as a gap in this node's room, the gap's `requires_ability` must carry the same ability_id. This is the field Contract 9 will read to know which gaps are supposed to be crossable pre-ability (should fail if attempted) versus post-ability (should succeed).

Gaps with `requires_ability: null` are expected to be crossable with base Player Controller stats — Contract 9 checks these against baseline `jump_force`/`speed`, not ability-modified values.

### enemy_placements coupling

Every `enemy_placements` entry's `enemy_id` must appear in the Enemy contract's `enemies[]` array with this `node_id` present in that enemy's `spawn_nodes`. The reverse must also hold — if an enemy declares this node in its `spawn_nodes`, at least one placement for it must exist here, or the enemy is defined but never actually appears.

### Boss room exclusivity

Boss nodes get their own arena but carry no `enemy_placements` — this was already established in Contract 6 (boss nodes reserved from Enemy `spawn_nodes`) and carries through here as a hard rule, not just a default.

## Common pitfalls

- **Validating reachability here instead of in Contract 9.** Resist the urge — this contract's job is honest measurement, not judgment.
- **Hardcoding an MCP target into every room.** Leave `mcp_target: null` unless there's a specific reason a room is locked to one hardware profile.
- **Orphaned enemy_placements or orphaned spawn_nodes.** Both directions of the coupling need checking, not just one.
- **Boss arenas with leftover enemy_placements** from a copy-paste of a combat arena template.
