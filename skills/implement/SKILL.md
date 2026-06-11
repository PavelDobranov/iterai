---
name: implement
description: "Build one approved GitHub issue on one branch, test-first: confirm scope, write the failing test, make it pass, run the suite, and stop before the PR. Use when the user says \"implement\", \"build this issue\", \"start issue #N\", or invokes implement with an issue reference."
user-invocable: true
argument-hint: "<issue number, issue URL, or slice title>"
---

# Implement

You build exactly one approved issue on one branch. Test-first: the failing test comes before the implementation, and the test passing is the proof the work is done. Do not expand the plan.

This skill ends with a working, tested change on a branch. Do not open a PR — that is the Ship phase and needs explicit approval.

## Workflow

### 1. Pick and confirm

- Use the given issue. If none is given and exactly one approved issue is unstarted, propose it; otherwise ask.
- Fetch the issue with `gh issue view` and read its Goal, Scope, Acceptance Criteria, and Verification. Read the linked plan's Design section and the relevant code.
- Confirm the scope back to the human in 2–3 lines and wait for a clear "yes" before creating a branch.
- Stop and ask if acceptance criteria are missing or cannot be checked, the issue is bigger than one PR, or the work no longer matches the approved plan.

### 2. Branch

- Create one branch for this issue from the default branch (e.g. `issue-<number>-<slug>`, or follow the project's convention).
- One issue = one branch = one PR. Never mix issues on a branch.

### 3. Red

- Write the smallest failing test that proves an acceptance criterion (for bugfixes: a test that reproduces the bug).
- Run it and show the failure. Confirm it fails for the expected reason.
- For docs or chores where tests do not apply, skip Red/Green and state how the work will be verified instead.

### 4. Green

- Write the minimal change that makes the test pass. Respect the interfaces in the plan's Design; widening an interface is a plan change — stop and ask.
- Run the test and show it passing.
- Repeat Red → Green per acceptance criterion until all are covered.

### 5. Verify

- Simplify code and tests while they stay green.
- Run the relevant test suite and the issue's Verification steps. If verification fails and the fix is not obvious, stop and ask.

### 6. Report and stop

Summarize: what changed, the failing-then-passing evidence, suite results, and acceptance criteria status. Call out anything you could not verify. Then stop:

```text
Issue #<number> implemented on branch <name>. All acceptance criteria verified.
Reply "ship" to open the PR, or leave feedback.
```

## Rules

- Stay inside the issue scope. New discoveries become follow-up issues — propose them, do not quietly include the work.
- No new dependencies, big refactors, or deletions without approval.
- Do not write implementation code before a failing test for the behavior.
- Tests describe behavior, not implementation details. Prefer real boundaries over mocks when practical.
- Commit in small steps that each run; write messages that reference the issue.
- If an assumption is low-risk, state it and keep moving. Otherwise stop and ask.
- Do not hide what you couldn't verify.
