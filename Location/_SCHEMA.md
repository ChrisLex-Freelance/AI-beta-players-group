---
id: location-schema
name: "Generic location schema"
version: 2
scope: "Location/*.md"
setting_agnostic: true
---

# Generic location schema — field definitions

Fiche de lieu **agnostique du setting** (fantasy, contemporain, SF, horreur...).
Le champ `kind` est un vocabulaire **suggéré, non fermé** ; le bloc `setting_block` (libre) accueille ce qui est propre à l'univers.
All data values are English; prose may stay in French.

**Important** : chaque valeur normée ci-dessous a une définition **stricte**. Un agent LLM doit interpréter la donnée exactement selon cette définition et **ne jamais extrapoler** au-delà.

## Champs génériques

| Field | Type | Required | Definition | Allowed values |
|---|---|---|---|---|
| `id` | string | ✅ | Location slug, matches file name. Never changes once created. | `<slug>` |
| `name` | string | ✅ | In-fiction name. | free |
| `kind` | enum* | ✅ | Type of place. Vocabulary **suggested, not closed**. See dictionary. | see dictionary |
| `setting` | enum | ✅ | Which universe this location belongs to. See dictionary. | 7 values, see dictionary |
| `parent_ref` | path | ⬜ | Containing location (`parent_ref` is the source of truth for nesting). | `../Location/<slug>.md` |
| `children_refs` | path[] | ⬜ | Known sub-locations (informational mirror). | list |
| `tags` | string[] | ⬜ | Free keywords. | free |
| `description` | string | ✅ | What players perceive on arrival. | free |
| `atmosphere` | enum | ⬜ | Emotional register the GM should convey. See dictionary. | 8 values, see dictionary |
| `access` | enum | ⬜ | How open the place is. See dictionary. | 5 values, see dictionary |
| `entry_points` | string[] | ⬜ | Named ways in/out. | free |
| `features` | object[] | ⬜ | Points of interest. | list |
| `npcs` | object[] | ⬜ | Beings on site. | list |
| `secrets` | object[] | ⬜ | **[meta — GM only]** Hidden contents/truths. | list |
| `hazards` | object[] | ⬜ | Dangers, obstacles. | list |
| `state` | object | ⬜ | Mutable world-state written by the orchestrator. | object |
| `setting_block` | object | ⬜ | **Setting-specific data**. Injected verbatim, never interpreted. | free |
| `notes` | string | ⬜ | **[meta]** Author notes — never injected into player prompts. | free |

### Objects `features[]`, `npcs[]`, `secrets[]`, `hazards[]`, `state`, `playtest.findings[]`

Mêmes sous-champs que la v1 (id, name, kind, visible, requires / disposition, schedule / content, revealed_by, consequence / trigger, severity / condition, changed / target, issue, suggested_fix, priority, evidence). Les valeurs normées de ces sous-champs sont définies dans le dictionnaire ci-dessous.

## Bloc `playtest` — annotations de test (renseignées APRÈS session, jamais par les LLM en jeu)

| Field | Type | Required | Definition | Allowed values |
|---|---|---|---|---|
| `visits` | int | ✅ | Number of times tables entered this location. | ≥ 0 |
| `sessions` | string[] | ✅ | Session ids that touched this location. | list |
| `interactions` | object[] | ⬜ | What players did here (round refs + summary). | list |
| `explored_ratio` | float | ⬜ | Share of `features` actually discovered. | 0.0–1.0 |
| `findings` | object[] | ⬜ | Adjustments to make. | list |

---

# Dictionnaire des valeurs normées

## `setting`

