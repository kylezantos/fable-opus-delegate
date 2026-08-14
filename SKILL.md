---
name: delegate
description: Fable → Opus delegation. In a Fable session, the main session delegates implementation to Opus subagents and acts as the orchestrator and quality gate, saving Fable tokens for judgment work. Use when the user says "delegate", "use Opus subagents", "orchestrator mode", or wants to conserve Fable budget on implementation-heavy tasks. Only applies when the session model is Fable — Opus delegating to Opus saves nothing.
---

# Fable → Opus Delegate

This skill is specifically for **Fable sessions**: the point is to spend cheaper Opus tokens on implementation and reserve Fable — the more expensive, more capable model — for orchestration and judgment.

**First, check what model you are.** If this session is not running on Fable (you're Opus, Sonnet, or something else), the economics don't work — Opus delegating to Opus just adds briefing overhead with zero savings. Tell the user this skill only pays off in a Fable session, and proceed with the task normally.

If you are Fable, you are the **orchestrator and quality gate** for this task. Do not implement the work yourself — delegate implementation to subagents launched with the Agent tool and `model: "opus"`. Reserve your own effort for decomposing the task, briefing subagents, judging their results, and integrating.

## Delegation

- Break the task into self-contained units of work. Launch independent units **in parallel** (multiple Agent calls in one message).
- Each subagent starts blind — it has none of your conversation context. Write each one a self-contained brief:
  - **Goal** — what done looks like, concretely.
  - **Files** — the specific paths involved, plus any context you've already learned that it would otherwise have to rediscover.
  - **Constraints** — existing patterns to match, things not to touch, scope limits.
  - **Definition of done** — the check it should run (typecheck, test, build) and that it must report the actual output, not a summary of success.
- Include this prohibition verbatim in every subagent prompt when uncommitted work exists: *"Never touch working-tree state: no `git stash`, `git reset`, `git checkout -- <path>`, or `git clean`. If your harness wants a clean tree, stop and report instead."*

## Quality control

When a subagent reports back, **verify — don't trust**:

1. Read the actual diff of what it changed.
2. Run the relevant check yourself (typecheck, lint, test, build) — do not accept the subagent's claim that it passed.
3. Check the work against the original brief: scope respected, patterns matched, nothing extra.

If work fails QC, send that subagent specific corrections and have it fix its own work — don't silently redo it yourself. After two failed rounds, take over that unit directly.

## Exceptions — do these yourself

- Trivially small changes (a few lines): delegation overhead exceeds the savings.
- Final integration and conflict resolution across subagent outputs.
- Judgment-heavy work with little typing (a design decision, a subtle one-file bug): delegating shifts almost no cost and adds a briefing round-trip.

## What this trades

This saves **Fable tokens** (the orchestrator's), not total tokens — each Opus subagent re-reads context Fable already had, so overall spend usually goes up while the expensive share shifts to the cheaper model. It also keeps the main session's context window lean, since subagent tool output never lands in the main conversation. Best for parallelizable, well-specified, implementation-heavy work.
