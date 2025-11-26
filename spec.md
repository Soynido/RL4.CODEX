# RL4-CODEX v1.0 — Universal Cognitive Snapshot Standard

Status: Production-ready  
Maintainer: R-Labs  
Date: 2025-11-26

---

## 1. Purpose & Rationale
RL4-CODEX v1.0 defines a text-only, LLM-agnostic snapshot format that captures a user’s active cognitive state and makes it portable across chats, devices, or models. Any human can copy a `<RL4-CODEX>` block, paste it into any LLM, and resume work instantly—without tools, code, or integrations. The standard prioritizes determinism, human readability, and forward compatibility so that identical inputs always produce identical snapshots under 4 KB.

## 2. Guiding Principles
1. **Universal Access** — Works in any editor, chat box, or model with plain text.  
2. **Deterministic Structure** — Canonical ordering, stable field names, minimal punctuation.  
3. **No Content Leakage** — Semantic summaries only; never raw code, credentials, or proprietary text.  
4. **Human-Friendly** — Short labels, simple grammar, copy/paste safe.  
5. **Future-Proof** — Unknown fields must be ignored but preserved.  
6. **Low Friction** — Entire payload recommended ≤ 4,096 bytes (hard cap 10,240 bytes).

## 3. Terminology
- **Snapshot** — A single `<RL4-CODEX>` payload.  
- **Encoder** — Human + LLM prompt that produces a snapshot.  
- **Loader** — Human + LLM prompt that consumes a snapshot to rebuild context.  
- **Token Stream** — Comma-separated clauses inside each block.  
- **CORE Fields** — Mandatory data required for a valid snapshot.  
- **OPTIONAL Fields** — Extensions that may appear without breaking compatibility.

## 4. Data Model
Every snapshot describes the same seven dimensions. CORE content is required; OPTIONAL content may appear anywhere inside the same block after the CORE entries.

| Block | Purpose | CORE Fields | OPTIONAL Fields |
| --- | --- | --- | --- |
| `IDENTITY` | Who is speaking & style | `persona`, `project`, `style` | `timezone`, `language`, `links` |
| `STATE` | Current cognitive state | `mode`, `phase`, `focus`, `energy` | `risks`, `confidence` |
| `GOALS` | Objectives snapshot | `short_term`, `long_term` | `done`, `blocked` |
| `CONTEXT` | Constraints & environment | `constraints`, `assets`, `stakeholders` | `tools`, `repos`, `notes` |
| `DECISIONS` | Active choices & ADRs | `recent`, `rationale`, `impacts` | `adr_refs`, `warnings` |
| `TASKS` | Immediate next actions | `priority_list` (ordered) | `calendar`, `owner` |
| `STYLE` | How to behave when resuming | `tone`, `communication`, `preferences` | `anti_patterns`, `guardrails` |

The OPTIONAL `<OPTIONAL>` block extends the payload with telemetry (`checksum`, `session`, `vendor`, etc.) but never replaces the CORE blocks.

## 5. Serialization Format
1. Snapshots **MUST** use the exact wrapper:  
   ```
   <RL4-CODEX v="1.0">
     ...
   </RL4-CODEX>
   ```
2. Blocks **MUST** appear in this canonical order: `IDENTITY`, `STATE`, `GOALS`, `CONTEXT`, `DECISIONS`, `TASKS`, `STYLE`, `OPTIONAL` (optional).  
3. Each block contains short clauses separated by ` | `. Clauses use `label=value` or `label:comma-list`. Example: `mode=focus | phase=design`.  
4. Whitespace between blocks is flexible; indentation is optional.  
5. Encoders **MUST NOT** include HTML entities, Markdown formatting, or list bullets inside the block content.  
6. Only ASCII characters are permitted; UTF-8 emoji **MAY** be used in OPTIONAL but should be avoided for maximum compatibility.  
7. Duplicate labels inside the same block are invalid.

## 6. Core Field Definitions

### 6.1 IDENTITY
- `persona` (**MUST**) — Short description of the active role (e.g., “Lead Backend Engineer”).  
- `project` (**MUST**) — High-level project nickname or hashed identifier.  
- `style` (**MUST**) — Desired interaction style (`decisive`, `mentor`, `fast`).  
- OPTIONAL: `timezone`, `language`, `links` (comma list of canonical references).

### 6.2 STATE
- `mode` (**MUST**) — `focus`, `explore`, `debug`, or `planning`.  
- `phase` (**MUST**) — Custom label for the current project phase.  
- `focus` (**MUST**) — Single sentence describing cognitive focus.  
- `energy` (**MUST**) — `high`, `steady`, or `low`.  
- OPTIONAL: `risks`, `confidence` (0–1 float).

