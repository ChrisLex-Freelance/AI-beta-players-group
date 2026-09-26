---
id: character-schema
name: "Character sheet schema"
version: 1
scope: "Character/*.md"
---

# Character schema — field definitions

Fiches des personnages interprétés par les players. All data values are English; prose may stay in French.

| Field | Type | Required | Definition | Allowed values |
|---|---|---|---|---|
| `id` | string | ✅ | Character slug (matches file name). | `gardien-du-seuil`, `tonnerre`, … |
| `name` | string | ✅ | In-fiction character name. | free |
| `player_ref` | path | ✅ | Persona playing this character, relative to the sheet. | `../Players/player-1.md` |
| `archetype` | string | ✅ | Class / archetype / role in the party. | free |
| `background` | string | ✅ | One-paragraph history. | free |
| `appearance` | string | ⬜ | Physical description. | free |
| `personality` | string[] | ✅ | 2–4 in-fiction personality traits. | free |
| `goals` | string[] | ✅ | Personal objectives driving decisions. | free |
| `bonds` | string[] | ⬜ | Ties to people, places, organisations. | free |
| `flaws` | string[] | ✅ | Character weaknesses (fuel for the GM). | free |
| `fears` | string[] | ⬜ | What the character dreads. | free |
| `inventory` | string[] | ⬜ | Notable carried items. | free |
| `stats` | object | ⬜ | System-specific numeric block (fill per game system). | free |
| `speaking_style` | string | ⬜ | How the character talks (in-character voice). | free |

## Orchestrator usage rules

1. The whole sheet (except future `[meta]` fields) is injected into the owning player's prompt.
2. `goals` + `flaws` are the primary levers the GM persona uses to hook this character.
