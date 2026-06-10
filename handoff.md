# Handoff — Iterai workflow repo

## Context

User owns a workflow repo describing **Iterai**, a manual AI-assisted development workflow. Repo is self-contained (docs + skills); user does **not** want to depend on global skills in `~/.pi/agent/skills/`.

This session injected ideas from **John Ousterhout's _A Philosophy of Software Design_** into the **planning phase** of Iterai. The goal is not to redesign Iterai itself, but to make Iterai produce projects (PRDs → plans → issues → code) that follow Ousterhout's design discipline.

## Repo layout

```
workflow/
├── README.md
├── ITERAI.md               # spec, source of truth
├── handoff.md              # this file
└── skills/
    ├── grill-me/SKILL.md
    ├── prd/SKILL.md
    └── plan/SKILL.md
```

Read `ITERAI.md` first. Skills mirror its templates with `<!-- mirror of ITERAI.md §N — keep in sync -->` markers.

## What was done this session

The framing: **agents are tactical by default** (make the test pass). The workflow's job is to **inject strategic design moments at the gates** where the human is already paying attention, and bake red-flag checks into the artifacts the agent has to produce anyway.

Five-step plan was agreed:

1. **PRD** — frame in interface terms, not implementation.
2. **Plan** — force a design pass before slicing.
3. **Issue** — carry the design contract into implementation.
4. **Implement / PR / Review** — design-first comments, red-flag self-check, design review section, design-debt capture.
5. **Project-level `.agents/iterai/design.md`** — living design ledger across iterations.

**Completed: steps 1–3 (the planning phase).** Steps 4–5 are deferred.

### Concrete changes shipped

**`ITERAI.md §4.1` + `skills/prd/SKILL.md` (PRD)**

- Acceptance Criteria must describe what an observer sees, not implementation details. If an AC names a class, file, or function, rewrite it.
- Two new optional sections in the PRD template:
  - `## External Surface` — the contract a user/caller/operator sees, not the mechanism. Forces outside-in framing.
  - `## Errors Designed Out` — failure modes the design makes impossible (vs. handles). Ousterhout's "define errors out of existence".

**`ITERAI.md §4.2` + `skills/plan/SKILL.md` (plan)**

- New required `## Design` section between `Approach` and `Affected Files / Areas`:
  - `### Modules` — each module touched (new/modified/removed): interface, what it hides, depth (deep/balanced/shallow + why).
  - `### Considered Alternatives` — at least one rejected decomposition. "Design it twice."
  - `### Red Flags Checked` — shallow modules, pass-throughs, info leakage, temporal decomposition, special-cases, repetition, layer-leaking abstractions. Each must be justified or fixed.
  - `### Complexity Direction` — adds / neutral / reduces. If adds, justify and capture debt.
- Slices must trace to a module from Design (`Touches:` field on each slice).
- Interface widening beyond Design is an explicit design decision, not a coding choice — revise Design first.
- Trivial-iteration escape hatch: `Design: N/A — <reason>`. Use sparingly; not twice in a row.
- Skill's workflow step "Design before slicing" inserted before slicing.
- Approval-gate summary must include modules + complexity direction.

**`ITERAI.md §4.3` (GitHub Issue template)**

- New required `## Contract` section:
  - Module(s) touched (traceable to `plan.md § Design`).
  - Interface after the change.
  - Information hidden inside.
  - Out of contract: what callers must NOT rely on.
  - Widening allowed? Default no.
- Same observable-AC rule as PRD.
- Same `Contract: N/A — <reason>` shorthand for trivial issues.
- Issue breakdown readiness checklist now requires each Contract to trace back to plan's Design.

### Defaults chosen on open questions

- Trivial slices: allow `Design: N/A — <reason>` shorthand, with friction (warn if used twice in a row).
- Rejected alternatives: required for every iteration, not only "adds-complexity" ones.
- `design.md` (step 5): soft `if exists, read it first` reference in plan skill — harmless until step 5 ships.

## What's next: deferred work

In priority order:

### Step 3 (next session): `implement` skill

Per original handoff this skill is unwritten. Open questions to resolve before writing:

1. **Argument shape.** Accept a GitHub issue number / URL, or local context only? Suggested default: **accept issue # or URL**, fall back to local context. Skill can `gh issue view N` to fetch the Contract.
2. **TDD ladder.** Keep §6.5's feature/bugfix/refactor ladders but prepend a step: **"write the interface comment (what this hides, what callers see) before the failing test"**. For refactor/docs: "state the invariant being preserved".
3. **Red-flag self-check** before declaring done. Checklist:
   - No new shallow modules.
   - No pass-through methods.
   - No information leaks (caller sees internal types/state).
   - No temporal decomposition disguised as modules.
   - No special-case branches that forced callers to know module internals.
   - No new repetition (or justified).
   - Code "looks like it was always there".
   - Public names create accurate expectations.
4. **When a flag fires.** Suggested default: **(a) stop and ask the human.** Not (b) auto-fix or (c) surface in PR body.
5. **Scope of self-check.** Suggested default: **diff only**, agent free to widen if it sees something suspicious nearby.
6. **Wider-than-Contract change** is a stop condition: propose tighten, expand contract (new approval), or split issue.

### Step 4: `pr` skill

Add `## Design Review` section to PR template, distinct from verification:

- Interface changes (added/changed/removed).
- What is now hidden.
- Depth check on new/modified modules.
- Red flags considered (yes/no + why).
- Strategic vs Tactical: name the kind of PR. If tactical, link design-debt follow-up.

### Step 4: `review` / merge skill

- Required reviewer prompt: "What did this PR make easier or harder to change next? Name one thing." (change-amplification heuristic)
- Mandatory follow-up scan before merge — create `design-debt` labeled issues.

### Step 5: `.agents/iterai/design.md` (project-level design ledger)

Lives forever per repo, alongside `iterations/`. Contents:

- Module Catalog (table).
- Key Invariants (must remain true across codebase).
- Standing Decisions ("we do X this way because…").
- Design Debt (what / where / why deferred / trigger).
- Glossary (names + meanings; promotes reuse over reinvention).

Wiring:

- `plan` reads it first; Design section is written against it; on merge, new modules append.
- `implement` consults for naming and invariants.
- `pr` updates it.
- `review` checks it was updated when it should have been.

Do step 5 last; let steps 3–4 run on real iterations first to see what naturally accumulates.

## Constraints to respect

- **Self-contained repo.** No dependencies on `~/.pi/agent/skills/`.
- **Docs are source of truth.** Skills mirror `ITERAI.md` with sync markers, never the other way around. If a skill needs new behavior, propose updating `ITERAI.md` first.
- **Minimal scope per turn.** Apply the workflow's own scope discipline.
- **No silent gate crossing.** Skills pause for explicit human approval per `ITERAI.md §2.2`.

## Pointers

- Spec: `ITERAI.md`
- Skills dir: `skills/`
- Landing: `README.md`
