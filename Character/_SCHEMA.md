---
id: character-schema
name: "Generic character sheet schema"
version: 2
scope: "Character/*.md"
system_agnostic: true
---

# Generic character schema — field definitions

Fiche de personnage **agnostique du système de jeu**. Le bloc `system_block` (libre) accueille les mécaniques propres au JDR ; tout le reste est universel.
All data values are English; prose (descriptions, background) may stay in French.

**Important** : chaque valeur normée ci-dessous a une définition **stricte**. Un agent LLM doit interpréter la donnée exactement selon cette définition et **ne jamais extrapoler** au-delà.

## Champs génériques

| Field | Type | Required | Definition | Allowed values |
|---|---|---|---|---|
| `id` | string | ✅ | Character slug, matches file name. Never changes once created. | `<slug>` |
| `name` | string | ✅ | In-fiction full name. | free |
| `player_ref` | path | ✅ | Persona playing this character, relative to this sheet. | `../Players/player-1.md` |
| `concept` | string | ✅ | One-line archetype. | free |
| `species` | string | ⬜ | In-fiction species/ancestry, system-neutral. | free |
| `gender` | enum | ⬜ | Character gender. See dictionary. | `male` \| `female` \| `other` \| `unspecified` |
| `age` | int | ⬜ | Character age in years. | free |
| `appearance` | string | ⬜ | Physical description (1–3 sentences). | free |
| `background` | string | ✅ | History in one paragraph. | free |
| `personality` | string[] | ✅ | 2–4 in-fiction personality traits. | free |
| `speaking_style` | string | ✅ | How the character talks (in-character voice). | free |
| `goals` | string[] | ✅ | Personal objectives driving decisions. | free |
| `bonds` | string[] | ⬜ | Ties to people, places, organisations. | free |
| `flaws` | string[] | ✅ | Character weaknesses (primary GM hooks). | free |
| `fears` | string[] | ⬜ | What the character dreads. | free |
| `motivation` | enum | ⬜ | Dominant drive — the tie-breaker when goals conflict. See dictionary. | 10 values, see dictionary |
| `moral_alignment` | enum | ⬜ | Rough ethical compass (NOT a D&D grid). See dictionary. | 5 values, see dictionary |
| `status` | enum | ⬜ | Current physical/mental state, updated by orchestrator. See dictionary. | 6 values, see dictionary |
| `inventory` | object[] | ⬜ | Carried items (see below). | list |
| `relationships` | object[] | ⬜ | Links to other characters/NPCs (see below). | list |
| `system_block` | object | ⬜ | **Game-system-specific data**. Injected verbatim, never interpreted. | free |
| `system` | string | ⬜ | Which system `system_block` follows. `generic` if none. | free |
| `notes` | string | ⬜ | **[meta]** Author notes — never injected into prompts. | free |

### Object `inventory[]`

| Field | Type | Required | Definition |
|---|---|---|---|
| `name` | string | ✅ | Item name. |
| `qty` | int | ⬜ | Quantity (default 1). |
| `kind` | enum | ⬜ | See dictionary below. |
| `notes` | string | ⬜ | Free remark. |

### Object `relationships[]`

| Field | Type | Required | Definition |
|---|---|---|---|
| `target` | string | ✅ | Name or id of the other party (PC, NPC, faction). |
| `relation` | enum | ✅ | See dictionary below. |
| `note` | string | ⬜ | Free remark. |

---

# Dictionnaire des valeurs normées

## `gender` (character)

- `male` / `female` — genre fictif du personnage. **N'implique AUCUN trait de personnalité ni compétence.**
- `other` — genre non binaire ou non humain standard. Pronoms à préciser dans `speaking_style`.
- `unspecified` — volontairement non défini ; l'agent ne doit pas en deviner un.

## `motivation`

- `duty` — accomplir une obligation perçue comme supérieure à soi (serment, ordre, mission).
- `greed` — accumuler richesse ou ressources matérielles.
- `revenge` — faire payer un préjudice précis, identifié dans `background` ou `bonds`.
- `curiosity` — comprendre ce qui est inconnu ou caché.
- `survival` — rester en vie et en sécurité ; prudence d'intérêt, pas forcément lâcheté.
- `redemption` — expier une faute passée, définie dans `background`.
- `glory` — être reconnu, laisser une marque, être loué par autrui.
- `love` — protéger ou rejoindre une personne ou communauté chère (voir `bonds`).
- `faith` — servir une cause spirituelle ou idéologique supérieure.
- `freedom` — préserver son autonomie ; refuse l'enfermement et le contrôle, quels qu'en soient les avantages.
- **Usage** : la motivation est le **départage** quand plusieurs `goals` entrent en conflit. **Ne remplace PAS** `goals` et n'implique pas l'absence des autres motivations — c'est la dominante.

## `moral_alignment`

- `altruistic` — privilégie systématiquement le bien d'autrui au sien, même à son coût.
- `pragmatic` — choisit selon l'efficacité ; aider ou nuire selon le résultat espéré.
- `opportunistic` — saisit l'avantage quand il se présente ; ne cherche pas activement à nuire.
- `selfish` — privilégie son propre intérêt ; peut nuire au passage mais sans cruauté gratuite.
- `cruel` — tire profit ou plaisir du préjudice d'autrui.
- **N'implique PAS** : obéissance aux lois (aucune de ces valeurs ne dit rien de la légalité).

## `status`

- `active` — pleinement opérationnel, aucune restriction.
- `injured` — blessé ; peut agir mais avec malus/limitations décrits par le système.
- `unconscious` — inapte à agir ; ne parle pas, ne décide pas. `absent` du point de vue du tour.
- `dying` — en danger de mort immédiat ; nécessite intervention.
- `dead` — définitif (sauf résurrection prévue par le système) ; le persona joueur ne joue plus ce personnage.
- `absent` — hors scène (éclaireur parti, capturé ailleurs) ; peut revenir ; n'est PAS mort ni blessé.

## `inventory[].kind`

- `weapon` — conçu pour nuire ; `armor` — conçu pour protéger ; `tool` — usage pratique/métier.
- `consumable` — à usage unique ; `treasure` — valeur d'échange ; `memento` — valeur sentimentale uniquement (souvent un hook dramatique).
- `other` — ne rentre dans aucune catégorie ; le `notes` doit préciser.

## `relationships[].relation`

- `ally` — coopère vers un objectif commun (temporaire ou durable) ; `friend` — attachement personnel.
- `family` — lien du sang ou adoptif ; `rival` — compétition sans hostilité ouverte ; `enemy` — hostilité active.
- `mentor` — a formé ou guide ; `debtor` — doit quelque chose (dette morale ou matérielle) ; `lover` — relation amoureuse.
- Chaque valeur décrit le **lien**, pas son intensité : l'intensité/détail va dans `note`.


---

# Règles d'interprétation pour les agents LLM

1. **Littéral strict** : la valeur signifie exactement sa définition ci-dessus. Toute nuance supplémentaire doit venir des champs prose — jamais de la valeur d'enum.
2. **Pas de fusion** : deux valeurs combinées ne créent pas un nouveau comportement ; elles s'appliquent indépendamment.
3. **Pas d'extrapolation d'axe** : un champ ne dit rien des axes qu'il ne décrit pas (ex. `severity` ne dit rien de la fréquence).
4. **Défaut = non-défini** : si un comportement/état n'est décrit ni par l'enum ni par la prose, l'agent reste neutre sur ce point.
