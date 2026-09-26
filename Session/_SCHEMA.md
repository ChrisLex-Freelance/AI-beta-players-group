---
id: session-schema
name: "Session log schema"
version: 1
scope: "Session/<session-id>/_SESSION.md, Session/<session-id>/round-*.md"
---

# Session schema — field definitions

Objectif : chaque session est **lisible par un humain** (prose des tours de jeu) ET **auditable par machine** (frontmatter YAML structuré). All data values are English; prose may stay in French.

## Structure de dossiers

```
Session/
  <session-id>/            # ex: 2026-09-26-playtest-01
    _SESSION.md            # métadonnées globales + bilan
    round-001.md            # un fichier par tour de jeu
    round-002.md
    summary.md              # synthèse finale (audit)
```

## `_SESSION.md` — champs du frontmatter

| Field | Type | Required | Definition | Allowed values |
|---|---|---|---|---|
| `id` | string | ✅ | Session slug, matches folder name. | `<date>-<slug>` |
| `date` | date | ✅ | ISO date of the session run. | YYYY-MM-DD |
| `status` | enum | ✅ | Lifecycle of the session. | `planned` \| `running` \| `completed` \| `aborted` |
| `scenario_ref` | path | ✅ | Scenario entry point. | `../../Scenario/00-synopsis.md` |
| `gm_persona_ref` | path | ✅ | GM persona used. | `../../Players/gm-male.md` |
| `players` | object[] | ✅ | Table seats. | list (see below) |
| `rules_system` | string | ✅ | Game system used. | free (e.g. `dnd5e`, `call-of-cthulhu`, `homebrew`) |
| `rules_profile` | enum | ✅ | How dice/rules were handled. | `raw` \| `cinematic` \| `homebrew` \| `diceless` |
| `max_rounds` | int | ✅ | Round budget set by the orchestrator. | ≥ 1 |
| `rounds_played` | int | ⬜ | Actual count — filled at end of run. | ≥ 0 |
| `scenario_version` | string | ⬜ | Git SHA / tag of the tested scenario. | free |
| `orchestrator` | string | ⬜ | Script/tool that ran the session. | free |
| `started_at` / `ended_at` | datetime | ⬜ | Run timestamps (ISO 8601). | free |
| `outcome` | enum | ⬜ | Final state — filled at end of run. | `scenario-complete` \| `tpk` \| `early-exit` \| `off-rails` \| `timeout` |

### Object `players[]`

| Field | Type | Required | Definition |
|---|---|---|---|
| `seat` | int | ✅ | Seat / initiative order (1–4). |
| `player_persona_ref` | path | ✅ | Persona file. |
| `character_ref` | path | ✅ | Character sheet. |
| `character_name` | string | ✅ | In-fiction name (fast read in logs). |
| `status` | enum | ⬜ | `alive` \| `dead` \| `unconscious` \| `absent` — updated during play. |

## `round-NNN.md` — champs du frontmatter

| Field | Type | Required | Definition | Allowed values |
|---|---|---|---|---|
| `id` | string | ✅ | `<session-id>-round-NNN`. | free |
| `round` | int | ✅ | Round number, zero-padded in filename. | ≥ 1 |
| `phase` | enum | ✅ | What kind of round this was. | `scene-setup` \| `exploration` \| `dialogue` \| `combat` \| `downtime` \| `climax` \| `debrief` |
| `scene_ref` | path | ⬜ | Scenario scene this round maps to (audit rail). | `../../Scenario/actes/acte-1/scene-03.md` |
| `location` | string | ⬜ | In-fiction location of the party. | free |
| `gm_persona_ref` | path | ✅ | GM persona that narrated this round. | path |
| `dice_rolls` | object[] | ⬜ | Mechanical rolls made this round. | list (see below) |
| `flags_raised` | string[] | ⬜ | Test flags observed this round (audit). | free |
| `off_rails` | boolean | ⬜ | `true` if players left the scenario's written path. | `true` \| `false` |
| `duration_weight` | float | ⬜ | Relative cost of the round (prompt size / time). | ≥ 0 |

### Object `dice_rolls[]`

| Field | Type | Required | Definition |
|---|---|---|---|
| `actor` | string | ✅ | `gm` or character id (e.g. `tonnerre`). |
| `roll` | string | ✅ | Dice notation, e.g. `d20+5`. |
| `result` | int | ✅ | Total rolled. |
| `purpose` | string | ✅ | What the roll was for (e.g. `persuasion-gate`, `attack`, `perception-trap`). |
| `success` | enum | ✅ | `critical-success` \| `success` \| `failure` \| `critical-failure` |

## Corps du fichier (prose)

Après le frontmatter, chaque `round-NNN.md` suit toujours ce plan :

```markdown
## Narration MJ
<description de la scène vue par la table>

## Actions des joueurs
### <Character Name> (joueur : <persona name>)
<intention déclarée + dialogue>

## Résolution MJ
<arbitrage, conséquences, jets mentionnés dans dice_rolls>

## Fin du tour
<état du monde à la fin du tour, cliffhanger éventuel>
```

## Règles d'usage pour l'orchestrateur

1. **Reprise** : pour relancer une session `running`, lire `_SESSION.md` + le dernier `round-*.md` (frontmatter `phase`, `location`, état `players[]`) comme point de reprise.
2. **Audit** : les champs `scene_ref`, `off_rails` et `flags_raised` permettent de reconstruire la trajectoire : quelles scènes ont été jouées, lesquelles ont été sautées, où le scénario a cassé.
3. **Interdits** : aucun champ `[meta]` de Players/ ne doit fuiter dans les logs de session ; les logs ne contiennent que ce qui a été dit/publié à table.
4. L'orchestrateur écrit le frontmatter ; les LLM écrivent uniquement la prose des sections.
