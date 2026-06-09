# Iterai

A manual, human-in-the-loop AI-assisted development workflow.

> AI accelerates execution. The human controls direction.

## Contents

- [`ITERAI.md`](./ITERAI.md) — the full workflow spec (principles, phases, templates, open questions)
- [`skills/`](./skills) — invocable skills derived from the spec

## At a glance

```text
Planning → Implementation → PR → Review and Merge
```

Each iteration lives at `.agents/iterai/iterations/YYYY-MM-DD-<slug>/` in the target repo and produces:

- `prd.md` — what and why
- `plan.md` — how, sliced into issues
- GitHub Issues — source of truth for issue content
- GitHub PR — source of truth for review and merge

## Status

Manual MVP. Skills and automation are derived from `ITERAI.md`, not the other way around.
