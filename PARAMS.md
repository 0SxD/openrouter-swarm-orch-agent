# PARAMS -- Operational Contract

```yaml
version: "1.0"
created: "2026-04-24"
owner: "Model Status Manager (MSM)"

# -----------------------------------------------
# MODEL TIERS
# -----------------------------------------------
models:
  orchestrator:
    id: "anthropic/claude-opus-4-6"
    use: "Trinity gate decisions, BLOCK/LOOP_BACK evaluation only"
    cost_input_per_m: 5.00
    cost_output_per_m: 25.00
    guard: "Gate decisions ONLY. Never use for research or drafting."

  workhorse:
    primary:
      id: "anthropic/claude-sonnet-4-6"
      use: "Research, wiki writing, drafting, coding"
      cost_input_per_m: 3.00
      cost_output_per_m: 15.00
    alternates:
      - id: "moonshot/kimi-k2.6"
        use: "Swarm-native tasks, up to 300 sub-agents, native tool-use"
        cost_input_per_m: 0.60
        cost_output_per_m: 2.80
      - id: "deepseek/deepseek-r1"
        use: "Reasoning-heavy tasks, architecture review"
        cost_input_per_m: 0.50
        cost_output_per_m: 2.15

  scout:
    - id: "google/gemini-2.0-flash-exp:free"
      use: "Fast research loops, drafts, status checks"
      cost: 0
    - id: "meta-llama/llama-3.3-70b-instruct:free"
      use: "Reasoning without spend"
      cost: 0
    - id: "minimax/minimax-m2.7"
      use: "High-volume, low-cost tasks"
      cost_input_per_m: 0.30
      cost_output_per_m: 1.20

# -----------------------------------------------
# COST TARGETS
# -----------------------------------------------
cost_targets:
  sonnet_to_opus_ratio_min: 3  # Target: >3:1 Sonnet calls vs Opus calls
  daily_spend_soft_cap_usd: 5.00
  daily_spend_hard_cap_usd: 20.00
  overnight_run_budget_usd: 10.00

# -----------------------------------------------
# TRINITY GATE THRESHOLDS
# -----------------------------------------------
trinity:
  proceed_threshold: 15
  loop_back_min: 10
  block_max: 9
  max_loop_iterations: 3  # After 3 loops, BLOCK and flag for human

# -----------------------------------------------
# RESEARCHER THRESHOLDS
# -----------------------------------------------
researcher:
  improvement_rate_stop_threshold: 0.05  # Stop when delta improvement <5% per iteration
  max_iterations_per_task: 10
  citation_required: true  # Zero uncited claims in wiki output
  hallucination_check: "cross-reference at least 2 sources per claim"

# -----------------------------------------------
# MEMORY LIMITS
# -----------------------------------------------
memory:
  hot_cache_max_words: 500
  hot_cache_mode: "overwrite"  # Never append
  protocol_load: "on_gate_only"  # Not at startup

# -----------------------------------------------
# DIRECTORY CONTRACT
# -----------------------------------------------
directories:
  immutable: [".raw/"]
  agent_writable: ["wiki/", "research/", "memory/"]
  telemetry: ["metrics_ledger.md"]
  system: ["CLAUDE.md", "MISSION.md", "PARAMS.md", "spec.md", "skills.md", "PROTOCOL.md"]

# -----------------------------------------------
# OPENROUTER PROTOCOL SKIN (ANTHROPIC SKIN)
# -----------------------------------------------
openrouter_config:
  base_url: "https://openrouter.ai/api"     # NO /v1 -- triggers Anthropic protocol skin
  auth_token_var: "ANTHROPIC_AUTH_TOKEN"     # Set to sk-or-v1-... key
  api_key_var: "ANTHROPIC_API_KEY"           # Must be empty string
  model_var: "ANTHROPIC_MODEL"               # Orchestrator model ID

  shell_export: |
    export ANTHROPIC_BASE_URL="https://openrouter.ai/api"
    export ANTHROPIC_AUTH_TOKEN="sk-or-v1-YOUR_KEY"
    export ANTHROPIC_API_KEY=""
    export ANTHROPIC_MODEL="anthropic/claude-opus-4-6"

  powershell_export: |
    [System.Environment]::SetEnvironmentVariable("ANTHROPIC_BASE_URL",   "https://openrouter.ai/api", "User")
    [System.Environment]::SetEnvironmentVariable("ANTHROPIC_AUTH_TOKEN", "sk-or-v1-YOUR_KEY",         "User")
    [System.Environment]::SetEnvironmentVariable("ANTHROPIC_API_KEY",    "",                           "User")
    [System.Environment]::SetEnvironmentVariable("ANTHROPIC_MODEL",      "anthropic/claude-opus-4-6",  "User")
```

---

*PARAMS v1 | 2026-04-24 | MSM-owned*
