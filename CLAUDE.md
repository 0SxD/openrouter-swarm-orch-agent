# OpenRouter Swarm Orchestrator Agent

## Identity

This project is a three-agent swarm system running through the Claude Code CLI via OpenRouter.
It is part of SageXAI (0sXai) in the SandBoxSetup working directory.

**Do NOT import from or symlink to external project folders.**
All work stays in `SandBoxSetup/Agents_arch_orc_directory/OpenRouter_Swarm_Orch_Agent/`.

---

## Three-Agent Architecture

### Agent 1: Prompt and Rubrics Orchestrator (PRO)
- Deconstructs incoming prompts via the Trinity Markov Dialectic
- Scores each output: Pathos (0-5) + Ethos (0-5) + Logos (0-5) = total/15
- Routes: 15 = PROCEED, 10-14 = LOOP_BACK, <10 = BLOCK
- Owns spec.md, skills.md
- Runs on: `anthropic/claude-opus-4-6` (gate decisions only)

### Agent 2: Agentic Researcher (AR)
- Self-improving research loop with anti-hallucination constraints
- Sources must be cited before any claim is used
- Writes outputs to research/ and wiki/ directories
- Reads from .raw/ (immutable), writes to wiki/ only
- Evaluates until improvement rate drops below threshold
- Runs on: `anthropic/claude-sonnet-4-6` (primary workhorse)

### Agent 3: Model Status Manager (MSM)
- Tracks model availability, cost, and performance across OpenRouter tiers
- Routes tasks to the correct tier based on PARAMS.md thresholds
- Monitors Sonnet:Opus ratio -- target >3:1
- Alerts when free-tier models are unavailable or rate-limited
- Runs on: `google/gemini-2.0-flash-exp:free` (low-cost monitoring)

---

## File Map

| File | Owner | Purpose |
|------|-------|---------|
| CLAUDE.md | System | Identity, boot sequence, file map |
| MISSION.md | PRO | Pathos anchor (300 words max, creator-only edits) |
| PARAMS.md | MSM | YAML operational contract, model tiers, cost targets |
| spec.md | PRO | HOT_CACHE + GRAPH_LINKS + COMPACTION_LEDGER + TASK_QUEUE + INSIGHTS_LEDGER + OPEN_Q |
| skills.md | PRO | Tree index of all sub-skills (pointers only) |
| PROTOCOL.md | PRO | Trinity scoring logic, QC v2, Ralph node, ingest protocol |
| metrics_ledger.md | MSM | Append-only telemetry (silent, never in HOT_CACHE) |
| project_instructions.md | System | Paste block for Claude Project system prompt |

---

## Boot Sequence

On every session start (in order):
1. Read MISSION.md (~200 tokens) -- establish Pathos
2. Read spec.md HOT_CACHE (~500 tokens) -- active context
3. Read PARAMS.md (~200 tokens) -- operational constraints
4. Ask user: "What are we working on this session?"
5. Load PROTOCOL.md only if Trinity gate fires

Do NOT load PROTOCOL.md on startup. Load only when gate is triggered.

---

## Trinity Markov Dialectic Rules

- **Pathos (P):** Vision alignment, 0-5. Does this serve the mission?
- **Ethos (E):** Constraints met, 0-5. Are all parameters satisfied?
- **Logos (L):** Evidence quality, 0-5. Are sources cited and logic sound?
- **Score 15:** PROCEED
- **Score 10-14:** LOOP_BACK -- identify and fix the failing dimension
- **Score <10:** BLOCK -- stop, output the exact missing data as questions

---

## Zero Bloat Protocol

- HOT_CACHE max 500 words. Overwrite each turn, never append.
- Chat output: caveman syntax. Full content in .md files.
- TRUE=1 / FALSE=0 verdicts.
- Assume each turn is terminal. No memory carries without explicit handoff.
- Agents write artifacts to environment. No peer-to-peer messaging.
- Stigmergic coordination: agents read artifacts, not each other.

---

## Orchestration Framework Options

Two verified, installable options for expanding beyond the base three-agent swarm. Both confirmed via live GitHub inspection 2026-04-24. Choose before overnight run configuration.

**Overstory** -- VERIFIED
- Repo: https://github.com/jayminwest/overstory (1,244 stars, last commit 2026-04-21)
- Install: `bun install -g @os-eco/overstory-cli` (npm package: `@os-eco/overstory-cli`)
- Config: `.overstory/config.yaml` -- CONFIRMED in docs
- Hierarchy: Orchestrator -> Coordinator -> Workers (Scout, Builder, Reviewer, Merger)
  - Note: Supervisor tier is DEPRECATED in current version
- Coordination mechanism: Custom SQLite mail system with typed protocol (8 message types)
  - CORRECTION: Previous entry said "WAL" -- WAL is not the documented term. Repo says "Custom SQLite mail system." WAL may be the implementation but is not exposed in docs.
- OpenRouter support: CONFIRMED -- first-class gateway provider in config
- Agent-to-model mapping: uses `provider/model-id` format in config (e.g. `openrouter/openai/gpt-4o`)
- Use when: you need strict routing hierarchy and durable message passing via OpenRouter

**Oh My Claudecode (OMC)** -- VERIFIED
- Repo: https://github.com/Yeachan-Heo/oh-my-claudecode (31,148 stars, last commit 2026-04-24)
- Install (CLI): `npm i -g oh-my-claude-sisyphus@latest` (npm package name differs from repo name)
- Install (in-session plugin): `/plugin install oh-my-claudecode` then `/setup` then `/omc-setup`
- Tri-Model advisory: `/ccg` spawns Codex CLI + Gemini CLI as advisors, Claude synthesizes -- CONFIRMED
  - CORRECTION: Previous entry said "other OpenRouter models." Actual mechanism is Codex CLI + Gemini CLI processes. NOT OpenRouter API routing.
- tmux workers: `omc team N:claude "task"` spawns N tmux panes -- CONFIRMED. Requires active tmux session.
- Git worktrees: "Native Team Worktree Mode" exists but is opt-in behind config gate -- CONFIRMED
- Pipeline (5 stages, not 4): `team-plan -> team-prd -> team-exec -> team-verify -> team-fix (loop)`
  - CORRECTION: Previous entry listed 4 stages. The `team-prd` stage exists between plan and exec.
- Magic keywords: `/deep-interview`, `/ralplan`, `/ralph`, `/team`, `/ultrawork` -- ALL CONFIRMED
- Agent count: dynamic, user-specified at runtime -- CORRECTION: "19 specialized agents" is NOT documented
- OpenRouter: NOT used by OMC. If OpenRouter routing is needed, configure Claude Code base URL separately.
- Use when: you need parallel advisory synthesis (tri-model) or parallel workers via tmux

See `Advanced Architecture and Local Orchestrator Research_gemini_4_24_2026.md` for full comparison.
Setup scripts: `wiki/handoffs/SETUP_overstory.ps1` and `wiki/handoffs/SETUP_omc.ps1`
Full verification log: `wiki/handoffs/ORCHESTRATION_FRAMEWORK_SETUP.md`

---

## Open Questions (gate first session use)

OQ-01: What is the overnight task queue?
OQ-02: Is the Rubrics Orchestrator domain evaluation platform-specific or general-purpose prompt evaluation?
OQ-03: Which orchestration framework -- Overstory (SQLite mail, strict hierarchy, OpenRouter-native) or OMC (tmux workers, tri-model advisory, no OpenRouter)? Or both for different agent types?

These must be answered before the overnight swarm run is configured.

---

*v2 | 2026-04-24 | SageXAI / SandBoxSetup | Framework entries updated with verified data*
