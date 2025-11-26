# RL4-CODEX v1.0 Behavioral Compliance Contract

This document establishes the required behavior for any LLM acting as an RL4-CODEX encoder or loader. Compliance levels use RFC 2119 terminology.

---

## 1. Roles
- **Encoder LLM** — Collaborates with a human to produce a valid `<RL4-CODEX v="1.0">` payload.
- **Loader LLM** — Consumes an existing payload to rehydrate the human’s context and proceed.

---

## 2. Encoder Requirements
| # | Requirement | Level |
| --- | --- | --- |
| E1 | MUST collect enough information to populate all CORE blocks (IDENTITY, STATE, GOALS, CONTEXT, DECISIONS, TASKS, STYLE). | MUST |
| E2 | MUST maintain canonical block order and `label=value` clause formatting separated by `" | "`. | MUST |
| E3 | MUST keep payload <= 4 KB (warn user if approaching limit) and reject inputs that would exceed 10 KB. | MUST |
| E4 | MUST refuse to include raw code, secrets, file paths, or verbatim logs. Summaries only. | MUST |
| E5 | SHOULD ask clarifying questions when a required field is missing or ambiguous. | SHOULD |
| E6 | MAY add OPTIONAL block entries (`session`, `vendor`, `checksum`, `ext.*`) provided they stay under 512 characters. | MAY |
| E7 | MUST output ONLY the finalized `<RL4-CODEX>` payload with no preface or appendix unless the user explicitly asks for commentary. | MUST |
| E8 | SHOULD store the final payload in memory/context so the human can request a resend without re-encoding. | SHOULD |

---

## 3. Loader Requirements
| # | Requirement | Level |
| --- | --- | --- |
| L1 | MUST validate that all CORE blocks exist and appear in canonical order before acting on the snapshot. | MUST |
| L2 | MUST restate each block in natural language while preserving priorities, tone, and constraints. | MUST |
| L3 | MUST treat missing or malformed fields as `UNKNOWN` and inform the user of any gaps. | MUST |
| L4 | MUST ask the user what they want to do next after summarizing the snapshot. | MUST |
| L5 | SHOULD keep the decoded summary compact (< 400 words) to avoid overwhelming the user. | SHOULD |
| L6 | MUST NOT alter or “fix” the original payload text; it must remain available for future copying. | MUST |
| L7 | MAY suggest maintenance actions (e.g., re-encoding, trimming GOALS) if payload quality is poor. | MAY |
| L8 | MUST ignore unknown blocks or fields gracefully, echoing that they were skipped if relevant. | MUST |

---

## 4. Error Tolerance & Recovery
- If validation fails (missing block, duplicate field, >10 KB), the LLM MUST explain the issue and request a corrected snapshot.  
- If the payload appears truncated, the loader SHOULD ask for confirmation before proceeding.  
- Encoders encountering unclear inputs MUST pause and seek clarification rather than hallucinating values.  
- Both roles MUST remain deterministic: the same inputs must produce identical outputs unless the user provides new information.

---

## 5. Extension Handling
- Unknown fields using the `ext.` prefix MUST be preserved and echoed by loaders, even if their semantics are not understood.  
- Future block additions MUST be ignored by v1.0 loaders while relaying the data unmodified.  
- Encoders MAY include vendor-specific telemetry inside `<OPTIONAL>` as long as it stays non-invasive and under 512 characters.

---

## 6. Compliance Levels
- **Full Compliance** — All MUST and SHOULD requirements satisfied.  
- **Partial Compliance** — All MUST satisfied, some SHOULD unmet.  
- **Non-Compliant** — Any MUST violated.

Implementations MUST document their compliance level in public release notes or model cards.

