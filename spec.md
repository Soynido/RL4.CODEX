# 🏛️ Official Spec — RL4.CODEX Dual-Mode (v1.0 + v1.1)

RL4.CODEX — Dual-Mode Cognitive Snapshot Standard  
Version: 1.1

Modes:
- v1.0 — Simple Mode (full snapshot only)
- v1.1 — Complex Mode (full snapshot + fragments)

---

## 1. Purpose
RL4.CODEX defines a universal, LLM-agnostic, text-only cognitive snapshot format, allowing any user to save, reload, transfer, and continue their cognitive state across any LLM model or platform.

It enables:
- Single-block context restoration (Simple Mode)
- Incremental context journaling (Complex Mode)
- Cross-model round-trip continuity
- Long-term state portability

No tools, no extensions, no environment required. Only copy/paste.

---

## 2. Mode A — Simple Mode (v1.0)

### 2.1 Overview
Simple Mode uses one standalone block:
```
<RL4-CODEX v="1.0">
  ...
</RL4-CODEX>
```
It contains:
- identity
- goals
- constraints
- reasoning style
- decisions
- tasks
- context summary
- next steps
- system expectations for the LLM

It is complete and self-sufficient.

### 2.2 Required Fields
Each field MUST appear exactly once:
- `USER_IDENTITY`
- `USER_PREFERENCES`
- `WORK_STYLE`
- `CONTEXT_SUMMARY`
- `ACTIVE_GOALS`
- `DECISIONS`
- `TASKS`
- `CONSTRAINTS`
- `NEXT_STEPS`
- `LLM_BEHAVIOR_RULES`

### 2.3 Behavior Contract
LLMs MUST:
- Load and interpret all fields
- Maintain constraints and reasoning style
- Continue the user’s workflow exactly
- Not rewrite identity or change rules
- Not hallucinate missing fields

### 2.4 Use Cases
- Short sessions
- Beginners
- Non-technical users
- Simple context restoration
- Switching models once or twice

---

## 3. Mode B — Complex Mode (v1.1)

### 3.1 Overview
Complex Mode adds incremental fragments: mini-snapshots representing partial updates.

It consists of:
1. Full snapshot:
   ```
   <RL4-CODEX v="1.1">
     ...
   </RL4-CODEX>
   ```
2. 0 or more fragments:
   ```
   <RL4-CODEX-FRAGMENT v="1.0">
     TIMESTAMP: ...
     NEW_CONTEXT: [...]
     UPDATED_DECISIONS: [...]
     UPDATED_TASKS: [...]
     NOTES: [...]
   </RL4-CODEX-FRAGMENT>
   ```

Fragments allow incremental, chronological context accumulation.

### 3.2 Fragment Rules
- A fragment MUST NOT contain identity fields.
- A fragment MUST contain a TIMESTAMP.
- A fragment MAY contain context, decisions, tasks, constraints, style updates.
- A fragment MUST be mergeable with other fragments.
- Latest fragment wins in conflicts.

### 3.3 Merging Algorithm
Given:
- Full snapshot `S`
- Fragment list `F1..Fn` in chronological order

Apply:
```
for fragment in fragments:
    apply(fragment.changes)
```

Override rules:
- Fragment overrides snapshot fields where overlapping
- Missing fields remain untouched
- Conflicts MUST be surfaced
- Unknown fields SHOULD be ignored

### 3.4 Loader Requirements
LLMs MUST:
- Load full snapshot
- Detect fragments
- Merge fragments before generating NEXT_STEPS
- Maintain consistency of identity and constraints
- Warn if fragments conflict

### 3.5 Use Cases
- Long-running workflows
- Research
- Multi-day sessions
- Multi-LLM collaboration
- Journaling and progressive context accumulation

---

## 4. Versioning Model
- v1.0 — Simple Mode
- v1.1 — Adds fragments (backward compatible)

Future versions:
- v1.2 = metadata
- v2.0 = structural compression / auto-validation

No breaking changes allowed until v2.x.

---

## 5. Example — Simple Mode Snapshot
```
<RL4-CODEX v="1.0">
IDENTITY: ...
GOALS: ...
CONSTRAINTS: ...
DECISIONS: [...]
TASKS: [...]
NEXT_STEPS: ...
STYLE: ...
</RL4-CODEX>
```

---

## 6. Example — Complex Mode (v1.1)

Full Snapshot:
```
<RL4-CODEX v="1.1">
IDENTITY: ...
GOALS: ...
DECISIONS: [...]
CONTEXT_SUMMARY: ...
NEXT_STEPS: ...
</RL4-CODEX>
```

Fragment #1:
```
<RL4-CODEX-FRAGMENT v="1.0">
TIMESTAMP: 2025-11-26T10:14:20Z
UPDATED_DECISIONS:
  - clarified strategic objective X
UPDATED_TASKS:
  - added task "refactor section"
</RL4-CODEX-FRAGMENT>
```

Fragment #2:
```
<RL4-CODEX-FRAGMENT v="1.0">
TIMESTAMP: 2025-11-26T11:02:10Z
NEW_CONTEXT:
  - discussed design pattern Y
NOTES:
  - assistant agreed on constraint Z
</RL4-CODEX-FRAGMENT>
```

---

## 7. Round Trip Protocol (Dual-Mode)
Simple Mode  
S1 → Load → S2 → Compare

Complex Mode  
F1 → F2 → Sfull → Load → Merge → Continue → Sfull2 → Compare

Semantic equivalence MUST be preserved.

---

## 8. Compliance Requirements
LLMs MUST:
- Respect protocol version
- Respect field semantics
- Preserve identity
- Apply fragments chronologically
- Not invent fields
- Maintain reasoning constraints
- Surface conflicts clearly

🏁 FIN DES SPECS OFFICIELLES

