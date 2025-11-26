# RL4-CODEX Dual-Mode Behavioral Compliance Contract

This document defines the MUST / SHOULD / MAY obligations for any LLM operating RL4.CODEX Simple Mode (v1.0) or Complex Mode (v1.1). Requirements follow RFC 2119 terminology.

---

## 1. Roles
- **Simple Mode Encoder** — Produces a single `<RL4-CODEX v="1.0">` snapshot.  
- **Simple Mode Loader** — Consumes that snapshot and resumes work.  
- **Fragment Encoder** — Emits `<RL4-CODEX-FRAGMENT v="1.0">` updates.  
- **Complex Mode Loader/Merger** — Loads `<RL4-CODEX v="1.1">` plus fragments, merges them, and continues the workflow.

---

## 2. Simple Mode (v1.0) Requirements

### 2.1 Encoder Obligations
| ID | Requirement | Level |
| --- | --- | --- |
| S-E1 | Capture all ten required fields exactly once (USER_IDENTITY → LLM_BEHAVIOR_RULES). | MUST |
| S-E2 | Keep payload ≤ 4 KB; warn at 3.5 KB; refuse >10 KB. | MUST |
| S-E3 | Ask clarifying questions for missing data instead of inventing values. | MUST |
| S-E4 | Never include raw code, secrets, file paths, or verbatim logs. | MUST |
| S-E5 | Preserve user wording whenever safety allows (identity, style, constraints). | SHOULD |
| S-E6 | Output ONLY the `<RL4-CODEX>` block with no commentary. | MUST |

### 2.2 Loader Obligations
| ID | Requirement | Level |
| --- | --- | --- |
| S-L1 | Validate presence/order of all fields before using the snapshot. | MUST |
| S-L2 | Restate each field in natural language, preserving tone and priorities. | MUST |
| S-L3 | Explicitly label missing/ambiguous fields as `UNKNOWN`. | MUST |
| S-L4 | Prompt the user for next steps after summarizing. | MUST |
| S-L5 | Keep the decoded summary concise (< 400 words). | SHOULD |
| S-L6 | Provide recommendations if payload hygiene is poor (e.g., too long). | MAY |

---

## 3. Complex Mode (v1.1 + Fragments) Requirements

### 3.1 Fragment Encoder
| ID | Requirement | Level |
| --- | --- | --- |
| C-F1 | Include `TIMESTAMP` (ISO 8601 UTC) in every fragment. | MUST |
| C-F2 | Restrict content to updates: NEW_CONTEXT, UPDATED_DECISIONS, UPDATED_TASKS, UPDATED_CONSTRAINTS, STYLE_UPDATES, NOTES. | MUST |
| C-F3 | Never restate identity fields or LLM behavior rules. | MUST |
| C-F4 | Keep fragments ≤ 1 KB each; split if necessary. | SHOULD |
| C-F5 | Emit fragments with no commentary beyond the XML block. | MUST |

### 3.2 Complex Loader / Merger
| ID | Requirement | Level |
| --- | --- | --- |
| C-L1 | Load the base `<RL4-CODEX v="1.1">` snapshot first. | MUST |
| C-L2 | Sort all fragments by `TIMESTAMP` ascending and apply chronologically. | MUST |
| C-L3 | In conflicts (two fragments touching the same field), latest wins BUT loader MUST report `CONFLICT:` with both values. | MUST |
| C-L4 | Preserve identity, constraints, and behavior rules exactly as declared in the base snapshot. | MUST |
| C-L5 | Ignore unknown fragment fields but echo them back under an “Ignored fields” note. | SHOULD |
| C-L6 | After merging, output a refreshed `<RL4-CODEX v="1.1">` or a structured summary showing the merged state plus unresolved conflicts. | SHOULD |

---

## 4. Fragment Compliance Rules
1. **Chronology** — Fragments MUST be applied strictly in timestamp order; back-dated fragments must be inserted at the correct historical point.  
2. **Idempotency** — Reapplying a fragment set MUST yield the same merged state.  
3. **Isolation** — Missing fragments MUST be reported (`ERROR:missing-fragment:<timestamp>`); loaders MUST NOT guess.  
4. **Merge Transparency** — Every overridden field MUST mention the fragment that caused the override.  
5. **Conflict Surfacing** — When two fragments compete and neither can be auto-resolved, the loader MUST surface the conflict and pause until the user decides.

---

## 5. Backward Compatibility & Versioning
- Loaders MUST accept v1.0 snapshots even when operating in v1.1 mode.  
- Encoders targeting models that only know v1.0 MAY down-level by omitting fragments.  
- Unknown version numbers MUST trigger `WARN:unsupported-version` while still attempting best-effort parsing.  
- Future v1.2+ metadata blocks MUST NOT invalidate v1.0/v1.1 behavior.

---

## 6. Error Tolerance & Recovery
- Missing fields or size violations → Loader MUST explain and request a clean snapshot.  
- Truncated fragment lists → Loader MUST stop merging and request the absent entries.  
- Serialization errors → Encoder MUST retry rather than emitting malformed XML.  
- Determinism → Same input snapshots/fragments MUST always yield the same merged state and same summary unless new data is introduced.

---

## 7. Extension Handling
- Fields using `ext.` prefixes MUST be preserved verbatim by loaders and merge assistants.  
- Vendor telemetry inside `<OPTIONAL>` MUST not exceed 512 characters and MUST NOT override required fields.  
- Loaders SHOULD echo unknown extensions back to the user so they know the data survived.

---

## 8. Compliance Levels
- **Full Compliance** — All MUST and SHOULD satisfied for both modes.  
- **Simple-Only Compliance** — All Simple Mode MUST satisfied (Complex Mode unsupported).  
- **Partial Compliance** — All MUST satisfied but some SHOULD unmet.  
- **Non-Compliant** — Any MUST violated.

Implementations MUST declare their compliance level in release notes or model cards.

