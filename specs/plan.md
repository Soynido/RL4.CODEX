# Plan RL4-CODEX

## Vision
Établir un dépôt dédié au protocole RL4-CODEX v1.1 afin de centraliser la spécification (`spec.md`), structurer la feuille de route et préparer les itérations futures (encodeur/décodeur, validations, SDK). Toutes les évolutions doivent rester strictement alignées sur les principes énoncés dans `spec.md` §0‑14.

## Phases
1. **Bootstrap du dépôt**  
   - Ajouter la spécification officielle.  
   - Formaliser le plan et la liste de tâches.  
   - Publier le dépôt sur GitHub (RL4.CODEX) avec description conforme.

2. **Itération protocolaire**  
   - Définir les modèles de payload et d’exemples synthétiques.  
   - Préparer les tests de validation et les scripts d’audit (DoR dans `tasks.md`).  
   - Documenter les processus d’encodage/décodage guidés par `spec.md` §§3‑8.

3. **Implémentations de référence**  
   - Créer un encodeur minimal (CLI/LLM) respectant `spec.md` §§6‑11.  
   - Créer un décodeur minimal aligné sur `spec.md` §8.  
   - Intégrer les contrôles de sécurité (`spec.md` §9) et les règles de versionnement (§10).

## Contraintes
- Taille cible des payloads < 4 KB (cf. `spec.md` §6).  
- Aucune fuite de contenu brut — seulement des identifiants normalisés (§2, §9).  
- Chaque fonctionnalité passe d’abord par `tasks.md`, puis validation UX/UI via audit MCP Playwright.

## Indicateurs de succès
- Dépôt GitHub public `RL4.CODEX` publié avec documentation minimale (spec, plan, tâches).  
- Traçabilité tâche → commit garantie (1 tâche par commit).  
- Audit MCP Playwright configuré ou planifié après chaque itération.

