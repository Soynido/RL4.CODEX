# RL4-CODEX v1.2 — Cognitive State Exchange Protocol (Normative Specification)

Status: Draft Standard  
Maintainer: R-Labs  
Date: 2025-11-26

## 1. Introduction
RL4-CODEX defines a deterministic, content-safe method for serializing the cognitive state of an AI project or conversation into a compact payload that can be exchanged between large-language-model agents, IDE extensions, or automation frameworks without revealing raw code or proprietary text. Version 1.2 supersedes v1.1 while remaining backward compatible.

## 2. Conventions and Terminology
- The key words **MUST**, **SHOULD**, **MAY**, **MUST NOT**, and **SHOULD NOT** are to be interpreted as described in RFC 2119.  
- **CognitiveState**: Structured internal representation containing `ProjectContext`, `TemporalContext`, `CognitiveSignals`, `DecisionContext`, and `DeveloperProfile`.  
- **Cycle**: A cohesive burst of reasoning or execution (e.g., edit, refactor, analysis).  
- **Hashed Identifier**: A deterministic, non-reversible token (min. 8 base16 chars) prefixed with a namespace, e.g., `mod#1fa0`.  
- **Token Stream**: Compact textual sequence separated by `|` or commas, with no JSON objects.  
- **Unknown Tag**: Any XML-like element not defined in this specification; must be ignored but preserved when relaying payloads.

## 3. Protocol Overview
An RL4-CODEX payload is a single `<RL4-CODEX>` element containing five required child blocks and one OPTIONAL block. Each block carries semantic abstractions of the workspace state, recent activity, context, cognitive insights, and decisions. The protocol enforces canonical ordering, deterministic serialization, and strict zero-content rules.

## 4. Conformance Requirements
### 4.1 Encoders MUST
- Produce payloads with blocks ordered `DATA`, `TIMELINE`, `CONTEXT`, `INSIGHTS`, `DECISIONS`, `OPTIONAL`.  
- Respect all hashing/normalization rules (Section 7).  
- Guarantee payload size under 10,240 bytes (target ≤ 4,096 bytes).  
- Emit only ASCII characters (UTF-8 encoded).  
- Include all required blocks even if their contents are empty placeholders.  
- Validate payloads against the algorithm in Section 10 before distribution.

### 4.2 Decoders MUST
- Parse any payload that conforms to Sections 5–8.  
- Treat missing fields within a block as `UNKNOWN`.  
- Ignore and preserve unknown tags.  
- Surface validation errors in a deterministic warning format (Section 11).  
- Reconstruct a `CognitiveState` object as described in `decoder.md`.

## 5. Canonical Payload Model
### 5.1 Structure
```
<RL4-CODEX v="1.2">
  <DATA>...</DATA>
  <TIMELINE>...</TIMELINE>
  <CONTEXT>...</CONTEXT>
  <INSIGHTS>...</INSIGHTS>
  <DECISIONS>...</DECISIONS>
  <OPTIONAL>...</OPTIONAL> <!-- optional -->
</RL4-CODEX>
```
Whitespace is flexible; indentation is not prescribed.

### 5.2 Size Guarantees
- Total payload **MUST NOT** exceed 10,240 bytes.  
- Encoders **SHOULD** target ≤ 4,096 bytes; decoders **MUST** support up to the hard limit.  
- OPTIONAL blocks **MUST NOT** exceed 512 characters combined.

### 5.3 Token Grammar
```
payload      := "<RL4-CODEX v=\"1.2\">" blocks "</RL4-CODEX>"
blocks       := data timeline context insights decisions optional?
stream       := token (separator token)*
token        := label [ "=" value ] | label ":" value | symbol
separator    := " | " | " , "
```
JSON, YAML, base64 blobs, or raw text excerpts are forbidden.

## 6. Block Specifications
Each block is a token stream. Within a block, tokens **SHOULD** be ordered from most to least recent relevance, breaking ties alphabetically.

### 6.1 DATA — Current Cognitive State
- MUST describe: `project hash`, `mode` (`strict`, `normal`, `flexible`, `exploratory`), `phase`, `task counts`, `KPI set`, `cognitive health score (0–1)`, `maturity tier`.  
- MAY include additional hashed indicators (e.g., `focus`, `energy`).  
- Example tokens: `proj#7fa2`, `mode=strict`, `phase=build`, `tasks=3.active/7.total`, `kpi:velocity=steady`, `cog.health=0.82`.

### 6.2 TIMELINE — Recent Activity (24–48h)
- MUST include `cycles`, `action mix`, `bursts/droughts`, `hotspot hashes`, and `temporal anomalies`.  
- SHOULD avoid timestamps; use symbolic descriptors (`night-shift`, `idle-dip`).  
- Example tokens: `cycles=5`, `actions=edit+analysis`, `bursts=2`, `hotspots=mod#a913`, `anomalies=idle-dip`.

### 6.3 CONTEXT — Stable Metadata
- MUST include project type, hashed stack descriptors, developer profile signals, constraints, success criteria, reasoning style, and risk posture.  
- Example tokens: `type=fullstack`, `stack=hash(fe,api,llm)`, `devDNA=focus/iterative`, `constraints=strict-zero-content`, `risk=drift-low`.

### 6.4 INSIGHTS — Cognitive Signals
- MUST encode patterns, correlations, forecasts, and trend signals.  
- Format: `pattern:<name>(weight=x.xx,conf=y.yy)` or `trend:<name>(conf=y.yy)`.  
- Weights and confidences MUST be normalized floats between 0 and 1 (two decimal precision).  
- Literal snippets or stack traces are prohibited.

