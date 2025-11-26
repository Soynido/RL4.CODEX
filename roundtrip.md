# RL4-CODEX v1.0 Round-Trip Reliability Protocol

This procedure validates that snapshots remain stable when passed between different LLMs or humans. Follow it to certify multi-model interoperability.

---

## 1. Test Overview
1. **Encode** — Model A (Encoder) generates an RL4-CODEX snapshot from a controlled brief.  
2. **Load** — Model B (Loader) ingests the snapshot, restates context, and reports readiness.  
3. **Re-Encode** — Model B immediately re-encodes the state based on its understanding.  
4. **Re-Load** — Model A loads Model B’s snapshot.  
5. **Compare** — Humans compare the original and round-tripped snapshots for semantic equivalence.

This completes one “round trip”. Perform at least three trips per pairing to establish stability.

---

## 2. Semantic Equivalence Criteria
Snapshots are considered equivalent when:
- All CORE fields convey the same meaning, even if wording differs.  
- Ordered lists (e.g., `priority_list`) preserve their order and essence.  
- Styles, constraints, and decisions remain aligned (no contradictions).  
- Optional telemetry may differ (e.g., new checksum) as long as CORE intent is intact.

Any drift that changes goals, decisions, or priority ordering counts as a failure.

---

## 3. Failure Modes & Remedies
| Failure | Symptom | Remedy |
| --- | --- | --- |
| Missing block | Loader refuses or hallucinated data | Re-run encoding with generator prompt, ensure fields captured |
| Inflation | Payload exceeds 4 KB after round trip | Compress clauses, remove filler words |
| Style drift | Tone/communication preferences mutate | Remind loaders to quote STYLE as-is |
| Task reordering | Priority sequence changes | Require loaders to restate tasks verbatim before re-encoding |
| Lossy goals | Short or long-term goals disappear | Add explicit checklist verifying GOALS content |

---

## 4. Multi-Model Test Matrix
| Pair | Direction | Notes |
| --- | --- | --- |
| GPT-4 → Claude 3 → GPT-4 | Encode → Load → Re-encode → Load | Baseline comparison |
| Claude 3 → Gemini 1.5 → Claude 3 | Tests non-OpenAI bridging |
| GPT-4 → Command R+ → GPT-4 | Checks instruction-heavy compliance |
| Mistral Large → GPT-4 → Mistral Large | Ensures open-weight compatibility |

Teams SHOULD cover at least two distinct vendor pairs before declaring production readiness.

---

## 5. Execution Checklist
- [ ] Provide identical briefs to all encoders (persona, goals, constraints, tasks).  
- [ ] Log each payload verbatim with timestamps.  
- [ ] Record loader summaries to inspect for hallucinations.  
- [ ] Run diff tooling on the original vs round-tripped snapshots, focusing on CORE blocks.  
- [ ] File issues for any drift and iterate on prompts until results stabilize.  
- [ ] Store validated payloads in `examples/` for future regressions.

---

## 6. Automation Hints
- Use a spreadsheet to track payload sizes, drift notes, and compliance status.  
- Consider hashing each block to detect subtle alterations (e.g., SHA-1 per block).  
- When models disagree, run a third “arbiter” model to identify discrepancies objectively.

Consistent, lossless round trips are mandatory before RL4-CODEX can be advertised as production-ready in a given workflow.

