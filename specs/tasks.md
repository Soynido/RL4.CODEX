# Tasks RL4-CODEX

| ID | Priorité | Description | Référence | Statut |
| --- | --- | --- | --- | --- |
| TASK-001 | P0 | Bootstraper la documentation protocolaire (`spec.md`, `plan.md`, `tasks.md`) pour aligner le dépôt sur RL4-CODEX v1.1. | `spec.md` §§0‑14, `plan.md` Phase 1 | DONE |
| TASK-002 | P0 | Initialiser le dépôt git + `README.md` minimal puis publier `RL4.CODEX` via GitHub CLI avec la description officielle (`https://github.com/Soynido/RL4.CODEX`). | `plan.md` Phase 1 | DONE |
| TASK-003 | P0 | Recomposer `spec.md` pour qu'il corresponde mot pour mot au texte anglais source RL4-CODEX v1.1. | `spec.md` §§0‑14 | DONE |
| TASK-004 | P0 | Rendre `plan.md` et `tasks.md` invisibles sur GitHub (déplacer hors racine tout en conservant leur rôle de référence). | `specs/plan.md`, `specs/tasks.md` | DONE |
| TASK-005 | P0 | Refonte du `README.md` pour le rendre production-ready (sections claires, workflow test/intégration, exemples synthétiques). | `specs/plan.md` Phase 2, `spec.md` §§3‑8 | DONE |
| TASK-006 | P0 | Créer un dossier `examples/` avec plusieurs payloads RL4-CODEX commentés + use cases détaillés. | `specs/plan.md` Phase 2, `spec.md` §§3‑8 | DONE |

> Règles : une tâche par commit, validation via audit MCP Playwright après implémentation, références strictes à `spec.md`/`plan.md`.

