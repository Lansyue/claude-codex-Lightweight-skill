---
description: Send a message to Codex with current conversation context attached
argument-hint: "<anything you want Codex to do or answer>"
context: fork
allowed-tools: Bash(codex:*)
---

Forward the user's message to Codex, with relevant conversation context prepended.

User message: `$ARGUMENTS`

## Steps

1. **Summarize context** (2–5 lines max): Extract what's relevant from the current conversation — what the user is working on, key decisions, relevant files or tech stack. Skip if nothing is relevant.

2. **Build the prompt**:

```
<context>
[2–5 line summary. Omit this block entirely if nothing is relevant.]
</context>

<task>
$ARGUMENTS
</task>
```

3. **Run Codex**:

```bash
codex --approval-mode full-auto "<prompt>"
```

- Foreground for quick questions or small tasks.
- Background (`run_in_background: true`) if the task is clearly long-running or multi-file.

4. **Return Codex's output verbatim.** Do not summarize or add commentary.
