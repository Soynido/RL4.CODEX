# RL4-CODEX Decoder Specification (v1.2)

Status: Implementer Guidance  
Scope: Defines the normative decoder behavior for reconstructing a `CognitiveState` object from any RL4-CODEX v1.1 or v1.2 payload.

## 1. Decoder Objectives
1. Parse every required block (DATA, TIMELINE, CONTEXT, INSIGHTS, DECISIONS) in canonical order.  
2. Gracefully accept optional extensions and unknown tags without failing.  
3. Reconstruct a structured `CognitiveState` while preserving semantic fidelity.  
4. Surface deterministic warnings or errors when payloads are partial, corrupted, or non-conformant.  
5. Maintain forward compatibility with future protocol versions.

## 2. Input Requirements
- Input MUST be a UTF-8 string beginning with `<RL4-CODEX`.  
- Decoders MUST validate against Section 10 of `spec.md` prior to reconstruction.  
- Payload size MUST NOT exceed 10,240 bytes; otherwise return `ERROR:size-exceeded`.  
- Unknown versions (e.g., `v="1.3"`) MAY be parsed but MUST produce `WARN:unsupported-version`.

## 3. Decoding Pipeline
1. **Normalize**: Trim whitespace, collapse repeated spaces, ensure newline endings.  
2. **Extract Blocks**: Use a streaming XML or regex parser that captures block boundaries without altering token order.  
3. **Validate**: Re-run the validation algorithm. Abort if required blocks are missing.  
4. **Parse Tokens**: Split each block on `|` (fallback to commas). Preserve original token order.  
5. **Map Tokens**: Convert tokens into typed fields per Sections 4–8.  
6. **Assemble CognitiveState**: Populate the structure described in Section 7.  
7. **Emit Diagnostics**: Collect warnings/errors for the caller.

## 4. Block Parsers
### 4.1 DATA Block Parser
- Expect tokens for `proj`, `mode`, `phase`, `tasks`, `kpi:*`, `cog.health`, `maturity`.  
- Missing KPIs MUST result in `UNKNOWN` values rather than zeros.  
- Malformed tokens (e.g., `tasks=abc`) SHOULD trigger `WARN:data-malformed:<token>` and be skipped.  
- Hash validation: tokens with `#` MUST match `[a-z]{3,}#[0-9a-f]{4,}`; otherwise warn.

### 4.2 TIMELINE Block Parser
- Split `actions` token into a list (e.g., `edit+analysis`).  
- Translate bursts/droughts/anomalies into `TemporalContext.events[]`.  
- Hotspots MUST remain hashed; decoders MUST NOT attempt reversal.  
- If cycles count is missing, set `cycles=UNKNOWN` and warn `WARN:timeline-missing-cycles`.

### 4.3 CONTEXT Block Parser
- Map `type`, `stack`, `devDNA`, `constraints`, `success`, `reasoning`, `risk`.  
- Additional constraints SHOULD be stored in a key-value map for downstream policies.  
- Stack tokens prefixed with `hash(...)` SHOULD be converted into normalized arrays if possible.

### 4.4 INSIGHTS Block Parser
- Tokens follow `kind:label(weight=x.xx,conf=y.yy)` or `kind:label(conf=y.yy)`.  
- The parser MUST enforce numeric ranges (0 ≤ value ≤ 1). Violations trigger `WARN:insight-range`.  
- Parsed insights populate `CognitiveSignals.patterns[]`, `trends[]`, `forecasts[]`, `correlations[]`.

### 4.5 DECISIONS Block Parser
- Extract ADR hashes, drift types, anomalies, integrity state, and adjustments.  
- Integrity MUST be mapped onto an enum {`GREEN`, `AMBER`, `RED`}. Unknown values default to `UNKNOWN`.  
- Adjustments SHOULD form an ordered list preserving encoder priority.

### 4.6 OPTIONAL Block Parser
- Recognize `vendor`, `encoder`, `session`, `checksum`, `policy`, and any `ext.*` tokens.  
- Unknown optional fields MUST be stored verbatim under an extensions map.  
- Optional overflow ( > 512 chars ) SHOULD already be caught during validation; if still encountered, truncate with warning.

## 5. Handling Missing or Unknown Fields
- Any absent token MUST be recorded as `UNKNOWN`.  
- Decoders MUST NOT invent values or default numbers to zero.  
- When a block is present but empty, fill its section with `UNKNOWN` markers and emit `WARN:block-empty:<name>`.  
- Unknown tags at the top level MUST be ignored while preserving the rest of the payload.

