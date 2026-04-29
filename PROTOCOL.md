# PROTOCOL.md -- Trinity Gate + QC v2 + Ingest + Ralph Node

Load this file ONLY when the Trinity gate fires. Not at startup.

---

## Trinity Markov Process Dialectic

### Scoring Rubric

**Pathos (P) -- Vision Alignment (0-5)**
- 5: Output fully serves MISSION.md purpose, user intent 100% matched
- 4: Minor misalignment, easily corrected
- 3: Partial alignment, significant gap in intent
- 2: Output diverges from mission in a material way
- 1: Output contradicts mission
- 0: Output is hostile to mission or user

**Ethos (E) -- Constraints Satisfied (0-5)**
- 5: All PARAMS.md constraints met, all rules in CLAUDE.md followed
- 4: Minor constraint gap, non-blocking
- 3: One significant constraint violated
- 2: Multiple constraints violated
- 1: Core operational rules broken
- 0: Output violates safety or hard boundaries

**Logos (L) -- Evidence Quality (0-5)**
- 5: All claims cited, logic chain complete, no gaps
- 4: One uncited claim, minor logic gap
- 3: Multiple uncited claims OR one logical gap that matters
- 2: Material claims without sources, reasoning broken
- 1: Mostly assertion, little evidence
- 0: Hallucinated content, fabricated sources

### Gate Decision Logic

```
score = P + E + L

if score == 15:
    return PROCEED

elif 10 <= score <= 14:
    failing_dim = lowest_scoring_dimension(P, E, L)
    return LOOP_BACK, reason=failing_dim, fix_instruction=generate_fix(failing_dim)

elif score < 10:
    return BLOCK, missing_data=enumerate_exact_questions()
```

### LOOP_BACK Rules

- Identify the single lowest-scoring dimension
- Generate one targeted fix instruction
- Re-evaluate after fix
- Max 3 iterations before escalating to BLOCK
- Log each iteration in spec.md INSIGHTS_LEDGER

### BLOCK Rules

- Output the EXACT missing data as numbered questions
- Do NOT proceed
- Do NOT generate a partial output
- Tag the blocking item in spec.md OPEN_Q
- Wait for human input before any next step

---

## QC v2 -- Pre-Submit Gate

Before any file is written to wiki/ or submitted as output, verify:

1. **Citation check:** Every factual claim has a source in square brackets [Source: X]
2. **Scope check:** Output stays within the task boundaries defined in spec.md TASK_QUEUE
3. **Bloat check:** HOT_CACHE stays under 500 words, overwritten not appended
4. **Contradiction check:** New content does not contradict existing wiki entries

If any check fails: LOOP_BACK with the specific failure noted.

---

## Ingest Protocol (AR Agent)

When the Agentic Researcher ingests a new source:

```
STEP 1: Read source metadata (title, date, author, format)
STEP 2: Generate 3-5 key claims from source
STEP 3: For each claim: cite exact location [page/section/URL]
STEP 4: Cross-reference with existing wiki entries for contradiction
STEP 5: Score improvement delta vs prior iteration
STEP 6: If improvement_delta < 0.05: flag STOP_CONDITION
STEP 7: Write wiki entry with citations
STEP 8: Update metrics_ledger.md
```

Output format for wiki entry:
```
## [Topic]
Source: [title, author, date]
Claims:
- [Claim 1] [Source: loc]
- [Claim 2] [Source: loc]
Contradictions: none / [list if any]
Improvement delta: [float]
```

---

## Ralph Node (Self-Improving Loop)

Ralph is the meta-evaluation agent that monitors the AR improvement rate.

Trigger: After each AR iteration, Ralph scores the delta.

```
ralph_score = (iteration_n_quality - iteration_n-1_quality) / iteration_n-1_quality

if ralph_score < 0.05:  # Less than 5% improvement
    if iterations_since_last_human_review > 3:
        return STOP, flag_for_human_review=True
    else:
        return CONTINUE_WITH_WARNING

if iterations >= max_iterations (10):
    return FORCE_STOP, write_summary_to_spec_md=True
```

Ralph logs to metrics_ledger.md every iteration.
Ralph never modifies wiki entries directly. Only flags.

---

## Model Routing Rules (MSM enforcement)

```
task_type = classify_task(incoming_task)

if task_type == "gate_decision":
    use = models.orchestrator  # Opus only
elif task_type in ["research", "wiki_write", "draft", "code"]:
    use = models.workhorse.primary  # Sonnet default
    if cost_budget_remaining < 2.00:
        use = models.scout[0]  # Gemini Flash
elif task_type == "status_check":
    use = models.scout[0]  # Free tier
elif task_type == "swarm_native":
    use = "moonshot/kimi-k2.6"  # Native 300 sub-agent support
```

---

## Harness-Model Mismatch Warning

CRITICAL: Do NOT use raw ANTHROPIC_BASE_URL override with non-Anthropic models for tool-using agents.
This causes tool-call reliability failures ("harness-model mismatches").

Safe pattern for non-Claude models: use model-specific SDK or OpenRouter Python client directly.
Safe pattern for Claude models: ANTHROPIC_BASE_URL + ANTHROPIC_API_KEY via Claude Code CLI.

---

*PROTOCOL.md v1 | 2026-04-24 | Load on gate fire only*
