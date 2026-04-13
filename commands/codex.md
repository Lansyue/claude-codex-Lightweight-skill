---
description: Talk to Codex directly. Smart resume: 0 tokens if consecutive, Claude summarizes if new context exists.
argument-hint: "[--fresh] <message>"
context: fork
allowed-tools: Bash(node:*)
---

User input: `$ARGUMENTS`

If `$ARGUMENTS` contains `--fresh`, strip it and go to **Fresh Start**.

---

## Step 1 — Check for active Codex thread

```bash
node "${CLAUDE_PLUGIN_ROOT}/scripts/codex-companion.mjs" task-resume-candidate --json
```

---

### If `available: false` → Fresh Start

Summarize relevant context from the conversation (2–5 lines: what the user is working on, key decisions, tech stack). Then run:

```bash
node "${CLAUDE_PLUGIN_ROOT}/scripts/codex-companion.mjs" task --write "<context>\n[summary]\n</context>\n\n<task>\n$ARGUMENTS\n</task>"
```

---

### If `available: true` → Check for new Claude context

Look at the conversation history between the last `/codex` call and now:

**No new Claude conversation** (last messages were just `/codex` + Codex output):
→ Pure resume. Send only the user's new message. 0 Claude tokens.

```bash
node "${CLAUDE_PLUGIN_ROOT}/scripts/codex-companion.mjs" task --write --resume-last "$ARGUMENTS"
```

**New Claude conversation exists** (user and Claude discussed something in between):
→ Summarize only the new discussion (2–3 lines), then resume with that context attached.

```bash
node "${CLAUDE_PLUGIN_ROOT}/scripts/codex-companion.mjs" task --write --resume-last "<new_context>\n[summary of new discussion only]\n</new_context>\n\n$ARGUMENTS"
```

---

Return Codex output verbatim. Do not summarize or add commentary.
