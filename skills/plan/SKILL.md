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

### 2. Design before slicing

The Design section in the template is required. Do it before splitting the work into slices, not after.

- Name every module touched (new, modified, removed). For each, write the interface callers see and what it hides.
- Sketch at least two decompositions and keep the loser as a one-line rejected alternative. One option is not a design.
- Walk the red-flag list and flag anything the plan introduces: shallow modules, pass-throughs, information leakage, temporal decomposition, repeated logic, special-cases that should be generalized, abstractions that break layer separation. Justify or fix.
- State the net complexity direction: adds, neutral, or reduces. If it adds, note why this is the right trade and capture any deferred refactor as a follow-up.
- If a project-level design artifact exists (e.g. `.agents/iterai/design.md`), read it first and write the Design section against it.
- If the design pass cannot be completed without inventing requirements, stop and surface the question. Do not slice on top of a vague design.

### 3. Split the work

- Break the work into small, independently reviewable vertical slices suitable for one focused agent execution, review, and rollback.
- Prefer vertical slices over layer-by-layer plans.
- Order slices by dependency and risk.
- Surface shared decisions, architecture constraints, dependency changes, migrations, deletion/replacement strategy, and major risk once before the affected slices.
- Each slice must respect the modules and interfaces defined in the Design section. If a slice requires widening an interface beyond Design, call that out explicitly and treat it as a design decision, not a coding choice.
- Keep the local plan at planning level. Do not duplicate full GitHub issue bodies in `plan.md`; briefly describe intended slices until issues are created.
- Include error behavior in the slice or plan section that owns it.
- If a slice needs many acceptance criteria or mixes unrelated decision clusters, split it.

### 4. Write

Write `plan.md` using the minimum template below. Add optional sections only when useful for the size, risk, or ambiguity of the work.

#### Minimum Template

<!-- mirror of ITERAI.md §4.2 — keep in sync -->

```markdown
# Plan: <Title>

## Source

- PRD/brief/request: <path, link, or summary>
- Status: Draft pending human approval

## Approach

How will we implement this?

## Design

The design pass that must happen before slicing.

### Modules

For each module touched (new, modified, removed):

- **<name>** — new | modified | removed
  - Interface: what callers see (signature shape, surface area)
  - Hides: what this module owns that nothing else needs to know
  - Depth: deep | balanced | shallow — and why that is acceptable

### Considered Alternatives

At least one alternative decomposition we rejected, and why.

### Red Flags Checked

Note any introduced by this plan, with justification:

- shallow module (interface roughly the size of its implementation)
- pass-through method or class
- information leakage between modules
- temporal decomposition (modules shaped like a sequence of steps, not by what they hide)
- special-case logic that should be generalized
- repeated logic across slices
- new abstraction that breaks layer separation

### Complexity Direction

Net effect on structural complexity: **adds** | **neutral** | **reduces**.

If it adds, why is that the right trade now? Link any design-debt follow-ups.

## Affected Files / Areas

What parts of the system are likely to change?

## Verification

How will this be tested or checked?

## Slices / Issues

Source of truth: GitHub Issues.

Before issue creation, briefly describe the intended vertical slices without duplicating full issue bodies. Each slice must respect the modules and interfaces from Design.

### Slice 1: <Title>

- Goal: What concrete outcome should this slice produce?
- Context: What does an agent need to know without the prior conversation?
- Relevant files or references: Paths, docs, APIs, issues, or commands to inspect.
- Proposed approach: High-level implementation direction without overscripting routine choices.
- Touches: Which module(s) from Design does this slice change?
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

### 5. Pause at the approval gate

After writing or revising, print:

```text
Plan written to .agents/iterai/iterations/YYYY-MM-DD-<feature-slug>/plan.md
Review and reply "approve" to proceed to issue breakdown, "edit" to revise, or leave feedback.
```

Also include a brief 3-5 bullet summary of approach, **design (modules + complexity direction)**, affected areas, verification, intended slices, and risks/open questions. Then stop. Do not create issues, implement, branch, or run further tools until the human explicitly approves the next gate.

### 6. Revise

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
- The plan is ready when the approach is clear, the Design section is complete (modules named, alternatives considered, red flags checked, complexity direction stated), affected areas are identified, verification is concrete, slices are small enough for one branch/PR each and respect the design, risks/unknowns are visible, and issue breakdown can proceed without inventing context.
- Do not skip the Design section even for small work. For trivial slices it may be short, but it must exist: at minimum the module touched, what it hides, and the complexity direction.
- For doc-only, dependency bump, or typo-class iterations, a one-line `Design: N/A — <reason>` is acceptable in place of the full section. Use this sparingly; if you reach for it twice in a row, the work is probably not as trivial as it looks.
- If a slice requires changing an interface beyond what Design specifies, stop and revise Design first — do not let implementation discover the design.
- Acceptance criteria describe outcomes, not implementation steps.
- Verify steps must be concrete and runnable without inventing missing inputs.
- Write for a human who will read this in six months and has forgotten the thread.
- Each slice must carry enough context for an AI agent with no prior session.
- If two implementation approaches would materially change scope, risk, dependencies, or verification, call out the decision or ask before writing.
- If implementation reveals extra work later, the agent should stop and propose updating scope, creating a follow-up issue, returning to planning, or splitting/rejecting the current work.
- Keep plans lightweight for small work; expand only for large, risky, cross-cutting, architecture-sensitive, security-sensitive, performance-sensitive, migration, deletion, replacement, or dependency-heavy work.
