---
id: example-auberge
name: "Auberge de la Rose Rouille"
kind: building
setting: fantasy
parent_ref: "../Location/example-bourg.md"
tags: [social-hub, starting-point, rest]
description: >
  Une auberge de deux étages aux poutres noircies. Au rez-de-chaussée, la salle commune
  sent la bière et le ragoût ; le feu de l'âtre ne s'éteint jamais.
atmosphere: welcoming
access: public
entry_points: [porte-principale, porte-de-cuisine, étable]
features:
  - { id: cache-bar, name: "Cache sous le bar", kind: cache, visible: hidden, requires: "indice ou fouille déclarée" }
  - { id: registre, name: "Registre des chambres", kind: clue, visible: searchable }
npcs:
  - { id: aubergiste-marn, name: "Marn", disposition: friendly, schedule: "toujours présent" }
secrets:
  - id: dette-marn
    content: "Marn doit de l'argent au cartel local ; la cache contient ses remboursements."
    revealed_by: [cache-bar]
    consequence: "Marn devient une source de chantage."
setting_block:
  tech_level: medieval
  magic: { presence: low, visible: false }
state:
  condition: pristine
  changed: []
---

## Exemple — lieu de type building (setting fantasy)

Fiche d'exemple : à adapter ou supprimer une fois les vrais lieux écrits.
