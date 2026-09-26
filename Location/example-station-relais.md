---
id: example-station-relais
name: "Station-relais K-77 « L'Éventail »"
kind: structure
setting: sci-fi
tags: [rest, refuel, social-hub, danger]
description: >
  Une station torique de classe moyenne accrochée au point de Lagrange. Anneau marchand
  bondé, coursives graisseuses, odeur de café reconstitué et d'huile de graissage.
atmosphere: tense
access: restricted
entry_points: [écluse-principale, quai-marchand, baie-dérive]
features:
  - { id: terminal-dock, name: "Terminal d'amarrage", kind: device, visible: obvious }
  - { id: soute-cache, name: "Soute technique désaffectée", kind: cache, visible: hidden, requires: "plan de la station ou accès maintenance" }
  - { id: bulletin-board, name: "Tableau des contrats", kind: clue, visible: obvious }
npcs:
  - { id: docker-zeth, name: "Zeth", disposition: wary, schedule: "cycle jour" }
hazards:
  - { id: zero-g-ring, name: "Jonction en apesanteur", kind: environment, trigger: "traverser sans équipement", severity: moderate }
setting_block:
  gravity: "0.3g (anneau) / 0g (jonction)"
  atmosphere: breathable
  jurisdiction: corporate-zone
  comms: { relayed: true, delay: "12min" }
state:
  condition: pristine
  changed: []
---

## Exemple — lieu de type structure (setting sci-fi)

Fiche d'exemple : montre l'usage de hazards, setting_block SF et kinds génériques.