## 6. Corrupted or Partial Payloads
| Condition | Required Behavior |
| --- | --- |
| Missing required block | Fail fast with `ERROR:missing-block:<name>`; optional attempt to return partial state if caller requests. |
| Malformed XML | Reject payload, emit `ERROR:unparseable`. |
| Hash rule violation | Reject payload, emit `ERROR:hash-violation:<token>`. |
| Optional block overflow | Truncate field, emit `WARN:optional-truncated`. |
| Unsupported version | Parse best-effort, emit `WARN:unsupported-version`, flag fields added after known version as `UNKNOWN`. |

## 7. CognitiveState Reconstruction
```
CognitiveState {
  projectContext: {
    projectHash, mode, phase, taskLoad, maturity, constraints[], successCriteria[]
  }
  temporalContext: {
    cycles, actions[], bursts[], droughts[], hotspots[], anomalies[]
  }
  cognitiveSignals: {
    patterns[], trends[], forecasts[], correlations[]
  }
  decisionContext: {
    adrRefs[], driftSignals[], anomalies[], integrity, adjustments[]
  }
  developerProfile: {
    devDNA, reasoningStyle, riskProfile, stackDescriptors[]
  }
  optionalMetadata: {
    vendor?, encoder?, session?, checksum?, policy?, extensions{}
  }
}
```
All arrays preserve encoder order. Fields marked `?` are optional.

## 8. Reference Decoder Pseudocode
```
function decodeRL4Codex(payload):
  normalize(payload)
  blocks = extractBlocks(payload)
  diagnostics = []
  required = ["DATA","TIMELINE","CONTEXT","INSIGHTS","DECISIONS"]

  for name in required:
    if name not in blocks:
      raise Error("ERROR:missing-block:" + name)

  state = CognitiveState()
  state.projectContext = parseData(blocks["DATA"], diagnostics)
  state.temporalContext = parseTimeline(blocks["TIMELINE"], diagnostics)
  state.developerProfile, state.projectContext.constraints = \
      parseContext(blocks["CONTEXT"], diagnostics)
  state.cognitiveSignals = parseInsights(blocks["INSIGHTS"], diagnostics)
  state.decisionContext = parseDecisions(blocks["DECISIONS"], diagnostics)

  if "OPTIONAL" in blocks:
    state.optionalMetadata = parseOptional(blocks["OPTIONAL"], diagnostics)

  return state, diagnostics
```
Implementations MAY adapt the helper functions but MUST preserve the observable behavior.

## 9. Graceful Degradation Strategy
- If DATA or TIMELINE parsing fails, the decoder SHOULD still return partial structures for other blocks.  
- Diagnostics MUST differentiate between blocking errors (`ERROR:*`) and recoverable warnings (`WARN:*`).  
- When replaying into an agent, missing segments SHOULD be announced as “context unavailable” rather than silently ignored.

## 10. Version Stability Matrix
| Payload Version | Decoder Requirement | Stable Structures |
| --- | --- | --- |
| v1.0 | SHOULD be supported when feasible; treat INSIGHTS/DECISIONS as free-form tokens. | DATA/TIMELINE/CONTEXT |
| v1.1 | MUST be supported; OPTIONAL block may be absent. | All required blocks |
| v1.2 | MUST be fully supported; OPTIONAL block includes vendor/session/checksum/policy. | All blocks + optional metadata map |

Future versions will extend OPTIONAL semantics first; decoders MUST store unknown optional tokens verbatim.

## 11. LLM-Only Decoder Instructions
Prompt template:
```
You are an RL4-CODEX decoder.
Given the payload below, output:
1. Project context
2. Timeline summary
3. Cognitive insights
4. Decision context
5. Optional metadata
Mark missing items as UNKNOWN. Never invent code or filenames.
Payload:
<RL4-CODEX ...>
```
Agents MUST present UNKNOWN markers verbatim so downstream workflows can branch deterministically.

## 12. IDE / Agent Integration Patterns
- **IDE Restore**: Upon reopening a workspace, load the latest payload, run `decodeRL4Codex`, and hydrate UI panels with project/timeline data while logging any warnings.  
- **Multi-Agent Relay**: Attach the payload to handoff messages; receiving agent decodes and continues reasoning without re-reading files.  
- **Observability Pipelines**: Store decoded CognitiveStates as structured logs; correlate KPIs over time without exposing source code.  
- **CI/CD Gates**: Decode payloads emitted at each stage to ensure integrity state is `GREEN` before promoting builds.

## 13. Compliance Checklist
- [ ] Validation algorithm executed before decoding.  
- [ ] All five required blocks parsed and represented.  
- [ ] Unknown tags ignored but preserved.  
- [ ] Missing fields surfaced as `UNKNOWN`.  
- [ ] Diagnostics emitted for every warning/error scenario.  
- [ ] CognitiveState object matches the schema in Section 7.  
- [ ] Optional metadata stored without reinterpretation.  
- [ ] Decoder tested against the compliance suite in `spec.md` §12.

Implementers MUST satisfy the checklist prior to releasing any decoder.

