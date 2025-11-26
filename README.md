# RL4.CODEX

> RL4-CODEX v1.0 — Universal LLM cognitive snapshot standard  
> _Copy one block of text, resume any project anywhere._

---

## 1. What is RL4-CODEX?
RL4-CODEX is a lightweight text standard that captures your working memory—persona, state, goals, decisions, tasks, and style—inside a single `<RL4-CODEX>` payload. Copy it from one chat window, paste it into another LLM, and continue instantly. No SDKs, no extensions, no vendor lock-in.

---

## 2. Why it matters
- **Continuity** — Switch between GPT, Claude, Gemini, etc. without re-explaining.  
- **Portability** — Works offline, in docs, in terminals, anywhere text is allowed.  
- **Safety** — Designed to prevent raw code or secrets from leaking.  
- **Determinism** — Canonical block order keeps snapshots predictable.  
- **Extensibility** — Future versions can add blocks without breaking today’s payloads.

---

## 3. Quick Start (No Tools Needed)

### A. Encode your state
1. Open your favorite LLM.  
2. Paste the “Snapshot Generator Prompt” from [`prompts.md`](prompts.md).  
3. Answer the clarifying questions (persona, goals, tasks, etc.).  
4. Copy the `<RL4-CODEX v="1.0">` payload it returns.

### B. Load it elsewhere
1. Open any other LLM (or the same one later).  
2. Paste the “Snapshot Loader Prompt” from [`prompts.md`](prompts.md).  
3. When asked, paste your saved snapshot.  
4. The model summarizes the context and asks what you want to do next.

Done—you just teleported your context.

---

## 4. Repository Guide
| File | Purpose |
| --- | --- |
| `spec.md` | Authoritative v1.0 standard: data model, serialization, size rules, examples. |
| `prompts.md` | Canonical encoder/loader prompts (copy/paste friendly). |
| `compliance.md` | Behavioral contract for LLMs acting as encoders or loaders. |
| `roundtrip.md` | Multi-model test plan to verify snapshots survive encode ↔ load loops. |
| `examples.md` | Good / bad snapshots, minimal payload, truncation behavior. |
| `roadmap.md` | v1.1 & v2.0 direction + adoption strategy. |
| `examples/` | Raw `.codex` payloads for demos (legacy artifacts). |

---

## 5. Example Snapshot
```
<RL4-CODEX v="1.0">
  <IDENTITY>persona=Product Engineering Lead | project=Atlas Mobile 2.3 | style=decisive</IDENTITY>
  <STATE>mode=focus | phase=release candidate | focus=stabilize push notifications | energy=steady</STATE>
  <GOALS>short_term=close crashers, finish load tests | long_term=reduce latency</GOALS>
  <CONTEXT>constraints=ship by Friday | assets=QA dashboard | stakeholders=mobile PM</CONTEXT>
  <DECISIONS>recent=freeze feature flags | rationale=protect stability | impacts=feature debt grows</DECISIONS>
  <TASKS>priority_list=1) triage crashes ; 2) rerun soak tests ; 3) prep release note</TASKS>
  <STYLE>tone=direct | communication=hourly bullet update | preferences=cite metrics first</STYLE>
</RL4-CODEX>
```
Fits in a tweet thread, yet carries everything another model needs to continue.

---

## 6. Edge Cases & Limitations
- **Payload too long?** Trim GOALS/TASKS, replace lists with summaries, or move meta-notes into `<OPTIONAL>`.  
- **Sensitive content?** Hash identifiers or describe them abstractly. Never paste raw code or secrets.  
- **Truncated block?** Loaders must stop, warn, and request a resend (see `examples.md`).  
- **Multiple snapshots?** Name them clearly or store in versioned docs; never merge two payloads.

---

## 7. Contributing
1. Review `roadmap.md` + open issues.  
2. Propose a change (spec, prompts, tests, examples).  
3. Work on a feature branch and include updated docs/tests.  
4. Submit a PR referencing the relevant roadmap goal.  
5. Pass the round-trip protocol before claiming compliance.

---

## 8. License
MIT — build on it, remix it, embed it anywhere. Just keep the format open and interoperable.

