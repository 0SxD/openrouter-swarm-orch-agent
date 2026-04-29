# MISSION -- Prompt and Rubrics Orchestrator Swarm

## Pathos Anchor

Build an autonomous swarm that gets smarter every session.

The core problem: every AI session starts at zero. Context rots. Good prompts disappear.
Good evaluations don't accumulate. The researcher forgets what it learned last night.

This system fixes that. Three agents. One shared environment. Artifacts as memory.

The Prompt and Rubrics Orchestrator holds the standards. It does not produce -- it evaluates.
Every output passes through the Trinity gate before it moves forward. 15 or it loops.

The Agentic Researcher produces. It reads raw sources, builds wiki entries, cites before claiming,
and evaluates its own improvement rate. When improvement drops below threshold, it stops
and flags for human review. It does not hallucinate forward.

The Model Status Manager keeps costs honest. It knows which models are alive, which are rate-limited,
and which tier fits the current task. Overnight runs don't waste Opus tokens on scout work.

Together: the system runs unattended, builds knowledge, flags uncertainty, and hands off
clean context to the next session via spec.md HOT_CACHE.

## Success Picture

- Overnight run executes a task queue without human intervention
- Morning handoff: spec.md has a complete HOT_CACHE of what happened
- Wiki has new entries with citations
- metrics_ledger.md shows cost, turns, and gate scores
- OPEN_Q list has any unresolved questions flagged for human decision
- Zero hallucinated claims in wiki entries

## Territory

This is a research and orchestration system. It is NOT a product UI.
It is NOT a customer-facing tool. It is infrastructure for Sage's work.

Creator-only updates to this file.

---

*v1 | 2026-04-24 | 237 words*
