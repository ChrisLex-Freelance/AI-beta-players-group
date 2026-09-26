---
id: location-schema
name: "Generic location schema"
version: 1
scope: "Location/*.md"
setting_agnostic: true
---

# Generic location schema — field definitions

Fiche de lieu **agnostique du setting** : fonctionne pour la fantasy, le contemporain, la SF, l'horreur, etc.
Le champ `kind` est un vocabulaire **suggéré, non fermé** (voir liste) ; le bloc `setting_block` (libre) accueille ce qui est propre à l'univers.
All data values are English; prose (descriptions) may stay in French.

## Champs génériques

| Field | Type | Required | Definition | Allowed values |
|---|---|---|---|---|
| `id` | string | ✅ | Location slug, matches file name. | `<slug>` |
| `name` | string | ✅ | In-fiction name. | free |
| `kind` | enum* | ✅ | Type of place. Vocabulary below is **suggested, not closed** — any slug is valid. | see vocabulary |
| `setting` | enum | ✅ | Which universe this location belongs to. | `fantasy` \| `contemporary` \| `sci-fi` \| `horror` \| `historical` \| `post-apocalyptic` \| `other` |
| `parent_ref` | path | ⬜ | Containing location (nesting: room → building → district → city → region → planet). | `../Location/<slug>.md` |
| `children_refs` | path[] | ⬜ | Known sub-locations (informational; `parent_ref` is the source of truth). | list of paths |
| `tags` | string[] | ⬜ | Free keywords (social-hub, starting-point, danger, safe-house...). | free |
| `description` | string | ✅ | What players perceive on arrival (read-aloud or summary). | free |
| `atmosphere` | enum | ⬜ | Emotional register the GM should convey. | `welcoming` \| `neutral` \| `tense` \| `eerie` \| `hostile` \| `oppressive` \| `desolate` \| `wondrous` |
| `access` | enum | ⬜ | How open the place is to the party. | `public` \| `restricted` \| `hidden` \| `sealed` \| `moving` ( véhicule/vaisseau ) |
| `entry_points` | string[] | ⬜ | Named ways in/out (doors, docks, airlocks, windows...). | free |
| `features` | object[] | ⬜ | Points of interest (see below). | list |
| `npcs` | object[] | ⬜ | Beings on site (see below). | list |
| `secrets` | object[] | ⬜ | **[meta — GM only]** Hidden contents/truths (see below). | list |
| `hazards` | object[] | ⬜ | Dangers, obstacles, environmental effects (generic: trap, toxin, vacuum, curse...). | list |
| `state` | object | ⬜ | Mutable world-state written by the orchestrator (see below). | object |
| `setting_block` | object | ⬜ | **Setting-specific data** (tech level, magic presence, gravity, local laws...). Injected verbatim, never interpreted. | free |
| `notes` | string | ⬜ | **[meta]** Author notes — never injected into player prompts. | free |

## Bloc `playtest` — annotations de test (renseignées APRÈS session, jamais par les LLM en jeu)

| Field | Type | Required | Definition | Allowed values |
|---|---|---|---|---|
| `visits` | int | ✅ | Number of times tables entered this location. | ≥ 0 |
| `sessions` | string[] | ✅ | Session ids that touched this location. | list |
| `interactions` | object[] | ⬜ | What players did here (round refs + summary). | list |
| `explored_ratio` | float | ⬜ | Share of `features` actually discovered (0–1). | 0.0–1.0 |
| `findings` | object[] | ⬜ | Adjustments to make (see below). | list |

### Object `features[]`

| Field | Type | Required | Definition |
|---|---|---|---|
| `id` | string | ✅ | Stable slug (referenced by playtest findings). |
| `name` | string | ✅ | Player-facing name. |
| `kind` | enum | ⬜ | `cache` \| `clue` \| `exit` \| `container` \| `device` \| `shrine` \| `vantage` \| `hazard-source` \| `other` |
| `visible` | enum | ⬜ | `obvious` \| `searchable` (needs a declared search) \| `hidden` (needs clue/roll) |
| `requires` | string | ⬜ | What it takes to find/use it (key, skill, tool, password). |
| `notes` | string | ⬜ | Free remark. |

