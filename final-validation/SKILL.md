---
name: final-validation
description: Mandatory final review for any completed implementation, bug fix, refactor, configuration change, dependency change, or production code modification. Use immediately before reporting completion.
---

# Final Validation

Do not finish the task before performing this validation.

## Changed behavior

Confirm:

- The requested behavior was implemented.
- The implementation matches the full requirement.
- Existing behavior was preserved where required.
- Edge cases were considered.
- Failure behavior is intentional.
- No unrelated behavior was changed.

## Diff inspection

Review every changed file.

Check for:

- Accidental changes
- Debug statements
- Temporary code
- Commented-out code
- Hardcoded credentials
- Hardcoded environment values
- Unused imports
- Dead code
- Duplicate code
- Unsafe casts
- Broad types
- Missing error handling
- Sensitive logs
- Unnecessary dependency additions

## Automated validation

Discover and run the project's applicable commands, such as:

- Format check
- Lint
- Type checking
- Unit tests
- Integration tests
- End-to-end tests
- Build
- Dependency audit

Do not invent command names. Inspect project configuration first.

## Requirement traceability

Map each requirement to:

- The implementation that satisfies it
- The test or validation that verifies it

If a requirement has no validation, report it as a remaining risk.

## Final status

Classify the implementation as one of:

### Validated

All relevant checks were executed successfully.

### Partially validated

The implementation was completed, but one or more relevant checks could not be
executed.

### Not validated

Relevant checks failed or the environment prevented meaningful validation.

Never use `Validated` when tests, build, lint, or other applicable checks failed
or were not executed.