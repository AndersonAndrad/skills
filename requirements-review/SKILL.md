---

name: requirements-review
description: Reviews requirements before implementation to identify ambiguity, missing business rules, hidden assumptions, affected flows, acceptance criteria, compatibility risks, security implications, edge cases, and validation needs. Use before implementing features, changing behavior, fixing complex bugs, modifying public contracts, or starting work with incomplete requirements.
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Requirements Review

Review the requirement before modifying production code.

The objective is to ensure that the implementation solves the correct problem,
covers the expected behavior, and does not rely on unsafe assumptions.

Do not start implementing a non-trivial change until the requirement has been
reviewed.

## 1. Understand the requested outcome

Identify:

* What problem must be solved
* Who is affected
* What behavior is expected
* What behavior must remain unchanged
* What input starts the flow
* What output or observable result is expected
* Which failures must be handled
* How success will be validated

Do not confuse the requested implementation with the actual desired outcome.

A requirement such as:

> Add a trim interceptor.

may represent a broader objective such as:

> Prevent accidental whitespace in specific textual fields without altering
> passwords, tokens, secrets, or meaningful user content.

Review the objective, not only the proposed technical solution.

## 2. Inspect existing behavior

Before asking questions or proposing changes:

1. Inspect the relevant entry points.
2. Trace the affected execution flow.
3. Search for similar behavior in the project.
4. Inspect existing validation rules.
5. Inspect tests.
6. Inspect public contracts.
7. Inspect frontend and backend behavior when both are affected.
8. Inspect database and integration constraints when applicable.

Do not ask the developer questions that the repository already answers.

## 3. Separate facts from assumptions

Classify the requirement details internally as:

### Confirmed

Explicitly defined by:

* The task description
* Existing tests
* Public contracts
* Current business rules
* Project documentation
* Existing implementation patterns
* Direct developer answers

### Inferred

Likely behavior based on context, but not explicitly confirmed.

### Unknown

Behavior that cannot be safely determined.

Do not implement behavior classified as unknown when it can affect:

* Business rules
* Data integrity
* Security
* Authorization
* Financial operations
* Public contracts
* User-visible behavior
* External integrations
* Backward compatibility

Ask targeted questions first.

## 4. Identify actors and permissions

Determine:

* Who performs the action?
* Which roles can perform it?
* Which resources can they access?
* Can users act on resources owned by others?
* Is administrative access different?
* Does the operation require authentication?
* Does it require additional authorization?
* Can the action be performed by automated processes?
* Can an integration or webhook execute the same behavior?

Authentication and authorization must be treated as separate requirements.

## 5. Identify the complete flow

Map the affected flow when applicable:

1. User interaction
2. Frontend validation
3. Request contract
4. Authentication
5. Authorization
6. Input normalization
7. Business validation
8. Business operation
9. Persistence
10. External integration
11. Error handling
12. Logging and tracing
13. Response
14. User feedback
15. Tests
16. Documentation
17. Monitoring

Do not review only the layer explicitly mentioned in the task.

## 6. Clarify input behavior

For each relevant input, determine:

* Is it required?
* Can it be empty?
* Can it be null?
* Can it contain only whitespace?
* What is the minimum length?
* What is the maximum length?
* Which format is accepted?
* Is casing meaningful?
* Are leading or trailing spaces meaningful?
* Are internal spaces meaningful?
* Can the value contain Unicode?
* Can the value contain emojis?
* Can the value contain line breaks?
* Can the value be pasted with formatting?
* Should the value be normalized?
* Should invalid values be rejected or transformed?

Do not normalize values blindly.

Be especially careful with:

* Passwords
* Tokens
* API keys
* Cryptographic signatures
* Hashes
* Encoded values
* Verification codes
* Free-form text
* File content
* External identifiers
* Values compared byte-for-byte

If surrounding spaces are prohibited in a sensitive value, prefer explicit
validation over silent mutation.

## 7. Clarify state transitions

For workflows involving status or lifecycle changes, identify:

* Initial state
* Allowed next states
* Forbidden transitions
* Final states
* Reversible states
* Who can change the state
* Preconditions
* Side effects
* Retry behavior
* Duplicate execution behavior
* Audit requirements

Examples include:

* Order status
* Payment status
* Account onboarding
* Approval
* Cancellation
* Refund
* Delivery
* User activation
* Document processing

Do not allow arbitrary state changes without validating the transition.

## 8. Clarify error behavior

Determine what should happen when:

* Input is invalid
* Authentication fails
* Authorization fails
* The resource does not exist
* The resource already exists
* The current state does not permit the action
* Persistence fails
* An external service times out
* An external service rejects the request
* A duplicate request is received
* A partial operation succeeds
* The client retries
* The operation is interrupted

For each expected failure, identify:

