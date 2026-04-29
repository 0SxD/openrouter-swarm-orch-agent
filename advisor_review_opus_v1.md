# Advisor Review -- OpenRouter Swarm Orchestrator Agent
# Opus Architectural Review v1
# 2026-04-24 | Reviewer: Opus Advisor Agent

---

## Executive Summary

The 8-file scaffold is coherent at the conceptual level. Core design decisions (stigmergic coordination, Trinity gate, HOT_CACHE overwrite, model tier contract) are sound and consistently expressed across files. However, three structural gaps will cause the system to fail before completing a single overnight run: the overnight task queue is undefined, Ralph's identity and quality metric are unresolved, and the overnight invocation command in project_instructions.md is broken. The scaffold is not yet runnable. It is a well-formed blueprint with three load-bearing beams missing.

---

## File-by-File Verdicts

| File | Verdict | Reason |
|------|---------|--------|
| CLAUDE.md | SOUND | Boot sequence, agent roles, Zero Bloat rules, and OQ gates are all correctly stated. Minor: "agent S2" referenced nowhere. |
| MISSION.md | SOUND | 237 words (under 300 cap). Purpose is concrete. Success picture is testable. Creator-only rule stated. |
| PARAMS.md | NEEDS_REVISION | No overnight_queue block (OQ-01 gap). `memory/` directory listed but never defined or assigned. $2.00 cost fallback threshold in PROTOCOL.md is not defined here. |
| spec.md | SOUND | HOT_CACHE accurate and under 500 words. OPEN_Q correctly gates the two blockers. INSIGHT_02 harness warning is critical and correctly placed. Minor: "agent S2 open" is undefined shorthand. |
| skills.md | NEEDS_REVISION | AR-04 (improvement_eval) and AR-05 (karpathy_loop) overlap with Ralph node from PROTOCOL.md. Three constructs for one function with no deconfliction. Ralph has no skill entry and no model assignment. |
| PROTOCOL.md | CRITICAL_GAP | Ralph is called "the meta-evaluation agent" but has no model, no agent owner, and no identity in the 3-agent architecture. "Quality" in ralph_score formula is undefined -- no numeric proxy specified. |
| metrics_ledger.md | SOUND | Header schema is complete. Append-only rule stated. Seed row correct. |
| project_instructions.md | CRITICAL_GAP | Overnight run command uses `$(cat project_instructions.md)` as its own prompt (self-referential, includes installation notes). `/project:openrouter-swarm` is not a real Claude Code CLI slash command. No `overnight_prompt.md` file exists. |

---

## Top 3 Structural Changes (Priority Order)

### 1. Define Ralph completely or eliminate the abstraction

**Why first:** Ralph is referenced in PROTOCOL.md as a "meta-evaluation agent" with its own pseudo-code logic. It has no model, no agent owner in the 3-agent architecture, no skill entry in skills.md, and its core formula references "quality" which is never defined. If the AR self-improvement loop runs overnight, Ralph is the stop condition. An undefined stop condition on an unattended run is a runaway loop risk.

**Required actions:**
- Assign Ralph as a skill under one agent. The correct owner is MSM (it already monitors ratios and cost). Add MSM-04: ralph_monitor to skills.md.
- Define "quality" as a numeric proxy. The most consistent choice given existing infrastructure is to use the trinity_total score from PROTOCOL.md as the quality measure for each AR iteration. Add this to PARAMS.md under researcher:
  ```yaml
  researcher:
    quality_metric: "trinity_total"  # 0-15, from gate score per iteration
    improvement_rate_stop_threshold: 0.05
  ```
- Delete the vague "meta-evaluation agent" label from PROTOCOL.md. Ralph is MSM-04, runs on the free tier (Gemini Flash), logs to metrics_ledger.md, and fires the STOP_CONDITION flag to spec.md OPEN_Q.

### 2. Write the overnight_queue block in PARAMS.md (answer OQ-01 first)

**Why second:** project_instructions.md overnight run command instructs the agent to "Run overnight task queue per PARAMS.md." PARAMS.md has no overnight_queue section. The agent will receive an instruction referencing a contract that doesn't exist. This is not a soft failure -- it will produce undefined behavior on first overnight run.

**Required structure to add to PARAMS.md:**
```yaml
overnight_queue:
  task_order: []        # Sage fills after answering OQ-01
  time_budget_minutes: null
  model_default: "anthropic/claude-sonnet-4-6"
  fallback_on_failure: "write_partial_hotcache_and_stop"
  success_condition: "all tasks in TASK_QUEUE marked complete OR flagged OPEN_Q"
  on_completion: "overwrite spec.md HOT_CACHE with session summary"
```

The fallback_on_failure field is especially important. Without it, a mid-run failure leaves spec.md HOT_CACHE stale with no record of how far the run got.

### 3. Fix the overnight invocation command in project_instructions.md

**Why third:** Two separate issues make the current overnight command non-functional.

Issue A: The command is `$(cat project_instructions.md)` as the prompt argument. This cat's the entire file including installation notes, CLI usage section, and the PASTE BLOCK END marker into the system prompt. The agent receives a garbled prompt containing its own installation instructions.

Issue B: `/project:openrouter-swarm` is not a real Claude Code CLI slash command. This will throw an unrecognized command error.

**Required fix:**
- Create a dedicated file: `overnight_prompt.md` -- contains only the runtime instruction for the overnight run (boot sequence + task queue instruction + completion requirement). Keep it under 200 tokens.
- Replace the overnight command with:
  ```powershell
  claude --bare --model "anthropic/claude-sonnet-4-6" \
    -p "$(cat overnight_prompt.md)"
  ```
