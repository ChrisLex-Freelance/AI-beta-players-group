---
id: session-schema
name: "Session log schema"
version: 2
scope: "Session/<session-id>/_SESSION.md, Session/<session-id>/round-*.md"
---

# Session schema — field definitions

Objectif : chaque session est **lisible par un humain** (prose des tours de jeu) ET **auditable par machine** (frontmatter YAML structuré). All data values are English; prose may stay in French.

**Important** : chaque valeur normée ci-dessous a une définition **stricte**. Un agent LLM doit interpréter la donnée exactement selon cette définition et **ne jamais extrapoler** au-delà.

## Structure de dossiers

```
Session/
  <session-id>/            # ex: 2026-09-26-playtest-01
    _SESSION.md            # métadonnées globales + bilan
    round-001.md            # un fichier par tour de jeu
    summary.md              # synthèse finale (audit)
```

## `_SESSION.md` — champs du frontmatter

| Field | Type | Required | Definition | Allowed values |
|---|---|---|---|---|
| `id` | string | ✅ | Session slug, matches folder name. Format `<date>-<slug>`. | free |
| `date` | date | ✅ | ISO date of the session run. | YYYY-MM-DD |
| `status` | enum | ✅ | Lifecycle of the session. See dictionary. | 4 values, see dictionary |
| `scenario_ref` | path | ✅ | Scenario entry point. | path |
| `gm_persona_ref` | path | ✅ | GM persona used. | path |
| `players` | object[] | ✅ | Table seats. | list (see below) |
| `rules_system` | string | ✅ | Game system used (free-form identifier). | free |
| `rules_profile` | enum | ✅ | How dice/rules were handled. See dictionary. | 5 values, see dictionary |
| `max_rounds` | int | ✅ | Round budget set by the orchestrator. | ≥ 1 |
| `rounds_played` | int | ⬜ | Actual count — filled at end of run. | ≥ 0 |
| `scenario_version` | string | ⬜ | Git SHA / tag of the tested scenario. | free |
| `orchestrator` | string | ⬜ | Script/tool that ran the session. | free |
| `started_at` / `ended_at` | datetime | ⬜ | Run timestamps (ISO 8601). | free |
| `outcome` | enum | ⬜ | Final state — filled at end of run. See dictionary. | 5 values, see dictionary |

### Object `players[]`

| Field | Type | Required | Definition |
|---|---|---|---|
| `seat` | int | ✅ | Seat / initiative order (1–4). |
| `player_persona_ref` | path | ✅ | Persona file. |
| `character_ref` | path | ✅ | Character sheet. |
| `character_name` | string | ✅ | In-fiction name (fast read in logs). |
| `status` | enum | ⬜ | Mirrors `Character` `status`. See Character dictionary. |

## `round-NNN.md` — champs du frontmatter

| Field | Type | Required | Definition | Allowed values |
|---|---|---|---|---|
| `id` | string | ✅ | `<session-id>-round-NNN`. | free |
| `round` | int | ✅ | Round number, zero-padded in filename. | ≥ 1 |
| `phase` | enum | ✅ | What kind of round this was. See dictionary. | 7 values, see dictionary |
| `scene_ref` | path | ⬜ | Scenario scene this round maps to (audit rail). | path |
| `location_ref` | path | ⬜ | Location sheet this round takes place in. | path |
| `location` | string | ⬜ | In-fiction location of the party (fast read). | free |
| `gm_persona_ref` | path | ✅ | GM persona that narrated this round. | path |
| `dice_rolls` | object[] | ⬜ | Mechanical rolls made this round. | list |
| `flags_raised` | string[] | ⬜ | Test flags observed this round (audit). | free |
| `off_rails` | boolean | ⬜ | `true` if players left the scenario's written path. | `true` \| `false` |
| `duration_weight` | float | ⬜ | Relative cost of the round. | ≥ 0 |

### Object `dice_rolls[]`

| Field | Type | Required | Definition |
|---|---|---|---|
| `actor` | string | ✅ | `gm` or character id. |
| `roll` | string | ✅ | Dice notation, e.g. `d20+5`. |
| `result` | int | ✅ | Total rolled (dice + modifiers). |
| `purpose` | string | ✅ | What the roll was for. |
| `success` | enum | ✅ | See dictionary. |

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

---

# Dictionnaire des valeurs normées

## `status` (session)

