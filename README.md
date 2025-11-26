# RL4.CODEX

> RL4-CODEX v1.1 — Cognitive State Exchange Protocol (Draft Standard)  
> _“Let’s build the next cognitive protocol.”_

## Objet
Ce dépôt véhicule uniquement la couche de spécification et de gouvernance du protocole RL4-CODEX. Toute implémentation (encodeur, décodeur, SDK) doit respecter :

- `spec.md` — définition normative complète.
- `specs/plan.md` — vision et phases (déplacé hors racine pour rester discret).
- `specs/tasks.md` — traçabilité tâche ↔️ commit.

## Structure initiale
- `spec.md` : texte intégral du standard v1.1.
- `specs/plan.md` : feuille de route stratégique alignée sur la spec.
- `specs/tasks.md` : registre des tâches opérationnelles (numérotées, référencées).

## Workflow imposé
1. Choisir la tâche prioritaire dans `specs/tasks.md`.  
2. Implémenter **une seule** tâche par commit.  
3. Mettre à jour `specs/tasks.md` (statuts, références) après le commit.  
4. Lancer un audit UX/UI via MCP Playwright après chaque implémentation.

## Licence
MIT (recommandé par la spec §14).

## Mainteneur
R-Labs — protocole ouvert pour l’interopérabilité cognitive multi-LLM.

