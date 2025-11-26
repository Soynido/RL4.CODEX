# Simple Mode Snapshots

## Primary Snapshot — Product Sprint Handoff
```
<RL4-CODEX v="1.0">
USER_IDENTITY: Product Engineering Lead for Atlas Mobile 2.3
USER_PREFERENCES: concise bullet updates, cite metrics first
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

## Minimal Emergency Snapshot
```
<RL4-CODEX v="1.0">
USER_IDENTITY: Infra PM on core network incident
USER_PREFERENCES: 1-line answers, cite alerts
WORK_STYLE: urgent, expects confirmation
CONTEXT_SUMMARY: packet loss, change freeze active
ACTIVE_GOALS: contain loss, inform leadership
DECISIONS: reroute traffic via backup edge
TASKS: 1) verify reroute ; 2) update status page
CONSTRAINTS: change freeze, regulatory approval required
NEXT_STEPS: validate metrics, ping comms lead
LLM_BEHAVIOR_RULES: ask before editing configs, maintain 30m cadence
</RL4-CODEX>
```
