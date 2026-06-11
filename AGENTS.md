# Working with Iterai

This project uses the Iterai workflow: AI does the work, the human decides the direction. If you are an AI agent working in this repo, follow this guidance even when no skill is invoked.

## The Flow

```text
Plan → Implement → Ship
```

One issue = one branch = one PR.

Work happens in iterations. Each iteration lives at `.agents/iterai/iterations/YYYY-MM-DD-<slug>/` and produces `prd.md`, `plan.md`, GitHub issues (one per slice), and PRs (one per issue).

## Skills

- `grill-me`: pressure-test an idea before writing anything (optional, for ideas with open decisions).
- `prd`: write or revise the PRD — what to build and why.
- `plan`: write or revise the implementation plan — design first, then small vertical slices.
- `issues`: copy the approved plan's slices into GitHub issues. Copying, not writing.
- `implement`: build one approved issue on one branch, test-first.
- `ship`: take one implemented branch through PR, review, and merge.

When the user asks for new functionality without invoking a skill, do not start coding. Route to the right phase: no PRD → `prd`; PRD approved but no plan → `plan`; plan approved but no issues → `issues`; issue approved → `implement`. Trivial fixes (typos, docs, obvious one-line bugs) may skip the pipeline — say so and keep the change minimal.

## Gates

<!-- authoritative list — README.md §2.2/§2.3 point here -->

Never cross these without an explicit "yes" from the human:

- writing the plan (after the PRD is approved)
- creating issues and starting work (after the plan is approved)
- starting a branch, opening a PR, merging
- any change to scope, architecture, or dependencies — including new dependencies, big refactors, and deletions

Stop and ask when requirements are unclear, acceptance criteria cannot be checked, the work exceeds one PR, the work no longer matches the approved plan, or verification fails without an obvious fix. New work discovered mid-implementation becomes a follow-up issue — never quietly included.

## Rules

- Committed `prd.md` and `plan.md` are the source of truth, then GitHub issues and PRs.
- GitHub Issues hold issue content; never keep a local `issues.md`. No index files or extra status tracking.
- Test-first: the failing test comes before the implementation. For docs and chores, state how the work was verified instead.
- Acceptance criteria describe outcomes, not implementation steps.
- Do not hide what you couldn't verify.
