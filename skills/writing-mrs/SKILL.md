---
name: writing-mrs
description: Use when implementation is complete on a feature branch and a merge request needs to be opened, to generate a structured MR description from the implementation plan and git history
---

# Writing Merge Requests

## Overview

Generate a complete, reviewer-ready GitLab MR description from the implementation plan that drove the work. The plan is the source of truth for *what* was supposed to happen; `git log` confirms *what actually happened*. The MR bridges both.

**Announce at start:** "I'm using the writing-mrs skill to generate the MR description."

**Template:** `mr_template.md`

**Save MR drafts to:** `docs/superpowers/mrs/YYYY-MM-DD-<feature-name>.md`

---

## When to Use

- Implementation is done: all plan tasks are checked off
- Feature is ready for review and merge on a feature branch
- After `superpowers:executing-plans` or `superpowers:subagent-driven-development` completes

**Not for:** Work-in-progress drafts where tasks are still pending.

---

## Inputs

| Input               | How to provide                                  | Required? |
| ------------------- | ----------------------------------------------- | --------- |
| Implementation plan | File path or `docs/superpowers/plans/` filename | Required  |
| User story          | File path — auto-detected from plan if present  | Optional  |
| Target branch       | Default: `feature branch`                       | Optional  |

If no plan path is provided, ask the user before proceeding.

---

## Generation Steps

### 1. Read the plan

Extract from the plan file:
- **Goal** → `## Summary` section
- **Architecture** + **Tech Stack** → `## Implementation Notes`
- **File Map** table → cross-check against actual git diff
- **Task list** (all `- [x]` items) → `## What Changed` bullets

Flag any tasks still marked `- [ ]` and warn the user before continuing.

### 2. Gather git evidence

From the plan's File Map, identify the repo path and feature branch to run the git command on <repo-root-directory>.

IMPORTANT: Always run diff against the develop branch to ensure the changes align with the plan.

```bash
# Commits on this branch
cd <repo-root-directory> && git log --oneline develop...HEAD

# Files actually changed
cd <repo-root-directory> && git diff --stat develop...HEAD
```

If the files in `git diff` diverge meaningfully from the plan's File Map, note the divergence in `## Implementation Notes`.

### 3. Detect related artefacts

Check the plan's header for links to:
- User story (`docs/superpowers/user-stories/`)
- ADR (`docs/superpowers/adrs/`)
- Spec (`docs/superpowers/specs/`)

If found, read the user story's **Goals** section to enrich `## Summary` with motivation.

### 4. Fill the template

Fill every section of `mr_template.md`. No placeholder text — every section must contain real content derived from the plan and git evidence.

**`## Summary`** — one paragraph, 2–3 sentences:
- Sentence 1: what the MR delivers (from plan Goal)
- Sentence 2: why it matters (from user story Goals, or plan Architecture)
- Sentence 3: scope / what it does NOT do (if useful)

**`## What Changed`** — one bullet per completed plan task, rephrased as an observable behaviour change, not a code action:
- ❌ "Added `OutcomeTest` Pydantic model"
- ✅ "YAML configs now support optional experiment-level `alpha`/`beta` parameters"

**`## Implementation Notes`** — include only non-obvious decisions or deviations. If none, write `_None_`.

**`## Testing → Manual Verification`** — use the exact `pytest` command from the plan's test steps.

**`## Reviewer Focus Areas`** — identify 1–3 places in the diff where a reviewer should spend extra time: statistical correctness, backwards-compatibility, complex branching logic.

### 5. Self-review

Before saving, check:
1. **No placeholders** — every bracket `[...]` replaced with real content
2. **Checklist completeness** — author checklist accurately reflects the work done
3. **Artefact links** — all file paths in the Context table exist
4. **Summary accuracy** — the summary matches the plan Goal exactly

### 6. Save and hand off

Save the MR description to `docs/superpowers/mrs/YYYY-MM-DD-<feature-name>.md`.

---

## MR Title Format

```
<type>(<scope>): <short description>
```

| Type       | When                                  |
| ---------- | ------------------------------------- |
| `feat`     | New feature or behaviour              |
| `fix`      | Bug fix                               |
| `refactor` | Refactoring without behaviour change  |
| `test`     | Test additions or fixes only          |
| `docs`     | Documentation only                    |
| `chore`    | Build, CI, config, dependency updates |

Example: `feat(register): add experiment-level alpha/beta YAML parameters`

---

## Common Mistakes

| Mistake                                                | Fix                                                       |
| ------------------------------------------------------ | --------------------------------------------------------- |
| Summary describes code changes, not behaviour          | Write what the user/system gains, not what was written    |
| `## What Changed` mirrors the plan task names verbatim | Rephrase as observable outcomes                           |
| Reviewer Focus Areas left empty or generic             | Pick 1–3 specific diff locations that need careful review |
| Testing section has no exact commands                  | Copy the `pytest` command verbatim from the plan          |
| Plan tasks still unchecked when MR is opened           | Warn the user and block generation until resolved         |
| Context links point to non-existent files              | Verify each path with `ls` before saving                  |

---

## Integration

**Upstream skills (inputs):**
- **superpowers:writing-plans** — the plan this skill reads
- **superpowers:subagent-driven-development** — marks tasks complete before this skill runs
- **superpowers:executing-plans** — alternative execution path
- **superpowers:code-reviewer** — reviews the Python code for correctness, style, and adherence to project conventions before the MR is merged