### 6.5 DECISIONS — High-Level Decision Context
- MUST reference hashed ADR identifiers, drift signals (`plan`, `task`, `phase`), detected anomalies, integrity status (`green`, `amber`, `red`), and recommended adjustments.  
- Adjustments MUST be semantic (e.g., `adjust=stabilize-context-cache`).  
- DECISIONS MUST capture the “reasoning climate” at the time of encoding.

### 6.6 OPTIONAL — Vendor Metadata (v1.2 Enhancements)
The OPTIONAL block MAY include the following fields, each expressed as tokens:
- `vendor` — identifier of encoder implementation.  
- `encoder` — hashed agent or model reference.  
- `session` — stable fingerprint for continuity.  
- `checksum` — hash of the payload body (excluding `<RL4-CODEX>` wrapper).  
- `policy` — reference to governance policy hash.  
- Any additional tokens MUST stay under the OPTIONAL size budget and MUST NOT leak raw data.

Encoders MAY omit `<OPTIONAL>` entirely. If present, it MUST appear last.

## 7. Hashing and Zero-Content Rules
1. All identifiers for projects, modules, branches, ADRs, users, or files **MUST** be hashed using a deterministic function (minimum 64-bit entropy) and prefixed with a namespace (`proj#`, `mod#`, `adr#`).  
2. Raw filenames, code snippets, commit messages, timestamps, or stack traces **MUST NOT** appear anywhere in the payload.  
3. KPI names **SHOULD** use generic labels (`velocity`, `latency`, `quality`).  
4. When referencing technologies, encoders MUST normalize to abstract tokens (e.g., `stack=hash(fe,api,llm)` instead of `React+Node`).  
5. Payloads containing un-hashed PII or source content MUST be rejected by validators.

## 8. Deterministic Serialization Procedure
1. Gather the latest CognitiveState inputs.  
2. Normalize signals into token/value pairs.  
3. Sort tokens within each block alphabetically unless a recency order is defined (TIMELINE, INSIGHTS).  
4. Concatenate tokens using ` | ` separators; avoid trailing separators.  
5. Assemble blocks in canonical order, omitting `<OPTIONAL>` if empty.  
6. Prepend the XML header `<RL4-CODEX v="1.2">` and append the closing tag.  
7. Run validation (Section 10). If any rule fails, encoders MUST NOT emit the payload.

## 9. Forward Compatibility
- Decoders MUST ignore and preserve unknown tags or tokens.  
- Encoders MUST NOT introduce new required blocks without versioning the protocol.  
- Optional tokens **SHOULD** declare namespaces when extending semantics (`ext.foo=bar`).  
- When down-converting to v1.1, encoders MUST remove v1.2-only fields but retain the five required blocks.

## 10. Validation Algorithm (Pseudo-code)
```
function validate(payload):
  assert payload.startsWith("<RL4-CODEX")
  blocks = parseBlocks(payload)
  required = ["DATA","TIMELINE","CONTEXT","INSIGHTS","DECISIONS"]
  for name in required:
    assert name in blocks and len(blocks[name]) > -1
  assert blocks are in canonical order
  assert byteLength(payload) <= 10240
  for block in blocks:
    assert no raw code/text patterns (regex checks)
    assert all identifiers match namespace#hash
  if "OPTIONAL" in blocks:
    assert len(blocks["OPTIONAL"]) <= 512 chars
  return VALID
```
Encoders MUST implement equivalent logic; decoders SHOULD re-run validation before consuming the payload.

## 11. Error Handling
- **Missing Block**: treat payload as invalid; decoders MAY attempt partial reconstruction but MUST emit `ERROR:missing-block:<name>`.  
- **Size Exceeded**: reject payload with `ERROR:size-exceeded`.  
- **Hash Violation**: reject payload; report offending token.  
- **Unknown Version**: decoders MAY parse but MUST warn `WARN:unsupported-version`.  
- **Optional Block Overflow**: ignore excess tokens beyond 512 chars and warn `WARN:optional-truncated`.

## 12. Compliance Tests
Implementations MUST pass the following reference tests:
1. **Canonical Order Test**: shuffle blocks and confirm validator rejects.  
2. **Hash Enforcement Test**: inject raw filename, ensure rejection.  
3. **Size Test**: feed payload >10,240 bytes, expect failure.  
4. **Forward Compatibility Test**: add `<FUTURE>` tag; decoder ignores it while keeping data intact.  
5. **Optional Block Test**: include checksum + fingerprint; ensure decoder surfaces them without affecting required blocks.  
6. **Partial Data Test**: remove non-critical tokens inside DATA; decoder fills `UNKNOWN` and continues.

## 13. Security and Privacy Considerations
- Payloads MUST never leak proprietary content, secrets, or identifiers that can be reverse-engineered.  
- Hashes SHOULD use salt or namespace segregation when multiple organizations share the same pipeline.  
- Encoders SHOULD encrypt payloads at rest or in transit if required by policy; RL4-CODEX itself remains plaintext for interoperability.  
- OPTIONAL fingerprints MUST NOT be derived from unhashed user data.

## 14. Versioning Policy
- RL4-CODEX v1.2 is backward compatible with v1.1; decoders MUST accept v1.1 unless explicitly limited.  
- Minor releases (v1.x) MAY add OPTIONAL tokens or clarifications but MUST NOT break canonical ordering.  
- Major releases (v2.x) MAY redefine structure; encoders MUST update the `v` attribute accordingly.  
- Change history is tracked in repository commits; see README for version roadmap.

## 15. License
RL4-CODEX is distributed under the MIT License to encourage broad adoption and ecosystem growth.

---

**End of RL4-CODEX v1.2 Specification**

