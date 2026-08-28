
# ADR Template

Follow this ADR template format and sections.

````markdown
# ADR [Number]: [Short, Descriptive Title]

## Status

Proposed | Accepted | Deprecated | Superseded by [Link]

## 1. Context

Describe the problem, current use cases, platform context, and broader opportunity.

### 1.1 Problem Statement

We have observed [problem / pain point].

This leads to [impact on people / teams / community / business / platform].

We believe this is happening because [root cause or contributing factors].

### 1.2 Statistical Context

Describe any statistical, experimentation, causal inference, measurement, data quality, or analytical context relevant to the decision.

Include relevant assumptions, constraints, risks, or methodological considerations.

### 1.3 Use Cases and Scenarios

Describe the canonical use cases and scenarios that the solution must support.

Where useful, include synthetic or real examples, sample data, workflows, or acceptance scenarios that can support design, execution, and testing.

### 1.4 Functional and Non-Functional Requirements

Describe the main requirements that will be used to evaluate the considered options.

Include both:

- Functional requirements
- Non-functional requirements, such as scalability, reliability, maintainability, observability, cost, security, privacy, reproducibility, extensibility, and performance

## 2. Considered Options

Describe the solution approaches that could address the problem and support the canonical use cases.

### 2.1 Decision Drivers

List the criteria used to evaluate and choose a solution.

Examples:

- Statistical validity
- Experiment reproducibility
- Data quality
- Developer experience
- Operational complexity
- Cost
- Scalability
- Integration with existing systems
- Migration effort
- Observability
- Long-term maintainability

### 2.2 Options Evaluation and Comparison

Compare the considered options against the decision drivers.

Include pros, cons, risks, and trade-offs.

Follow the opportunity solution tree format and guidelines (https://www.producttalk.org/opportunity-solution-trees/).

```text
Outcome / Goal
└── Opportunity / User Need / Pain Point with current limitation
    ├── Candidate Solution A
    ├── Candidate Solution B
    └── Candidate Solution C
```

### 2.3 Decision Outcome

State the selected decision and explain the rationale.

Use this Y-statement format:

In the context of [use case or situation],  
facing [concern or problem],  
we decided for [selected option],  
to achieve [desired quality or outcome],  
accepting [trade-off or downside].

### 2.4 Architecture Diagrams

Use C4 diagrams to communicate the architectural context when needed using `superpowers:drawing-c4-diagrams` skill.

## 3. Consequences

Discuss the implications of the decision.

### Positive Consequences

- [Benefit 1]
- [Benefit 2]
- [Benefit 3]

### Negative Consequences / Trade-offs

- [Trade-off 1]
- [Trade-off 2]
- [Risk or limitation]

### Follow-up Actions

- [Action 1]
- [Action 2]
- [Action 3]

## 4. References

List relevant documents, discussions, articles, RFCs, tickets, datasets, experiments, or prior ADRs that influenced this decision.
````