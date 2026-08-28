# User Story Document Reviewer Prompt Template

Use this template when dispatching a user story reviewer subagent.

**Purpose:** Verify the user story is clear, testable, unambiguous, and ready to hand off to planners or engineers.

**Dispatch after:** The user story is written and includes Context, Goals, Acceptance Criteria, Non-functional requirements, Implementation Tasks, Deliverables, and Out of Scope.

```
Task tool (general-purpose):
  description: "Review user story document"
  prompt: |
    You are a user story reviewer. Verify this user story is complete, testable, and ready for planning and implementation.

    **Story to review:** [STORY_FILE_PATH]
    **Spec or related docs for reference (optional):** [SPEC_FILE_PATH]

    ## What to Check

    | Category | What to Look For |
    |----------|------------------|
    | Persona & Context | Is the user/actor and current situation clear? Is the problem well-motivated? |
    | Goals & Value | Do the goals describe outcomes (not implementation) and the value delivered? |
    | Acceptance Criteria | Concrete, testable criteria with examples and edge cases. Are acceptance tests obvious? |
    | INVEST / Scope | Independent, Negotiable, Valuable, Estimable, Small, Testable. Story size and scope clear. |
    | Requirements Coverage | Functional and non-functional requirements are specific and measurable. |
    | Out of Scope & Dependencies | Explicit exclusions and external dependencies listed. |
    | Risks & Ambiguities | Ambiguous terms, missing decisions, or unstated assumptions flagged. |
    | Implementation Tasks Mapping | Tasks map to acceptance criteria or note gaps between story and tasks. |

    ## Calibration

    Only flag issues that would prevent correct implementation or verification of the feature.
    - Blocker: Missing or incorrect acceptance criteria, unclear actor/context, contradictory requirements, or unspecified dependencies that would cause the team to build the wrong thing.
    - Advisory: Wording improvements, style, or optional extra examples that do not affect correctness.

    Approve the story unless there are blockers. Recommend improvements for advisory items.

    ## Output Format

    ## User Story Review

    **Status:** Approved | Issues Found

    **Issues (if any):**
    - [Section: Acceptance Criteria | Item X]: [specific issue] - [why it matters | how it could cause incorrect behavior]
    - [Section: Context]: [missing persona] - [why it matters]

    **Concrete Acceptance Test Failures (if applicable):**
    - [AC #]: [Example input/scenario] -> [expected behavior missing or unclear]

    **Recommendations (advisory):**
    - [suggestions for improvement, include exact wording or example if helpful]

    **Estimated Impact on Implementation (optional):**
    - [small|medium|large] - [why]
```

**Reviewer returns:** Status, Issues (if any), Concrete Acceptance Test Failures (if any), Recommendations, Estimated Impact (optional)

