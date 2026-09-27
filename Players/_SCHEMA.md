---
id: persona-schema
name: "Players persona schema"
version: 3
scope: "Players/*.md"
---

# Persona schema — field definitions

This file defines every frontmatter YAML field of the personas (`Players/*.md`).
All **data values** are in English for machine consistency. Prose (descriptions, notes) may stay in French.
An orchestrator MUST always filter out fields tagged `[meta]` before injecting a persona into an LLM prompt.

**Important** : chaque valeur normée ci-dessous a une définition **stricte**. Un agent LLM doit interpréter la donnée exactement selon cette définition et **ne jamais extrapoler** au-delà (pas d'inférence de traits non listés, pas de fusion de valeurs).

## Common fields (players and GMs)

| Field | Type | Required | Definition | Allowed values |
|---|---|---|---|---|
| `id` | string | ✅ | Unique stable identifier, used in session logs. Never changes once created. | `player-1` … `player-8`, `gm-male`, `gm-female` |
| `name` | string | ✅ | Persona first name. Fictional; no real-person reference. | free |
| `gender` | enum | ✅ | Persona gender. See dictionary below. | `male` \| `female` \| `other` |
| `pronouns` | string | ✅ | Pronouns used in dialogue and narration. Must match `gender` unless `other`. | `"he"`, `"she"`, `"they"` |
| `age` | int | ✅ | Persona age in years. Influences tone and references, NOT play style by itself. | 18–70 |
| `role` | enum | ✅ | Table role. See dictionary below. | `player` \| `game-master` |
| `traits` | string[] | ✅ | 2–4 observable table behaviours (what others SEE them do), not inner psychology. | free (prose) |
| `testing_goals` | string[] | ✅ | **[meta]** What this persona should reveal about the scenario. Never injected. | free |
| `notes` | string | ⬜ | **[meta]** Author notes about this persona. Never injected. | free |

## Player-specific fields

| Field | Type | Required | Definition | Allowed values |
|---|---|---|---|---|
| `play_style` | enum | ✅ | Dominant play style — the DEFAULT approach, not an exclusive one. A daredevil can still talk; they simply default to risk. See dictionary. | 8 values, see dictionary |
| `experience` | enum | ✅ | TTRPG seniority — affects rules knowledge and self-confidence, NOT intelligence. See dictionary. | 4 values, see dictionary |
| `energy` | float | ✅ | Initiative/talkativeness at the table. Weight for turn order and speaking frequency; NOT charisma or competence. | 0.0–1.0 |
| `rule_compliance` | enum | ✅ | Tendency to stay on the scenario's intended path. See dictionary. | `low` \| `medium` \| `high` |
| `quirks` | string[] | ✅ | Visible in-game mannerisms (inject into prompt). | free |
| `pet_peeves` | string[] | ✅ | What pulls them out of the game. | free |
| `loves` | string[] | ✅ | Themes/mechanics they actively pursue. | free keywords |
| `avoids` | string[] | ✅ | Themes/mechanics they avoid or endure. Must NOT overlap with `loves`. | free keywords |
| `breaking_tendencies` | string[] | ✅ | Recurring behaviours that "break" the scenario. Guides engine generation; never injected raw. | free |
| `character_ref` | path | ✅ | Played character sheet, relative to this persona file. | `../Character/<name>.md` |

## GM-specific fields

| Field | Type | Required | Definition | Allowed values |
|---|---|---|---|---|
| `narrative_style` | enum | ✅ | Relationship to the scenario text. See dictionary. | 4 values, see dictionary |
| `rules_arbitration` | enum | ✅ | Relationship to the rules. See dictionary. | 3 values, see dictionary |
| `pacing` | enum | ✅ | Table rhythm — how long scenes run before being moved along. See dictionary. | `slow` \| `balanced` \| `fast` |
| `secrets_keeping` | enum | ✅ | Ability to keep scenario secrets unrevealed until earned. See dictionary. | `low` \| `medium` \| `high` |
| `prep_style` | enum | ✅ | Preparation depth. See dictionary. | 3 values, see dictionary |
| `voice` | string | ✅ | Narrative register (one sentence). | free |

---

# Dictionnaire des valeurs normées

Chaque valeur est définie **strictement**. Interprétation = la définition, rien de plus. En cas d'ambiguïté, l'agent doit s'en tenir au sens littéral ci-dessous et aux `traits`/`quirks` explicitement listés.

## `gender`

- `male` — persona masculin. Pronoms `"he"`.
- `female` — persona féminin. Pronoms `"she"`.
- `other` — persona non binaire / non précisé. Pronoms `"they"` sauf indication contraire. **N'implique AUCUN trait de personnalité.**

## `role`

- `player` — défend l'intérêt de SON personnage ; n'a aucune connaissance des coulisses ; peut négocier avec le MJ mais jamais arbitrer.
- `game-master` — arbitre et narre le monde ; ne joue AUCUN personnage joueur ; est la seule source de vérité sur l'état du monde.

## `play_style`

- `optimizer` — prend les décisions qui maximisent la réussite mécanique (builds, combos, probabilités). **Ne signifie PAS** : égoïste, anti-rôle, ou hostile à l'improvisation.
- `roleplayer` — privilégie le dialogue, les relations et la cohérence interne du personnage. **Ne signifie PAS** : mauvais en combat, timide, ou désintéressé des règles.
- `rule-lawyer` — cite et exige l'application exacte des règles écrites ; conteste l'arbitrage non fondé. **Ne signifie PAS** : mauvais joueur, malveillant, ou opposé à toute règle maison **annoncée**.
- `daredevil` — par défaut, agit avant d'évaluer le risque ; accepte l'échec comme partie du jeu. **Ne signifie PAS** : suicidaire, stupid, ou destructeur intentionnel.
- `explorer` — fouille, cartographie, interroge le décor de façon systématique avant d'avancer. **Ne signifie PAS** : lent, peureux, ou collectionneur compulsif.
- `chaotic` — fait des choix surprenants/amusants, priorise le fun sur l'efficacité. **Ne signifie PAS** : trolls la table, sabote les autres joueurs, ou agit au hasard pur.
- `cautious` — évalue le risque, préfère l'option sûre, demande des confirmations. **Ne signifie PAS** : inutile, lâche, ou muet.
- `spectator` — suit le flux, réagit plutôt qu'initie. **Ne signifie PAS** : démotivé, sur-numéraire, ou incompétent.

## `experience`

- `beginner` — connaît à peine les règles de base ; le MJ doit clarifier les mécaniques ; peut mal formuler une action (l'intention reste valide).
- `casual` — joue rarement ; connaît les bases mais pas les subtilités ; confiance variable.
- `regular` — joue souvent ; maîtrise les règles courantes ; autonome sauf cas edge.
- `veteran` — connaît le système en profondeur, y compris ses angles morts ; s'attend à une application cohérente des règles.

## `energy` (0.0–1.0)

- `0.0–0.3` — parle rarement sans être sollicité ; agit quand c'est son tour ; n'interpelle jamais le premier.
- `0.4–0.6` — participe normalement ; initie occasionnellement ; suit les autres si besoin.
- `0.7–1.0` — initie souvent ; pose des questions au MJ ; peut occuper l'espace conversationnel.
- **N'implique PAS** : charisme, leadership, compétence, ou volume de la voix. C'est une **fréquence de prise de parole**, pas un volume.

## `rule_compliance`

- `low` — cherche activement les sorties de piste : options que le scénario n'a pas prévues ; interprète les indices au second degré ; teste les frontières.
- `medium` — suit la piste principale si elle est claire, mais s'attarde sur le contenu secondaire et peut s'en écarter si motivé.
- `high` — suit les indices donnés ; prend la quête au premier degré ; ne cherche PAS les angles morts.
- **N'implique PAS** (dans les trois cas) : obéissance au MJ, honnêteté, ou qualité du roleplay.

## `narrative_style` (GM)

- `immersive` — enrichit le texte avec improvisation sensorielle ; adaptera une scène maigre pour servir l'ambiance.
- `faithful` — reste au plus près du texte écrit ; signale plutôt qu'improvise si le texte manque.
- `sandbox` — laisse les joueurs piloter ; le scénario est un décor, pas un itinéraire.
- `railroad` — ramène activement la table vers l'intrigue prévue ; ajuste les événements pour la maintenir sur les rails.

## `rules_arbitration` (GM)

- `raw` — applique les règles à la lettre, y compris quand le résultat est absurde ou fatal.
- `cinematic` — règle en faveur du drama ; peut écarter une règle si elle casse la scène.
- `homebrew` — applique des variantes maison **annoncées** ; sinon se comporte comme `raw` ou `cinematic` selon les variantes.

## `pacing` (GM)

- `slow` — descriptions longues, laisse les silences s'installer ; une même scène peut tenir plusieurs rounds.
- `balanced` — alterne description et action selon la tension de la scène.
- `fast` — scènes courtes, résume le temps mort, pousse vers la décision suivante.

## `secrets_keeping` (GM)

- `low` — laisse fuiter des indices par excès de zèle narratif (révèle prématurément un secret si la scène le suggère).
- `medium` — garde les secrets sauf erreur d'inattention sous pression.
- `high` — ne révèle JAMAIS un secret avant qu'il ne soit gagné par les joueurs ; trouve des réponses de repli.

## `prep_style` (GM)

- `improviser` — part du synopsis et improvise le reste ; les scènes détaillées sont des amorces, pas des scripts.
- `per-act` — prépare chaque acte avant de le jouer ; improvise à l'intérieur d'un acte.
- `full-prep` — prépare chaque scène ; improvise seulement quand les joueurs forcent.

---

## Règles d'interprétation pour les agents LLM

1. **Littéral strict** : la valeur signifie exactement sa définition ci-dessus. Toute nuance supplémentaire doit venir des champs prose (`traits`, `quirks`, `pet_peeves`, `loves`, `avoids`) — jamais de la valeur d'enum.
2. **Pas de fusion** : `daredevil` + `veteran` ne crée pas un nouveau style ; les deux s'appliquent indépendamment (connais les risques, les prend quand même).
3. **Pas d'extrapolation d'axe** : `energy` ne dit rien de la compétence ; `experience` ne dit rien de la personnalité ; `rule_compliance` ne dit rien de la loyauté envers le MJ.
4. **Défaut = non-défini** : si un comportement n'est décrit ni par l'enum ni par la prose, l'agent reste neutre sur ce point (comportement "joueur raisonnable moyen").

## Orchestrator usage rules

1. **Player prompt injection**: `name, pronouns, age, play_style, experience, energy, rule_compliance, traits, quirks, loves, avoids` + the `character_ref` sheet.
2. **GM prompt injection**: common fields + GM-specific fields.
3. **Never inject**: `testing_goals`, `notes`, or raw `breaking_tendencies` — `breaking_tendencies` guides the engine's persona *generation*, not the prompt.
4. Use `energy` to weight turn order / speaking frequency in game rounds.
5. **Inject ce dictionnaire** (ou la section pertinente) dans le prompt système de chaque agent avec son persona, pour que les valeurs soient interprétées selon la définition et non extrapolées.
