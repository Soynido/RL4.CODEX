# RL4.CODEX

> RL4-CODEX Dual-Mode — Simple Mode (v1.0) + Complex Mode (v1.1)  
> _Copy/paste snapshots or fragments and resume anywhere._

---

## 1. What is RL4-CODEX?
RL4-CODEX is the universal, text-only way to save and reload your cognitive state across any LLM.  
**Simple Mode (v1.0)** = one self-contained `<RL4-CODEX>` block.  
**Complex Mode (v1.1)** = one full snapshot plus optional fragments to journal changes over time.

---

## 2. Choosing the Right Mode
| Mode | Use When | Snapshot Structure |
| --- | --- | --- |
| Simple (v1.0) | Short sessions, quick handoffs, non-technical users | Single `<RL4-CODEX v="1.0">` block with identity → rules |
| Complex (v1.1) | Multi-day work, research logs, multi-LLM teams | `<RL4-CODEX v="1.1">` + zero or more `<RL4-CODEX-FRAGMENT v="1.0">` blocks |

Fragments never repeat identity—they just capture incremental updates (new context, decisions, tasks, notes).

---

## 3. Quick Start — Simple Mode
1. Open any LLM and paste the **Simple Mode Snapshot Generator** prompt from [`prompts.md`](prompts.md).  
2. Answer the questions (identity, goals, constraints, tasks, rules).  
3. Copy the `<RL4-CODEX v="1.0">` block you receive.  
4. In another LLM, paste the **Simple Mode Loader** prompt, then paste your snapshot when asked.  
5. Continue working—your context just teleported.

---

## 4. Quick Start — Complex Mode with Fragments
1. Encode the baseline `<RL4-CODEX v="1.1">` snapshot using the Complex Mode generator prompt.  
2. As work progresses, paste the **Fragment Generator** prompt to create timestamped `<RL4-CODEX-FRAGMENT>` updates (e.g., new tasks, context, decisions).  
3. When switching LLMs, provide the Complex Mode loader with the full snapshot plus every fragment (chronological order).  
4. The loader merges fragments, surfaces conflicts, and resumes exactly where you left off.

**Fragment Workflow (Non-Technical Explanation)**  
Think of fragments as sticky notes you append to the main document. The loader reads the document, then applies each note in timestamp order, with the latest note overriding earlier details if they collide.

---

## 5. Repository Guide
| File | Purpose |
| --- | --- |
| `spec.md` | Official dual-mode specification (Simple + Complex + fragments). |
| `prompts.md` | Five canonical prompts (Simple generator/loader, fragment generator, merge assistant, complex loader). |
| `compliance.md` | MUST/SHOULD/MAY requirements for encoders, loaders, and fragment handling. |
| `roundtrip.md` | Test protocol covering Simple round trips and Complex fragment merges across LLMs. |
| `examples.md` | Narrative guidance plus good/bad payloads and truncation behavior. |
| `examples/` | Concrete `.md` payloads (Simple snapshot, full v1.1, fragment set, merged output). |
| `roadmap.md` | Plans for v1.1 (metadata) and v2.0 (compression + validation toolkit). |

---

## 6. Simple Mode Example
```
<RL4-CODEX v="1.0">
USER_IDENTITY: Product Engineering Lead for Atlas Mobile 2.3
USER_PREFERENCES: concise bullet replies, cite metrics first
WORK_STYLE: decisive, expects explicit next steps
CONTEXT_SUMMARY: stabilizing push notifications, RC week
ACTIVE_GOALS: close crashers, finish load tests, prep release doc
DECISIONS:
  - freeze feature flags
  - run nightly soak tests before sign-off
TASKS:
  1) triage crash signatures
  2) rerun soak tests
  3) draft release email
CONSTRAINTS: ship by Friday, no DB migrations, downtime <30s
NEXT_STEPS: rerun soak tests tonight, compare metrics, notify PM
LLM_BEHAVIOR_RULES: keep answers <150 words, confirm constraints before suggestions
</RL4-CODEX>
```

---

## 7. Complex Mode Example (Snapshot + Fragments)
See `/examples/full-snapshot-v1.1.md` for the base snapshot, plus `/examples/fragment-1.md` and `/examples/fragment-2.md` for incremental updates.  
`/examples/merged-snapshot-example.md` shows the result after applying both fragments in chronological order.

---

## 8. Round Trip Expectations
- **Simple Mode:** Encode → Load → Re-encode → Compare. Fields must match semantically.  
- **Complex Mode:** Encode → Add fragments → Load + Merge → Re-encode → Compare merged result.  
- Details and multi-model matrices live in [`roundtrip.md`](roundtrip.md).

---

## 9. Edge Cases & Limitations
- Oversized payload? Summarize goals/tasks or archive older fragments.  
- Sensitive content? Hash or describe abstractly—never paste raw code or secrets.  
- Missing fragment? Loader must warn and request it instead of guessing.  
- Conflicts? Loader surfaces them verbatim and asks which version to keep.

---

## 10. Contributing
1. Review `spec.md`, `compliance.md`, and `roadmap.md`.  
2. Discuss changes via issue/PR (one feature per PR).  
3. Provide updated examples/tests plus round-trip evidence.  
4. Keep documentation copy/paste friendly.  
5. License remains MIT—everything stays open.

---

## 11. License
MIT License — free to adopt, fork, and extend as long as the standard stays interoperable.

