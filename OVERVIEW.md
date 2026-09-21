# How This Works — Overview

AI-assisted automation for Wagtail, running three workflows locally:
**issue triage**, **issue → draft PR**, and **PR review**. All GitHub reads
and writes go through your own account (`gh` CLI).

## Goal

Reduce manual triage and review effort: issues get classified, labelled, and
bug-reproduced automatically; triaged issues can become draft PRs with tests;
PRs get a first-pass review. **The AI only proposes — a human signs off, and
deterministic code applies the result to GitHub.**

## How a workflow runs

| Step | Type | What it does |
|---|---|---|
| 1. Prefetch | Script | Fetches the issue/PR, comments, labels |
| 2. Investigate | LLM agent | Reproduces bugs, writes code, drafts outputs |
| 3. Sign-off | Human gate | You approve, revise, or discard — nothing is posted before this |
| 4. Apply | Script | Posts comment / sets labels / creates draft PR, enforcing safety rules |

If the agent runs out of its time budget, a gate asks: keep going, wrap up,
or abandon. Guardrails in the apply step: label allowlists, @-mentions
stripped, PRs always draft and template-compliant.

Every run keeps its own directory (`.runs/<issue>/`) containing the working
environment (git worktree, virtualenv) and all findings — notes, diffs,
draft outputs — so you can inspect exactly what the agent did afterwards.

## Data sources

- GitHub (issues, PRs, reviews) via `gh`
- Local Wagtail checkout (code, bug reproduction via bakerydemo)
- Local label snapshot — labels are chosen from a fixed list, never invented
- Prior run artifacts, reused across workflows

## Tools needed

- **Conductor** — the workflow engine
- **LLM API key** (`CONDUCTOR_WORKFLOW_API_KEY`)
- **`gh` CLI** (authenticated), `jq`, Python 3
- **Local Wagtail checkout** (+ bakerydemo for reproductions)
