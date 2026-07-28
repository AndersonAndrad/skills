---
name: testing
description: Defines mandatory testing practices for features, bug fixes, refactors, endpoints, business rules, integrations, and behavior changes. Use during implementation and before declaring any code task complete.
---

# Testing

Tests must validate behavior, not merely increase coverage.

## Before writing tests

Identify:

- Expected successful behavior
- Expected failures
- Boundary values
- Invalid inputs
- Permission failures
- External dependency failures
- Existing behavior that must remain unchanged

## Test selection

Use the lowest test level that proves the behavior reliably.

### Unit tests

Use for:

- Business rules
- Pure transformations
- Validation logic
- Isolated error handling
- Deterministic calculations

### Integration tests

Use for:

- Persistence behavior
- Framework configuration
- Database queries
- External adapters
- Serialization
- Authentication and authorization flows

### End-to-end tests

Use for critical flows that require validating the complete application
behavior.

Do not replace all unit and integration tests with end-to-end tests.

## Bug fixes

Every bug fix should include a regression test when reasonably possible.

The regression test should:

1. Fail before the correction.
2. Pass after the correction.
3. Represent the real failure scenario.

## Required scenarios

When applicable, test:

- Valid input
- Invalid input
- Missing input
- Boundary values
- Empty collections
- Duplicate operations
- Unauthorized access
- Forbidden access
- Resource not found
- Dependency timeout
- Dependency failure
- Concurrent or repeated execution
- Backward compatibility

## Test quality

Tests must:

- Have descriptive names
- Avoid unnecessary implementation details
- Be deterministic
- Avoid real external services
- Clean up created state
- Make failures understandable
- Assert meaningful outcomes

Do not weaken an existing test merely to make a new implementation pass.

## Execution

Run the smallest relevant test set during development.

Before completion, run the broader project test commands when practical.

Never claim tests passed without executing them.