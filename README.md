# openrouter-swarm-orch-agent

OpenRouter-routed swarm orchestration agent for multi-model dispatch and overnight task-queue management.

## Status

R&D / Experimental. Maintained by Sage / 0SxD as part of an ongoing prompt-engineering and agent-skills research portfolio.

## What this is

An agent scaffold exploring multi-model orchestration via OpenRouter as the dispatch layer. Three cooperating agent roles - Prompt and Rubrics Orchestrator, Agentic Researcher, and Model Status Manager - share a common environment and pass artifacts as memory across sessions. The design is a research prototype for understanding how model routing policy, overnight run management, and evaluation gate patterns interact.

## Layout

- `MISSION.md` - three-agent system purpose and Pathos anchor
- `PARAMS.md` - operating parameters and constraints
- `PROTOCOL.md` - Trinity gate scoring rubric (Pathos/Ethos/Logos); loaded on gate fire
- `CLAUDE.md` - session operating rules
- `spec.md` - working state hot-cache
- `skills.md` - skill registry
- `metrics_ledger.md` - cost and progress ledger
- `advisor_review_opus_v1.md` - advisor review output from first build session
- `project_instructions.md` - paste-in system prompt for agent sessions
- `agentic_auto_buildX_ai_bundle_repos/` - bundled reference components from related agent repos
- `Advanced Architecture and Local Orchestrator Research_gemini_4_24_2026.md` - architecture research note

## License

MIT. See `LICENSE`.
Author: Sage / 0SxD

## Notes

This repo is part of an active R&D portfolio. Content may move, change, or be withdrawn. Issues welcome but reviews are best-effort.
