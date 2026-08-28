---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code
---

# Writing Plans

## Overview

Write comprehensive implementation plans assuming the engineer has zero context for our codebase and questionable taste. Document everything they need to know: which files to touch for each task, code, testing, docs they might need to check, how to test it. Give them the whole plan as bite-sized tasks. DRY. YAGNI. TDD. NO commits.

Assume they are a skilled developer, but know almost nothing about our toolset or problem domain. Assume they don't know good test design very well.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

**Context:** This should be run on the feature branch provided by the user. If no feature branch is provided, ask the user for one. DO NOT create a branch yourself.

**Save plans to:** `docs/superpowers/plans/YYYY-MM-DD-<feature-name>.md`
- (User preferences for plan location override this default)

## Scope Check

If the spec covers multiple independent subsystems, it should have been broken into sub-project specs during brainstorming. If it wasn't, suggest breaking this into separate plans — one per subsystem. Each plan should produce working, testable software on its own.

## File Structure

Before defining tasks, map out which files will be created or modified and what each one is responsible for. This is where decomposition decisions get locked in.

- Design units with clear boundaries and well-defined interfaces. Each file should have one clear responsibility.
- You reason best about code you can hold in context at once, and your edits are more reliable when files are focused. Prefer smaller, focused files over large ones that do too much.
- Files that change together should live together. Split by responsibility, not by technical layer.
- In existing codebases, follow established patterns. If the codebase uses large files, don't unilaterally restructure - but if a file you're modifying has grown unwieldy, including a split in the plan is reasonable.

This structure informs the task decomposition. Each task should produce self-contained changes that make sense independently.

## Task Right-Sizing

A task is the smallest unit that carries its own test cycle and is worth a
fresh reviewer's gate. When drawing task boundaries: fold setup,
configuration, scaffolding, and documentation steps into the task whose
deliverable needs them; split only where a reviewer could meaningfully
reject one task while approving its neighbor. Each task ends with an
independently testable deliverable.

## Bite-Sized Task Granularity

**Each step is one action (2-5 minutes):**
- "Write the failing test" - step
- "Run it to make sure it fails" - step
- "Implement the minimal code to make the test pass" - step
- "Run the tests and make sure they pass" - step

## Plan Document Header

**Every plan MUST start with this header:**

```markdown
# [Feature Name] Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

**Spec:** [path to the spec/design doc this plan implements — the plan
argues from the spec, so the spec travels with it; executors read both]

## Global Constraints

[The spec's project-wide requirements — version floors, dependency limits,
naming and copy rules, platform requirements — one line each, with exact
values copied verbatim from the spec. Every task's requirements implicitly
include this section.]

**If C4 diagrams are warranted (see C4 Diagram Assessment section), insert this section immediately after the `---` divider:**

```markdown
# Architecture Overview

[Brief description of the architecture and the diagrams below]

## Architecture Diagrams

[diagrams from superpowers:drawing-c4-diagrams]

---
```

## Task Structure

````markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Interfaces:**
- Consumes: [what this task uses from earlier tasks — exact signatures]
- Produces: [what later tasks rely on — exact function names, parameter
  and return types. A task's implementer sees only their own task; this
  block is how they learn the names and types neighboring tasks use.]

- [ ] **Step 1: Write the failing test**

MUST follow the test implementation best practices defined in [best_practices.md](best_practices.md#test-implementation-best-practices).

```python
class TestSpecificBehavior:
    @staticmethod
    def arrange_fixture(context: InputType) -> FixtureType:
        """Arrange a test fixture from context.
        
        Parameters
        ----------
        context : InputType
            The context to use for the test.
        
        Returns
        -------
        FixtureType
            The test fixture.
        """
        return fixture

    @staticmethod
    def assert_result_equals_expected(result: ResultType, expected: ResultType) -> None:
        """Assert result matches the expected value.
        
        Parameters
        ----------
        result : ResultType
            The result to check.
        expected : ResultType
            The expected result.
        """
        assert result == expected, f"Expected {expected}, got {result}"

    def test_specific_behavior(self) -> None:
        """Test the specific behavior of the function.

        Given <context>
        When <action>
        Then <expected result>
        """
        # Arrange
        fixture = self.arrange_fixture(context)

        # Act
        result = function(fixture)

        # Assert
        self.assert_result_equals_expected(result, expected)
