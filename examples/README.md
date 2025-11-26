# Exemples RL4-CODEX

Ces payloads démontrent comment appliquer le standard v1.1 dans des situations concrètes tout en restant < 4 KB et sans exposer de contenu sensible. Ils respectent strictement l’ordre des balises DATA → TIMELINE → CONTEXT → INSIGHTS → DECISIONS (OPTIONAL si nécessaire).

## Fichiers fournis

| Fichier | Cas d’usage | Points clés |
| --- | --- | --- |
| `product-handoff.codex` | Passer le relais d’un sprint produit entre deux agents (build → refactor). | Accent sur la continuité cognitive, la gestion des dérives et l’activation d’ADR anonymisées. |
| `ci-health.codex` | Suivre l’état cognitif d’une pipeline CI/CD sur 24 h. | Montre comment encoder KPIs de santé, anomalies temporelles et métadonnées optionnelles. |

## Mode d’emploi
1. **Lire `spec.md`** pour vérifier chaque contrainte (hashing, taille, ordre des blocs).  
2. **Copier un payload** tel quel puis utiliser le prompt de décodage (spec §12) dans le LLM de votre choix.  
3. **Adapter les tokens** : remplacez les identifiants hashés par vos propres empreintes (jamais de noms explicites).  
4. **Valider** la taille et l’absence de contenu brut avant diffusion.  
5. **Automatiser** : intégrez ces exemples dans vos tests (ex. CI) pour s’assurer que votre encodeur/décodeur reste conforme.

## Bonnes pratiques rappelées
- Pas de timestamps exacts ni de chemins de fichiers.  
- Toujours normaliser/hash les modules, ADR, branches et identités.  
- Préférer des streams compacts lisibles (pas de JSON).  
- Ajouter `<OPTIONAL>` uniquement si les 5 blocs obligatoires sont déjà présents.  
- Ignorer les tags inconnus côté décodeur pour rester compatible avec les futures versions.

