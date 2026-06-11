---
name: plan
description: "Write or revise an implementation plan at .agents/iterai/iterations/YYYY-MM-DD-<feature-slug>/plan.md from an approved PRD, brief, or user request. Use when the user says \"write a plan\", \"implementation plan\", \"plan this\", invokes plan with input, or asks to convert an approved PRD into implementation tasks."
user-invocable: true
argument-hint: "<approved PRD path, brief, user request, feature slug, or planning input>"
---

# Plan

You turn an approved PRD, brief, or user request into a short implementation plan. The plan must be reviewable by a human, useful later without the conversation, and detailed enough to copy into GitHub issues. Keep it proportional to the size and risk of the work.

This skill writes only `plan.md`. Do not create issues, write code, create branches, or open PRs unless the user explicitly asks for the next step after approving the plan.

## Workflow

### 1. Prepare

- Prefer an approved PRD as input. If given a slug, look for `.agents/iterai/iterations/YYYY-MM-DD-<slug>/prd.md` and write the sibling `plan.md`. If given a PRD path, write `plan.md` next to it.
- If no iteration folder exists and the input is enough, derive a slug and write to `.agents/iterai/iterations/YYYY-MM-DD-<feature-slug>/plan.md` using the current date.
- When revising the same iteration, update the existing `plan.md`. Ask only if the target is ambiguous.
- Read the input and relevant repo context before splitting the work. If code can answer a question, read the code instead of asking.
- Ask when missing information would change the approach, slicing, verification, architecture, or dependencies. If the input is too vague for a useful plan, stop instead of inventing tasks.

### 2. Design first

Do the Design section before splitting the work into slices.

- Name every module that is new, changed, or removed. For each, write the interface callers see and what it hides.
- Sketch at least two ways to split the work; keep the loser as a one-line rejected alternative. One option is not a design.
- Watch out for: modules that only pass data through, logic repeated across slices, special cases that should be one general rule. Fix or justify these.
- If a project design file exists (e.g. `.agents/iterai/design.md`), read it first and write Design against it.
- If Design cannot be completed without inventing requirements, stop and ask.

### 3. Split the work

- Split into small vertical slices. Each slice fits one branch, one PR, and one focused agent run.
- Order slices by dependency and risk.
- Each slice must respect the interfaces in Design. If a slice needs a wider interface, that is a design change — revise Design first.
- Keep `plan.md` at planning level. Describe slices briefly; do not write full issue bodies.
- If a slice needs many acceptance criteria or mixes unrelated decisions, split it.

### 4. Write

Use the template below. Add optional sections only when the work is big, risky, or unclear.

#### Template

<!-- mirror of README.md §4.2 — keep in sync -->

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

### Slice 1: <Title>

- Goal: What concrete outcome should this slice produce?
- Context: What does an agent need to know without the prior conversation?
- Relevant files or references: Paths, docs, APIs, issues, or commands to inspect.
- Touches: Which module(s) from Design does this slice change?
- Acceptance criteria:
  - [ ] Outcome that must be true when this slice is done.
- Verify: Concrete command, test, review, or manual check.

## Risks / Unknowns

What might block or change the plan?
```

Optional sections, when useful: `Constraints`, `Dependencies`, `Migration Notes`, `Security Notes`, `Performance Notes`, `Follow-Ups`.

### 5. Pause

After writing or revising, print:

```text
Plan written to .agents/iterai/iterations/YYYY-MM-DD-<feature-slug>/plan.md
Review and reply "approve" to proceed to issue creation, "edit" to revise, or leave feedback.
```

Add a 3–5 bullet summary of approach, design, verification, slices, and risks. Approving the plan approves its slices; issue creation afterwards is copying, not a new review. Then stop. Do not create issues, implement, or branch until the human approves.

### 6. Revise

On feedback: update the same `plan.md`, summarize what changed, list remaining open questions, and pause again.

When the user approves, change the `Status` line to `Approved <YYYY-MM-DD>`. That line is the only workflow state.

## Rules

- Use this skill only when the user explicitly asks for a plan. Mentioning an idea, feature, or bug is not enough.
- Human approval is required before issue creation or implementation. Never cross that gate silently.
- GitHub Issues are the issue source of truth. Do not create a local `issues.md`.
- The plan is ready when: the approach and design are clear, verification is concrete, each slice fits one branch and one PR, and issues can be created from it without inventing context.
- Do not skip Design. For trivial work (docs, typos, dependency bumps), `Design: N/A — <reason>` is fine — but use it sparingly.
- Acceptance criteria describe outcomes, not implementation steps.
- Error behavior belongs to the slice that owns it; do not let error handling fall between slices.
- Verify steps must be concrete and runnable.
- Write for a human who reads this in six months. Each slice must carry enough context for an agent with no prior session.
- If two approaches would meaningfully change scope, risk, or dependencies, call out the decision or ask before writing.