- `planned` — configurée, pas encore lancée ; aucune prose de round n'existe.
- `running` — en cours OU interrompue temporairement ; reprise possible depuis le dernier round.
- `completed` — menée au terme prévu (`max_rounds` ou fin de scénario) ; bilan écrit dans `summary.md`.
- `aborted` — arrêtée volontairement avant terme (bug, scénario cassé) ; `rounds_played` documente jusqu'où c'est allé. **Un aborted n'est PAS un completed** : ne pas générer de `summary` de synthèse définitive sans le signaler.

## `rules_profile`

- `raw` — jets réels simulés, règles appliquées à la lettre ; `cinematic` — jets présents mais arbitré pour le drama.
- `homebrew` — variantes maison documentées (dans `Game-master/`) ; `diceless` — aucune mécanique de jet ; résolution purement narrative.
- `mixed` — profil changeant en cours de session (à éviter sauf test du rapport règles/récit).

## `outcome`

- `scenario-complete` — les joueurs ont atteint une fin écrite du scénario (pas forcément la "bonne").
- `tpk` — total party kill : tous les personnages sont `dead`/`unconscious` final.
- `early-exit` — fin avant `max_rounds` sans fin de scénario (retraite, abandon de quête) — **différent de `aborted` (décision hors-jeu)**.
- `off-rails` — la table a quitté toute trajectoire écrite ; la suite est de l'improvisation pure.
- `timeout` — `max_rounds` atteint sans conclusion ; `summary.md` note où en était la table.

## `phase` (round)

- `scene-setup` — présentation d'une nouvelle scène/lieu ; les joueurs écoutent, pas d'action résolue.
- `exploration` — fouille/déplacement/investigation du décor, sans combat ni conversation centrale.
- `dialogue` — échange verbal central (PNJ, PJ entre eux, négociation).
- `combat` — conflit ouvert mécanisé ; jets d'attache/défense central.
- `downtime` — repos/préparation/voyage hors tension ; le temps avance sans risque.
- `climax` — scène de résolution majeure (boss, révélation, choix décisif).
- `debrief` — bilan de fin de session (humain/audit), hors fiction. **Un debrief n'est jamais mélangé avec une phase de fiction.**

## `dice_rolls[].success`

- `critical-success` — le meilleur résultat possible du système, avec effet spécial prévu.
- `success` — l'objectif du jet est atteint ; `failure` — l'objectif n'est PAS atteint, sans catastrophe supplémentaire.
- `critical-failure` — le pire résultat possible, avec complication prévue par le système.
- **Strict** : une complication sur un `failure` simple est de l'extrapolation interdite ; elle exige un `critical-failure` (ou une décision du MJ documentée dans la prose de Résolution MJ).

## `players[].status`

Reprend strictement le dictionnaire du `Character/_SCHEMA.md` (`active`, `injured`, `unconscious`, `dying`, `dead`, `absent`) — voir ce fichier. Les deux valeurs doivent rester synchrones entre la fiche personnage et le log de session.


---

# Règles d'interprétation pour les agents LLM

1. **Littéral strict** : la valeur signifie exactement sa définition ci-dessus. Toute nuance supplémentaire doit venir des champs prose — jamais de la valeur d'enum.
2. **Pas de fusion** : deux valeurs combinées ne créent pas un nouveau comportement ; elles s'appliquent indépendamment.
3. **Pas d'extrapolation d'axe** : un champ ne dit rien des axes qu'il ne décrit pas (ex. `severity` ne dit rien de la fréquence).
4. **Défaut = non-défini** : si un comportement/état n'est décrit ni par l'enum ni par la prose, l'agent reste neutre sur ce point.


## Règles d'usage pour l'orchestrateur

1. **Reprise** : pour relancer une session `running`, lire `_SESSION.md` + le dernier `round-*.md` (frontmatter `phase`, `location`/`location_ref`, état `players[]`) comme point de reprise.
2. **Audit** : les champs `scene_ref`, `location_ref`, `off_rails` et `flags_raised` permettent de reconstruire la trajectoire : quelles scènes ont été jouées, lesquelles ont été sautées, où le scénario a cassé.
3. **Interdits** : aucun champ `[meta]` de Players/ ou Location/ ne doit fuiter dans les logs de session ; les logs ne contiennent que ce qui a été dit/publié à table.
4. L'orchestrateur écrit le frontmatter ; les LLM écrivent uniquement la prose des sections.
5. **Injecter le dictionnaire** (ou la section pertinente) dans le prompt système de chaque agent pour que les valeurs soient interprétées selon la définition et non extrapolées.