* Error category
* User-facing message
* Internal diagnostic context
* Whether retry is allowed
* Whether rollback is needed
* Whether the failure must be logged
* Whether monitoring or alerting is needed

## 9. Clarify idempotency and concurrency

Ask whether the operation can happen more than once.

Review:

* Double-clicks
* Client retries
* Network timeouts
* Queue redelivery
* Webhook redelivery
* Concurrent requests
* Scheduled reprocessing
* Multiple application instances
* Read-modify-write operations
* Duplicate external responses

Determine:

* Whether duplicate execution is acceptable
* Whether an idempotency key is required
* Whether a unique constraint is needed
* Whether a transaction is needed
* Whether optimistic or pessimistic locking is needed
* Whether an existing operation must be checked before retrying

Do not assume that retries are harmless.

## 10. Clarify data behavior

Determine:

* Which data is created
* Which data is updated
* Which data is removed
* Which fields are immutable
* Which fields are optional
* Which values must remain historically accurate
* Whether audit history is required
* Whether soft delete or hard delete applies
* Whether existing records require migration
* Whether older data remains compatible
* Whether partial updates are allowed
* Whether database constraints enforce the rule

Consider what happens to existing records after the change.

## 11. Clarify public contracts

Identify whether the task changes:

* HTTP endpoints
* Request body
* Response body
* Status codes
* Headers
* Error format
* Events
* Queue messages
* Webhooks
* Database schema
* Shared types
* SDK behavior
* Configuration
* Environment variables
* Command-line interfaces

For every public contract change, identify:

* Current consumers
* Compatibility risk
* Versioning requirements
* Migration strategy
* Deprecation strategy
* Documentation changes
* Tests required

Do not silently change a public contract.

## 12. Clarify frontend experience

When the task affects the interface, determine:

* What the user sees before the action
* What the user sees during loading
* What happens on success
* What happens on failure
* Whether duplicate submission must be prevented
* Whether fields retain their values after failure
* Whether validation is inline or on submission
* Whether the flow works with keyboard navigation
* Whether mobile behavior differs
* Whether empty states are required
* Whether permissions change the available actions
* Whether destructive actions require confirmation
* Whether undo is possible

Do not consider the frontend requirement complete only because the component
renders.

## 13. Clarify security and privacy

Determine whether the flow handles:

* Credentials
* Tokens
* Personal data
* Financial data
* Documents
* Files
* Internal identifiers
* Location
* Authentication data
* Authorization rules
* Administrative actions
* Audit records

Identify:

* Who can access the data
* What can be logged
* What must be redacted
* Retention requirements
* Encryption requirements
* Rate limiting needs
* Abuse scenarios
* Enumeration risks
* Exposure through errors
* Exposure through frontend state

Do not log complete request or response objects without confirming that they
contain no sensitive information.

## 14. Clarify integrations

For external services, identify:

* Provider
* Operation
* Timeout
* Retry policy
* Rate limits
* Idempotency support
* Authentication method
* Error formats
* Partial success behavior
* Webhook behavior
* Duplicate notifications
* Sandbox differences
* Production differences
* Fallback behavior
* Required observability

Do not assume that a timeout means the provider did not process the request.

## 15. Clarify observability

Determine how the feature will be investigated in production.

Consider:

* Structured logs
* Correlation ID
* Request ID
* Operation ID
* External provider identifiers
* Metrics
* Tracing
* Error categorization
* Dashboards
* Alerts
* Audit trail

Observability should support real operational questions, such as:

* How many requests failed?
* Which step failed?
* Was the provider called?
* Was the operation retried?
* Was a duplicate prevented?
* Which user or resource was affected?
* How long did the operation take?

Avoid logging sensitive payloads merely to improve diagnostics.

## 16. Define acceptance criteria

Convert the requirement into testable statements.

Good acceptance criteria are:

* Observable
* Specific
* Testable
* Independent from implementation details
* Clear about success and failure
* Clear about permissions
* Clear about edge cases

Example:

### Weak

* Trim user fields.

### Better

* Leading and trailing spaces are removed from `name`, `email`, and `city`.
* Internal spaces in names are preserved.
* Passwords, tokens, API keys, and free-form messages are not modified.
* Email normalization is applied before uniqueness validation.
* The backend applies the same rules when called without the frontend.
* Tests cover allowed, rejected, and preserved values.

## 17. Define validation scenarios

Before implementation, identify tests for:

### Happy path

The expected successful flow.

### Invalid input

Missing, malformed, empty, oversized, or unsupported values.

### Boundaries

Minimum, maximum, zero, empty collections, and limits.

### Permissions

Unauthenticated, unauthorized, and resource ownership scenarios.

### Failure paths

Database, provider, timeout, parsing, and infrastructure failures.