### Object `npcs[]`

| Field | Type | Required | Definition |
|---|---|---|---|
| `id` | string | ✅ | NPC slug. |
| `name` | string | ✅ | Player-facing name. |
| `ref` | path | ⬜ | Full sheet if defined in `Scenario/pnj/`. |
| `disposition` | enum | ✅ | `friendly` \| `neutral` \| `wary` \| `hostile` \| `hiding` |
| `schedule` | string | ⬜ | When present (free text — hours, watch, orbit...). |

### Object `secrets[]` **[meta]**

| Field | Type | Required | Definition |
|---|---|---|---|
| `id` | string | ✅ | Secret slug. |
| `content` | string | ✅ | The hidden truth. |
| `revealed_by` | string[] | ⬜ | Clues/features that can expose it. |
| `consequence` | string | ⬜ | What changes if discovered. |

### Object `hazards[]`

| Field | Type | Required | Definition |
|---|---|---|---|
| `id` | string | ✅ | Hazard slug. |
| `name` | string | ✅ | Player-facing name. |
| `kind` | enum | ⬜ | `trap` \| `environment` (froid, vide, radiation) \| `creature` \| `social` (garde, douane) \| `mental` \| `other` |
| `trigger` | string | ⬜ | What activates it. |
| `severity` | enum | ⬜ | `minor` \| `moderate` \| `severe` \| `lethal` |

### Object `state` (mutable, written by orchestrator during play)

| Field | Type | Required | Definition |
|---|---|---|---|
| `condition` | enum | ⬜ | `pristine` \| `disturbed` \| `damaged` \| `destroyed` \| `altered` |
| `changed` | string[] | ⬜ | Human-readable list of permanent changes (traps sprung, doors forced, NPC dead). |
| `as_of_session` | string | ⬜ | Last session id that updated this state. |

### Object `findings[]` (audit)

| Field | Type | Required | Definition |
|---|---|---|---|
| `target` | string | ✅ | What to fix: feature/npc/secret id, or `description`, `access`, `atmosphere`... |
| `issue` | string | ✅ | What the playtest revealed. |
| `suggested_fix` | string | ⬜ | Proposed adjustment. |
| `priority` | enum | ✅ | `low` \| `medium` \| `high` |
| `evidence` | string[] | ⬜ | Round refs supporting the finding. |

## Vocabulaire suggéré pour `kind` (NON fermé — tout slug pertinent est valide)

`settlement` (village, ville, station orbitale) \| `district` \| `building` (auberge, labo, temple, hangar) \| `interior` (pièce, salle, cabine) \| `structure` (donjon, bunker, ruine, vaisseau) \| `natural` (forêt, grotte, canyon, astéroïde) \| `water` (lac, rivière, océan, nébuleuse) \| `route` (route, rivière navigable, route commerciale spatiale) \| `threshold` (portal, jump gate, tunnel) \| `other`

## Exemples de `setting_block` (structure libre)

```yaml
# Fantasy
setting: fantasy
setting_block:
  tech_level: medieval
  magic: { presence: low, visible: false }
  fast_travel: none

# Sci-fi
setting: sci-fi
setting_block:
  gravity: 0.8g
  atmosphere: breathable
  jurisdiction: corporate-zone
  comms: { relayed: true, delay: "20min" }

# Contemporain
setting: contemporary
setting_block:
  era: 2020s
  surveillance: high
  phone_coverage: full
```

## Règles d'usage pour l'orchestrateur

1. **Injection prompt joueur** : `name, kind, description, atmosphere, access, entry_points, visible features, npcs (disposition)` — jamais `secrets`, ni `notes`.
2. **Injection prompt MJ** : tout, y compris `secrets` et `notes`.
3. `state` est mis à jour par l'orchestrateur en fin de tour si le lieu a été modifié.
4. `playtest` n'est JAMAIS écrit pendant le jeu ; c'est l'étape de consolidation d'audit post-session (croisement des `round-NNN.md` → mise à jour des fiches de lieux).
5. Un même lieu peut être référencé par plusieurs scénarios ; une fiche `Scenario/` ne fait que pointer vers `Location/`, jamais l'inverse.
