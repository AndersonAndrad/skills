---
name: development-workflow
description: Mandatory development workflow for implementing features, fixing bugs, refactoring code, changing behavior, creating endpoints, modifying business rules, or editing production code. Use before, during, and after every code implementation.
---

# Development Workflow

Follow this workflow whenever creating, modifying, refactoring, or removing
production code.

This workflow is mandatory. Do not consider an implementation complete until
all applicable validation steps have been performed.

## 1. Understand the task

Before editing code:

1. Restate the expected behavior internally.
2. Identify the affected modules, files, interfaces, and integrations.
3. Inspect existing implementations for similar behavior.
4. Identify existing project conventions.
5. Identify possible backward compatibility risks.
6. Identify unclear requirements or assumptions.

Do not start by creating new abstractions before inspecting the existing code.

## 2. Inspect the affected flow

Trace the complete flow when applicable:

- Entry point
- Input validation
- Authentication and authorization
- Business rules
- Persistence
- External integrations
- Error handling
- Logging
- Returned response
- Tests
- Documentation

Do not inspect only the file explicitly mentioned in the task.

## 3. Plan the smallest correct change

Prefer the smallest implementation that completely solves the problem.

Avoid:

- Unrelated refactors
- Premature abstractions
- New dependencies without clear necessity
- Duplicating existing utilities
- Changing public contracts unnecessarily
- Silently changing behavior outside the requested scope

## 4. Implement incrementally

During implementation:

1. Follow existing project patterns.
2. Keep functions and classes focused.
3. Use clear and domain-oriented names.
4. Validate data at system boundaries.
5. Handle expected failure cases.
6. Preserve backward compatibility unless explicitly instructed otherwise.
7. Avoid exposing secrets or sensitive information.
8. Add or update tests with the implementation.

## 5. Apply specialized validations

After implementing, apply all relevant project skills:

- `code-quality`
- `security-review`
- `testing`
- `architecture-review`
- `error-handling`
- `final-validation`

A specialized validation may be skipped only when it is clearly not applicable.
When skipped, state why in the final report.

## 6. Validate the implementation

Run the commands available in the project when applicable:

1. Formatter
2. Linter
3. Type checker
4. Unit tests
5. Integration tests
6. Build
7. Application-specific validation

Never claim that a command passed unless it was actually executed successfully.

If execution is impossible, clearly report:

- Which command was not executed
- Why it could not be executed
- What remains unverified

## 7. Review the final diff

Before finishing:

1. Inspect the complete diff.
2. Remove accidental changes.
3. Remove debug code and temporary files.
4. Check for hardcoded values.
5. Check for commented-out code.
6. Check for unnecessary formatting changes.
7. Confirm that tests cover the changed behavior.
8. Confirm that no secrets were added.

## 8. Report completion

The final response must contain:

### Implemented

Briefly describe what changed.

### Validated

List the commands and checks actually performed.

### Risks or limitations

Describe remaining risks, assumptions, skipped validations, or unverified
behavior.

Do not say that the task is fully validated when relevant checks were skipped
or failed.