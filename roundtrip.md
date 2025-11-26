# RL4-CODEX Dual-Mode Round-Trip Protocol

This protocol verifies that both Simple Mode (v1.0) and Complex Mode (v1.1) snapshots survive encode → load → re-encode cycles across multiple LLMs without semantic drift.

---

## 1. Simple Mode Round Trip (v1.0)
1. **Encode S₁** — Model A runs the Simple Mode generator prompt and produces `<RL4-CODEX v="1.0">`.  
2. **Load** — Model B runs the Simple Mode loader prompt, confirms understanding, and states readiness.  
3. **Re-Encode S₂** — Model B immediately re-encodes the same context (still in Simple Mode).  
4. **Re-Load** — Model A loads S₂.  
5. **Compare** — Humans verify S₁ vs S₂ for semantic equivalence.

Repeat at least three times per model pairing. Any change to identity, goals, decisions, constraints, tasks, or behavior rules counts as a failure.

---

## 2. Complex Mode Round Trip (v1.1 + fragments)
1. **Encode Sfull** — Model A creates `<RL4-CODEX v="1.1">`.  
2. **Generate Fragments F₁..Fₙ** — Over time, Model A (or the user) produces `<RL4-CODEX-FRAGMENT v="1.0">` entries.  
3. **Transfer Set** — Provide Sfull plus all fragments (sorted by timestamp) to Model B.  
4. **Load + Merge** — Model B runs the Complex Mode loader prompt, merges fragments, surfaces conflicts, and continues the session.  
5. **Re-Encode Sfull₂** — After acting, Model B emits a refreshed `<RL4-CODEX v="1.1">` plus new fragments if needed.  
6. **Re-Load** — Model A loads Sfull₂ (and any new fragments) to confirm continuity.  
7. **Compare** — Human reviewers diff Sfull vs Sfull₂, ensuring the fragment history was honored.

Criteria:
- All fragments applied in chronological order (latest wins).  
- Identity and constraints unchanged.  
- Conflicts surfaced explicitly.  
- Tasks/goals ordering preserved unless the fragment intentionally changed it.  
- Notes/history fields may grow, but meaning must remain intact.

---

## 3. Semantic Equivalence Criteria
Snapshots/fragments are equivalent when:
- Identity, constraints, reasoning style, and behavior rules communicate the same instructions.  
- Goals and tasks retain their order and intent.  
- Decisions/next steps remain logically consistent.  
- Fragment merges do not lose data; new fragments only add or override explicitly.  
- Optional metadata (checksums, vendor tags) may differ.

---

## 4. Failure Modes & Remedies
| Failure | Symptom | Remedy |
| --- | --- | --- |
| Missing field | Loader invents or omits required sections | Re-run generator, ensure all 10 Simple Mode fields present |
| Fragment drop | Later snapshot lacks updates from Fᵢ | Enforce timestamp sorting and checksum fragments in CI |
| Conflict silence | Competing fragments overwrite silently | Loader MUST emit `CONFLICT:` lines and request direction |
| Inflation | Payload grows beyond 4 KB (Simple) or 10 KB total | Summarize goals/tasks, archive stale fragments |
| Order drift | Priority or chronology changes unintentionally | Require loaders to quote existing tasks before editing |

---

## 5. Cross-LLM Test Matrix
| Flow | Purpose |
| --- | --- |
| GPT-4 → Claude 3 → GPT-4 | Baseline compliance (Simple + Complex) |
| Claude 3 → Gemini 1.5 → Claude 3 | Confirms non-OpenAI interoperability |
| GPT-4 → Command R+ → GPT-4 | Stress-tests instruction-heavy models |
| Mistral Large → GPT-4 → Mistral Large | Ensures open-weight ↔ hosted compatibility |
| GPT-4 → Claude 3 (fragments only) | Validates fragment-only updates with no new full snapshot |

Teams SHOULD complete at least two distinct vendor paths per mode before claiming production readiness.

---

## 6. Execution Checklist
- [ ] Use identical briefs for all encoders (identity, goals, tasks, constraints).  
- [ ] Log every snapshot and fragment verbatim with timestamps.  
- [ ] Track payload sizes and note when compression is required.  
- [ ] Store loader summaries to inspect for hallucinations/conflict handling.  
- [ ] Diff snapshots/fragment histories after each cycle (manual or scripted).  
- [ ] Archive validated examples inside `examples/merged-snapshot-example.md`.

---

## 7. Automation Hints
- Hash each block (or fragment) to detect silent alterations.  
- Use spreadsheets or CI to track equivalence verdicts across model pairs.  
- Introduce a neutral “arbiter” model when teams disagree on drift.  
- For Complex Mode, maintain a manifest (`timestamp`, `hash`, `type`) for every fragment so missing entries are obvious.

Consistent, lossless round trips are mandatory before advertising RL4-CODEX compatibility.

