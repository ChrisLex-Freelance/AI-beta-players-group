---
id: persona-schema
name: "Schéma des personas Players"
version: 1
scope: "Players/*.md"
---

# Schéma des personas — définitions des champs

Ce fichier définit chaque champ du frontmatter YAML des personas (`Players/*.md`).
Un orchestrateur doit **toujours filtrer** les champs marqués `[méta]` avant d'injecter le persona dans un prompt LLM.

## Champs communs (players et MJ)

| Champ | Type | Obligatoire | Définition | Valeurs normées |
|---|---|---|---|---|
| `id` | string | ✅ | Identifiant unique, stable, utilisé par les logs de session. | `player-1` … `player-8`, `gm-male`, `gm-female` |
| `name` | string | ✅ | Prénom du persona. | libre |
| `gender` | enum | ✅ | Genre du persona. | `masculin` \| `feminin` \| `autre` |
| `pronouns` | string | ✅ | Pronoms utilisés dans les échanges. | `"il"`, `"elle"`, `"iel"` |
| `age` | int | ✅ | Âge du persona (influence le style de jeu). | 18–70 |
| `role` | enum | ✅ | Rôle à table. | `player` \| `game-master` |
| `traits` | string[] | ✅ | 2–4 traits de personnalité observables à table. | libre |
| `testing_goals` | string[] | ✅ | **[méta]** Ce que ce persona doit révéler du scénario. | libre |
| `notes` | string | ⬜ | **[méta]** Notes de l'auteur sur ce persona. | libre |

## Champs spécifiques aux players

| Champ | Type | Obligatoire | Définition | Valeurs normées |
|---|---|---|---|---|
| `play_style` | enum | ✅ | Style de jeu dominant. | `optimizer` (min-max) \| `roleplayer` (drama social) \| `rule-lawyer` (littéraliste des règles) \| `casse-cou` (risque-tout) \| `explorateur` (systématique, cartographie) \| `chaotique` (imprévisible, troll) \| `prudent` (évite le conflit) \| `spectateur` (suit le flux) |
| `experience` | enum | ✅ | Ancienneté avec les JDR. | `debutant` \| `occasionnel` \| `regulier` \| `veteran` |
| `energy` | float | ✅ | Niveau d'initiative/loquacité à table (0 = muet, 1 = meneur de discussion). | 0.0–1.0 |
| `rule_compliance` | enum | ✅ | Tendance à rester sur les rails du scénario. | `low` (teste les sorties de piste) \| `medium` \| `high` (suit les indices) |
| `quirks` | string[] | ✅ | Manies visibles dans le jeu (à injecter dans le prompt). | libre |
| `pet_peeves` | string[] | ✅ | Ce qui le sort du jeu (utile pour tester les scènes faibles). | libre |
| `loves` | string[] | ✅ | Thèmes/mécaniques qu'il poursuit activement. | libre (ex: `intrigue`, `combat`, `exploration`, `mystere`, `drama`, `loot`) |
| `avoids` | string[] | ✅ | Thèmes/mécaniques qu'il fuit ou subit. | idem `loves` |
| `breaking_tendencies` | string[] | ✅ | Comportements récurrents qui « cassent » le scénario. | libre |
| `character_ref` | path | ✅ | Fiche du personnage interprété, chemin relatif au persona. | `../Character/<nom>.md` |

## Champs spécifiques au MJ

| Champ | Type | Obligatoire | Définition | Valeurs normées |
|---|---|---|---|---|
| `narrative_style` | enum | ✅ | Rapport au texte du scénario. | `immersif` (improvise pour servir l'ambiance) \| `fidele` (suit le texte au plus près) \| `sandbox` (laisse les joueurs piloter) \| `railroad` (ramène vers l'intrigue) |
| `rules_arbitration` | enum | ✅ | Rapport aux règles. | `raw` (applique à la lettre) \| `cinematique` (règle pour le drama) \| `homebrew` (variantes maison) |
| `pacing` | enum | ✅ | Rythme de la table. | `lent` (descriptions longues) \| `equilibre` \| `rapide` (scènes courtes, action) |
| `secrets_keeping` | enum | ✅ | Capacité à ne pas divulguer les secrets du scénario. | `low` \| `medium` \| `high` |
| `prep_style` | enum | ✅ | Type de préparation. | `improvise` \| `prepare-actes` \| `prepare-tout` |
| `voice` | string | ✅ | Registre de narration (1 phrase). | libre |

## Règles d'usage pour l'orchestrateur

1. **Injection prompt joueur** : `name, pronouns, age, play_style, experience, energy, rule_compliance, traits, quirks, loves, avoids` + fiche `character_ref`.
2. **Injection prompt MJ** : champs communs + champs spécifiques MJ.
3. **Interdits** : jamais `testing_goals`, `notes`, ni `breaking_tendencies` brut — `breaking_tendencies` guide la *génération* du persona par le moteur, pas le prompt.
4. `energy` sert à pondérer l'ordre/la fréquence de prise de parole dans les tours de jeu.
