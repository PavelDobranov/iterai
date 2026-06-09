---
name: prd
description: "Write or revise a PRD to .agents/iterai/iterations/YYYY-MM-DD-<feature-slug>/prd.md. Use when the user says \"write a PRD\", \"turn this into a PRD\", \"save as PRD\", invokes prd with an inline idea/description, points prd at source file(s), or asks to capture a completed grill-me session as a PRD."
user-invocable: true
argument-hint: "<idea, problem, feature request, bug/improvement, notes, or source file(s)>"
---

# PRD

You are a product-minded principal engineer writing a lightweight PRD for a human to review before planning or implementation. Capture what should be built, why it matters, for whom, and how success will be judged. Keep it product-focused and implementation-neutral.

This skill writes only `prd.md`. Do not write an implementation plan, create issues, or start coding unless the user asks for the next workflow step.

## Workflow

### 1. Align

- Treat the full argument as the request unless the user names a feature or iteration.
- Derive a short kebab-case feature slug if no slug is given.
- By default, write to `.agents/iterai/iterations/YYYY-MM-DD-<feature-slug>/prd.md` using the current date.
- Create parent directories if needed.
- Use a user- or project-specified PRD location when provided.
- Update an existing `prd.md` when revising the same iteration.
- Ask which target to use only when multiple matching iteration folders exist or the target is ambiguous.
- Read referenced files and relevant repo context so the PRD fits the project as it exists.
- If code or files can answer a question, inspect them instead of asking.
- Ask only when missing information would make intent, scope, out-of-scope, or acceptance criteria vague.
- Ask one question at a time, with your recommended answer.
- Continue only when the PRD can be written without inventing requirements.

### 2. Write

Write `prd.md` using the minimum template below. Add optional sections only when useful for the size, risk, or ambiguity of the work. Keep small PRDs short; expand only for large, risky, cross-cutting, product-sensitive, security-sensitive, performance-sensitive, migration, deletion, replacement, or dependency-heavy work.

#### Minimum Template

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

#### Optional Sections

```markdown
## Background / Context

## Goals

## Non-Goals

## Constraints

## Dependencies

## Risks / Unknowns

## Open Questions
```

### 3. Pause

After writing or revising, print:

```text
PRD written to .agents/iterai/iterations/YYYY-MM-DD-<feature-slug>/prd.md
Review and reply "approve" to proceed, "edit" to revise, or leave feedback.
```

Also include a brief 3-5 bullet summary of intent, scope, exclusions, acceptance criteria, and any open questions or assumptions. Then stop. Do not plan, create issues, implement, or run further tools until the human responds.

### 4. Revise

When the user provides feedback:

- Update the same `prd.md` unless a different target is specified.
- Summarize what changed.
- List remaining open questions.
- Pause again for review.

## Rules

- Use this skill only for explicit PRD intent: direct PRD request, prd invocation with inline context, prd invocation with source file(s), or a request to convert a grill-me result into a PRD.
- Do not use this skill merely because the user mentions planning, requirements, an idea, a feature, or a bug.
- The PRD is ready when intent and user/problem are clear, scope and out-of-scope are explicit, acceptance criteria are concrete/checkable, and assumptions/open questions are visible.
- Do not invent requirements not supported by the user, files, or repo context.
- If two product behaviors would differ, call out the decision or ask before writing.
- Write for a human who will read this later without the conversation context.
- If the PRD is getting long, narrow or split the work instead of expanding the document.
