# fable-opus-delegate

A Claude Code skill for **Fable sessions**: turn Fable into an **orchestrator + QC agent** and hand the actual implementation to Opus subagents. Fable's tokens go to judgment; Opus's tokens go to typing.

This only makes sense in a Fable session — Opus delegating to Opus saves nothing, and the skill will tell you so instead of pretending otherwise.

## Why this exists

I [posted](https://x.com/kylezantos) that in a Fable session you can just say:

> "Use Opus subagents to execute the work, & you to act as the orchestrator and QC agent to make sure the work gets executed correctly"

…and your Fable spend goes way 📉.

Then [Tom Johnson](https://x.com/tomjohndesign) replied *"you should make a skill for this."*

So this is Tom's fault.

## What it does

Invoking `/delegate` in a Fable session puts it in orchestrator mode:

- **Delegates implementation** to subagents running on Opus, in parallel where possible, each with a self-contained brief (goal, files, constraints, definition of done).
- **Verifies instead of trusting** — Fable reads the actual diffs and runs the checks itself before accepting any subagent's work.
- **Keeps judgment work at the top** — decomposition, review, and integration stay with Fable; the typing goes to Opus.
- **Knows when not to delegate** — trivial edits and judgment-heavy one-file problems are handled directly, where delegation overhead would exceed the savings.
- **Checks the session model first** — if you're not on Fable, it says so and just does the task normally.

## Honest caveat

This saves **Fable tokens, not total tokens**. Each Opus subagent has to re-read context Fable already had, so overall spend usually goes *up* — you're shifting the expensive share of the work onto the cheaper model, and keeping your main session's context window lean as a bonus. It shines on parallelizable, implementation-heavy tasks; on a single gnarly sequential problem it mostly adds overhead.

## Install

```sh
git clone https://github.com/kylezantos/fable-opus-delegate ~/.claude/skills/delegate
```

Then in any Fable session:

```
/delegate refactor the settings page to use the new form components
```

Or just describe what you want and mention delegating to Opus subagents — the skill triggers on that too.

## License

MIT
