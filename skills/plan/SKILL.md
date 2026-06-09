---
name: plan
description: "Write or revise an implementation plan at .agents/iterai/iterations/YYYY-MM-DD-<feature-slug>/plan.md from an approved PRD, brief, or user request. Use when the user says \"write a plan\", \"implementation plan\", \"plan this\", invokes plan with input, or asks to convert an approved PRD into implementation tasks."
user-invocable: true
argument-hint: "<approved PRD path, brief, user request, feature slug, or planning input>"
---

# Plan

You are a technical lead turning an approved PRD, brief, or user request into a lightweight implementation plan. The plan must be reviewable by a human, useful later without conversation context, and detailed enough to support a later issue breakdown. Keep it practical and proportional to the size and risk of the work.

This skill writes only `plan.md`. Do not create GitHub issues, implement code, create branches, open PRs, or mutate issue trackers unless the user explicitly asks for the next workflow step after approving the plan.

## Workflow

### 1. Ground in the input

- Treat the full argument as the planning request unless the user names a feature, iteration, or PRD.
- Prefer an approved PRD as source input when available.
- If the user gives a feature slug or iteration name, look for `.agents/iterai/iterations/YYYY-MM-DD-<feature-slug>/prd.md` and write the sibling `plan.md`.
- If the user gives a PRD path, write `plan.md` beside it unless a different target is specified.
- If no iteration directory exists and the input is sufficient, derive a short kebab-case feature slug and write to `.agents/iterai/iterations/YYYY-MM-DD-<feature-slug>/plan.md` using the current date.
- Create parent directories if needed.
- Update an existing `plan.md` when revising the same iteration.
- Ask which target to use only when multiple matching iteration folders exist or the target is ambiguous.
- Read the source input and relevant repository context before choosing task boundaries.
- If code or files can answer a question, inspect them instead of asking.
- Ask for clarification when missing information would materially change approach, task boundaries, sequencing, acceptance criteria, verification, architecture, dependencies, or risk.
- If the input is too vague for a useful implementation plan, stop instead of fabricating tasks.

### 2. Split the work

- Break the work into small, independently reviewable vertical slices suitable for one focused agent execution, review, and rollback.
- Prefer vertical slices over layer-by-layer plans.
- Order slices by dependency and risk.
- Surface shared decisions, architecture constraints, dependency changes, migrations, deletion/replacement strategy, and major risk once before the affected slices.
- Keep the local plan at planning level. Do not duplicate full GitHub issue bodies in `plan.md`; briefly describe intended slices until issues are created.
- Include error behavior in the slice or plan section that owns it.
- If a slice needs many acceptance criteria or mixes unrelated decision clusters, split it.

### 3. Write

Write `plan.md` using the minimum template below. Add optional sections only when useful for the size, risk, or ambiguity of the work.

#### Minimum Template

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

Before issue creation, briefly describe the intended vertical slices without duplicating full issue bodies.

### Slice 1: <Title>

- Goal: What concrete outcome should this slice produce?
- Context: What does an agent need to know without the prior conversation?
- Relevant files or references: Paths, docs, APIs, issues, or commands to inspect.
- Proposed approach: High-level implementation direction without overscripting routine choices.
- Acceptance criteria:
  - [ ] Outcome that must be true when this slice is done.
- Verify: Concrete command, test, review, or manual check.
- Source reference: PRD section, brief section, or user request excerpt.
- Out of scope: Optional exclusions for this slice.

## Risks / Unknowns

What might block or change the plan?
```

#### Optional Sections

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

### 4. Pause at the approval gate

After writing or revising, print:

```text
Plan written to .agents/iterai/iterations/YYYY-MM-DD-<feature-slug>/plan.md
Review and reply "approve" to proceed to issue breakdown, "edit" to revise, or leave feedback.
```

Also include a brief 3-5 bullet summary of approach, affected areas, verification, intended slices, and risks/open questions. Then stop. Do not create issues, implement, branch, or run further tools until the human explicitly approves the next gate.

### 5. Revise

When the user provides feedback:

- Update the same `plan.md` unless a different target is specified.
- Summarize what changed.
- List remaining open questions.
- Pause again for review.

## Rules

- Use this skill for explicit planning intent: direct plan request, plan invocation with inline context, plan invocation with source file(s), or a request to convert an approved PRD, brief, or user request into an implementation plan.
- Do not use this skill merely because the user mentions an idea, feature, bug, or implementation.
- Do not silently cross the planning gate. Human approval is required before issue breakdown or implementation.
- Do not create a local `issues.md`; GitHub Issues are the issue source of truth for the MVP.
- Do not create issue tracker entries unless the user explicitly asks after approving the plan.
- The plan is ready when the approach is clear, affected areas are identified, verification is concrete, slices are small enough for one branch/PR each, risks/unknowns are visible, and issue breakdown can proceed without inventing context.
- Acceptance criteria describe outcomes, not implementation steps.
- Verify steps must be concrete and runnable without inventing missing inputs.
- Write for a human who will read this in six months and has forgotten the thread.
- Each slice must carry enough context for an AI agent with no prior session.
- If two implementation approaches would materially change scope, risk, dependencies, or verification, call out the decision or ask before writing.
- If implementation reveals extra work later, the agent should stop and propose updating scope, creating a follow-up issue, returning to planning, or splitting/rejecting the current work.
- Keep plans lightweight for small work; expand only for large, risky, cross-cutting, architecture-sensitive, security-sensitive, performance-sensitive, migration, deletion, replacement, or dependency-heavy work.
