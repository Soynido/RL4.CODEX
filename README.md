# RL4.CODEX

> RL4-CODEX v1.2 — Cognitive State Exchange Protocol  
> _Deterministic, content-safe cognition snapshots for any LLM._

## 1. What is RL4-CODEX?
RL4-CODEX is an open protocol for serializing the cognitive state of an AI workspace or conversation into a deterministic, privacy-preserving payload under 4 KB. The payload contains semantic descriptors only—never code, text, or filenames—and can be decoded by any compliant agent to resume reasoning instantly.

## 2. Why RL4-CODEX exists
- Maintain cognitive continuity across LLMs, devices, and IDEs.
- Share reasoning outcomes without exposing proprietary content.
- Persist session memory in a portable, hashed structure.
- Enable tooling ecosystems to exchange context safely and predictably.

## 3. Problems solved
- **State teleportation** between agents without raw transcripts.  
- **Secure collaboration**: hashed identifiers prevent leakage.  
- **Deterministic replays**: identical input state → identical CODEX.  
- **Auditability**: every payload is small, inspectable, and forward compatible.

## 4. Protocol guarantees
1. Zero content transfer (hash-first identifiers only).  
2. Canonical block order: DATA → TIMELINE → CONTEXT → INSIGHTS → DECISIONS → OPTIONAL.  
3. Payload target < 4 KB, hard ceiling 10 KB.  
4. Deterministic serialization rules defined in `spec.md`.  
5. Forward compatibility: unknown tags are ignored, required blocks are never omitted.  
6. LLM-agnostic prompts for encoder/decoder roles.

## 5. Quick start
### Encode
1. Inventory the active cognitive state (tasks, KPIs, risks, ADR hashes).  
2. Normalize every identifier (hash modules, branches, ADRs, developer DNA).  
3. Populate DATA/TIMELINE/CONTEXT/INSIGHTS/DECISIONS streams with compact tokens.  
4. Add OPTIONAL metadata (fingerprint, checksum) only after required blocks.  
5. Validate size + zero-content constraints, then share/store the payload.

### Decode
1. Load the payload as XML-like text.  
2. Follow `decoder.md` to parse each block, handling missing fields as UNKNOWN.  
3. Reconstruct a `CognitiveState` object and rehydrate your agent/IDE.  
4. Preserve unknown tags for forward compatibility, but ignore their content.

## 6. Reference prompts
### Encoder (LLM-only)
```
You are an RL4-CODEX encoder.
Summarize this session into a <4 KB RL4-CODEX v1.2 payload.
Fill DATA, TIMELINE, CONTEXT, INSIGHTS, DECISIONS (OPTIONAL if needed).
Use semantic, hashed descriptors only. No raw quotes, no code, no file paths.
Respond with the XML payload only.
```

### Decoder (LLM-only)
```
You are an RL4-CODEX decoder.
Given a payload, rebuild the CognitiveState:
project context, timeline, insights, decisions, developer profile.
Treat missing fields as unknown, never expect raw code.
Resume the conversation as if the entire history were present.
```

## 7. Example payload (v1.2)
```
<RL4-CODEX v="1.2">
  <DATA>
    proj#7fa2 | mode=strict | phase=build | tasks=3
    kpi:velocity=steady | cog.health=0.82 | maturity=iter2
  </DATA>
  <TIMELINE>
    cycles=5 | actions=edit+analysis | bursts=2
    hotspots=mod#a913,api#f02c | anomalies=idle-dip
  </TIMELINE>
  <CONTEXT>
    type=fullstack | stack=hash(fe,api,llm)
    devDNA=focus/iterative | constraints=strict-zero-content
    success=ship-mvp | reasoning=chain-strict | risk=drift-low
  </CONTEXT>
  <INSIGHTS>
    pattern:handoff-friction(weight=0.60,conf=0.71)
    trend:velocity-up(conf=0.71)
    forecast:phase-shift-ready
  </INSIGHTS>
  <DECISIONS>
    adr#c3d1 | drift=plan-micro | anomaly=spotlight-fe
    integrity=green | adjust=stabilize-context-cache
  </DECISIONS>
  <OPTIONAL>
    vendor=reference-kit | checksum#92af | session=fingerprint#aa71
  </OPTIONAL>
</RL4-CODEX>
```

## 8. Use cases
- Multi-agent handoff (GPT → Claude → internal agent).  
- IDE session restore after crash or device swap.  
- Secure collaboration with external auditors.  
- CI/CD checkpoints for reasoning-aware observability.  
- Air-gapped agent deployments needing deterministic context sync.

## 9. Repository layout
- `README.md` — this onboarding document.  
- `spec.md` — RL4-CODEX v1.2 normative standard (MUST/SHOULD, grammar, validation).  
- `decoder.md` — implementer guide for CognitiveState reconstruction.  
- `examples/` — ready-to-use payloads + instructions.  
- `specs/plan.md` & `specs/tasks.md` — roadmap + governing tasks (kept discreet).

## 10. Contribution model
1. Pick an open task or propose one via issue/PR.  
2. Work on a dedicated branch (`feature/*`, `fix/*`, `refactor/*`).  
3. One commit per task, referencing the task ID.  
4. Update `specs/tasks.md` with status + references.  
5. Run/plan MCP Playwright UX audit post-implementation.  
6. Open a PR; CI/CD + reviewers enforce protocol integrity.

## 11. Versioning
- Current release: **v1.2** (backward compatible with v1.1).  
- Unknown tags/fields MUST be ignored by decoders.  
- OPTIONAL block extensions are allowed if required blocks stay intact.  
- Version roadmap lives in `specs/plan.md`; changelog is tracked in commits.

## 12. License & maintainer
MIT License (see `spec.md` §14).  
Maintained by R-Labs — committed to an open, deterministic cognitive interoperability layer.

