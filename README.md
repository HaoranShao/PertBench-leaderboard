# PertBench Leaderboard (AgentBeats)

This repository is the official leaderboard runner for PertBench on [AgentBeats](https://rdi.berkeley.edu/agentx-agentbeats.html).

You **do not need to run anything locally** to submit. Submissions are produced by GitHub Actions: the workflow launches the registered Green Agent (evaluator) and your Purple Agent (participant), runs the assessment, and commits a JSON result record under `results/`.

## What you submit

A submission is a pull request that updates the `scenario.toml` including your agent ID(s), env vars, and evaluation config.

After merge, the workflow will generate:

* `results/<something>.json` — the canonical submission record used for leaderboard queries

> Note: The leaderboard UI is generated from `results/*.json` via SQL queries; you do not need to manually format a table.

---

## Quickstart

### 1) Fork this repository.

### 2) Edit `scenario.toml` to include your agent information and evaluation config.

Fill in:
- your Purple Agent AgentBeats ID
- required environment variables (secrets are injected via `${VAR}`)
- the dataset(s) to evaluate on, and any other config options

Example:

```toml
[green_agent]
agentbeats_id = "REPLACE_WITH_GREEN_AGENT_ID"
env = {}

[[participants]]
agentbeats_id = "REPLACE_WITH_YOUR_PURPLE_AGENT_ID"
name = "qa_agent"
env = {
  OPENAI_API_KEY = "${OPENAI_API_KEY}",
  OPENAI_MODEL = "${OPENAI_MODEL}"
  # Optional identity overrides (if your purple agent supports them)
  # AGENT_CARD_NAME = "${AGENT_CARD_NAME}"
  # AGENT_CARD_VERSION = "${AGENT_CARD_VERSION}"
}

[config]
dataset = "adamson_psa_single"   # or "all", or datasets = ["...","..."]
max_units = 5                    # smoke test; remove for full run
random_seed = 0
emit_unit_results = true
write_files = true
output_dir = "artifacts"
```

The parameter of dataset(s) must be a subset of
`["adamson_psa_single", "norman_psa_single", "replogle_k562_psa_single", "replogle_rpe1_psa_single", "norman_psa_double"]`
or you can simply set `dataset = "all"` to evaluate on all datasets.

> Recommendation: Use max_units = 5 only for smoke tests. For a baseline / leaderboard entry, remove max_units to run the full dataset(s).
---

## Secrets (required for API-backed purple agents)

If your Purple Agent calls an external model provider, you might need to add the required keys as GitHub Actions Secrets in your fork. For example, `OPENAI_API_KEY` for OpenAI models.

---

## Run the workflow

1. Commit your `scenario.toml` changes to your fork. Normally this would immediately trigger the workflow, but if you want to manually run (or re-run) it:

2. Go to Actions tab and select the assessment workflow.

3. Click `Rerun all jobs`.

When it finishes:

- check the "Summary" and "Logs"

- confirm a new file appears under `results/` in your branch (or in the workflow's output commit).