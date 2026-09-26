---
id: character-schema
name: "Generic character sheet schema"
version: 1
scope: "Character/*.md"
system_agnostic: true
---

# Generic character schema — field definitions

Fiche de personnage **agnostique du système de jeu** : aucun champ ne présuppose D&D, CoC ou autre.
Le bloc `system_block` (libre) accueille les mécaniques propres au JDR ; tout le reste est universel.
All data values are English; prose (descriptions, background) may stay in French.

## Champs génériques

| Field | Type | Required | Definition | Allowed values |
|---|---|---|---|---|
| `id` | string | ✅ | Character slug, matches file name. | `<slug>` |
| `name` | string | ✅ | In-fiction full name. | free |
| `player_ref` | path | ✅ | Persona playing this character, relative to this sheet. | `../Players/player-1.md` |
| `concept` | string | ✅ | One-line archetype (e.g. "disgraced paladin sentinel"). | free |
| `species` | string | ⬜ | In-fiction species/ancestry, system-neutral. | free |
| `gender` | enum | ⬜ | Character gender. | `male` \| `female` \| `other` \| `unspecified` |
| `age` | int | ⬜ | Character age. | free |
| `appearance` | string | ⬜ | Physical description (1–3 sentences). | free |
| `background` | string | ✅ | History in one paragraph. | free |
| `personality` | string[] | ✅ | 2–4 in-fiction personality traits. | free |
| `speaking_style` | string | ✅ | How the character talks (in-character voice). | free |
| `goals` | string[] | ✅ | Personal objectives driving decisions. | free |
| `bonds` | string[] | ⬜ | Ties to people, places, organisations (id or free text). | free |
| `flaws` | string[] | ✅ | Character weaknesses (primary GM hooks). | free |
| `fears` | string[] | ⬜ | What the character dreads. | free |
| `motivation` | enum | ⬜ | Dominant drive. | `duty` \| `greed` \| `revenge` \| `curiosity` \| `survival` \| `redemption` \| `glory` \| `love` \| `faith` \| `freedom` |
| `moral_alignment` | enum | ⬜ | Rough ethical compass (NOT a D&D grid). | `altruistic` \| `pragmatic` \| `opportunistic` \| `selfish` \| `cruel` |
| `status` | enum | ⬜ | Current physical/mental state, updated by orchestrator. | `active` \| `injured` \| `unconscious` \| `dying` \| `dead` \| `absent` |
| `inventory` | object[] | ⬜ | Carried items (see below). | list |
| `relationships` | object[] | ⬜ | Links to other characters/NPCs (see below). | list |
| `system_block` | object | ⬜ | **Game-system-specific data** (stats, skills, powers, gear rules). Structure entirely free; orchestrator injects it verbatim. | free |
| `system` | string | ⬜ | Which system `system_block` follows (e.g. `dnd5e`, `cof7`). `generic` if none. | free |
| `notes` | string | ⬜ | **[meta]** Author notes — never injected into prompts. | free |

### Object `inventory[]`

| Field | Type | Required | Definition |
|---|---|---|---|
| `name` | string | ✅ | Item name. |
| `qty` | int | ⬜ | Quantity (default 1). |
| `kind` | enum | ⬜ | `weapon` \| `armor` \| `tool` \| `consumable` \| `treasure` \| `memento` \| `other` |
| `notes` | string | ⬜ | Free remark. |

### Object `relationships[]`

| Field | Type | Required | Definition |
|---|---|---|---|
| `target` | string | ✅ | Name or id of the other party (PC, NPC, faction). |
| `relation` | enum | ✅ | `ally` \| `friend` \| `family` \| `rival` \| `enemy` \| `mentor` \| `debtor` \| `lover` \| `other` |
| `note` | string | ⬜ | Free remark. |

## Exemples de `system_block` (structure libre, jamais imposée)

```yaml
# D&D 5e
system: dnd5e
system_block:
  class: paladin
  level: 5
  ability_scores: { str: 16, dex: 10, con: 14, int: 12, wis: 13, cha: 15 }
  hp: { current: 44, max: 44 }
  ac: 18

# Year Zero Engine
system: yze
system_block:
  key_attribute: empathy
  strength: 4
  skills: { close-combat: 2, ranged-combat: 1, medicine: 3 }
  stress: { current: 0, max: 6 }

# Système maison / générique
system: generic
system_block:
  competences:
    - { nom: "Escalade", niveau: 3 }
```

## Règles d'usage pour l'orchestrateur

1. **Injection prompt joueur** : tous les champs sauf `notes`. Le `system_block` est injecté tel quel — l'orchestrateur ne doit pas tenter de l'interpréter.
2. `goals`, `flaws`, `fears` et `motivation` sont les leviers principaux dont le MJ se sert pour accrocher le personnage.
3. `status` est mis à jour par l'orchestrateur en cours de session (miroir du champ `status` de `Session/_SESSION.md`).
4. Toute nouvelle fiche DOIT remplir les champs génériques obligatoires ; le reste dépend du système de jeu choisi pour la session.
