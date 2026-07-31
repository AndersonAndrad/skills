---
name: final-validation
description: Mandatory final review for any completed implementation, bug fix, refactor, configuration change, dependency change, or production code modification. Use immediately before reporting completion.
---

# Final Validation

Do not finish the task before performing this validation.

## Pre-validation gate

Before running validation commands, confirm:

* The complete diff was reviewed.
* The intended requirement was compared against the implementation.
* Architecture was reviewed when the change affects structure or boundaries.
* Security was reviewed when the change affects input, authentication,
  authorization, secrets, personal data, files, or external access.
* Edge cases were reviewed.
* Duplicate execution and concurrency were considered when relevant.
* Production impact was reviewed.
* Observability was reviewed for operationally relevant flows.
* Dependency changes were reviewed.
* Migrations and backward compatibility were reviewed.
* Test coverage was evaluated for missing scenarios.
* Critical and High findings were corrected.
* The final diff was reviewed again after corrections.

If an applicable review was not executed, final status cannot be `Validated`.

Use `Partially validated` and clearly state which review remains missing.


## Final validation is not a code review

Final validation does not replace:

* Requirements review
* Architecture review
* Security review
* Edge-case review
* Staff PR review
* Critical code review
* Dependency review
* Observability review
* Test coverage analysis

Formatter, lint, type checking, tests, and build only prove that the executed
checks completed successfully.

They do not prove that:

* The requirement is correct
* The architecture is appropriate
* Authorization is complete
* Security risks are absent
* Production behavior is safe
* Important tests exist
* Regressions were not introduced
* Error handling is complete
* The implementation is maintainable
* The public contract remains compatible

Before final validation begins, verify that the applicable review skills have
been completed.

For every applicable review, record:

* Skill applied
* Scope reviewed
* Findings discovered
* Corrections applied
* Remaining findings
* Unresolved questions
* Review status

Do not proceed when:

* A Critical finding remains unresolved.
* A High finding remains unresolved.
* A material business-rule question remains unanswered.
* A public contract change has not been reviewed.
* A dependency change has not been reviewed.
* A migration lacks a rollback assessment.
* An important production flow cannot be investigated.
* The complete diff has not received a Staff PR review.

Final validation is the last gate, not the only gate.


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