### Duplicate execution

Repeated request, double-click, retry, and queue redelivery.

### Compatibility

Existing consumers, old records, and unchanged behavior.

### User experience

Loading, errors, disabled states, keyboard behavior, and responsive behavior.

## 18. Identify implementation impact

List the areas that may need changes:

* Frontend components
* Form validation
* API contracts
* DTOs or schemas
* Controllers or handlers
* Services
* Domain rules
* Persistence
* Migrations
* Integrations
* Cache
* Queues
* Logs
* Metrics
* Tests
* Documentation
* Environment configuration
* Deployment
* Rollback

Do not modify every listed area automatically.

Use the list to identify the real implementation scope.

## 19. Risk classification

Classify identified requirement risks as:

### Critical

Unclear behavior may cause:

* Security compromise
* Financial loss
* Data corruption
* Unauthorized access
* Irreversible effects
* Major production outage

### High

Unclear behavior may cause:

* Incorrect business processing
* Duplicate operations
* Significant user impact
* Breaking public contracts
* Persistent data inconsistency

### Medium

Unclear behavior may cause:

* Edge-case failure
* Weak validation
* Poor error handling
* Operational difficulty
* Incomplete user experience

### Low

Unclear behavior mainly affects:

* Minor usability
* Local consistency
* Readability
* Non-blocking improvements

Do not inflate severity.

## 20. Ask targeted questions

Ask questions only when the answer can materially change the implementation.

Each question must contain:

### Question

A direct question about the expected behavior.

### Why this matters

The concrete technical or business risk.

### Current evidence

What the repository or requirement currently indicates.

### Options

Likely alternatives when they help the developer answer.

Example:

### Question

Should whitespace be removed from every string field or only from explicitly
classified textual fields?

### Why this matters

Trimming every string can silently modify passwords, tokens, signatures,
encoded values, and free-form content.

### Current evidence

The current requirement mentions unnecessary spaces but does not define which
fields may be normalized.

### Options

1. Trim only explicitly configured fields.
2. Trim all fields except an exclusion list.
3. Reject surrounding spaces in selected fields.
4. Preserve all values and validate individually.

Do not ask:

* Questions already answered by the code
* Generic questions without explaining the risk
* Questions about minor style choices
* Questions whose answer does not affect implementation
* Dozens of low-value questions

Prioritize Critical and High uncertainty.

## 21. Safe assumptions

When minor details remain unclear, prefer existing project conventions.

Safe assumptions may include:

* Reusing the existing error format
* Following the current naming pattern
* Reusing the existing design system
* Using established test tools
* Following the current folder structure
* Preserving public behavior
* Avoiding new dependencies

Do not treat the following as safe assumptions:

* Permission rules
* Financial behavior
* Destructive behavior
* Retry safety
* Data normalization
* Public contract changes
* State transitions
* Sensitive data handling
* Migration behavior
* External provider guarantees

## 22. Review output

Before implementation, provide:

## Requirement summary

Summarize the desired outcome without copying the task verbatim.

## Confirmed behavior

List the behavior supported by evidence.

## Assumptions

List inferred behavior that can safely follow project conventions.

## Material questions

Ask only questions that must be answered before implementation.

## Acceptance criteria

Provide testable criteria based on the confirmed requirement.

## Affected areas

List the likely implementation scope.

## Risks

List relevant risks by severity.

## Proposed validation

Describe the tests and checks that should prove the implementation.

## 23. After receiving answers

After the developer responds:

1. Map each answer to the related question.
2. Update the confirmed behavior.
3. Remove invalid assumptions.
4. Update the acceptance criteria.
5. Update implementation scope.
6. Identify remaining risks.
7. Proceed with the smallest complete implementation.
8. Apply the relevant development skills.
9. Validate the implementation against the acceptance criteria.

Do not ask the same question again after it was answered.

## 24. When questions are unnecessary

Proceed directly when:

* The requirement is explicit.
* Existing behavior clearly defines the rule.
* Tests and contracts remove ambiguity.
* The change is small and non-behavioral.
* Remaining uncertainty can safely follow project conventions.

Do not turn every task into a requirements interview.

## 25. Anti-patterns

Do not:

* Begin implementation before understanding the desired outcome.
* Treat the proposed technical solution as the requirement.
* Invent business rules.
* Ask questions answered by the repository.
* Ask generic or low-value questions.
* Normalize all strings blindly.
* Assume retries are safe.
* Assume frontend validation is sufficient.
* Ignore existing records.
* Ignore permissions.
* Ignore compatibility.
* Ignore loading and failure behavior.
* Accept vague criteria such as “must work”.
* Produce an oversized design for a small change.
* Block implementation over insignificant uncertainty.

The review must reduce real implementation risk, not create unnecessary
bureaucracy.
