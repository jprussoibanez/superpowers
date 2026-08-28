# Test Implementation Best Practices

## Hard Requirements for Unit Test Implementation

These are mandatory rules for the eventual implementation.

### Unit testing scope
- Only create **unit tests**
- Do not propose or implement integration, API, UI, or end-to-end tests
- Isolate the logic under test from external dependencies
- Always use a Pytest class instead of standalone functions for better organization and readability.
- Test realistic different scenarios and use cases with real numbers assertion instead of just validating result structure.

### Mocking
- Use mocks/stubs/fakes as needed to isolate test logic
- Mock external collaborators and side effects
- Avoid relying on real databases, APIs, filesystems, network calls, or framework-heavy runtime behavior unless the codebase defines that as a unit-test pattern

### Parametrization
- Use test parametrization when it improves clarity and reduces duplication
- Do not overcomplicate tests with excessive parametrization
- Prefer readability over cleverness
- Remove magic numbers by parametrizing them with pytest parametrization decorators.

### Reuse helpers
- Create and use general **arrange** helpers to reduce setup duplication
- Create and use general **assert** helpers to reduce repeated assertions
- Keep helpers simple, readable, and broadly useful
- Do not create abstractions that hide the intent of the test
- ALL asserts must provide a helpful message explaining what is being tested.

### Docstrings and type hints
- Add docstrings to each test case
- Add type hints to helper functions and tests where appropriate under repo conventions
- Use **NumPy-style docstrings**
- In each test docstring, express the scenario using **Given / When / Then** on the docstrings description before the parameters and returns sections
- DO NOT add parameters and returns sections when empty.

### BDD expression
- Express the test use case in the docstring using:
  - Given
  - When
  - Then

### Code style
- Remove all code comments
- Prefer descriptive test names over explanatory comments
- Keep test code explicit and easy to review
- Remove internal import statements from tests (use top-level imports)

# Task Implementation Best Practices

## Code Style Rules

These rules apply to all generated code unless overridden by explicit style constraints for a specific task.

- Do not add code comments
- Use descriptive names for variables, functions, and classes
- Extract complex logic into well-named helper functions
- Keep functions short and focused on a single responsibility
- Prefer composition over inheritance
- Follow Python Zen (PEP 8-style)

## Docstring Rules

These rules apply to all generated code.

- Add docstrings to all functions and methods
- Use **NumPy-style docstrings** with Parameters, Returns, and Raises sections
- Do not add parameters and returns sections when empty
- Provide usage examples when they clarify intent

## Type Hinting Rules

These rules apply to all generated code unless explicitly overridden for a specific task.

- Add type hints to all functions and methods
- Use precise types instead of Any when practical
- Use protocols for flexible interfaces
- Keep hints consistent with repository conventions