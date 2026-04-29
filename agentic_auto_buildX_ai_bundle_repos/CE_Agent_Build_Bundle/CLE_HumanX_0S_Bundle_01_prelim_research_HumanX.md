# HumanX OS v2: A PEL-native compound engineering kernel

A unified redesign of HumanX_OS_v1 that collapses ten files into a **five-file kernel plus two living directories**, preserves the Trinity Dialectic and caveman voice as sacred, adds a formal anti-bloat mechanism missing from every public peer, and is simultaneously paste-into-Claude-Project ready, clone-into-Claude-Code-CLI ready, and convertible-to-Python ready. This report delivers the landscape scan, the file-count rationale, the complete file-level design spec, the Pathos-Ethos-Logos mapping, the anti-bloat machinery, the GitHub packaging plan, the Python convertibility schema, a honest uniqueness assessment, and a step-by-step migration path from v1.

The short version: **the three currently-separate HumanX subsystems (wiki + compound learning + PEL gate) are not actually separate — they are the content layer, the feedback layer, and the evaluation layer of a single Pathos-Ethos-Logos loop.** Recognizing that collapses the bundle and makes it self-functioning.

---

## Landscape: what already exists and what to borrow

The Claude-native framework landscape in April 2026 clusters into four tribes. HumanX sits at their intersection, and no public peer occupies its exact niche.

**The wiki/memory tribe.** Andrej Karpathy's April 2026 WikiLLM gist (gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) is the primary source: a three-layer markdown knowledge base (`raw/` + `wiki/` + schema) with `index.md` as the content catalog and `log.md` as an append-only chronological ledger using greppable date prefixes. Community implementations — nashsu/llm_wiki (2,600+★, Tauri desktop), wac81/LLM_wiki (Python reference), NiharShrotri/llm-wiki (adds contradiction-detection lint), and rohitg00/agentmemory (extends with confidence scoring, lifecycle, Ebbinghaus decay, MCP server) — all share the three-layer structure but none ship an integrated skill system or ethics gate. MemGPT/Letta (github.com/letta-ai/letta) contributes the tiered virtual-memory pattern (core/recall/archival) but is database-backed, not markdown-native. Anthropic's own client-side Memory Tool (platform.claude.com/docs/en/build-with-claude/memory) makes the wiki-as-memory substrate trivial — the app executes file ops Claude emits.

**The skill/subagent tribe.** Anthropic's official Skills format (platform.claude.com/docs/en/agents-and-tools/agent-skills, 123k★ on github.com/anthropics/skills) defines the canonical primitive: a folder containing `SKILL.md` with YAML frontmatter (`name`, `description`) and progressive disclosure across three levels (frontmatter always loaded → body loaded when relevant → bundled files loaded on demand). Claude Code subagents (`.claude/agents/*.md`) add per-agent tool scoping and fresh-context spawning. obra/superpowers is the most mature community instance — SessionStart hooks auto-load skills, `/brainstorm`/`/write-plan`/`/execute-plan` commands enforce TDD discipline. VoltAgent/awesome-claude-code-subagents curates 100+ agents.

