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
| [Planning](#5-phase-1--planning) | Idea → approved planning artifacts | PRD, plan, issue breakdown |
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

This always applies to PRDs, plans, and issue breakdowns. It may apply to PR bodies and review responses when the human wants extra control.

### 2.2 Approval Gates

Before moving between major steps, the AI must not assume approval. It performs a read-back:

1. State the gate being crossed.
2. State what permissions are unlocked.
3. Summarize the next actions in 3–5 bullets.
4. Ask for explicit confirmation.

Only after confirmation does the workflow continue.

**Major gates.** Approval is required before:

- moving from PRD to implementation plan
- moving from plan to issue breakdown
- moving from issue breakdown to implementation
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
```

Optional sections (add only when useful for the size, risk, or ambiguity of the work):

```markdown
## Background / Context
## Goals
## Non-Goals
## Constraints
## Dependencies
## Risks / Unknowns
## Open Questions
```

### 4.2 `plan.md`

```markdown
# Plan: <Title>

## Source

- PRD/brief/request: <path, link, or summary>
- Status: Draft pending human approval

## Approach

How will we implement this?

## Affected Files / Areas

What parts of the system are likely to change?

## Verification

How will this be tested or checked?

## Slices / Issues

Source of truth: GitHub Issues.

Before issue creation, briefly describe the intended vertical slices without duplicating full issue bodies. After issue creation, list issue links or numbers.

## Risks / Unknowns

What might block or change the plan?
```

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

## Acceptance Criteria

- [ ] What must be true when this issue is done?

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
- GitHub issue breakdown, namespaced by iteration via title prefix, label, or milestone such as `iterai: YYYY-MM-DD-<slug>`.

### 5.3 Flow

```text
Idea
→ Grilling session
→ PRD draft
→ Human comments (Plannotator preferred) → AI revises → AI summarizes → Human approves
→ Implementation plan draft
→ Human comments → AI revises → AI summarizes → Human approves
→ Issue breakdown draft (GitHub Issues)
→ Human comments → AI revises → Human approves
→ Planning phase complete
```

Each artifact uses the review cycle from [§2.1](#21-review-cycle).

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
- affected areas are identified
- verification is concrete
- slices are small enough for one branch and one PR each
- risks and unknowns are visible
- issue breakdown can proceed without inventing context

Add detail when the implementation affects architecture, dependencies, migrations, security, performance, or multiple modules.

**Issue breakdown** (template: [§4.3](#43-github-issue)). Converts the plan into small, independently reviewable vertical slices in GitHub Issues.

GitHub Issues are the source of truth for issue content. Do not create a local `issues.md`. The local `plan.md` may link to issue numbers but should not duplicate full issue bodies.

Ready when:

- each issue is small enough to fit one branch and one PR
- each issue has goal, scope, acceptance criteria, and verification
- issues are ordered by dependency and risk

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
- Should issues be created in GitHub during this phase or drafted in markdown first?

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
