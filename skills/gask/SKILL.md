---
name: gask
description: Async via gask, end turn immediately; use only when user explicitly delegates to Gemini (ask/@gemini/let gemini/review); NOT for questions about Gemini itself.
---

# Ask Gemini Skill

Send a question to Gemini AI assistant running in a separate terminal (async).

## Trigger Conditions

Use this skill ONLY when user **explicitly delegates** to Gemini:
- User says "@gemini" / "ask gemini" / "let gemini" / "问gemini" / "让gemini" / "请gemini" + action verb
- User requests Gemini to review/analyze/suggest/debug/help with code or design

**DO NOT trigger** when:
- User asks questions **about** Gemini (e.g., "What is Gemini?", "How does Gemini work?")
- User mentions Gemini in passing without delegation intent

## Execution (MANDATORY)

**CRITICAL: ALWAYS use run_in_background=true - NO EXCEPTIONS**

```
Bash(gask <<'EOF'
$ARGUMENTS
EOF
, run_in_background=true)
```

Where `$ARGUMENTS` is the user's delegation request (forwarded as-is to Gemini).

## Workflow (IMPORTANT)

This is an **async task submission** workflow:
1. Submit task to background → Gemini processes in separate terminal
2. IMMEDIATELY END your turn → Do NOT wait
3. Background completes → System auto-recalls you with result
4. You can start new conversation immediately

## After Execution (MANDATORY)

**If Bash succeeds:**
1. Tell user "Gemini processing..." (include task_id if available)
2. **IMMEDIATELY END your turn**
3. **DO NOT wait for result**
4. **DO NOT check status**
5. **DO NOT continue working**

**If Bash fails (error/non-zero exit):**
1. Report the error to user
2. Suggest checking if Gemini session is running (`ccb status gemini`)
3. Do NOT pretend task is processing

The system will automatically recall you when Gemini responds.

## Wrong vs Right

❌ WRONG: `Bash(gask "question")` - blocks and waits
❌ WRONG: `Bash(gask "<question>", run_in_background=true)` - placeholder syntax
❌ WRONG: Submit then check status - wastes time
✅ RIGHT: `Bash(gask <<'EOF' ... EOF, run_in_background=true)` then END turn

## Parameters

- `--timeout SECONDS` optional (default 3600)
- `--output FILE` optional: write reply to FILE
