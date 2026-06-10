# Iterai

A simple way to build software with AI, where the human stays in control.

> AI does the work. The human decides the direction.

## Contents

- [`ITERAI.md`](./ITERAI.md) — the full workflow: rules, phases, templates
- [`skills/`](./skills) — skills built from the workflow

## At a glance

```text
Plan → Implement → Ship
```

The core rule: **one issue = one branch = one PR**.

Each unit of work (an *iteration*) lives at `.agents/iterai/iterations/YYYY-MM-DD-<slug>/` in the target repo and produces:

- `prd.md` — what to build and why
- `plan.md` — how to build it, split into small slices
- GitHub Issues — one per slice
- GitHub PR — one per issue

The human approves each step: the PRD, the plan, starting work, and merging.

## Status

Manual MVP. Skills and automation are built from `ITERAI.md`, not the other way around.
