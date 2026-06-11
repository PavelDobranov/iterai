---
name: issues
description: "Copy the approved slices of a plan into GitHub Issues and record the links back in plan.md. Use when the user says \"create the issues\", invokes issues with a plan path or iteration slug, or asks to turn an approved plan into GitHub issues."
user-invocable: true
argument-hint: "<plan.md path, iteration slug, or nothing if only one approved plan exists>"
---

# Issues

You copy the approved slices of a `plan.md` into GitHub Issues. This is copying, not writing: the slices were already reviewed when the plan was approved. Do not redesign, reorder, merge, split, or expand them.

This skill only creates issues and adds the links to the plan's `Slices / Issues` section. Do not write code, create branches, or open PRs.

## Workflow

### 1. Find the approved plan

- If given a plan path, use it. If given a slug, use `.agents/iterai/iterations/YYYY-MM-DD-<slug>/plan.md`.
- If nothing is given and exactly one approved plan has no issues yet, use it. Otherwise ask.
- The plan's `Status` line must read `Approved <date>`. If not, stop: the plan must be approved first.
- If some slices already have issue links, stop and ask whether to create only the missing ones.

### 2. Create the issues

For each slice, create one GitHub issue with `gh issue create`:

- Title: `iterai: YYYY-MM-DD-<slug> — <slice title>` (or follow the project's existing convention).
- Fill the body only from the plan. If a slice lacks something the template needs, stop and ask — the fix belongs in the plan.
- Create issues in the plan's slice order.

#### Issue Template

<!-- mirror of iterai README.md §4.3 — keep in sync -->

```markdown
# Issue: <Title>

## Goal

What concrete outcome should this issue produce?

## Scope

What changes are included?

## Design

- See: <path to plan.md> § Design
- Touches: <module(s)>

## Acceptance Criteria

- [ ] What must be true when this issue is done?

## Verification

What command, test, or manual check proves it works?
```

Link the plan's Design section — do not copy it into the issue. For trivial issues: `Design: N/A — <reason>`.

Optional sections: Out of Scope, Dependencies, Blockers.

### 3. Record and stop

- Add each issue link next to its slice in the plan's `Slices / Issues` section.
- Print the list of created issues and stop. Starting implementation requires explicit human approval.

## Rules

- Copying only. Issue content comes from the approved plan; never invent it.
- One slice = one issue = one future branch and PR.
- Acceptance criteria describe outcomes, not implementation steps.
- Do not create a local `issues.md`; GitHub Issues are the source of truth.
- If `gh` is not authenticated or the repo has no GitHub remote, stop and report instead of improvising another tracker.
