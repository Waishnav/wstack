---
name: agent-matrix
description: Which provider, model and reasoning effort to delegate implementation, QA or code review to, with fallbacks. Use before delegating any of those to another agent (delegate_task, subagents or child threads).
---

# Agent matrix

The user's current choice of agent for each kind of delegated work. Availability changes, so this file is the source of truth: it overrides memory and the harness default. IDs are T3 Code `delegate_task` targets (`providerInstanceId` / `model` / options). Confirm them with `orchestrator_capabilities` once per thread.

Research and planning stay with the orchestrator, which is usually Claude Opus 5.5. Don't delegate them.

## Matrix

| Work | When | Provider instance | Model | Effort option |
| --- | --- | --- | --- | --- |
| Implementation | Hard or medium tasks | `claudeAgent` | `claude-opus-5-5` | `effort: medium` |
| Implementation | Simple to medium tasks where the codebase already defines the pattern | `claudeAgent` | `claude-sonnet-5-5` | `effort: high` (`medium` when trivial) |
| QA | Scenarios and QA tooling already set up | `codex_ac1` or `codex_ac2` (Codex Go 1 / 2) | `gpt-6-luna` | `reasoningEffort: high` |
| QA | Same, when both Go accounts are busy or out of quota | `antigravity` | `gemini-3.8-flash` | `thinking_level: high` |
| QA | Complex QA: no scenarios yet, unclear setup, or hard-to-reproduce behaviour | `codex` | `gpt-6.1-terra` | `reasoningEffort: medium` |
| Review | Independent code review | `codex` | `gpt-6.1-sol` | `reasoningEffort: high` |

**Fallbacks.**
- Implementation: Opus and Sonnet stand in for each other. Sonnet as a stand-in for Opus gets `effort: high`.
- Routine QA: Go 1, then Go 2, then Antigravity. Antigravity's quality is equivalent to Luna high, but its runs take longer, so budget for that.
- Complex QA and review have no listed fallback. If the target is unavailable, tell the user and ask; don't pick a substitute.

Use the parallelism that fits: one QA agent per independent scenario set, and at most one per Go account.

## Triage review findings

`gpt-6.1-sol` at high effort is very thorough, and it reports edge cases that real users rarely or never hit. Don't apply its output wholesale. For each finding:

1. **Fix** anything that causes incorrect behaviour, data loss or a security or privacy issue on a path users actually take, and anything that breaks a contract the product depends on.
2. **Defer** a real but rare edge case when the product's near-term direction doesn't make it matter. Record it in the PR's Known gaps or a follow-up, with the reason.
3. **Drop** findings that need an impossible or contrived state, that duplicate an existing guard, or that only add speculative defensiveness.

When the right call depends on where the product is heading, say which findings you deferred or dropped and why, so the user can overrule you.

## Keep it current

When the user changes a preference or reports a model as unavailable or worse, update this table in `~/agent-dots` rather than remembering it elsewhere.
