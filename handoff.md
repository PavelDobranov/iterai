# Handoff — Iterai workflow repo

## Context

User owns a workflow repo describing **Iterai**, a manual AI-assisted development workflow. Repo is intended to be self-contained (docs + skills); user does **not** want to depend on global skills in `~/.pi/agent/skills/`.

This session focused on **documents only**. Skills are next.

## Current state of the repo

```
workflow/
├── README.md       # 27 lines — landing page
├── ITERAI.md       # 592 lines — full spec (single source of truth)
└── skills/
    ├── grill-me/SKILL.md
    ├── prd/SKILL.md
    └── plan/SKILL.md
```

Read `ITERAI.md` first. Its outline:

1. Flow
2. Universal Patterns (review cycle, approval gates, universal stop conditions, scope discipline)
3. Artifacts (layout, source of truth)
4. Templates (`prd.md`, `plan.md`, GitHub Issue, PR description)
5. Phase 1 — Planning
6. Phase 2 — Implementation
7. Phase 3 — Implementation → PR
8. Phase 4 — Review and Merge
9. Open Questions

All cross-references are anchor links within the same file.

## What was done this session

1. Reviewed original 6 markdown docs + 3 skills. Identified problems: duplication across files, dangling `[[AI Flow PRD]]` wikilinks, naming inconsistency ("AI Flow" vs "Iterai"), missing skills for half the workflow, overlap with global skills.
2. **First pass** (later superseded): renamed `AI Flow *` → `Iterai - *`, slimmed Overview, added README + Open Questions doc, deduped templates. Resulted in 8 files / 767 lines.
3. **Second pass (final)**: combined all 7 Iterai docs into a single `ITERAI.md` (592 lines) + tiny `README.md` (27 lines). Deleted the 7 intermediate files. No content loss; just deduplication of universal patterns, paths, templates.

Old filenames (now deleted, do not look for them):
`AI Flow Overview.md`, `AI Flow Artifact Templates.md`, `AI Flow Phase - {Planning,Implementation,PR,Review and Merge}.md`, and their intermediate `Iterai - *.md` renames, plus `Iterai - Open Questions.md`.

## What's next: skills

User confirmed direction in this session but did NOT execute it yet. The plan:

- Treat `ITERAI.md` as source of truth; skills are derived artifacts that mirror it.
- Repo is named Iterai, so skills do **not** need an `iterai-` prefix.
- Proposed skill set (one per workflow artifact):

  ```
  skills/
    grill-me/     # pre-PRD interview (canonical Iterai version, replace existing)
    prd/          # Phase 1 — writes prd.md (refresh existing)
    plan/         # Phase 1 — writes plan.md (refresh existing)
    issues/       # Phase 1 — drafts GitHub issue breakdown (new)
    implement/    # Phase 2 — executes one approved issue, embeds TDD (new)
    pr/           # Phase 3 — drafts PR body (new)
    review/       # Phase 4 — handles review feedback + merge readiness (new)
  ```

- Skill design rules (agreed):
  - Top of file: `Source: ITERAI.md §N` pointer
  - Templates inlined (skills must work standalone), marked `<!-- mirror of ITERAI.md §4.x — keep in sync -->`
  - Hard-coded approval gate pause (read-back + explicit confirmation), consistent with `ITERAI.md §2.2`
  - No dependency on global skills (`~/.pi/agent/skills/`)
  - TDD rules live inside `implement`, not a separate skill

### Open questions for the user before executing the skills pass

1. Confirm the 7 skills above are the right scope (vs. folding `grill-me` into `prd`, or splitting `implement`).
2. Confirm `user-invocable: true` with argument-hints on all 7 (default yes).
3. For `implement` / `pr` / `review` — should they accept a GitHub issue # / PR URL as argument, or work from local context only?

## Existing skills — current state

- `skills/prd/SKILL.md` — interviews user, writes `.agents/iterai/iterations/<>/prd.md`. Inlines the PRD template. Pauses at approval gate. Needs refresh to point at `ITERAI.md` and trim inlined template to a mirror with sync marker.
- `skills/plan/SKILL.md` — same pattern for `plan.md`. Same refresh needed.
- `skills/grill-me/SKILL.md` — ~5 lines, generic interview prompt. Decide whether to keep as-is or make it Iterai-aware (mention "before PRD").

## Suggested skills for the next session

- **grill-me** — if the next agent wants to stress-test the skill design before executing, use the local `skills/grill-me/SKILL.md` (per user's "no global skills" stance, prefer the local one if it suits).
- No other skills should be invoked. The next session is editing files in this repo, not running a higher-level workflow.

## Pointers

- Spec: `ITERAI.md`
- Landing: `README.md`
- Skills dir: `skills/`

## Constraints to respect

- **Self-contained repo.** Do not introduce dependencies on `~/.pi/agent/skills/` or any global skill.
- **Docs are the source of truth.** Skills mirror them, not vice versa. If a skill needs new behavior, propose updating `ITERAI.md` first.
- **Minimal scope per turn.** Apply the workflow's own scope discipline to changes here: one cohesive change per turn, propose before executing big restructurings.
