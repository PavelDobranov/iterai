---
name: ship
description: "Take one implemented branch through PR, review, and merge: write the PR description, open the PR with approval, handle review verdicts, run the pre-merge checklist, merge, and close the issue. Use when the user says \"ship\", \"open the PR\", \"merge this\", or approves shipping after implement."
user-invocable: true
argument-hint: "<issue number, branch name, or nothing if on the implemented branch>"
---

# Ship

You take one implemented branch to a merged PR and a closed issue. The human decides at every gate: opening the PR, the review verdict, and the merge.

This skill assumes the work is already implemented and verified on a branch. If it is not, stop and point to `implement`.

## Workflow

### 1. Confirm the branch

- Use the current branch, or the one matching the given issue. Check `git status` is clean and the branch is pushed up to date (push it if not).
- Confirm the branch matches exactly one approved issue. If it contains unrelated work or spans issues, stop and ask.
- Re-check the issue's acceptance criteria against the branch. If any are unmet or verification evidence is stale, stop and report — do not open a PR on hope.

### 2. Write the PR description

Fill the template below from the issue, the branch's commits, and the verification evidence from implementation. Do not invent content; if evidence is missing, go back and produce it.

#### Template

<!-- mirror of README.md §4.4 — keep in sync -->

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

Show the description to the human and wait for a clear "yes" before opening the PR.

### 3. Open the PR

- Open it with `gh pr create` using the approved description. One issue = one branch = one PR.
- Print the PR link and stop. The human reviews.

### 4. Handle the verdict

- **Approve** → go to step 5.
- **Request changes** → update the same branch, re-run the relevant tests and the issue's verification, summarize what changed, and ask for re-review. Stay inside the issue scope; new discoveries become follow-up issues.
- **Reject** → stop, summarize why in 2–3 lines, and ask whether to go back to the issue or the plan. Do not delete the branch without approval.

### 5. Merge and close

Before merging, check:

- the issue is linked and its acceptance criteria are met
- verification evidence is current
- all review comments are resolved
- follow-up work is captured as issues

Merge only with explicit approval. Then confirm the issue is closed (the `Closes #` link should do it), create any agreed follow-up issues, and report: PR link, merge state, issue state, follow-ups.

## Rules

- Never open a PR, merge, or delete a branch without explicit approval.
- One issue = one branch = one PR. No drive-by changes during review fixes.
- The PR body comes from real evidence: actual commands run, actual failing-then-passing output. Never fabricate verification.
- Review feedback that grows the scope is a plan question — propose a follow-up issue instead of absorbing it.
- If CI fails, fix what the change caused; report unrelated failures instead of chasing them.
- If `gh` is not authenticated or there is no GitHub remote, stop and report instead of improvising.
