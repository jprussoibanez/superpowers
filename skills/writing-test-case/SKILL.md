---
name: writing-test-case
description: Use when you need to write a manual acceptance test case document for a feature that has been implemented, to verify it works correctly in a staging or production environment before sign-off
---

# Writing Test Cases

## Overview

A test case document maps acceptance criteria from a user story to concrete, manually executable verification steps in a real environment. It tracks pass/fail status with checkboxes, links to actual experiments or data, and captures bugs found during testing.

**Template:** `test_case_template.md`  
**Reviewer prompt:** `test-case-document-reviewer-prompt.md`

---

## When to Use

- Implementation is complete and deployed to a test environment
- Need to verify a feature end-to-end in a real (not mocked) environment
- Coordinating QA across team members with a shared tracking artifact
- Signing off on acceptance before production release

**Not for:** Automated unit/integration tests (use `writing-user-story` + `test-driven-development`). Not for planning what to build.

---

## Document Structure

| Section | Purpose |
|---|---|
| **Test Experiments** | Links to real experiments/environments used for testing |
| **Setup / Prerequisites** | Configurations or test fixtures needed before testing begins |
| **Acceptance Criteria** | Dimensions to verify, mirroring the user story requirements |
| **Test Cases** | Numbered scenario groups with checkbox-tracked individual tests |
| **Strategy Notes** | Coverage decisions and explicit exclusions |
| **Improvements Found** | Bugs and issues discovered during testing |

---

## Test Case Anatomy

```markdown
- [X] <Scenario>: [<Experiment Name>](<url>) (<Variant, label, or config specifics>)

  <Expected result — screenshot, JSON snippet, or description>
```

- `[X]` = passed, `[ ]` = pending or failed
- Always link to the specific experiment, dataset, or environment
- Include variant/label/config so another person can reproduce it
- Attach screenshots or example data for visual or complex assertions

---

## Mapping User Story → Test Cases

Each **Functional Requirement** in the user story becomes one numbered section. Sub-requirements become individual test cases. Group by functional area, not by experiment.

| User Story | Test Case Section |
|---|---|
| FR1: Stage 0 object structure | 1. Stage 0 in GST study report |
| FR5: No-data edge case | N. Edge cases |
| NFR: All existing tests pass | Covered by automated tests, not this doc |

---

## Common Mistakes

| Mistake | Fix |
|---|---|
| Vague scenario ("it works") | Be specific: experiment link, variant, label, expected value |
| Missing prerequisites | List every config needed before tests run |
| No expected result documented | Always show what "correct" looks like |
| Open `[ ]` boxes left silent | Either fix the issue or note it in Improvements |
| Improvements buried in prose | Capture every bug in the Improvements section with an issue link |

---

## Self-Review

After creating a test case document, use this prompt to self review using the same criteria as the [Test Case Document Reviewer Prompt](test-case-document-reviewer-prompt.md).

## File Naming

Save alongside `docs/superpowers/test-cases/YYYY-MM-DD-<feature-name>.md`

Example: `2026-05-14-feature-name-test-case.md`

## Integration

**Required workflow skills:**
- **writing-user-story** — defines the acceptance criteria this document verifies
- **writing-plans** — creates the implementation plan whose deliverables this document tests
