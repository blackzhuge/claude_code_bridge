---
name: oask
description: Async via oask, end turn immediately; use only when user explicitly delegates to OpenCode (ask/@opencode/let opencode/review); NOT for questions about OpenCode itself.
---

# Ask OpenCode Skill

Send a question to OpenCode AI assistant running in a separate terminal (async).

## Trigger Conditions

Use this skill ONLY when user **explicitly delegates** to OpenCode:
- User says "@opencode" / "ask opencode" / "let opencode" / "问opencode" / "让opencode" / "请opencode" + action verb
- User requests OpenCode to review/analyze/suggest/debug/help with code or design

**DO NOT trigger** when:
- User asks questions **about** OpenCode (e.g., "What is OpenCode?", "How does OpenCode work?")
- User mentions OpenCode in passing without delegation intent

## Execution (MANDATORY)

**CRITICAL: ALWAYS use run_in_background=true - NO EXCEPTIONS**

```
Bash(oask <<'EOF'
$ARGUMENTS
EOF
, run_in_background=true)
```

Where `$ARGUMENTS` is the user's delegation request (forwarded as-is to OpenCode).

## Workflow (IMPORTANT)

This is an **async task submission** workflow:
1. Submit task to background → OpenCode processes in separate terminal
2. IMMEDIATELY END your turn → Do NOT wait
3. Background completes → System auto-recalls you with result
4. You can start new conversation immediately

## After Execution (MANDATORY)

**If Bash succeeds:**
1. Tell user "OpenCode processing..." (include task_id if available)
2. **IMMEDIATELY END your turn**
3. **DO NOT wait for result**
4. **DO NOT check status**
5. **DO NOT continue working**

**If Bash fails (error/non-zero exit):**
1. Report the error to user
2. Suggest checking if OpenCode session is running (`ccb status opencode`)
3. Do NOT pretend task is processing

The system will automatically recall you when OpenCode responds.

## Wrong vs Right

❌ WRONG: `Bash(oask "question")` - blocks and waits
❌ WRONG: `Bash(oask "<question>", run_in_background=true)` - placeholder syntax
❌ WRONG: Submit then check status - wastes time
✅ RIGHT: `Bash(oask <<'EOF' ... EOF, run_in_background=true)` then END turn

## Parameters

- `--timeout SECONDS` optional (default 3600)
- `--output FILE` optional: write reply to FILE
