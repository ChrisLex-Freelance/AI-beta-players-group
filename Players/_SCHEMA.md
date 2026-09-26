---
id: persona-schema
name: "Players persona schema"
version: 2
scope: "Players/*.md"
---

# Persona schema — field definitions

This file defines every frontmatter YAML field of the personas (`Players/*.md`).
All **data values** are in English for machine consistency. Prose (descriptions, notes) may stay in French.
An orchestrator MUST always filter out fields tagged `[meta]` before injecting a persona into an LLM prompt.

## Common fields (players and GMs)

| Field | Type | Required | Definition | Allowed values |
|---|---|---|---|---|
| `id` | string | ✅ | Unique stable identifier, used in session logs. | `player-1` … `player-8`, `gm-male`, `gm-female` |
| `name` | string | ✅ | Persona first name. | free |
| `gender` | enum | ✅ | Persona gender. | `male` \| `female` \| `other` |
| `pronouns` | string | ✅ | Pronouns used in dialogue. | `"he"`, `"she"`, `"they"` |
| `age` | int | ✅ | Persona age (influences play style). | 18–70 |
| `role` | enum | ✅ | Table role. | `player` \| `game-master` |
| `traits` | string[] | ✅ | 2–4 observable table behaviours. | free (prose) |
| `testing_goals` | string[] | ✅ | **[meta]** What this persona should reveal about the scenario. | free |
| `notes` | string | ⬜ | **[meta]** Author notes about this persona. | free |

## Player-specific fields

| Field | Type | Required | Definition | Allowed values |
|---|---|---|---|---|
| `play_style` | enum | ✅ | Dominant play style. | `optimizer` \| `roleplayer` \| `rule-lawyer` \| `daredevil` \| `explorer` \| `chaotic` \| `cautious` \| `spectator` |
| `experience` | enum | ✅ | TTRPG seniority. | `beginner` \| `casual` \| `regular` \| `veteran` |
| `energy` | float | ✅ | Initiative/talkativeness at the table (0 = silent, 1 = leads the discussion). | 0.0–1.0 |
| `rule_compliance` | enum | ✅ | Tendency to stay on scenario rails. | `low` (probes off-rails) \| `medium` \| `high` (follows leads) |
| `quirks` | string[] | ✅ | Visible in-game mannerisms (inject into prompt). | free |
| `pet_peeves` | string[] | ✅ | What pulls them out of the game (useful to probe weak scenes). | free |
| `loves` | string[] | ✅ | Themes/mechanics they actively pursue. | free (e.g. `intrigue`, `combat`, `exploration`, `mystery`, `drama`, `loot`) |
| `avoids` | string[] | ✅ | Themes/mechanics they avoid or endure. | same as `loves` |
| `breaking_tendencies` | string[] | ✅ | Recurring behaviours that "break" the scenario. | free |
| `character_ref` | path | ✅ | Played character sheet, path relative to the persona file. | `../Character/<name>.md` |

## GM-specific fields

| Field | Type | Required | Definition | Allowed values |
|---|---|---|---|---|
| `narrative_style` | enum | ✅ | Relationship to the scenario text. | `immersive` (improvises to serve mood) \| `faithful` (sticks to the text) \| `sandbox` (lets players steer) \| `railroad` (herds back to the plot) |
| `rules_arbitration` | enum | ✅ | Relationship to the rules. | `raw` (applies to the letter) \| `cinematic` (rules for drama) \| `homebrew` (house variants) |
| `pacing` | enum | ✅ | Table rhythm. | `slow` \| `balanced` \| `fast` |
| `secrets_keeping` | enum | ✅ | Ability to keep scenario secrets. | `low` \| `medium` \| `high` |
| `prep_style` | enum | ✅ | Preparation style. | `improviser` \| `per-act` \| `full-prep` |
| `voice` | string | ✅ | Narrative register (one sentence). | free |

## Orchestrator usage rules

1. **Player prompt injection**: `name, pronouns, age, play_style, experience, energy, rule_compliance, traits, quirks, loves, avoids` + the `character_ref` sheet.
2. **GM prompt injection**: common fields + GM-specific fields.
3. **Never inject**: `testing_goals`, `notes`, or raw `breaking_tendencies` — `breaking_tendencies` guides the engine's persona *generation*, not the prompt.
4. Use `energy` to weight turn order / speaking frequency in game rounds.