### 6.3 GOALS
- `short_term` (**MUST**) — Up to three bullet-equivalent phrases separated by commas.  
- `long_term` (**MUST**) — Persistent north-star objectives.  
- OPTIONAL: `done`, `blocked`.

### 6.4 CONTEXT
- `constraints` (**MUST**) — Policies, deadlines, or non-negotiables.  
- `assets` (**MUST**) — Key artifacts (documents, repos) described abstractly.  
- `stakeholders` (**MUST**) — Who must be satisfied; names may be hashed.  
- OPTIONAL: `tools`, `repos`, `notes`.

### 6.5 DECISIONS
- `recent` (**MUST**) — Latest decision made (short clause).  
- `rationale` (**MUST**) — Why the decision stands.  
- `impacts` (**MUST**) — Expected outcomes or trade-offs.  
- OPTIONAL: `adr_refs`, `warnings`.

### 6.6 TASKS
- `priority_list` (**MUST**) — Ordered actions encoded as `1) ... ; 2) ...`.  
- OPTIONAL: `calendar` (relative timing), `owner`.

### 6.7 STYLE
- `tone` (**MUST**) — e.g., `direct`, `supportive`, `technical`.  
- `communication` (**MUST**) — Preferred cadence or format.  
- `preferences` (**MUST**) — Reusable heuristics (e.g., “cite specs before coding”).  
- OPTIONAL: `anti_patterns`, `guardrails`.

### 6.8 OPTIONAL Block
- Extensible container for telemetry such as `checksum`, `session`, `vendor`, `ext.note`.  
- MUST only appear after STYLE.  
- MUST remain under 512 characters.

## 7. Size & Performance
- Total payload **MUST NOT** exceed 10,240 bytes.  
- Encoders **SHOULD** keep payloads under 4,096 bytes.  
- Blocks with more than 512 characters SHOULD be summarized further.  
- Truncated payloads MUST still include all CORE blocks even if their contents degrade to `field=UNKNOWN`.

## 8. Versioning & Compatibility
- `v` attribute declares the snapshot format. v1.0 is the baseline.  
- Future versions may add new blocks or fields but MUST preserve existing semantics.  
- Decoders encountering newer versions MUST attempt to parse known blocks and treat unknown blocks as extensions.  
- Encoders targeting older decoders MAY down-level by removing unknown fields while keeping canonical order.

## 9. Error Handling
- **Missing Block** — Payload is invalid; loader MUST request re-encode.  
- **Missing Field** — Loader MUST treat as `field=UNKNOWN` and warn the user.  
- **Duplicate Field** — Loader MUST keep the first occurrence and warn about duplicates.  
- **Malformed Clause** — Loader MUST skip the clause, note the error, and continue if at least one valid clause remains per block.  
- **Oversized Payload** — Loader SHOULD instruct the user to compress or split GOALS/TASKS.  
- **Unknown Block** — Loader MUST ignore it but MAY echo it back untouched.

## 10. Example Snapshot
```
<RL4-CODEX v="1.0">
  <IDENTITY>
    persona=Lead Backend Engineer | project=NovaPay API rewrite | style=decisive
  </IDENTITY>
  <STATE>
    mode=focus | phase=stabilization | focus=eliminate latency regressions | energy=steady
    | risks=cache-invalidation, api-downtime
  </STATE>
  <GOALS>
    short_term=ship hotfix, finish perf tests, sync with mobile team
    | long_term=reach p95<120ms, enable zero-downtime deploys
  </GOALS>
  <CONTEXT>
    constraints=PCI scope, downtime<30s | assets=perf dashboard, incident log
    | stakeholders=payments squad, compliance lead
  </CONTEXT>
  <DECISIONS>
    recent=freeze feature flags | rationale=reduce moving parts
    | impacts=slower roadmap but safer rollout
  </DECISIONS>
  <TASKS>
    priority_list=1) patch redis cluster ; 2) rerun soak tests ; 3) write rollout note
  </TASKS>
  <STYLE>
    tone=direct | communication=bullet updates hourly | preferences=cite metrics before opinions
  </STYLE>
  <OPTIONAL>
    session=hash#81aa | checksum=sha1#902fe1
  </OPTIONAL>
</RL4-CODEX>
```

## 11. Validation Checklist
Encoders SHOULD run the following before sharing a snapshot:
1. All CORE blocks present and non-empty.  
2. Field count per block ≤ 8 (to maintain readability).  
3. Payload length < 4 KB (warning issued if above).  
4. No raw credentials, code, or file paths.  
5. Clauses use only ASCII punctuation and avoid Markdown bullets.  
6. OPTIONAL block, if present, follows STYLE and stays under 512 characters.

---

**End of RL4-CODEX v1.0 Specification**

