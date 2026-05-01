# spec.md -- OpenRouter Swarm Orchestrator Agent

---

## HOT_CACHE

```
STATUS: scaffold complete. Files written. Advisor review pending.

SESSION_TYPE: initial setup session.

ACTIVE_TASK: configure overnight task queue and answer OQ-01 + OQ-02.

NEXT_ACTION (Sage): answer two blocking questions:
  OQ-01: What is the overnight task queue?
  OQ-02: Is PRO domain evaluation platform-specific or general-purpose?

NEXT_ACTION (agent S2 open): load HOT_CACHE, run Trinity on OQ answers, configure PARAMS.md overnight_queue block, write session_plan for first overnight run.

ACTIVE_VARS:
- three_agents: PRO (Opus gates), AR (Sonnet research), MSM (free-tier monitoring)
- model_tier_contract: locked in PARAMS.md
- trinity_thresholds: 15=PROCEED, 10-14=LOOP_BACK, <10=BLOCK
- hot_cache_mode: overwrite, 500 word max
- overnight_run: NOT YET CONFIGURED (gated by OQ-01)
- rubrics_domain: NOT YET DEFINED (gated by OQ-02)
- advisor_review: PENDING (Opus advisor queued)

CONFIDENCE: TRUE=1 scaffold. FALSE=0 overnight run.
```

---

## GRAPH_LINKS

| File | Path | Status |
|------|------|--------|
| CLAUDE.md | ./CLAUDE.md | LIVE |
| MISSION.md | ./MISSION.md | LIVE |
| PARAMS.md | ./PARAMS.md | LIVE |
| PROTOCOL.md | ./PROTOCOL.md | LIVE |
| skills.md | ./skills.md | LIVE |
| metrics_ledger.md | ./metrics_ledger.md | LIVE |
| project_instructions.md | ./project_instructions.md | LIVE |
| dispatch_v2 bundle | ./agentic_auto_buildX_ai_bundle_repos/compounding_learning_OS_dispatch_2026-04-21.zip | REFERENCE |
| rubrics bundle | ./agentic_auto_buildX_ai_bundle_repos/rubrics_correction_dispatch_bundle_2026-04-22b.zip | REFERENCE |
| arch research | ./Advanced Architecture and Local Orchestrator Research_gemini_4_24_2026.md | REFERENCE |

---

## OPEN_Q

OQ-01 (BLOCKING): What is the overnight task queue? Specific tasks, order, models, time budget.
OQ-02 (RESOLVED 2026-04-24): PRO domain IS evaluation platform-specific. Rubrics Correction project. Canon: bootstrap.md + skills.md + spec.md pattern (zero_bloat_protocol, HOT_CACHE, GRAPH_LINKS, COMPACTION_LEDGER, INSIGHTS_LEDGER, OPEN_Q). Examples uploaded by Sage confirm exact template.
OQ-03 (ADVISOR): Which files need structural revision per Opus advisor review?

---

## TASK_QUEUE

- [ ] T01: Answer OQ-01 + OQ-02 (Sage input required)
- [ ] T02: Opus advisor review of all 8 scaffold files
- [ ] T03: Configure overnight_queue block in PARAMS.md
- [ ] T04: Write first overnight session_plan
- [ ] T05: Dry-run verification (zero-context Sonnet check)
- [ ] T06: First overnight run

---

## INSIGHTS_LEDGER

INSIGHT 01 (2026-04-24): Stigmergic coordination is load-bearing. Agents write to environment artifacts,
not to each other. Prevents context collisions in multi-agent swarm. Maps to OpenBrainLM L3 Stigmergy layer.

INSIGHT 02 (2026-04-24): harness-model mismatch WARNING from arch research doc: raw ANTHROPIC_BASE_URL
override with non-Anthropic models causes tool-call reliability failures. Must use model-specific
harness config or Claude-only models for tool-using agents.

---

## NAMING_CONVENTION

RULE (2026-04-24): "143_Protocol" or "143 Protocol" is DEPRECATED as a reference name.
Replacement: use "system_directive_protocol" or the agent-specific name (e.g. "audit_directive", "research_directive").
Reason: personal reference, not portable, confuses new agents and collaborators.
HOW TO APPLY: when you encounter "143_Protocol" in any spec/skill/project file you are editing, rename it in that file at that time. Do not do a bulk find-and-replace pass. Change on contact only.
The system_directive.md file in each agent folder IS the new canonical anchor for what 143_Protocol was pointing to.

---

## COMPACTION_LEDGER

2026-04-24 SCAFFOLD: 8 files written from dispatch_v2 + rubrics_correction_dispatch bundles.
Source: agentic_auto_buildX_ai_bundle_repos/.
Not yet deployed to overnight run.

---

*spec.md v1 | 2026-04-24*
