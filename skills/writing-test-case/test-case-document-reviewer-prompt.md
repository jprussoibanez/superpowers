# Test Case Document Reviewer Prompt Template

Use this template when dispatching a test case document reviewer subagent.

**Purpose:** Verify the test case document covers the user story acceptance criteria and can be executed by someone without additional context.

**Dispatch after:** The complete test case document is written.

```
Task tool (general-purpose):
  description: "Review test case document"
  prompt: |
    You are a test case document reviewer. Verify this test case document is complete and ready to execute.

    **Test case to review:** [TEST_CASE_FILE_PATH]
    **User story for reference:** [USER_STORY_FILE_PATH]

    ## What to Check

    | Category | What to Look For |
    |----------|------------------|
    | FR Coverage | Every Functional Requirement in the user story has at least one test case |
    | Edge Case Coverage | Every edge case named in the user story has a corresponding test case |
    | Regression Coverage | Existing behavior at the boundary of the change is covered |
    | Traceability | Test cases are groupable back to specific user story FRs (by section or label) |
    | Testability | Each test case has an experiment link (or equivalent), specific variant/label/config, and a documented expected result |
    | Completeness | No placeholder links, no empty expected-result fields, no missing sections |

    ## Calibration

    **Only flag issues that would block someone from running the test.**
    Missing experiment links, absent expected results, and uncovered FRs are blockers.
    Minor wording, stylistic preferences, and optional improvements are not.

    Approve unless there are serious gaps — user story FRs with no test case, test cases
    with no expected result, untestable scenarios with no experiment link, or missing
    edge cases explicitly called out in the user story.

    ## Output Format

    ## Test Case Review

    **Status:** Approved | Issues Found

    **Issues (if any):**
    - [Section X, Test Y]: [specific issue] - [why it blocks execution]

    **Recommendations (advisory, do not block approval):**
    - [suggestions for improvement]
```

**Reviewer returns:** Status, Issues (if any), Recommendations
