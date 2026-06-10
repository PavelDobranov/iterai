# ITERAI

A manual, human-in-the-loop AI-assisted development workflow.

> AI accelerates execution. The human controls direction.

The AI may draft, propose, revise, implement, and summarize. The human approves gates, resolves ambiguity, and decides when to move forward.

This is the manual MVP. Skills and automation are derived from this document, not the other way around.

---

## 1. Flow

```text
Planning → Implementation → PR → Review and Merge
```

| Phase | Purpose | Output |
| --- | --- | --- |
| [Planning](#5-phase-1--planning) | Idea → approved planning artifacts | PRD, plan, transcribed issues |
| [Implementation](#6-phase-2--implementation) | Execute one approved issue | Branch + verification |
| [PR](#7-phase-3--pr) | Package the slice | Reviewable PR |
| [Review and Merge](#8-phase-4--review-and-merge) | Resolve feedback, merge, capture follow-ups | Merged PR + closed issue |

---

## 2. Universal Patterns

These patterns apply across every phase. Phase sections refer back here rather than restating them.

### 2.1 Review Cycle

Important artifacts follow this loop:

```text
AI drafts artifact
→ Human comments (Plannotator preferred for markdown/docs)
→ AI updates artifact and summarizes changes
→ Human approves or comments again
```

This always applies to PRDs and plans. Issue creation is transcription from the approved plan, not a review cycle (see §5.4). It may apply to PR bodies and review responses when the human wants extra control.

### 2.2 Approval Gates

Before moving between major steps, the AI must not assume approval. It performs a read-back:

1. State the gate being crossed.
2. State what permissions are unlocked.
3. Summarize the next actions in 3–5 bullets.
4. Ask for explicit confirmation.

Only after confirmation does the workflow continue.

**Major gates.** Approval is required before:

- moving from PRD to implementation plan
- moving from approved plan to issue transcription and implementation
- starting work on a branch
- opening or publishing a PR (if the human wants that control)
- merging (unless project rules say otherwise)
- changing scope, architecture, dependencies, deletion/replacement strategy, or large refactor direction

### 2.3 Universal Stop Conditions

The AI stops and asks before continuing when:

- requirements are unclear
- acceptance criteria are missing or untestable
- an issue or slice is too large
- the work no longer matches the approved plan
- scope, architecture, or dependency changes are needed
- deletion/replacement strategy is needed
- a large refactor is needed
- verification fails and the fix is not obvious
- the human has not approved the next gate

Phase sections may add phase-specific stop conditions.

### 2.4 Scope Discipline

```text
One approved issue = one branch = one PR
```

If implementation reveals extra work, the AI does not silently include it. It stops and proposes one of:

- update the issue scope with approval
- create a follow-up issue
- return to planning
- reject or split the current work

---

## 3. Artifacts

Every iteration produces the same artifact types, with weight scaled to the work. Required artifacts, variable weight.

### 3.1 Layout

```text
.agents/
  iterai/
    iterations/
      YYYY-MM-DD-<slug>/
        prd.md
        plan.md
```

Rules:

- `.agents/` is the shared agent namespace.
- `iterai/` is the Iterai framework namespace.
- `iterations/` contains all work units.
- Iteration folders use `YYYY-MM-DD-<lowercase-kebab-case-slug>`.
- Local artifact filenames are lowercase.
- These files are committed to the target repository.
- Do not create a local `issues.md`; GitHub Issues are the issue source of truth.
- Do not create a local `readme.md`, global config, or index file inside `.agents/iterai/` for the MVP.

### 3.2 Source of Truth

In order:

1. committed Iterai local artifacts in the repository
2. GitHub Issues and PRs
3. this document
4. personal notes

---

## 4. Templates

### 4.1 `prd.md`

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

Acceptance criteria describe what an observer sees, not what happens inside. If an AC names a class, file, function, or other implementation detail, rewrite it in terms of observable behavior.
```

Optional sections (add only when useful for the size, risk, or ambiguity of the work):

```markdown
## Background / Context
## Goals
## Non-Goals
## External Surface
## Errors Designed Out
## Constraints
## Dependencies
## Risks / Unknowns
## Open Questions
```

`External Surface` describes the contract the user, caller, or operator sees — not the mechanism. Use it when the iteration introduces or changes anything externally visible (API, CLI, file format, UI affordance). Writing this section forces the PRD to be framed from the outside.

`Errors Designed Out` lists failure modes the design makes impossible (vs. failure modes the design merely handles). Example: instead of "handle missing config file", state "config is always present because the installer writes a default". Use it when error handling would otherwise complicate the design.

### 4.2 `plan.md`

```markdown
# Plan: <Title>

## Source

- PRD/brief/request: <path, link, or summary>
- Status: Draft pending human approval

## Approach

How will we implement this?

## Design

The design pass that must happen before slicing. Skipping this section is not allowed.

### Modules

For each module touched (new, modified, removed), one bullet:

- **<name>** — new | modified | removed
  - Interface: what callers see (signature shape, surface area)
  - Hides: what this module owns that nothing else needs to know
  - Depth: deep | balanced | shallow — and why that is acceptable

### Considered Alternatives

At least one alternative decomposition we rejected, and why.

One option is not a design. Sketch two; keep the loser as a one-line note.

Close the Design section with two one-liners:

- Red flags: none | <flag — justification>
- Complexity: adds | neutral | reduces — <why, if adds>

## Affected Files / Areas

What parts of the system are likely to change?

## Verification

How will this be tested or checked?

## Slices / Issues

Source of truth: GitHub Issues.

Describe the intended vertical slices without duplicating full issue bodies. These slices are reviewed as part of plan approval. After transcription to GitHub, list issue links or numbers here.

Each slice should respect the modules and interfaces defined in [Design](#design). Slices that require widening an interface beyond what Design specifies must call that out explicitly.

## Risks / Unknowns

What might block or change the plan?
```

Red flags to check: shallow module, pass-through method or class, information leakage between modules, temporal decomposition, special-case logic that should be generalized, repeated logic across slices, new abstraction that breaks layer separation. Justify or fix anything flagged. If complexity is added, link any design-debt follow-ups.

The `Status` line is the only workflow state. On approval, flip it to `Approved <YYYY-MM-DD>`. No other status tracking, front matter, or index files.

Optional sections:

```markdown
## Constraints
## Decisions Needed
## Dependencies
## Data / Migration Notes
## Security / Privacy Notes
## Performance Notes
## Rollback / Recovery
## Follow-Ups
```

### 4.3 GitHub Issue

```markdown
# Issue: <Title>

## Goal

What concrete outcome should this issue produce?

## Scope

What changes are included?

## Contract

The design contract this issue must respect, by reference. Do not restate the plan's Design section — link it.

- Plan: <path to plan.md> § Design
- Touches: <module(s) from Design>
- Out of contract: <one line — what callers must NOT rely on, if anything>

The implementing agent reads the linked Design section before coding. Widening an interface beyond Design requires revising the plan first — it is a design decision, not a coding choice.

For trivial issues (doc-only, dependency bump, typo): `Contract: N/A — <reason>`.

## Acceptance Criteria

- [ ] What must be true when this issue is done?

Acceptance criteria describe observable outcomes, not implementation steps. If a criterion names a class, file, or internal function, rewrite it.

## Verification

What command, test, review, or manual check proves it works?
```

Optional sections: Out of Scope, Dependencies, Blockers, Definition of Done.

### 4.4 PR Description

```markdown
# PR: <Title>

## Linked Issue

- Closes #<number>

## Summary

What changed?

## Scope Confirmation

Which approved scope does this PR cover?

## Verification

- Commands run and results
- Red/green evidence for feature or bugfix work

## Known Limitations

What is intentionally not solved here?

## Follow-Ups

Links to follow-up issues or notes.
```

### 4.5 Template Evolution

Update these templates when real usage shows that a section is:

- always useful and should be required
- rarely useful and should be optional
- unclear or duplicated
- missing for team handoff
- missing for historical traceability

---

## 5. Phase 1 — Planning

Turn an idea into reviewable planning artifacts before any implementation begins.

### 5.1 Inputs

- Initial idea, problem, feature request, bug, or improvement.
- Existing repo context, docs, issues, and constraints if available.

### 5.2 Outputs

- Local `prd.md` and `plan.md` at the path in [§3.1 Layout](#31-layout).
- GitHub issues transcribed from approved slices, namespaced by iteration via title prefix, label, or milestone such as `iterai: YYYY-MM-DD-<slug>`.

### 5.3 Flow

```text
Idea
→ Grilling session (optional)
→ PRD draft
→ Human comments (Plannotator preferred) → AI revises → AI summarizes → Human approves
→ Implementation plan draft (slices included)
→ Human comments → AI revises → AI summarizes → Human approves
→ Issues transcribed to GitHub from approved slices → links recorded in plan.md
→ Planning phase complete
```

Every step is human-triggered. No skill auto-advances to the next artifact.

Grilling is optional; invoke it when the idea has unresolved decisions. Its output feeds the PRD and is not persisted as an artifact — the PRD captures whatever mattered.

PRD and plan use the review cycle from [§2.1](#21-review-cycle). Slices are reviewed as part of plan approval; issue creation afterwards is pure transcription, with no separate review cycle.

### 5.4 When Each Artifact Is Ready

**PRD** (template: [§4.1](#41-prdmd)). Defines what should be built, why, for whom, and how success will be judged.

Ready when:

- intent and user/problem are clear
- scope and out-of-scope are explicit
- acceptance criteria are concrete and checkable
- assumptions and open questions are visible

Add detail when the iteration is large, risky, cross-cutting, or product-sensitive.

**Implementation plan** (template: [§4.2](#42-planmd)). Defines how the PRD will be implemented.

Ready when:

- approach is clear
- design pass is done: modules named, interfaces sketched, at least one alternative considered and rejected, red flags checked, complexity direction stated
- affected areas are identified
- verification is concrete
- slices are small enough for one branch and one PR each, and respect the design
- risks and unknowns are visible
- issue transcription can proceed without inventing context

Add detail when the implementation affects architecture, dependencies, migrations, security, performance, or multiple modules.

**Issue transcription** (template: [§4.3](#43-github-issue)). Creates GitHub Issues from the approved plan's slices. This is transcription, not a new review cycle — the slices were already reviewed at plan approval.

GitHub Issues are the source of truth for issue content. Do not create a local `issues.md`. The local `plan.md` links to issue numbers but does not duplicate issue bodies.

Ready when:

- each issue matches one approved slice, small enough for one branch and one PR
- each issue has goal, scope, contract, acceptance criteria, and verification
- each issue's contract references `plan.md` § Design rather than restating it
- issues are ordered by dependency and risk
- issue links are recorded back in `plan.md`

### 5.5 Phase-Specific Stop Conditions

In addition to [§2.3](#23-universal-stop-conditions), stop and ask if:

- the plan is too broad
- an issue is too large for one PR
- architecture, dependency, or major scope decisions are unresolved

---

## 6. Phase 2 — Implementation

Implement one approved issue at a time while keeping scope small, testable, and reviewable. This phase executes one approved vertical slice. It does not expand the plan.

### 6.1 Inputs

- Approved issue.
- Approved implementation plan.
- Existing repo state.
- Verification commands or test strategy.

### 6.2 Outputs

- One branch for the selected issue.
- Tests or verification evidence.
- Implemented change within approved scope.
- Notes about discoveries, blockers, or follow-up work.

### 6.3 Flow

```text
Select one approved issue
→ Confirm issue scope
→ Create/switch branch
→ Write failing test or define verification first
→ Run test/verification and capture baseline
→ Implement minimal change
→ Run verification again
→ Refactor only if needed and safe
→ Capture final verification evidence
→ Move to PR phase
```

### 6.4 Core Rules

- One issue = one branch = one PR.
- Stay inside the approved issue scope.
- Do not silently add scope.
- Do not install dependencies without approval.
- Do not perform large refactors without approval.
- Convert discoveries into follow-up issues or planning notes.

### 6.5 TDD / Verification

**Feature work**

1. Write the failing test first.
2. Show the failure.
3. Implement minimal code.
4. Show the passing test.
5. Run the relevant suite.

**Bugfix work**

1. Reproduce the bug with a failing regression test.
2. Implement the fix.
3. Show the regression test passing.
4. Run the relevant suite.

**Refactor / chore / docs work**

- Use appropriate verification.
- If tests are not applicable, explain why.

### 6.6 Phase-Specific Stop Conditions

In addition to [§2.3](#23-universal-stop-conditions), stop and ask if:

- the approved plan appears wrong
- tests cannot be meaningfully written
- implementation requires broader changes than the issue describes

---

## 7. Phase 3 — PR

Package one completed implementation slice into a clear, reviewable pull request. The PR makes scope, verification, and remaining risks obvious to the human reviewer.

### 7.1 Inputs

- Completed branch for one approved issue.
- Verification evidence.
- Any implementation notes or discovered follow-ups.

### 7.2 Outputs

- Pull request draft or opened PR.
- PR description following the template in [§4.4](#44-pr-description).
- Linked issue.

### 7.3 Flow

```text
Review branch status
→ Confirm changes match one approved issue
→ Draft PR description (see §4.4)
→ Human reviews PR draft if desired
→ Open or update PR
→ Move to Review and Merge phase
```

Optional pre-open review loop:

```text
AI drafts PR body → Human comments → AI revises → Human approves opening PR
```

### 7.4 PR Description Checklist

Every PR includes:

- linked issue (`Closes #N`)
- summary of changes
- scope confirmation
- tests added or updated
- verification commands and results
- red/green evidence for feature or bugfix work
- screenshots or manual notes if useful
- known limitations
- follow-up issues or recommendations

### 7.5 Phase-Specific Stop Conditions

In addition to [§2.3](#23-universal-stop-conditions), stop and ask if:

- the branch includes unrelated changes
- the PR covers more than one issue
- verification is missing or failed
- the implementation differs from the approved plan

---

## 8. Phase 4 — Review and Merge

Resolve review feedback, verify the final branch state, merge safely, and capture follow-up work. This phase closes the loop for one issue/branch/PR slice.

### 8.1 Inputs

- Open PR.
- Reviewer comments or approval.
- Verification requirements.

### 8.2 Outputs

- Updated PR if changes were requested.
- Final verification evidence.
- Merged PR or explicit rejection/rollback decision.
- Closed or updated issue.
- Follow-up issues or notes if needed.

### 8.3 Flow

```text
Human reviews PR
→ Outcome: accept, comment/request changes, or reject
→ If comments: AI updates same branch → re-runs verification → human reviews again
→ If accepted: merge → close/update issue → record follow-ups
→ If rejected: stop, summarize why, return to issue/plan/PRD as appropriate
→ Iteration slice complete
```

### 8.4 Review Outcomes

**Accept.** PR satisfies scope, acceptance criteria, and verification. Next: merge and close/update the issue.

**Comment / request changes.** PR is directionally correct but needs updates. Next: AI updates the same branch, re-verifies, summarizes changes, pauses for re-review.

**Reject.** PR should not proceed as-is. Next: stop, summarize why, and return to issue, plan, or PRD refinement.

### 8.5 Merge Checklist

Before merge:

- issue is linked
- acceptance criteria are satisfied
- verification evidence is current
- review comments are resolved
- follow-up work is captured
- no unexpected scope remains

### 8.6 Phase-Specific Stop Conditions

In addition to [§2.3](#23-universal-stop-conditions), stop and ask if:

- review feedback changes scope
- feedback contradicts the approved plan
- verification fails after review changes
- the PR should be rejected or split

---

## 9. Open Questions

Unresolved design questions for the workflow itself. Resolve through real use, then fold answers back into the relevant section and remove the question here.

### Planning

- What is the smallest useful PRD format?
- How lightweight can the required artifacts be before they stop being useful?
- How detailed should the implementation plan be?

### Implementation

- How strict should TDD be in practice?
- Should every implementation begin with explicit branch approval?
- How should verification evidence be captured: markdown, PR body, issue comment, or all three?
- When should small refactors be allowed without a new approval gate?
- What is the maximum acceptable size of one implementation slice?

### PR

- Should the human approve the PR body before every PR is opened?
- Should first push require explicit approval?
- Where should verification evidence live: PR body only, issue comment, or separate note?
- What PR size is too large?
- Should screenshots / manual test notes be required for UI work?

### Review and Merge

- Who performs the final merge: human only, AI with approval, or project-dependent?
- Should every merge require a final read-back confirmation?
- How should rejected PRs be documented?
- Should follow-up issues be created immediately or collected for later planning?
- What marks an iteration as complete?

### Cross-Cutting

- When does the manual MVP graduate to automation, and what is the first piece to automate?
- How are iterations indexed across multiple projects without a global index file?
