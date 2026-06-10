---
name: issues
description: "Transcribe the approved slices of a plan into GitHub Issues and record the links back in plan.md. Use when the user says \"create the issues\", \"transcribe issues\", invokes issues with a plan path or iteration slug, or asks to turn an approved plan into GitHub issues."
user-invocable: true
argument-hint: "<plan.md path, iteration slug, or nothing if only one approved plan exists>"
---

# Issues

You transcribe the approved slices of a `plan.md` into GitHub Issues. This is transcription, not authoring: the slices were reviewed and approved as part of plan approval. Do not redesign, reorder, merge, split, or expand them.

This skill only creates issues and updates the plan's `Slices / Issues` section with the resulting links. Do not implement code, create branches, or open PRs.

## Workflow

### 1. Locate the approved plan

- If given a plan path, use it. If given an iteration slug, use `.agents/iterai/iterations/YYYY-MM-DD-<slug>/plan.md`.
- If nothing is given and exactly one iteration has an approved, untranscribed plan, use it. Otherwise ask which one.
- Verify the plan's `Status` line reads `Approved <date>`. If it does not, stop: the plan must be approved before transcription.
- If the plan's slices already have issue links, stop and ask whether to create only the missing ones.

### 2. Transcribe

For each slice in the plan's `Slices / Issues` section, create one GitHub issue with `gh issue create`:

- Title: `iterai: YYYY-MM-DD-<slug> — <slice title>` (or use the project's existing iteration label/milestone convention if one exists).
- Body follows the template below, filled only from the plan — do not invent content. If a slice lacks something the template needs, stop and ask instead of fabricating.
- Create issues in the plan's slice order (dependency and risk order).

#### Issue Template

<!-- mirror of ITERAI.md §4.3 — keep in sync -->

```markdown
# Issue: <Title>

## Goal

What concrete outcome should this issue produce?

## Scope

What changes are included?

## Contract

- Plan: <path to plan.md> § Design
- Touches: <module(s) from Design>
- Out of contract: <one line — what callers must NOT rely on, if anything>

For trivial issues (doc-only, dependency bump, typo): `Contract: N/A — <reason>`.

## Acceptance Criteria

- [ ] What must be true when this issue is done?

## Verification

What command, test, review, or manual check proves it works?
```

Optional sections: Out of Scope, Dependencies, Blockers, Definition of Done.

### 3. Record and stop

- Add each issue link or number next to its slice in the plan's `Slices / Issues` section.
- Print the list of created issues and stop. Do not start implementation; the next gate (branch + implementation of one issue) requires explicit human approval.

## Rules

- Transcription only. The issue content comes from the approved plan; if it cannot be filled from the plan, stop and ask — the fix belongs in the plan, not the issue.
- Contracts are by reference: link `plan.md § Design`, never restate it.
- Acceptance criteria describe observable outcomes, not implementation steps.
- One slice = one issue = one future branch and PR. Do not merge or split slices during transcription.
- Do not create a local `issues.md`; GitHub Issues are the issue source of truth.
- If `gh` is unauthenticated or the repo has no GitHub remote, stop and report instead of improvising another tracker.
