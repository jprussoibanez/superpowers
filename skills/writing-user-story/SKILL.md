---
name: writing-user-story
description: Use when you need a precise, testable user story for a feature, bugfix, or change that will be handed to implementation planners and engineers
---

# Writing User Stories

## Overview

A user story is a structured specification that defines **what** to build and **why**, with enough precision that an engineer can implement it and a reviewer can verify it — without ambiguity about scope or acceptance.

**Template:** `user_story_template.md`

---

## When to Use

- Starting a new feature, bugfix, or behavior change
- Handing requirements to implementation planners or engineers
- Clarifying scope before writing a plan or ADR
- When "we need X" is too vague to implement unambiguously

**Not for:** Internal refactors with no behavior change, or when a one-line task description is sufficient.

---

## Story Structure

| Section | Purpose |
|---|---|
| **Context** | The problem, who is affected, current state |
| **Goals** | Outcomes this delivers (not implementation steps) |
| **Functional Requirements** | Testable behaviors with examples |
| **Non-Functional Requirements** | Quality attributes (performance, security, etc.) |
| **Implementation Tasks** | Concrete subtasks broken from requirements |
| **Deliverables** | Explicit artifacts produced |
| **Out of Scope** | Explicit exclusions to prevent scope creep |

---

## Good vs Bad Examples

### Context

❌ **Bad — vague problem, no stakeholder:**
```
We want to add MDE estimation to the calculator.
```

✅ **Good — specific problem, stakeholder, current state:**
```
Currently, the sample size calculator only supports one direction: given a
target MDE, calculate required sample size. Experiment planners often face
the opposite scenario: they have a fixed sample size and need to know what
effect size they can reliably detect.

This bidirectional calculation is essential for:
- **Feasibility assessment**: "I can only run 10K users, what can I detect?"
- **Trade-off exploration**: "If I reduce my target from 5% to 3%, how much
  more sample do I need?"
```

---

### Goals

❌ **Bad — implementation-focused, not outcome-focused:**
```
1. Add a PairwiseMDEEstimation class
2. Refactor the result type
3. Write tests
```

✅ **Good — outcome-focused, traceable to requirements:**
```
1. Enable Sample Size → MDE calculation for single-metric experiments
2. Create unified result interface for both calculation directions
3. Support round-trip validation (MDE → Sample → MDE should be consistent)
```

---

### Functional Requirements

❌ **Bad — untestable, no sub-requirements:**
```
1. **MDE Estimation**
   The system should calculate MDE from sample size.
```

✅ **Good — testable, hierarchical, with examples:**
```
1. **PairwiseMDEEstimation Class**
   - Accepts same configuration as `PairwiseSampleSizeEstimation` except
     uses `sample_size` instead of `minimum_detectable_effect`
   - Supports all multiple testing correction methods (Bonferroni, Šidák,
     Tukey-Kramer)
   - Returns `PairwiseEstimationResult` with calculated achievable MDE

4. **Round-Trip Consistency**
   - Sample Size → MDE → Sample Size should return approximately the same value
   - MDE → Sample Size → MDE should return approximately the same value
   - Tolerance defined and documented for floating-point comparisons
```

---

### Non-Functional Requirements

❌ **Bad — generic, not linked to quality attributes:**
```
- It should be fast and not break existing things.
```

✅ **Good — grouped by quality attribute, specific:**
```
1. **Calculation Accuracy**
   - MDE calculations match statistical theory
   - Results consistent with existing sample size calculations (inverse relationship)
   - Validated against known test cases

3. **API Consistency**
   - Constructor signature mirrors `PairwiseSampleSizeEstimation`
   - Same parameter names, types, and defaults (except sample_size vs minimum_detectable_effect)
   - Same validation patterns and error messages
```

Choose from: Performance, Scalability, Security, Reliability, Maintainability, Usability, Accessibility, Testability, Portability, Observability, Compliance.

---

### Out of Scope

❌ **Bad — missing entirely or too vague:**
```
We're not doing everything at once.
```

✅ **Good — explicit boundaries that prevent scope creep:**
```
- Multi-metric MDE estimation (Story 2)
- Performance optimization
- Graphical power analysis
- Confidence intervals for MDE estimates
```

---

## Common Mistakes

| Mistake | Fix |
|---|---|
| Goals describe implementation steps | Goals = outcomes delivered, not tasks executed |
| Acceptance criteria without sub-requirements | Break each requirement into verifiable sub-items |
| Missing Out of Scope | Always list explicit exclusions, even obvious ones |
| NFRs as generic quality words | Group under named quality attributes with specific criteria |
| Tasks that map 1:1 to requirements | Tasks are implementation breakdowns; they can span or split requirements |
| No examples in requirements | Add concrete examples under complex sub-requirements |

---

## File Naming

Save stories alongside `docs/superpowers/user-stories/YYYY-MM-DD-<feature-name>.md`

## Self-Review

After creating a user story, use this prompt to self review using the same criteria as the [User Story Document Reviewer Prompt](user-story-document-reviewer-prompt.md).

## Integration

**Required workflow skills:**
- **skills/writing-plans** - Creates the plan that this skill uses to create the user story for
- **skills/brainstorming** - Creates the spec that this skill uses to create the user story for
