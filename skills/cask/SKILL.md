---
name: cask
description: Async via cask, end turn immediately; use only when user explicitly delegates to Codex (ask/@codex/let codex/review); NOT for questions about Codex itself.
---

# Ask Codex Skill

Send a question to Codex AI assistant running in a separate terminal (async).

## Trigger Conditions

Use this skill ONLY when user **explicitly delegates** to Codex:
- User says "@codex" / "ask codex" / "let codex" / "问codex" / "让codex" / "请codex" + action verb
- User requests Codex to review/analyze/suggest/debug/help with code or design

**DO NOT trigger** when:
- User asks questions **about** Codex (e.g., "What is Codex?", "How does Codex work?")
- User mentions Codex in passing without delegation intent

## Execution (MANDATORY)

**CRITICAL: ALWAYS use run_in_background=true - NO EXCEPTIONS**

```
Bash(cask <<'EOF'
$ARGUMENTS
EOF
, run_in_background=true)
```

Where `$ARGUMENTS` is the user's delegation request (forwarded as-is to Codex).

## Workflow (IMPORTANT)

This is an **async task submission** workflow:
1. Submit task to background → Codex processes in separate terminal
2. IMMEDIATELY END your turn → Do NOT wait
3. Background completes → System auto-recalls you with result
4. You can start new conversation immediately

## After Execution (MANDATORY)

**If Bash succeeds:**
1. Tell user "Codex processing..." (include task_id if available)
2. **IMMEDIATELY END your turn**
3. **DO NOT wait for result**
4. **DO NOT check status**
5. **DO NOT continue working**

**If Bash fails (error/non-zero exit):**
1. Report the error to user
2. Suggest checking if Codex session is running (`ccb status codex`)
3. Do NOT pretend task is processing

The system will automatically recall you when Codex responds.

## Wrong vs Right

❌ WRONG: `Bash(cask "question")` - blocks and waits
❌ WRONG: `Bash(cask "<question>", run_in_background=true)` - placeholder syntax
❌ WRONG: Submit then check status - wastes time
✅ RIGHT: `Bash(cask <<'EOF' ... EOF, run_in_background=true)` then END turn

## Parameters

- `--timeout SECONDS` optional (default 3600)
- `--output FILE` optional: write reply to FILE
