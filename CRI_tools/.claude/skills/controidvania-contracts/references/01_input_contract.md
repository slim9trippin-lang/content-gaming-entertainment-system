# 01 — Input Contract

*Reconstructed from original build session history — recovered flash drive did not contain this file. Content below is restored from chat transcript, not a guess at what it might have said.*

The Input Contract is **controlled abstraction**: a deliberately bounded structured form of the user's creative direction. It's a filter, not a generator. Its job is to constrain everything downstream — if Input is sloppy, Interpretation guesses wildly and the whole pipeline drifts.

## Schema

```json
{
  "theme": "string (required) — the conflict at the heart of the game",
  "setting": "string (required) — world/era/locale",
  "protagonist_archetype": "string (required) — functional descriptor, not character backstory",
  "tone": "string (required) — register/pacing (gritty | whimsical | literary | pulpy | noir | balanced | ...)",
  "core_ability_style": "string (required) — shape of the moveset, not a list of abilities",
  "reference_inspirations": "array of strings (optional) — works the user named",
  "difficulty": "enum: easy | normal | hard",
  "session_length": "enum: short | medium | long"
}
```

## Field rules

### Required fields (no silent defaulting)
`theme`, `setting`, `protagonist_archetype`, `core_ability_style` — if any of these are missing from the user's prose, **ask one clarifying question before generating**. Defaulting these creates a game the user didn't ask for.

### Required fields with defaults
- `tone` defaults to `"balanced"` if not extractable
- `difficulty` defaults to `"normal"`
- `session_length` defaults to `"medium"`

### Optional fields
`reference_inspirations` — capture verbatim if user named anything ("like Hollow Knight"). Empty array if not.

## Extraction guidance

| Cue in user prose | Field it feeds |
|---|---|
| "a game about [X]" | `theme` |
| Geographic/era descriptors | `setting` |
| Character noun + role ("street tactician," "knight") | `protagonist_archetype` |
| Adjectives describing mood ("gritty," "whimsical") | `tone` |
| Verb phrases ("mobility + resource control") | `core_ability_style` |
| Named works ("like Hollow Knight," "Matrix vibe") | `reference_inspirations` |

## Example

User prose: *"I want a Controidvania about surviving in a hostile modern city, where the player is a street-smart kid who can scale buildings and outsmart rivals. Gritty tone, like The Wire but as a game."*

```json
{
  "theme": "survival and power",
  "setting": "hostile modern city",
  "protagonist_archetype": "street tactician",
  "tone": "gritty",
  "core_ability_style": "mobility + resource control",
  "reference_inspirations": ["The Wire"],
  "difficulty": "normal",
  "session_length": "medium"
}
```

## Common mistakes

- **Smuggling ability lists into `core_ability_style`.** "Double jump, dash, wall-climb" belongs in the Ability System contract. Here it's "vertical mobility kit" or similar — a shape, not an inventory.
- **Defaulting required fields silently.** If you can't extract `setting` from the prose, ask. Do not invent one.
