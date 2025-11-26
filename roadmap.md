# RL4-CODEX Roadmap

This roadmap captures the evolution plan beyond v1.0. It is intentionally concise to keep the standard focused and predictable.

---

## 1. Current Release — v1.0
- Canonical `<RL4-CODEX v="1.0">` structure (IDENTITY → STYLE).  
- Prompts, compliance contract, round-trip protocol, and public examples.  
- Text-only workflow with manual copy/paste across any LLM.

---

## 2. Near-Term Goals — v1.1 (Planned)
| Area | Objective |
| --- | --- |
| Optional Fields | Introduce `HISTORY` block for compressed timeline summaries (still optional). |
| Validation | Publish lightweight CLI/script that checks size, ordering, and missing fields locally. |
| Accessibility | Provide localized prompt templates (ES/FR/JP) while keeping payload language agnostic. |
| Adoption | Partner with at least two LLM vendors to pre-load the encoder/loader prompts in their prompt libraries. |

---

## 3. Mid-Term Vision — v2.0 (Exploratory)
| Theme | Description |
| --- | --- |
| Structured Compression | Define an optional canonical dictionary and token codes to push payloads under 2 KB without losing readability. |
| Validation Toolkit | Ship official open-source tooling (CLI + API) for automated round-trip testing and diffing. |
| Multi-Modal Bridges | Explore metadata hooks for speech/video agents while keeping text snapshot backward compatibility. |
| Governance | Formalize an open working group with voting rules for future changes. |

---

## 4. Adoption Strategy
1. **Education** — Continue publishing high-quality examples, blog posts, and recorded demos showing cross-model handoffs.  
2. **Integrations** — Encourage IDE extensions, browser plugins, and note-taking apps to implement one-click “Copy RL4-CODEX” buttons.  
3. **Certification** — Offer a lightweight checklist (based on `compliance.md` and `roundtrip.md`) so vendors can self-certify compatibility.  
4. **Community** — Maintain GitHub issues for feature proposals; accept PRs that include updated tests + examples.  
5. **Feedback Loop** — Run quarterly surveys with users to detect friction and feed learnings into v1.1/v2.0 iterations.

---

Contributors should reference this roadmap before proposing changes to ensure alignment with the standard’s evolution path.

