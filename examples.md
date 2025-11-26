# RL4-CODEX Dual-Mode Examples

This guide gathers copy/paste-ready payloads and edge cases for both Simple Mode (v1.0) and Complex Mode (v1.1). Each snippet also lives inside `/examples/` as a Markdown file for quick reuse.

---

## 1. Simple Mode — Product Sprint Handoff (`examples/simple-snapshot.md`)
```
<RL4-CODEX v="1.0">
USER_IDENTITY: Product Engineering Lead for Atlas Mobile 2.3
USER_PREFERENCES: concise bullet updates, cite metrics before opinions
WORK_STYLE: decisive, expects explicit next steps
CONTEXT_SUMMARY: stabilizing push notifications during release candidate week
ACTIVE_GOALS: close crashers, finish load tests, prep release doc
DECISIONS:
  - freeze feature flags
  - run nightly soak tests before sign-off
TASKS:
  1) triage crash signatures
  2) rerun soak tests
  3) draft release email
CONSTRAINTS: ship by Friday, no DB migrations, downtime <30s
NEXT_STEPS: rerun soak tests tonight, confirm metrics, notify PM
LLM_BEHAVIOR_RULES: keep answers <150 words, confirm constraints before suggesting changes
</RL4-CODEX>
```

---

## 2. Simple Mode — Minimal Emergency Snapshot (`examples/simple-snapshot.md`, section 2)
Use when time is critical; still includes every field but each clause is compressed.
```
<RL4-CODEX v="1.0">
USER_IDENTITY: Infra PM on core network
USER_PREFERENCES: 1-line answers, cite alerts
WORK_STYLE: urgent, expects confirmation
CONTEXT_SUMMARY: packet loss incident, change freeze active
ACTIVE_GOALS: contain loss, inform leadership
DECISIONS: reroute traffic via backup edge
TASKS: 1) verify reroute ; 2) update status page
CONSTRAINTS: change freeze, regulatory approval needed for rollbacks
NEXT_STEPS: validate metrics, ping comms lead
LLM_BEHAVIOR_RULES: ask before editing configs, keep cadence 30m
</RL4-CODEX>
```

---

## 3. Complex Mode Set
- Full snapshot: `examples/full-snapshot-v1.1.md`
- Fragment #1: `examples/fragment-1.md`
- Fragment #2: `examples/fragment-2.md`
- Merged result: `examples/merged-snapshot-example.md`

These files demonstrate how fragments append incremental updates (decisions, tasks, context) without repeating identity. Loaders apply them chronologically; the merged snapshot shows the final state after both fragments.

---

## 4. Bad Snapshot (Reject) (`examples/simple-snapshot.md`, section “bad”)
```
<RL4-CODEX v="1.0">
USER_IDENTITY: ???
USER_PREFERENCES: surprise me lol
WORK_STYLE: ???
CONTEXT_SUMMARY: ??? include repo https://example.com/private.git
ACTIVE_GOALS: finish stuff, be awesome
DECISIONS: ??
TASKS: 1) ??? ; 2) ???
CONSTRAINTS: none lol
NEXT_STEPS: shrug
LLM_BEHAVIOR_RULES: do whatever
</RL4-CODEX>
```
**Violations:** missing data, raw links, slang, shrug responses, ambiguous tasks. Loaders MUST reject and ask for a clean snapshot.

---

## 5. Truncation & Fragment Loss Handling
- If a Simple Mode snapshot is cut off (e.g., missing `LLM_BEHAVIOR_RULES`), the loader MUST stop, report the missing field, and request a resend.
- If a fragment list is incomplete, the loader MUST halt merging, indicate the missing timestamps, and wait for confirmation. Never guess.
- Always keep fragments ordered and archived; losing one means the merged context can no longer be trusted until it is recovered.

Keep these examples handy during onboarding and regression tests. Add new real-world payloads once they pass the round-trip protocol.
