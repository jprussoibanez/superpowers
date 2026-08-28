# MR Template

Follow this MR description template format and sections.

````markdown
## Summary

<!-- 2–3 sentences: what this MR builds or fixes, and why it matters now. -->

[What was built or fixed, in plain language.]

## Context

<!-- Links to the artefacts that motivated and shaped this work. -->

| Artefact | Link |
|---|---|
| User Story | <Ask for JIRA link if available> |
| ADR | <Ask for ADR link if available> |

## What Changed

<!-- High-level bullets — one per logical change. Mirror the plan's task list.
     Focus on behaviour, not file names. -->

- [Change 1: what it does for the user/system]
- [Change 2]
- [Change 3]

## Implementation Notes

<!-- Deviations from the plan, non-obvious decisions made during implementation,
     or anything a reviewer needs to understand that isn't visible in the diff. -->

_None_ <!-- or describe notable decisions -->

## Testing

### Automated

Run the full test suite for the affected module:

```bash
pytest <path/to/tests/> -v
```

Expected: all tests pass.

### Manual Verification

<!-- Steps a reviewer can follow to verify the feature end-to-end.
     Be specific: exact commands, expected outputs, URLs, or screenshots. -->

1. [Step 1]
2. [Step 2]
3. [Step 3 — expected result]

## Author Checklist

- [ ] Ruff linting passes locally
- [ ] All automated tests relevant to features pass locally
- [ ] Relevant documentation updated (if applicable) with docstrings and type hints.

## Reviewer Focus Areas

<!-- Flag anything that deserves extra scrutiny: complex logic, statistical
     correctness, backwards-compatibility risk, performance implications. -->

- [Area 1 — why it deserves attention]
- [Area 2]
````
