# RL4.CODEX

> RL4-CODEX v1.1 — Cognitive State Exchange Protocol (Draft Standard)  
> _“Let’s build the next cognitive protocol.”_

## Pourquoi RL4-CODEX ?
RL4-CODEX permet de déplacer l’état cognitif d’un projet **sans transférer le moindre contenu brut**. Le protocole fournit un snapshot déterministe (< 4 KB cible) partageable entre agents GPT, Claude, Mistral, Gemini, Llama, etc., assurant une continuité de raisonnement multi-outils tout en respectant la confidentialité.

## Contenu du dépôt
- `spec.md` — texte intégral du standard v1.1 (référence normative unique).
- `specs/plan.md` — feuille de route stratégique (phases et contraintes).
- `specs/tasks.md` — registre de tâches numérotées (traçabilité 1 commit = 1 tâche).
- `examples/` — payloads RL4-CODEX prêts à tester avec leur guide d’utilisation.

> Les fichiers de gouvernance sont volontairement discrets afin que le dépôt public reste concentré sur la spec.

## Principes clés (résumé de `spec.md` §§2‑6)
1. **Zero Content Transfer** — aucun code, texte ou diff n’est transporté.  
2. **Deterministic Serialization** — même état → même payload.  
3. **Model Agnostic** — fonctionne avec n’importe quel LLM ou IDE.  
4. **Payload compact** — objectif < 4 KB (max 10 KB).  
5. **Interopérable & composable** — compatible RL4 Snapshot / Cognitive Kernel.  
6. **Facile à décoder** — tout décodeur peut reconstruire un `CognitiveState`.

## Démarrage rapide
1. **Lire `spec.md`** pour maîtriser la structure `<RL4-CODEX>`.  
2. **Encoder** votre session via le prompt officiel (voir §11) en respectant les blocs DATA → TIMELINE → CONTEXT → INSIGHTS → DECISIONS.  
3. **Vérifier la taille** (cible < 4 KB) et l’absence de contenu brut.  
4. **Partager ou stocker** le payload — n’importe quel décodeur conforme pourra restaurer l’état cognitif.  
5. **Auditer** via MCP Playwright après chaque implémentation pour garantir l’UX décrite dans la spec.

## Encoder en 3 étapes
1. **Collecter les signaux** : tâches actives, phases, KPIs, hotspots (toujours normalisés/hashed).  
2. **Synthétiser** : transformer les signaux en token streams compacts (aucun JSON).  
3. **Sceller** : envelopper le tout dans `<RL4-CODEX v="1.1">` en respectant l’ordre strict des balises.

## Exemple synthétique RL4-CODEX
```
<RL4-CODEX v="1.1">
  <DATA>
    proj#7fa2 | mode=strict | phase=build | tasks=3
    kpi:vel=steady | cog.health=0.82 | maturity=iter2
  </DATA>
  <TIMELINE>
    cycles=5 | actions=edit+analysis | bursts=2
    hotspots=mod#a913,api#f02c | anomalies=idle-dip
  </TIMELINE>
  <CONTEXT>
    type=fullstack | stack=hash(fe,api,llm)
    devDNA=focus/iterative | constraints:strict-zero-content
    success=ship-mvp | reasoning=chain-strict | risk=drift-low
  </CONTEXT>
  <INSIGHTS>
    pattern:handoff-friction(weight=0.6)
    trend:velocity-up(conf=0.71)
    forecast:phase-shift-ready
  </INSIGHTS>
  <DECISIONS>
    adr#c3d1 | drift=plan-micro | anomaly=spotlight-fe
    integrity=green | adjust=stabilize-context-cache
  </DECISIONS>
</RL4-CODEX>
```
Ce payload respecte la taille compacte, l’anonymisation par hash, et démontre comment exprimer KPIs + signaux temporels sans référence à du contenu sensible.

## Cas d’usage typiques
- **Continuité multi-agents** : transférer un état Cognitif entre deux LLM lors d’un relais (ex. GPT → Claude).  
- **Restauration IDE** : redonner à une extension VS Code l’état d’un chat précédent après rechargement.  
- **Audit externe** : partager un snapshot normalisé avec une équipe sécurité sans diffuser du code.  
- **Handoff produit** : téléporter la mémoire d’un build en cours vers un autre device/agent en quelques secondes.

## Exemples prêts à l’emploi
- `examples/product-handoff.codex` : snapshot conçu pour passer le relais d’un sprint (inclut `<OPTIONAL>` avec fingerprint).  
- `examples/ci-health.codex` : suivi de pipeline CI sur 24 h, idéal pour vérifier vos encodeurs automatiques.  
- `examples/README.md` : mode d’emploi, bonnes pratiques et check-list de validation.

## Tester ou intégrer rapidement
- **LLM Sandbox** : copiez l’exemple ci-dessus dans n’importe quel modèle et demandez-lui d’agir en décodeur via le prompt §12.  
- **Pipelines CI/CD** : générez un CODEX après chaque étape clé (planification, build, refactor) puis stockez-le comme artefact sécurisé.  
- **Edge Devices** : injectez le payload dans un agent offline, il retrouvera le contexte sans voir les fichiers.  
- **Observabilité** : agrégez plusieurs payloads pour suivre la santé cognitive d’un programme sur 24‑48 h.

## Workflow de contribution
1. Choisir une tâche dans `specs/tasks.md`.  
2. Implémenter **exactement une tâche par commit**.  
3. Mettre à jour la table (`specs/tasks.md`) avec le statut et la référence de commit.  
4. Lancer/planifier l’audit MCP Playwright correspondant.  
5. Ouvrir une PR (branche `feature/*`, `fix/*`, `refactor/*`) puis laisser la CI/CD valider.

## Licence & Mainteneur
Licence MIT (cf. `spec.md` §14).  
Maintenu par R-Labs — couche protocolaire ouverte pour l’interopérabilité cognitive multi-LLM.

