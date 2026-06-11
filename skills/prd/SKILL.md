---
name: prd
description: "Write or revise a PRD to .agents/iterai/iterations/YYYY-MM-DD-<feature-slug>/prd.md. Use when the user says \"write a PRD\", \"turn this into a PRD\", \"save as PRD\", invokes prd with an inline idea/description, points prd at source file(s), or asks to capture a completed grill-me session as a PRD."
user-invocable: true
argument-hint: "<idea, problem, feature request, bug/improvement, notes, or source file(s)>"
---

# PRD

You write a short PRD for a human to review before planning or implementation. Capture what should be built, why, for whom, and how success will be judged. Stay product-focused: no implementation details.

This skill writes only `prd.md`. Do not write a plan, create issues, or start coding unless the user asks for the next step.

## Workflow

### 1. Prepare

- Treat the full argument as the request. Derive a short kebab-case slug if none is given.
- Write to `.agents/iterai/iterations/YYYY-MM-DD-<feature-slug>/prd.md` using the current date. Create folders if needed.
- When revising the same iteration, update the existing `prd.md`. Ask only if the target is ambiguous.
- Read referenced files and relevant repo context first. If code can answer a question, read the code instead of asking.
- Ask the user only when missing information would make intent, scope, or acceptance criteria vague. One question at a time, with your recommended answer.
- Do not invent requirements.

### 2. Write

Use the template below. Add optional sections only when the work is big, risky, or unclear.

#### Template

<!-- mirror of README.md §4.1 — keep in sync -->

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

### 3. Pause

After writing or revising, print:

```text
PRD written to .agents/iterai/iterations/YYYY-MM-DD-<feature-slug>/prd.md
Review and reply "approve" to proceed, "edit" to revise, or leave feedback.
```

Add a 3–5 bullet summary of intent, scope, exclusions, acceptance criteria, and any open questions. Then stop. Do not plan, create issues, or implement until the human responds.

### 4. Revise

On feedback: update the same `prd.md`, summarize what changed, list remaining open questions, and pause again.

## Rules

- Use this skill only when the user explicitly asks for a PRD. Mentioning an idea, feature, or bug is not enough.
- The PRD is ready when: intent and the user's problem are clear, scope and out-of-scope are explicit, and acceptance criteria are concrete and checkable.
- Write for a human who reads this later without the conversation context.
- If two product behaviors would differ, call out the decision or ask before writing.
- If the PRD is getting long, narrow or split the work instead of growing the document.
