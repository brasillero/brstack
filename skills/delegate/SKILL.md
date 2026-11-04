---
name: delegate
description: Split work between Codex (investigation and analysis), a Claude subagent (implementation) and Claude (orchestration and final review). Use when a task involves investigating a bug, analyzing or planning a change, or implementing code.
---

# Delegation

You orchestrate and own the final review. Codex and the implementer can take the heavy, token-expensive parts when that actually pays off.

## Pick only what the task needs

Loading this skill doesn't mean using every agent. The roles below are options, not a pipeline to run start to finish:
- A quick question, a small fix, or something you already understand: do it yourself.
- A tricky investigation with no implementation yet: Codex alone may be enough.
- A clear, well-scoped implementation: go straight to the implementer, no investigation step.
- Delegating has a cost too (writing the brief, reading the result). Delegate only when the work is big enough to be worth it.

When it's not clear whether to delegate, or to whom, ask the user before starting. Say in one line what you'd do and why.

## Roles

These are the defaults:

| Role | Who | Effort |
|---|---|---|
| Investigation, analysis, planning | Codex CLI, GPT-6.1 Sol | medium; xhigh when hard |
| Implementation (the implementer) | Claude subagent, Opus 5.5 | medium; high for complex or cross-cutting changes |
| Orchestration, final review | You: Claude Opus 5.5, the main session | medium; high for security or architecture |

Use another model or provider for a role only when the user names it explicitly for this task. Then use exactly what they named. Don't swap a default on your own, not even when it's unavailable or seems a better fit: say what's wrong and ask.

## Full flow (when the task needs all of it)

1. **Investigate** with Codex, read-only. Bring back the finding and a proposed fix. "Diagnose before acting" applies: stop here unless the user already gave the go-ahead.
2. **Implement** with the implementer, only after the go-ahead.
3. **Review** yourself.
   - Read the diff (`git diff`).
   - Run the project's checks: lint, type-check and format, whatever it has. Run the tests too, when they exist and are relevant to the change.
   - Treat any way around a check as a finding, unless the brief allowed it. That includes disabled lint or formatting rules, type casts to silence the type checker, skipped or loosened tests, and changed lint or format config.
   - Flag tests nobody needed.
   - For large diffs, you can ask Codex for a first-pass review and then filter it. Codex tends to flag more issues, and not all of them are actionable.
4. **Fix**.
   - Send the exact findings back to the implementer.
   - If the tool you run in can continue the implementer's session, for example by messaging the same subagent instead of starting a new one, do that, so it keeps its context. Otherwise start a new implementer with the original brief plus the findings.
   - Then review again, and repeat until the review is clean. After five rounds, stop and tell the user what's still failing.
   - Do trivial fixes, a few lines, yourself.

## Codex

- In non-interactive runs, close stdin, or it waits forever.
- Raise to `xhigh` when any of these apply:
  - there is no reliable repro;
  - the bug crosses modules or services;
  - it involves concurrency, security, or performance with conflicting evidence;
  - it is an architecture decision with real tradeoffs.
- Don't use `max`. It costs more and does no better than `xhigh`.
- Investigation never gets write access.

## Implementer

- Run the implementer as a Claude subagent on Opus 5.5. Set the model explicitly, so it doesn't fall back to a smaller default subagent model.
- It starts with no context from your session, so the brief is all it knows. When the task is complex, add the minimum context that changes how it implements: the investigation's finding, a decision the user made, an approach already ruled out. Leave out the rest of the conversation.
- Keep one task per implementer. If it loops re-reading files or stalls, stop it and re-brief smaller.

## Briefs

Codex and the implementer share no context with your session. Every brief is self-contained: goal, relevant files, constraints, what "done" means, and how to verify. Say whether it is an investigation or an implementation brief.

In implementation briefs, tell the implementer to fix problems rather than silence checks, and to say so when a check can't pass. Add tests only where they're needed and fit the project's existing tests. If the project has no tests at all, don't create committed ones; a throwaway check to verify the change is fine.

_Model lineup as of October 2026. Revisit when models change._
