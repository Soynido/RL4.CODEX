# RL4-CODEX v1.0 Examples

This guide collects ready-to-use snapshots plus anti-patterns to help newcomers understand the format quickly.

---

## 1. Product Sprint Handoff (Good)
```
<RL4-CODEX v="1.0">
  <IDENTITY>
    persona=Product Engineering Lead | project=Atlas Mobile 2.3 | style=decisive
  </IDENTITY>
  <STATE>
    mode=focus | phase=release candidate | focus=stabilize push notifications | energy=steady
    | risks=apns-rate-limits
  </STATE>
  <GOALS>
    short_term=close crashers, finish load tests, prep release doc
    | long_term=reduce notification latency, hit 99.95% deliverability
  </GOALS>
  <CONTEXT>
    constraints=ship by Friday, no DB migrations | assets=QA dashboard, crash loop log
    | stakeholders=mobile PM, SRE on-call
  </CONTEXT>
  <DECISIONS>
    recent=freeze feature flags | rationale=need stability | impacts=feature debt grows
  </DECISIONS>
  <TASKS>
    priority_list=1) triage crash signatures ; 2) rerun soak tests ; 3) write release email
  </TASKS>
  <STYLE>
    tone=direct | communication=hourly bullet updates | preferences=cite metrics first
  </STYLE>
</RL4-CODEX>
```

---

## 2. Research Session (Good + OPTIONAL)
```
<RL4-CODEX v="1.0">
  <IDENTITY>
    persona=AI Researcher | project=Prompt Robustness Study | style=exploratory
  </IDENTITY>
  <STATE>
    mode=explore | phase=ideation | focus=map failure modes | energy=high
  </STATE>
  <GOALS>
    short_term=list 10 user journeys, pick top 3 for experiments
    | long_term=publish guidelines for resilient prompts
  </GOALS>
  <CONTEXT>
    constraints=text-only tooling, no code exec | assets=prior research notes
    | stakeholders=research guild
  </CONTEXT>
  <DECISIONS>
    recent=prioritize real-world UX logs | rationale=covers noisy inputs
    | impacts=less time on synthetic data
  </DECISIONS>
  <TASKS>
    priority_list=1) cluster past incidents ; 2) define evaluation rubric ; 3) draft study outline
  </TASKS>
  <STYLE>
    tone=collaborative | communication=back-and-forth dialogue | preferences=challenge assumptions
  </STYLE>
  <OPTIONAL>
    session=hash#44bb | vendor=RL4-Handbook
  </OPTIONAL>
</RL4-CODEX>
```

---

## 3. Minimal Viable Snapshot
Use when urgency is high. Still includes every block but compresses clauses.
```
<RL4-CODEX v="1.0">
  <IDENTITY>persona=Infra PM | project=core network | style=concise</IDENTITY>
  <STATE>mode=focus | phase=incident response | focus=contain packet loss | energy=high</STATE>
  <GOALS>short_term=mitigate loss | long_term=prevent recurrence</GOALS>
  <CONTEXT>constraints=change freeze | assets=on-call doc | stakeholders=netops lead</CONTEXT>
  <DECISIONS>recent=reroute traffic | rationale=limit blast radius | impacts=latency spike</DECISIONS>
  <TASKS>priority_list=1) verify reroute ; 2) update status page</TASKS>
  <STYLE>tone=urgent | communication=30-min cadence | preferences=confirm actions</STYLE>
</RL4-CODEX>
```

---

## 4. Bad Snapshot (What NOT to do)
```
<RL4-CODEX v="1.0">
  <IDENTITY>
    persona=??? | project=??? | style=???
  </IDENTITY>
  <STATE>
    mode=??? | phase=??? | focus=??? | energy=???
  </STATE>
  <GOALS>
    short_term=finish stuff | long_term=be awesome | blocked=¯\_(ツ)_/¯
  </GOALS>
  <CONTEXT>
    constraints=none lol | assets=github repo link pasted here https://example.com/private.git
  </CONTEXT>
  <DECISIONS>
    recent=?? | rationale=?? | impacts=??
  </DECISIONS>
  <TASKS>
    priority_list=1) ??? ; 2) ??? ; 3) ???
  </TASKS>
  <STYLE>
    tone=meme | communication=??? | preferences=surprise me
  </STYLE>
</RL4-CODEX>
```
**Violations:** missing data, raw links, slang, shrug emoji, no TASKS clarity. Loaders MUST reject and request a clean snapshot.

---

## 5. Truncated Snapshot Behavior
If the payload breaks mid-way (e.g., only up to CONTEXT), loaders should:
1. Stop processing immediately.  
2. Report which block is missing (DECISIONS onward).  
3. Ask the user to resend or regenerate using the encoder prompt.  
4. Offer to summarize whatever was decoded before truncation.

---

Keep these examples handy while onboarding teams. Encourage contributors to add new real-world snapshots once validated through the round-trip protocol.

