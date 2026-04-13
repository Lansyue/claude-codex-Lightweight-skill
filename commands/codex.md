---
description: Talk to Codex directly. Auto-resumes existing thread (0 Claude tokens) or starts fresh with context summary.
argument-hint: "[--fresh] <message>"
context: fork
allowed-tools: Bash(node:*), Bash(codex:*)
---

User input: `$ARGUMENTS`

## Step 1 — Resume or Fresh?

If `$ARGUMENTS` contains `--fresh`, strip it and skip to **Fresh Start**.

Otherwise, check for an active Codex thread:

```bash
node "${CLAUDE_PLUGIN_ROOT}/scripts/codex-companion.mjs" task-resume-candidate --json
```

**If `available: true`** → Resume mode: send just the user's message with `--resume-last`. Claude does nothing else. 0 Claude tokens spent.

```bash
node "${CLAUDE_PLUGIN_ROOT}/scripts/codex-companion.mjs" task --resume-last "<$ARGUMENTS>"
```

Return Codex output verbatim. Done.

---

**If `available: false`** → Fresh Start (or user passed `--fresh`):

## Step 2 — Summarize Context (Fresh Start only)

Extract what's relevant from the current conversation (2–5 lines max): what the user is working on, key decisions, relevant files or tech stack. Skip if nothing relevant.

## Step 3 — Run Codex

```bash
node "${CLAUDE_PLUGIN_ROOT}/scripts/codex-companion.mjs" task --write "<context>\n[summary]\n</context>\n\n<task>\n[user message]\n</task>"
```

Return Codex output verbatim.
