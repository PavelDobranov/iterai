# Iterai

A simple way to build software with AI, where the human stays in control.

> AI does the work. The human decides the direction.

The AI writes drafts, code, and summaries. The human reviews, answers questions, and says when to move forward.

This document is the source of truth for the workflow, with one exception: the gate and stop-and-ask lists live in [`AGENTS.md`](./AGENTS.md), because that file must stand alone in target repos. The skills in [`skills/`](./skills) are built from these two documents, not the other way around.

Installing Iterai into a target repo means copying `AGENTS.md` and the skills there once the project is scaffolded. `AGENTS.md` is self-contained: it gives the default agent the flow, the skill routing, and the gates without needing this document.

**Status:** Manual MVP.

---

## 1. The Flow

```text
Plan → Implement → Ship
```

| Phase | What happens | Result | Skills |
| --- | --- | --- | --- |
| [Plan](#5-phase-1--plan) | Turn an idea into a PRD, a plan, and GitHub issues | Approved plan + issues | `grill-me` → `prd` → `plan` → `issues` |
| [Implement](#6-phase-2--implement) | Build one issue on one branch | Working, tested change | `implement` |
| [Ship](#7-phase-3--ship) | Open a PR, handle review, merge | Merged PR, closed issue | `ship` |

The core rule of the whole workflow:

```text
One issue = one branch = one PR
```

---

## 2. The Rules

These rules apply everywhere. The phases below refer back to them.

### 2.1 Review Loop

Every important document (PRD, plan) goes through this loop:

```text
AI writes a draft
→ Human comments
→ AI updates the draft and summarizes what changed
→ Human approves, or comments again
```

### 2.2 Ask Before Moving On

The AI never assumes approval. Before crossing a gate, it says what it wants to do next and waits for a clear "yes". The list of gates lives in [`AGENTS.md` § Gates](./AGENTS.md).

### 2.3 Stop and Ask

The AI stops and asks the human instead of pushing through uncertainty. The list of stop conditions lives in [`AGENTS.md` § Gates](./AGENTS.md).

### 2.4 Keep Scope Small

If new work appears during implementation, the AI does not quietly include it. It stops and proposes one of:

- update the issue scope (with approval)
- create a follow-up issue
- go back to planning

---

## 3. Files

Each unit of work is an **iteration**. Its files live in the target repo:

```text
.agents/iterai/iterations/YYYY-MM-DD-<slug>/
  prd.md
  plan.md
```

Rules:

- Folder names use the date plus a short lowercase-kebab-case slug.
- These files are committed to the repo.
- GitHub Issues hold the issue content. Do not keep a local `issues.md`.
- No index files, config files, or extra status tracking.

Source of truth, in order:

1. committed `prd.md` and `plan.md`
2. GitHub Issues and PRs
3. this document

---

## 4. Templates

Each template has a small required core. Add optional sections only when the work is big, risky, or unclear.

### 4.1 `prd.md` — what and why

```markdown
# PRD: <Title>

## Intent

What are we trying to accomplish?

## User / Problem

Who has the problem and what hurts?

## Scope

What is included in this iteration?

## Out of Scope

What is explicitly excluded?

## Acceptance Criteria

- [ ] What must be true for this to be accepted?
```

Acceptance criteria describe what a user or observer can see — not internal details. If a criterion names a class, file, or function, rewrite it as visible behavior.

Optional sections, when useful: `Background`, `Goals`, `Non-Goals`, `Constraints`, `Dependencies`, `Risks`, `Open Questions`.

### 4.2 `plan.md` — how

```markdown
# Plan: <Title>

## Source

- PRD: <path or link>
- Status: Draft

## Approach

How will we implement this?

## Design

For each module that is new, changed, or removed:

- **<name>** — new | changed | removed
  - Interface: what callers see
  - Hides: what only this module needs to know

One alternative we considered and rejected, and why (one line is enough).

## Affected Areas

What parts of the system will change?

## Verification

How will this be tested or checked?

## Slices / Issues

The small steps the work is split into. Each slice fits in one branch and one PR. After issues are created on GitHub, list the links here.

## Risks / Unknowns

What might block or change the plan?
```

Notes:

- The `Status` line is the only workflow state. On approval, change it to `Approved <YYYY-MM-DD>`.
- Design comes before slicing. Sketch at least two ways to split the work; keep the loser as a one-line note. One option is not a design.
- Watch out for: modules that only pass data through, logic repeated across slices, special cases that should be one general rule. Fix or justify these.
- Slices must respect the interfaces in Design. If a slice needs a wider interface, that is a plan change — revise the plan first.
- For trivial work (docs, typos, dependency bumps), `Design: N/A — <reason>` is fine.

Optional sections, when useful: `Constraints`, `Dependencies`, `Migration Notes`, `Security Notes`, `Performance Notes`, `Follow-Ups`.

### 4.3 GitHub Issue — one slice

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

### 4.4 PR Description

```markdown
# PR: <Title>

## Linked Issue

- Closes #<number>

## Summary

What changed?

## Verification

- Commands run and their results
- For features and bugfixes: show the test failing before and passing after

## Known Limitations

What is intentionally not solved here?

## Follow-Ups

Links to follow-up issues, if any.
```

### 4.5 Changing the Templates

Update a template when real use shows that a section is always needed, never needed, or confusing. Keep the required core small.

---

## 5. Phase 1 — Plan

Turn an idea into an approved plan and GitHub issues before any code is written.

```text
Idea
→ Grilling session (optional — useful when the idea has open decisions)
→ PRD draft → review loop (§2.1) → approved
→ Plan draft (with slices) → review loop (§2.1) → approved
→ Copy approved slices into GitHub issues → record links in plan.md
→ Done
```

Every step is started by the human. Nothing auto-advances.

**The PRD is ready when:** intent and the user's problem are clear, scope and out-of-scope are explicit, and acceptance criteria are concrete and checkable.

**The plan is ready when:** the approach and design are clear, verification is concrete, and each slice is small enough for one branch and one PR.

**Issue creation is copying, not writing.** The slices were already reviewed when the plan was approved. Each issue matches one slice, ordered by dependency and risk. If a slice is missing something the issue needs, fix the plan — do not invent content in the issue.

---

## 6. Phase 2 — Implement

Build one approved issue. Do not expand the plan.

```text
Pick one approved issue
→ Confirm its scope
→ Create a branch
→ Write the failing test first (or define how to verify)
→ Show the failure
→ Implement the minimal change
→ Show the test passing
→ Run the relevant test suite
→ Move to Ship
```

Rules:

- Stay inside the issue scope. New discoveries become follow-up issues (§2.4).
- No new dependencies or big refactors without approval (§2.2).
- For bugfixes: first reproduce the bug with a failing test, then fix it.
- For docs or chores where tests do not apply: say how it was verified instead.

---

## 7. Phase 3 — Ship

Open a PR, handle review feedback, and merge.

```text
Confirm the branch matches one approved issue
→ Write the PR description (§4.4) → human may review it first
→ Open the PR
→ Human reviews:
   - approve → merge → close issue → record follow-ups
   - request changes → AI updates the same branch, re-verifies, asks for re-review
   - reject → stop, summarize why, go back to the issue or the plan
→ Done
```

Before merging, check:

- the issue is linked and its acceptance criteria are met
- verification evidence is current
- all review comments are resolved
- follow-up work is captured as issues

---

## 8. Open Questions

Things to resolve through real use. When answered, fold the answer into the document and delete the question.

- How small can the PRD and plan get before they stop being useful?
- How strict should test-first be in practice?
- Where should verification evidence live: PR body, issue comment, or both?
- Who merges: human only, or AI with approval?
- When does this manual workflow graduate to automation, and which part first?