- `fantasy` — monde à magique/mentor pré-industriel ; `sci-fi` — technologie avancée extrapolée ; `contemporary` — monde réel actuel/near-future.
- `horror` — registre effroi/ survie psychologique (peut chevaucher un autre setting, l'ambiance prime) ; `historical` — époque réelle passée documentée ; `post-apocalyptic` — après-effondrement d'une civilisation ; `other` — aucun des précédents (préciser dans `setting_block`).

## `kind` (vocabulaire SUGGÉRÉ, non fermé — tout slug pertinent est valide)

- `settlement` — lieu habité permanent, toute taille (hameau → mégapole → station coloniale).
- `district` — subdivision administrative/informelle d'un settlement.
- `building` — structure fermée à usage dédié (auberge, labo, temple, hangar) ; `interior` — pièce/salle à l'intérieur d'un building.
- `structure` — lieu construit à vocation première non résidentielle/état d'usage (donjon, bunker, ruine, vaisseau).
- `natural` — formation naturelle (forêt, grotte, canyon, astéroïde) ; `water` — étendue/pvoie d'eau (lac, rivière, océan, nébuleuse).
- `route` — axe de circulation entre lieux (route, rivière navigable, corridor commercial spatial).
- `threshold` — point de passage exceptionnel entre zones normalement séparées (portal, jump gate, tunnel dimensionnel).
- `other` — aucun des précédents ; doit être précisé dans `tags` ou `notes`. **Le kind ne dit RIEN de l'ambiance ni du danger** (voir `atmosphere`, `hazards`).

## `atmosphere`

- `welcoming` — met à l'aise, invite à s'attarder ; `neutral` — fonctionnel, sans charge émotionnelle.
- `tense` — quelque chose ne va pas, le pression monte ; `eerie` — anormal, inquiétant sans menace visible.
- `hostile` — menace active et perceptible ; `oppressive` — écrase la volonté d'être/demeurer (froid intense, vide, dictature).
- `desolate` — vide, abandonné, absence d'activité ; `wondrous` — émerveillement, beauté inhabituelle.
- L'atmosphèse est ce que le lieu **fait ressentir**, pas ce qu'il contient (contenu = `features`, `npcs`, `hazards`).

## `access`

- `public` — entrée libre et légitime pour quiconque ; `restricted` — accès soumis à condition (paiement, rang, douane, badge).
- `hidden` — existence même du lieu/entrée non évidente ; il faut un indice pour le trouver.
- `sealed` — fermé physiquement/magiquement/techniquement ; forcer est nécessaire.
- `moving` — lieu mobile (vaisseau, caravane, plate-forme) ; l'accès dépend de sa position/heure.

## `features[].visible`

- `obvious` — visible sans action particulière dès l'entrée dans la pièce/zone.
- `searchable` — visible si un joueur DÉCLARE une fouille/examen de la cible correspondante.
- `hidden` — introuvable sans indice (`revealed_by`) ou sans action spécifique (`requires`).
- **Nuance stricte** : `searchable` ≠ `hidden` — un jet réussi ne révèle PAS un `hidden` sans indice.

## `features[].kind`

- `cache` — contenu caché intentionnellement ; `clue` — information qui oriente l'enquête ; `exit` — voie de passage supplémentaire.
- `container` — réceptacle ouvrable (coffre, casier) ; `device` — mécanisme manipulable ; `shrine` — lieu de dévotion/rituel.
- `vantage` — point d'observation avantageux ; `hazard-source` — source localisée d'un `hazard` ; `other` — à préciser en `notes`.

## `npcs[].disposition`

- `friendly` — approche proactive, aide sans contrepartie ; `neutral` — répond si interpellé, n'initie rien.
- `wary` — se méfie, ne donne rien sans être gagné ; `hostile` — opposé dès la rencontre (fuite, attaque, refus).
- `hiding` — présence NON révélée d'emblée ; les joueurs ne le voient que s'il est trouvé ou s'il choisit d'apparaître.
- La disposition est **l'état initial** ; elle peut évoluer en cours de jeu. Elle ne dit rien de la force ni des intentions profondes du PNJ.

## `hazards[].kind` et `severity`

- `trap` — dispositif intentionnel de nuisance ; `environment` — danger naturel du lieu (froid, vide, radiation, effondrement).
- `creature` — entité hostile résidente ; `social` — obstacle humain/institutionnel (garde, douane, foule hostile) ; `mental` — effet sur l'esprit (peur, hallucination, influence).
- `minor` — dérangement sans conséquence durable ; `moderate` — blessure/perte significative mais récupérable.
- `severe` — incapacite durable ou met en danger de mort ; `lethal` — peut tuer en un événement sans précaution.
- **La sévérité est le potentiel maximal**, pas la certitude : un `lethal` n'agit que si déclenché (`trigger`).

## `secrets[]` **[meta]**

- `revealed_by` liste les `features[].id` qui peuvent exposer le secret — un secret sans `revealed_by` est INJOIGNABLE par les joueurs (à éviter sauf intention).
- `consequence` décrit ce qui change dans le monde une fois révélé ; sans `consequence`, la révélation n'aura aucun effet narratif (à documenter).

## `state.condition`

- `pristine` — conforme à la description initiale ; `disturbed` — fouillé/traversé avec traces visibles.
- `damaged` — dégradation structurelle partielle ; `destroyed` — inutilisable en l'état ; `altered` — modifié durablement mais fonctionnel (occupation, remodelage).
- Écrit UNIQUEMENT par l'orchestrateur, en fin de tour concerné.

## `playtest.findings[].priority`

- `low` — cosmétique/comfort ; corriger si le temps le permet. `medium` — nuit à l'expérience sans bloquer ; à corriger avant la prochaine itération.
- `high` — un contenu clé est inaccessibile/cassé ; corriger AVANT toute nouvelle session de test.


---

# Règles d'interprétation pour les agents LLM

1. **Littéral strict** : la valeur signifie exactement sa définition ci-dessus. Toute nuance supplémentaire doit venir des champs prose — jamais de la valeur d'enum.
2. **Pas de fusion** : deux valeurs combinées ne créent pas un nouveau comportement ; elles s'appliquent indépendamment.
3. **Pas d'extrapolation d'axe** : un champ ne dit rien des axes qu'il ne décrit pas (ex. `severity` ne dit rien de la fréquence).
4. **Défaut = non-défini** : si un comportement/état n'est décrit ni par l'enum ni par la prose, l'agent reste neutre sur ce point.