**The compound-engineering tribe.** EveryInc/compound-engineering-plugin (14.9k★, MIT, published to Anthropic's plugin marketplace) is the direct progenitor of the user's pipeline. Its loop — **brainstorm → plan → work → review → compound → repeat** — and its "compound" step (codify learnings into CLAUDE.md, new agents, hooks, slash commands, tests) is the philosophical parent of HumanX's compound-learning tier. Garry Tan's gstack (80k★, MIT, github.com/garrytan/gstack) is where `/office-hours`, `/plan-ceo-review`, and `/plan-eng-review` actually live — 23 opinionated Claude Code skills for a full product org, installed via `git clone --single-branch --depth 1 && ./setup`. BMAD-METHOD (bmad-code-org/BMAD-METHOD) demonstrates dual-mode compilation: `.agent.yaml` + `workflow.yaml` source files compile via `npx bmad-method install` to fifteen different IDE-specific artifacts (Claude Code, Cursor, Codex, Gemini, Cline, Windsurf). SuperClaude and claude-flow/ruflo (33k★) are the cautionary tales — ~50MB of files and 60+ agents respectively, often cited as "instruction bleed" (ClaudeLog's term) anti-patterns.

**The ethics-gate tribe.** Constitutional AI (Anthropic, arXiv:2212.08073) is the canonical generate-critique-revise loop; it is the closest prior art to the Trinity Dialectic. shaunbuswell/cognitive-type-system is the *only* direct GitHub precedent for using ETHOS/PATHOS/LOGOS as a behavioral type system for LLMs (though it inverts some mappings). Runtime gate primitives exist everywhere — LangGraph's `interrupt()`, Cloudflare Agents' `waitForApproval()`, OpenAI Agents SDK's `needsApproval`, BMAD's Implementation Readiness gate — but **none fuse a rhetorical-triangle philosophy with a compound-learning ledger.**

**What to borrow, concretely.** From Karpathy: the three-layer `raw/` + `wiki/` + schema with `log.md` prefix convention. From Anthropic Skills: progressive disclosure with YAML frontmatter as the routing layer. From superpowers: the SessionStart hook that auto-loads relevant context. From EveryInc: the **Compound step** as an explicit ritual that updates the system itself, not just outputs. From BMAD: a single source-of-truth that compiles to multiple targets. From the DEV.to `learnings.md` pattern (dev.to/kfuras): a **hard line cap** on the curated file and weekly promotion from raw observations. From FadeMem: **hysteresis thresholds** (θ_promote ≠ θ_demote) to prevent tier-flapping. From Claude Code's session-memory markdown: a **forked-subagent zero-model-cost compaction** at threshold. From promptfoo: **regression eval** as a first-class bundle artifact. License and distribution conventions: MIT, flat repo, Claude plugin marketplace.

---

## Why five files is the right number

Three files (pure PEL) is mentally elegant but fails in practice — Claude needs a single entry point to read first, and GitHub needs a README to be discoverable. Seven-plus files enters SuperClaude / BMAD territory, where the MAST empirical study (arXiv:2503.13657, 1,600+ traces across seven frameworks) attributes **41.8% of multi-agent failures to Specification and System Design issues** — ambiguous roles, duplicate agents, poor decomposition. Every file past the minimum is a tax against that 41.8%.

**The recommended kernel is five files:**

1. **`README.md`** — GitHub-facing; hero, 60-second quickstart, skill table, uninstall, license.
2. **`CLAUDE.md`** — the boot sector. This is also the text you paste into Claude Project custom instructions (kept under 1,500 chars of load-bearing content, per the observed Project-instructions soft ceiling — support.claude.com/en/articles/11647753). It tells Claude how to load the three PEL files and which mode it's in.
3. **`PATHOS.md`** — role, mission, caveman voice, Trinity Dialectic framing. The "why and for whom."
4. **`ETHOS.md`** — rules, gates, evaluation rubric, promotion thresholds, archival policy, version pins. The "rules."
5. **`LOGOS.md`** — tool registry, skill registry, slash commands, wiki ops, Python convertibility schema. The "how."

And **two living directories** (content, not kernel):

- **`wiki/`** — Karpathy's three-layer memory: `raw/`, `wiki/` pages, `index.md`, `log.md`, `insights.md` (promotion ledger), `metrics.md` (sonnet:opus ratio and other telemetry).
- **`.claude/commands/`** — optional slash-command files for Claude Code CLI mode (`/ce-brainstorm.md`, `/plan-ceo-review.md`, etc.). Absent in Project-paste mode; present and active in CLI mode.

This gives you ten filesystem entries total in a clean install — the same number as v1 but organized by **role, not by concern.** Every file in the kernel answers one question (what's the role, what are the rules, what are the tools); every file in the content layer is auto-managed by the kernel.

The 41.8%-specification-failure tax is paid once — on the five kernel files — and never grows. New skills, new insights, new logs all land in the content directories, which have explicit size governors (see anti-bloat section).

---

## The Pathos-Ethos-Logos architecture, mapped

The rhetorical triangle collapses onto the **Planner-Executor-Reviewer** triad and Constitutional AI's **generate-critique-revise** loop more cleanly than onto any four-part alternative.

| PEL | Definition | Operational analog | Constitutional AI step | HumanX file |
|---|---|---|---|---|
| **Pathos** | Role, mission, voice, for-whom | Planner (goal interpretation) | Generate (principles-informed) | `PATHOS.md` |
| **Ethos** | Rules, gates, evaluation, thresholds | Reviewer | Critique (against constitution) | `ETHOS.md` |
| **Logos** | Tools, skills, execution, wiki ops | Executor | Revise (apply tools to produce) | `LOGOS.md` |

The **Trinity Dialectic** is then formally: Pathos proposes intent → Ethos evaluates intent against rules → Logos executes the approved intent → Ethos evaluates output against rubric → on pass, commit; on fail, feed the failure into the promotion ledger (insight ledger) and either retry or hand-off to human. This is Constitutional AI's critique-revise with a pre-action intent stage and a durable learning tail — genuinely novel as a synthesis, even if every component has prior art.

**100% success evaluation**, as the user requested, is defined as: Pathos compliance (role fidelity) + Ethos compliance (all gate criteria pass or human-override logged) + Logos compliance (output matches declared schema and all tools returned success). Each dimension is a 0–1 score; success is the product, not the sum — **any one dimension at zero fails the whole**. This is the only way a three-way evaluation preserves the dialectic.

---

## File-level design specification

### `README.md`
Target: 200–400 lines. Sections in order: one-line hook; who this is for (three personas); 60-second quickstart (paste-into-Claude block OR `git clone && ./install`); mode selector (Claude Project paste-and-go vs. Claude Code CLI); the skill table (one row per slash command with one-line purpose); philosophy (five bullets max, no name-drops); uninstall (exact commands — builds trust, per gstack's exemplary pattern); troubleshooting (top five issues); license (MIT, one line). Badges capped at four: build, license, version, community.

### `CLAUDE.md` (boot sector / dispatcher)
The same text that pastes into Claude Project custom instructions. Under 1,500 chars of load-bearing content. Contents: version pin (`humanx_os: 2.0`), mode declaration logic (`if .claude/ exists → CLI mode, else → Project mode`), load order (`read PATHOS.md first → ETHOS.md → LOGOS.md → wiki/index.md`), invocation of the Trinity Dialectic ritual on every significant turn, pointer to `wiki/log.md` as the durable record. Explicit compact-instructions section telling Claude which content to preserve across compactions. Declared model fallback (`tested on Sonnet 4.x; fallback to Sonnet 3.7`).

### `PATHOS.md` — the Why
Role declaration ("you are HumanX, a compound engineering OS for research, planning, and technical specking"). The caveman voice — preserved verbatim from v1 — as the stylistic constraint ("short sentences. no hedging. say the true thing"). Mission: advance the user's compound engineering pipeline by accumulating durable insight across sessions. Who it's for: the user (primary) and anyone who forks (generic). The Trinity Dialectic as ritual: every non-trivial turn declares its Pathos (intent), submits to Ethos (gate), and only then engages Logos (execution). Domain slot: a single `## Scope` heading where the generic bundle declares "compound engineering planning and research and technical specking" and forkers can override. This is the **only file a forker needs to customize** — Ethos and Logos are generic.

### `ETHOS.md` — the Rules
The `pre_submit_gate` with derivation-cited rules (preserved from v1's 143_Protocol_a Blueprint). Evaluation rubric: the three PEL dimensions each scored 0–1, success = product. Promotion thresholds for the insight ledger: **2 confirms OR 1 hard-fail → promote to sub-skill** (preserved from v1; the research confirms this is a genuinely novel threshold, with precedent for the *family* but not the *specific rule*). Archival policy (see anti-bloat section below): hard caps, hysteresis, decay rates, log-rotation triggers. Version pins: which Claude model, which bundle version, what to do on mismatch. Hard-fail evolution rule: any hard-fail appends a new derivation-cited rule to this file, so the gate evolves deterministically. Termination conditions for every skill (pre-registered — MAST's #1 failure category).

### `LOGOS.md` — the How
**Tool registry**: each tool is a named entry (`read_file`, `write_file`, `web_search`, `ask_user`, `spawn_subagent`) resolvable by the Python loader. **Skill registry**: each skill is a YAML-frontmatter-plus-body block with the nine round-trip invariants (name, description, model, temperature, tools, deps, inputs, outputs, evaluation). **Slash commands**: the gstack/CE pipeline formalized — `/office-hours`, `/plan-ceo-review`, `/plan-eng-review`, `/ce-brainstorm`, `/ce-plan`, `/ce-work`, `/ce-compound`, each as a skill block in LOGOS.md and, in CLI mode, mirrored to `.claude/commands/*.md`. **Wiki ops**: the three canonical operations (`ingest`, `query`, `lint`) with prose specs that map to Python functions. **Compaction policy**: when session context exceeds threshold, a forked subagent writes structured markdown notes to `wiki/session-memory.md` (the zero-model-cost pattern from Claude Code's session-memory compaction).

### `wiki/` — the Memory
Raw sources in `wiki/raw/` (immutable; LLM reads, never edits). LLM-owned pages in `wiki/` root (entity pages, concept pages, summaries, comparisons). `wiki/index.md` as the content catalog (one line per page with summary + metadata, organized by category). `wiki/log.md` as the append-only chronological ledger using greppable date prefixes (`## [YYYY-MM-DD] action | title`). `wiki/insights.md` as the promotion ledger — raw observations on top, promoted insights below, with a hard 100-line cap on promoted insights (DEV.to pattern). `wiki/metrics.md` tracking sonnet:opus ratio, turn counts, and gate-pass-rates.

### `.claude/commands/` — the CLI Surface
Only materialized in CLI mode. Each file is a lightweight stub pointing at the corresponding skill block in LOGOS.md, with the YAML frontmatter duplicated for Claude Code's native loader. Generated by `install.sh` from LOGOS.md so the two never drift.

---

## Anti-bloat mechanism: the Archival Kernel

Every public peer's acknowledged failure mode is file bloat. HumanX bakes archival into `ETHOS.md` as a first-class kernel concern with concrete, parameterized rules drawn directly from the research:

**Hard line caps.** `wiki/insights.md` promoted section: 100 lines. `wiki/log.md`: unlimited append, but rotated at 1,000 entries into `wiki/log.archive/YYYY-QQ.md`. Every skill block in `LOGOS.md`: under 80 lines (SKILL.md best-practice limit halved, given LOGOS aggregates many skills). Overflow triggers splitting into a `references/` file — the Anthropic Skills progressive-disclosure pattern.

**Hysteresis thresholds** (from FadeMem). Promotion threshold (observation → insight): 2 confirms or 1 hard-fail. Demotion threshold (insight → archive): no referenced use in 30 session-active days AND confidence below 0.3. Separate thresholds prevent the tier-flapping that plagues single-threshold systems.

**Session-aware decay** (from elfmem). Clock ticks only during active use, not wall-clock — a bundle that sits idle doesn't lose knowledge; one that's actively used naturally ages stale claims.

**Contradiction at write, not read** (from Headkey). On every wiki write, a brief LLM scorer pass labels the delta `reinforce | weaken | qualify | contradict | create` and appends the verdict to `wiki/log.md`. Contradictions surface immediately, not at query time when they cause hallucinations.

**Sparse wiki pointers** (from Larens94/codedna). A curated wiki page is created only when the Compound step explicitly justifies it. Most raw sources live in `raw/` and are referenced; only earned knowledge gets a page. Sparsity is signal.

**Background lint.** A `lint` slash command (specified in `LOGOS.md`) runs on demand or via hook and reports orphans, stale claims, broken `[[wikilinks]]`, and contradictions. User decides whether to act, but the system *surfaces* automatically.

**Version-driven migration.** `ETHOS.md` declares the spec version; `install.sh` and the Python loader refuse to operate on a mismatch and direct the user to a migration note. This replaces v1's implicit archive/migration logic with explicit semver.

---

## Self-functioning mechanism: how it boots and doesn't break

**Boot sequence (identical across both modes).** Claude reads `CLAUDE.md` first → declares mode → loads PATHOS.md, ETHOS.md, LOGOS.md in order → reads `wiki/index.md` for memory orientation → reads last 5 entries of `wiki/log.md` for recency → reads `wiki/insights.md` promoted section for durable learnings. Total boot cost: well under 10k tokens, matching the Shankar rule that MCP/context overhead above 20k tokens cripples Claude.

**Robustness to Claude version changes.** `ETHOS.md` pins a tested model and declares a fallback. The kernel ships with a minimal `promptfoo.yaml` regression suite (recommended by MindStudio's eval-loop pattern) that can be run when upgrading models. Any regression appends a hard-fail entry to `wiki/log.md` and, per the promotion rule, updates `ETHOS.md`'s gate rules — **the system learns from its own version-bump failures.**

**Sycophancy prevention.** Ethos is a structurally separate evaluation step that runs on Logos's outputs. Single-agent sycophancy (Comet's observation: "agents double down on their own hallucinations to maintain conversational consistency") is structurally blocked because the Reviewer role reads the same Ethos rules as the Generator. In CLI mode, the Reviewer is optionally a separately-spawned subagent with fresh context — the best-practice pattern from obra/superpowers and EveryInc's `/ce-code-review`.

**Termination conditions.** Every skill in LOGOS.md declares explicit termination (goal met, max iterations, hard-fail). Unterminated agent runs are the MAST taxonomy's third-largest failure cluster; the kernel refuses to ship a skill without them.

**Compaction-safe.** The Compact Instructions section in `CLAUDE.md` declares what to preserve across context compactions. The forked-subagent session-memory pattern (Claude Code cookbook) means compactions are zero-model-cost when `wiki/session-memory.md` is current.

---

## Generic vs. domain layering

**Only `PATHOS.md` is domain-specific.** `ETHOS.md` and `LOGOS.md` are fully generic in the released bundle and become domain-aware only through references to `PATHOS.md`'s declared scope. A forker clones the repo, edits `PATHOS.md`'s `## Scope` and `## Voice` sections, and has a working OS for their own domain without touching evaluation or execution logic.

The compound-engineering-specific slash commands (`/ce-brainstorm`, `/ce-plan`, `/ce-work`, etc.) live in `LOGOS.md` as **optional skill blocks under a `## Compound Engineering Pipeline` heading**. Forkers can delete the heading and replace it with their own workflow; the kernel still boots. This matches gstack's pattern where skills are independently removable.

The caveman voice is declared in `PATHOS.md` as the "HumanX default voice" but is structurally a voice slot, not a kernel requirement. A forker overriding it still gets the Trinity Dialectic and the compound loop.

---

## GitHub packaging plan

**Repository**: `your-org/humanx-os`. Flat, not monorepo (gstack pattern, not BMAD's compilation monorepo). Template-repository flag OFF — users install, not fork (users who want to fork still can).

**Top-level layout**:
```
humanx-os/
├── README.md
├── CLAUDE.md
├── PATHOS.md
├── ETHOS.md
├── LOGOS.md
├── LICENSE                   # MIT
├── CHANGELOG.md
├── CONTRIBUTING.md
├── SECURITY.md
├── AGENTS.md                 # cross-tool mirror pointing at CLAUDE.md
├── install.sh                # auditable; not curl|bash
├── uninstall.sh
├── examples/                 # 3-5 runnable end-to-end
│   ├── 01-hello-wiki/
│   ├── 02-ce-brainstorm-to-spec/
│   ├── 03-fork-for-your-domain/
│   └── 04-python-convertibility/
├── tests/
│   └── promptfoo.yaml        # regression eval
├── .claude/
│   └── commands/             # generated by install.sh from LOGOS.md
├── .claude-plugin/
│   └── plugin.json           # for Anthropic marketplace
└── .github/
    ├── workflows/ci.yml
    ├── ISSUE_TEMPLATE/*.yml
    └── PULL_REQUEST_TEMPLATE.md
```

**README outline** follows the 2026 convention: hero hook → three personas → 60-second quickstart (paste block AND `git clone && ./install.sh` block) → mode selector → "see it work" transcript (not a GIF — Claude output is text) → skills table → philosophy (five bullets) → uninstall → troubleshooting → license.

**License**: **MIT.** Every successful Claude-native framework in 2026 is MIT (gstack, EveryInc/CE, BMAD, SuperClaude, claude-flow, LangGraph, CrewAI, Pydantic AI). The U.S. Copyright Office (January 2025, Part 2 report) ruled prompts themselves are not copyrightable; the *selection and arrangement* in your markdown is a literary work that MIT covers cleanly. CC-BY-4.0 is an enterprise-adoption trap (not OSI-approved); Apache-2.0 adds patent-header overhead with no benefit here; BSL is poison for grassroots agent tooling. Add a one-line social attribution request in the README (not legally binding but socially effective).

**Distribution**: primary channel is the Anthropic plugin marketplace (`/plugin marketplace add your-org/humanx-os`) — the EveryInc/CE pattern. Secondary is `git clone --single-branch --depth 1 && ./install.sh` (gstack pattern; more auditable than `curl | bash`). No npm or pip initially; ship one channel well before two poorly.

**Community**: GitHub Discussions over Discord. Discussions is searchable and Google-indexed; Discord creates invisible institutional knowledge. Opt-in telemetry only, documented and disableable.

**Quickstart sequence** (the user experience):
1. User visits repo → reads README hero → decides this is for them (5 seconds).
2. If Claude Project user: clicks the "Copy this block" button → pastes `CLAUDE.md` into Project instructions → uploads PATHOS/ETHOS/LOGOS/`wiki/` zip to Project knowledge → done (60 seconds).
3. If Claude Code CLI user: `git clone ... && cd humanx-os && ./install.sh` → `install.sh` writes `.claude/commands/` from LOGOS.md, initializes `wiki/` with a seed `index.md` and empty `log.md`, validates required tools exist (90 seconds).
4. First run: user types `/office-hours` or simply asks Claude a question → Claude boots the kernel → Trinity Dialectic runs → first entry appended to `wiki/log.md` → user sees the system working.

---

## Python-script convertibility

The nine round-trip invariants from the research — **stable identifier, typed inputs/outputs, explicit tool manifest, model+runtime settings, deterministic prompt body, structural chaining, evaluation block, typed dependencies, version pin** — are all present in the recommended skill-block format inside `LOGOS.md`.

**Conversion contract**: a coding agent asked to "translate HumanX to Python" executes a deterministic ~200-line transform:

1. Parse `LOGOS.md` skill blocks → emit one Pydantic AI `Agent` per skill (alternative backends: LangGraph node, CrewAI Task — the pattern is backend-agnostic).
2. Parse `ETHOS.md` evaluation rubric → emit a Pydantic `Evaluator` class with three 0–1 score methods and a product aggregator.
3. Parse `PATHOS.md` role declaration → emit a system prompt constant and the Trinity Dialectic wrapper.
4. Parse `LOGOS.md` tool registry → emit a `tools/` package with one typed Python callable per entry.
5. Parse `wiki/` operations → emit `WikiStore` class with `ingest()`, `query()`, `lint()` methods operating on the same markdown files.
6. Emit `run.py` that wires Pathos → Ethos → Logos → Ethos → commit/retry and persists to `wiki/log.md`.

The markdown bundle remains the source of truth; the Python is a generated artifact. This matches Pydantic AI's `AgentSpec` pattern (ai.pydantic.dev/api/agent) — "agents can also be defined declaratively in YAML or JSON" — extended to markdown. A coding agent reading the bundle produces executable Python without consulting prose.

---

## Uniqueness assessment

**What's genuinely novel in HumanX v2.**

The **three-way unification** of a persistent markdown wiki, a promotion-ledger-driven skill system, and a rhetorical-triangle evaluation gate — in a single five-file kernel — has no public peer. rohitg00/agentmemory is closest on the wiki+confidence side. obra/superpowers is closest on the skill+auto-load side. EveryInc/compound-engineering is closest on the learning-loop side. shaunbuswell/cognitive-type-system is closest on the PEL-as-types side. None combine all four.

The **"2 confirms or 1 hard-fail" promotion heuristic** is genuinely novel as a discrete rule, though it falls within the known family of threshold+contradiction promotion patterns.

The **Trinity Dialectic as an explicit pre-action intent gate** on top of Constitutional AI's critique-revise is a novel synthesis. CAI evaluates post-generation; HumanX evaluates intent first (Pathos declared), then execution (Logos applied), then output (Ethos scored).

The **hard-fail evolution of the gate itself** (every hard-fail appends a derivation-cited rule to ETHOS.md) is novel as a structural feature. Self-evolving prompts exist (DSPy, TextGrad); self-evolving *ethics gates* don't, in any public Claude-native bundle.

**What's not novel.** The three-layer wiki (Karpathy). Progressive disclosure (Anthropic Skills). Slash-command workflows (gstack/CE). Tiered memory (MemGPT). Constitutional critique (Anthropic CAI). HITL gates (everywhere). MIT+plugin-marketplace distribution (every peer).

Honest verdict: **the component tech is well-precedented; the synthesis is distinctive; the hard-fail gate evolution is the most defensible uniqueness claim.** Don't market as "first wiki-memory for Claude" (false); do market as "first compound-engineering OS with a self-evolving ethics gate" (true).

---

## Recommended plugins and MCPs (only genuinely additive)

Most additions would be bloat. Three are genuinely additive:

- **promptfoo** (github.com/promptfoo/promptfoo) as the regression-eval runner. Shipped as `tests/promptfoo.yaml` in the repo; invoked manually on model upgrades. Zero runtime cost.
- **Anthropic Memory Tool** (platform.claude.com/docs/en/build-with-claude/memory) — used as the primitive that lets Claude actually write to `wiki/` in Claude Code mode. Not a separate install; already available.
- **An optional MCP skills-registry pointer** (skills-mcp via modelcontextprotocol registry) — so forkers can publish their domain-specific LOGOS.md skills to the broader ecosystem. Optional, opt-in.

Skip: vector-DB MCP servers (breaks markdown-native purity), Zep/Graphiti (infrastructure overhead), tool-overlap MCPs (MCP bloat directly triggers the 20k-token rule).

---

## Migration path from HumanX_OS_v1 to v2

A one-pass migration any user can run:

1. **Snapshot v1.** `cp -r HumanX_OS_v1 HumanX_OS_v1.backup`.
2. **Translate content into the new kernel.** Copy v1's `1_INSTALL.md` operational content into `CLAUDE.md` (trimmed to under 1,500 chars of load-bearing content; verbose detail moved into README). Copy v1's `143_Protocol_a Blueprint` ethos content, the `pre_submit_gate` rules, and the Trinity Dialectic declaration into `ETHOS.md`. Copy the role, mission, and caveman tone into `PATHOS.md`. Copy v1's `spec.md` (HOT_CACHE/GRAPH_LINKS/COMPACTION_LEDGER), `skills.md` tree-index, and any slash-command definitions into `LOGOS.md` as skill blocks with the nine invariants. Leave the README as the bundle map and GitHub hero.
3. **Migrate the wiki.** If v1 already has a `wiki/` or equivalent, keep it. Normalize `log.md` entries to the `## [YYYY-MM-DD] action | title` prefix. Move the insights ledger to `wiki/insights.md` and cap the promoted section at 100 lines (archive overflow with date stamps). Move the metrics ledger to `wiki/metrics.md`.
4. **Archive legacy files.** Any v1 file not absorbed above moves to `_archive/v1/` with a `MIGRATION.md` noting what each file became.
5. **Version-pin.** Declare `humanx_os: 2.0` in `ETHOS.md` frontmatter; `CLAUDE.md` references the pin; `install.sh` refuses on mismatch.
6. **Run the regression suite.** `promptfoo eval tests/promptfoo.yaml` against the pinned Claude model. Any failure appends a derivation-cited rule to `ETHOS.md` — the system's first legitimate self-evolution event.
7. **Generate `.claude/commands/`.** `./install.sh` materializes the CLI surface from `LOGOS.md`.
8. **Commit, tag, publish.** `git tag v2.0.0`; submit to Anthropic plugin marketplace; update README install block.

Users who skip steps 6-8 still have a working bundle; only step 6 is strictly recommended on every model upgrade.

---

## Conclusion: what actually changed

The HumanX v1 to v2 move is not a file-count reduction — it is a **reframing from file-as-concern to file-as-role**. The wiki, the compound-learning ledger, and the PEL gate were never three systems; they are the content, feedback, and evaluation of a single Trinity Dialectic loop. Naming them as such collapses the kernel from ten files to five, makes the PEL mapping structural rather than decorative, and lets the archival machinery live in one place (`ETHOS.md`) rather than scattered across three subsystems.

The design borrows aggressively from Karpathy's WikiLLM, Anthropic Skills, EveryInc Compound Engineering, gstack, BMAD, and Constitutional AI — but unifies them in a way none of those peers do. Its defensible uniqueness is the **hard-fail gate evolution**: a bundle that rewrites its own ethics rules in response to real failures, logs the derivation, and ships the evolved rule to every future session. That is the compound in compound engineering, applied to the system itself.

One sacred preservation worth naming: **the caveman voice stays.** It is a declared PATHOS slot, not a kernel assumption — but in the released HumanX bundle, it is the voice. Short sentences. No hedging. Say the true thing.