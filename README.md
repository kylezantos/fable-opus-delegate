# delegate

A Claude Code skill that turns your premium-model session (Fable, Opus 5, whatever you're rationing) into an **orchestrator + QC agent**, and hands the actual implementation to Opus subagents.

## Why this exists

I [posted](https://x.com/kylezantos) that in a Fable session you can just say:

> "Use Opus subagents to execute the work, & you to act as the orchestrator and QC agent to make sure the work gets executed correctly"

…and your Fable spend goes way 📉.

Then [Tom Johnson](https://x.com/tomjohndesign) replied *"you should make a skill for this."*

So this is Tom's fault.

## What it does

Invoking `/delegate` puts the session in orchestrator mode:

- **Delegates implementation** to subagents running on Opus, in parallel where possible, each with a self-contained brief (goal, files, constraints, definition of done).
- **Verifies instead of trusting** — the orchestrator reads the actual diffs and runs the checks itself before accepting any subagent's work.
- **Keeps judgment work at the top** — decomposition, review, and integration stay with the premium model; the typing goes to the cheaper one.
- **Knows when not to delegate** — trivial edits and judgment-heavy one-file problems are handled directly, where delegation overhead would exceed the savings.

## Honest caveat

This saves **premium-model tokens, not total tokens**. Each subagent has to re-read context the orchestrator already had, so overall spend usually goes *up* — you're shifting the expensive share of the work onto a cheaper model, and keeping your main session's context window lean as a bonus. It shines on parallelizable, implementation-heavy tasks; on a single gnarly sequential problem it mostly adds overhead.

## Install

```sh
git clone https://github.com/kylezantos/delegate-skill ~/.claude/skills/delegate
```

Then in any Claude Code session:

```
/delegate refactor the settings page to use the new form components
```

Or just describe what you want and mention delegating to Opus subagents — the skill triggers on that too.

## License

MIT