```

- [ ] **Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

- [ ] **Step 3: Write minimal implementation**

MUST follow the task implementation best practices defined in [best_practices.md](best_practices.md#task-implementation-best-practices).

```python
def function(input: InputType) -> OutputType:
    """
    Function description

    Examples
    --------
    >>> function(input)
    expected

    Parameters
    ----------
    input : InputType
        Description

    Returns
    -------
    OutputType
        Description

    Raises
    ------
    ExceptionType
        Description
    """
    return expected
```

- [ ] **Step 4: Run test to verify it passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

````

## No Placeholders

Every step must contain the actual content an engineer needs. These are **plan failures** — never write them:
- "TBD", "TODO", "implement later", "fill in details"
- "Add appropriate error handling" / "add validation" / "handle edge cases"
- "Write tests for the above" (without actual test code)
- "Similar to Task N" (repeat the code — the engineer may be reading tasks out of order)
- Steps that describe what to do without showing how (code blocks required for code steps)
- References to types, functions, or methods not defined in any task

## Remember
- Exact file paths always
- Complete code in every step — if a step changes code, show the code
- Exact commands with expected output
- DRY, YAGNI, TDD
- Provide examples to docstrings that demonstrate usage when it helps.
- Do not add any code comments in the code (extract it to docstrings if needed).
- DO NOT commit any changes with git. The user will commit changes after reviewing the plan.

## Self-Review

After writing the complete plan, look at the spec with fresh eyes and check the plan against it. This is a checklist you run yourself — not a subagent dispatch.

**1. Spec coverage:** Skim each section/requirement in the spec. Can you point to a task that implements it? List any gaps.

**2. Placeholder scan:** Search your plan for red flags — any of the patterns from the "No Placeholders" section above. Fix them.

**3. Type consistency:** Do the types, method signatures, and property names you used in later tasks match what you defined in earlier tasks? A function called `clearLayers()` in Task 3 but `clearFullLayers()` in Task 7 is a bug.

If you find issues, fix them inline. No need to re-review — just fix and move on. If you find a spec requirement with no task, add the task.

## C4 Diagram Assessment

After completing the self-review, decide whether C4 architecture diagrams add value for this plan.

**Generate diagrams when:**
- 3+ files with non-trivial interdependencies
- New class hierarchy, service boundary, or module interface introduced
- Database, external API, or messaging system involved
- Runtime flow involves 3 or more distinct participants

**Skip diagrams when:**
- Plan touches 1–2 files with straightforward changes
- No new structural relationships are introduced
- Diagrams were already generated at the brainstorming/spec stage and the architecture is unchanged

**If warranted:** **REQUIRED SUB-SKILL:** Invoke `superpowers:drawing-c4-diagrams`. Pass the completed plan content. The skill generates Container + Component + Sequence diagrams and returns an `## Architecture Diagrams` block. Insert it into the `# Architecture Overview` section of the plan (immediately after the header `---` divider).

If not warranted, proceed silently to Execution Handoff.

## Execution Handoff

After saving the plan, offer the following choices to the user in order:

### User story creation

Ask the user if they want a user story written for the plan. If the user says yes, then invoke the `skills/writing-user-story` passing the plan file as input.

### Execution method

After the user story is written (or skipped), offer the user the choice of execution method.

**"Plan complete and saved to `docs/superpowers/plans/<filename>.md`. Two execution options:**

**1. Subagent-Driven (recommended)** - I dispatch a fresh subagent per task, review between tasks, fast iteration

**2. Inline Execution** - Execute tasks in this session using executing-plans, batch execution with checkpoints

**Which approach?"**

**If Subagent-Driven chosen:**
- **REQUIRED SUB-SKILL:** Use superpowers:subagent-driven-development
- Fresh subagent per task + two-stage review

**If Inline Execution chosen:**
- **REQUIRED SUB-SKILL:** Use superpowers:executing-plans
- Batch execution with checkpoints for review