- Remove the `/project:openrouter-swarm` reference entirely or document the actual mechanism for loading project context into Claude Code CLI (if it requires `--add-dir` flags or system prompt injection, specify that).

---

## Internal Contradictions Found

**Contradiction 1: Ralph's identity vs 3-agent architecture**
CLAUDE.md declares exactly 3 agents (PRO, AR, MSM). PROTOCOL.md introduces Ralph as "the meta-evaluation agent" with its own autonomous logic. These two claims cannot both be true. Either Ralph is a 4th agent (requiring a model assignment and directory contract update) or it is a named skill within an existing agent. Currently the files say both simultaneously.

**Contradiction 2: self-assignment vs stigmergic separation**
project_instructions.md tells a single Claude session to "assign yourself based on the task." CLAUDE.md and PROTOCOL.md define a stigmergic system where agents are separate processes that coordinate via artifacts, not via self-assignment. These two models are fundamentally different. If one Claude session role-swaps between PRO, AR, and MSM within a single context window, it defeats stigmergy and creates context collision. The paste block model (one Claude = all three agents) contradicts the architecture documents.

**Contradiction 3: $2.00 cost fallback in PROTOCOL.md vs PARAMS.md cost contract**
PROTOCOL.md model routing rule: `if cost_budget_remaining < 2.00: use = models.scout[0]`. PARAMS.md defines soft cap at $5.00, hard cap at $20.00, overnight budget at $10.00. The $2.00 threshold is a hardcoded magic number that does not derive from PARAMS.md. This creates two competing cost governance mechanisms. If the overnight budget is $10.00, switching to free tier at $2.00 remaining wastes $8.00 of Sonnet capacity.

**Contradiction 4: memory/ directory in PARAMS.md vs every other file**
PARAMS.md directory contract lists `memory/` as agent_writable. CLAUDE.md file map does not mention it. spec.md GRAPH_LINKS does not reference it. No agent is assigned to own it. The directory does not exist on disk. Either `memory/` is a real working directory that needs to be created and assigned, or it should be removed from PARAMS.md and replaced with the correct directories (wiki/, research/).

---

## Missing Pieces That Would Block Overnight Run

| ID | Missing Item | Impact |
|----|-------------|--------|
| BLK-01 | overnight_queue block in PARAMS.md | Run has no task list to execute |
| BLK-02 | overnight_prompt.md file | Run invocation command has no valid prompt file |
| BLK-03 | "quality" metric undefined for Ralph | Self-improving loop has no stop condition |
| BLK-04 | wiki/, research/, .raw/, memory/, skills/ directories do not exist | AR write operations will fail immediately |
| BLK-05 | No session_id generation mechanism | metrics_ledger.md rows will have null session_id -- telemetry breaks |
| BLK-06 | skills/ sub-files all PENDING (PRO-01 through MSM-03) | If any gate fires, the skill file it points to does not exist |
| BLK-07 | HOT_CACHE handoff format not guaranteed | If overnight run crashes mid-task, spec.md may be left in a partial overwrite state with no recovery path |

Note on BLK-04: All four agent-writable directories are missing from disk. Running AR ingest (STEP 7: "Write wiki entry") against a nonexistent wiki/ directory will throw a file write error with no fallback defined anywhere in the scaffold.

Note on BLK-06: skills.md explicitly states "Skills are written ONLY after a task has failed at least once OR after a PRO gate has fired." This rule is correct for steady state but means the first overnight run will hit a gate with no skill file to execute. Either pre-populate the gate-critical skill (PRO-01: trinity_gate) before first run or document the no-skill fallback behavior explicitly in PROTOCOL.md.

---

## Recommended Next Session Actions

Ordered by dependency. Each action unblocks the one after it.

1. **Answer OQ-01 and OQ-02.** Everything else is blocked behind these. Write the answers directly into spec.md OPEN_Q as resolved, then propagate: OQ-01 answer fills PARAMS.md overnight_queue block, OQ-02 answer scopes PRO-03 (rubric_score skill).

2. **Create the four missing directories.** Run: `mkdir wiki research .raw memory skills/pro skills/ar skills/msm` from the project root. Confirm they exist before any agent write is attempted.

3. **Resolve Ralph.** Add MSM-04: ralph_monitor to skills.md. Add quality_metric to PARAMS.md researcher block. Rewrite Ralph section in PROTOCOL.md to explicitly name MSM as owner and Gemini Flash as model.

4. **Write overnight_prompt.md.** Separate, minimal file. Boot sequence + task queue reference + completion instruction. Under 200 tokens. Update overnight run command in project_instructions.md to reference it.

5. **Add session_id generation rule to PARAMS.md.** Simple format: `sYYMMDD-NN` (e.g., s260424-01). Define which agent generates it and when (MSM at session start, written to HOT_CACHE and all metrics_ledger rows).

6. **Pre-populate PRO-01: trinity_gate.md.** This is the one skill guaranteed to fire on the first overnight run. Writing it before launch is not speculation -- it is a certainty. The skill population rule should add a carve-out: "except PRO-01 which is required before first run."

7. **Run a dry-run zero-context Sonnet check (T05 in TASK_QUEUE).** Feed only spec.md HOT_CACHE and project_instructions.md paste block to a fresh Sonnet session. Ask it to describe what it would do next. If it cannot construct a coherent next step from those two files alone, the HOT_CACHE is insufficient for self-directed operation.

---

*advisor_review_opus_v1.md | 2026-04-24 | Opus Advisor | Internal infrastructure review*
