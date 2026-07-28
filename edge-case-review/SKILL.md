---

name: edge-case-review
description: Systematically challenges implementations with boundary values, malformed input, unusual states, duplicate execution, concurrency, partial failures, Unicode, large payloads, empty values, unexpected ordering, and compatibility scenarios. Use after implementing behavior changes and before final validation.
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Edge Case Review

Review the implementation by actively trying to break it.

The goal is to identify realistic scenarios that are not covered by the happy
path.

Do not generate arbitrary test cases only to appear thorough.

Prioritize edge cases that can affect:

* Correctness
* Security
* Data integrity
* Financial behavior
* User experience
* Availability
* Compatibility
* Operations in production

## 1. Understand the affected behavior

Before generating edge cases:

1. Identify the entry points affected by the change.
2. Trace the complete execution flow.
3. Identify input boundaries.
4. Identify state transitions.
5. Identify external dependencies.
6. Identify persistence behavior.
7. Identify retry and duplicate execution paths.
8. Inspect existing tests.
9. Inspect similar implementations.
10. Identify assumptions made by the implementation.

Do not review only the changed function.

## 2. Classify the reviewed flow

Determine which categories apply:

* Text input
* Numeric input
* Dates and time
* Collections
* Files
* Authentication
* Authorization
* State transitions
* Persistence
* External integrations
* Background jobs
* Queues
* Payments
* Frontend forms
* Search and filtering
* Pagination
* Caching
* Concurrency
* Configuration
* Public APIs

Apply only relevant categories.

## 3. Empty and missing values

Test the difference between:

* Field omitted
* `undefined`
* `null`
* Empty string
* String containing one space
* String containing multiple spaces
* Tab
* Newline
* Empty array
* Empty object
* Zero
* `false`
* Missing nested property

Do not assume these values have the same meaning.

Verify whether the system:

* Rejects the value
* Applies a default
* Preserves the value
* Removes the field
* Treats it as an update
* Treats it as no change
* Produces a clear error

Be especially careful with partial updates.

A missing field and a field explicitly set to `null` may represent different
intentions.

## 4. Text values

For text inputs, consider:

* Empty string
* Whitespace-only string
* Leading whitespace
* Trailing whitespace
* Multiple internal spaces
* Tabs
* Line breaks
* Very long strings
* Single-character strings
* Accented characters
* Unicode
* Emojis
* Right-to-left text
* Combining characters
* Different Unicode representations
* HTML
* Script-like content
* SQL-like content
* NoSQL operator-like content
* File paths
* URLs
* Encoded content
* Null characters
* Control characters

Examples:

```text
""
" "
"   "
"\t"
"\n"
" João "
"João  da  Silva"
"José"
"中文"
"😀"
"👨‍👩‍👧‍👦"
"<script>alert(1)</script>"
"../../arquivo"
"$ne"
```

Do not recommend rejecting Unicode or emojis unless the domain requires it.

Verify length by the same unit used by the business rule:

* Bytes
* Characters
* Unicode code points
* Grapheme clusters

These are not always equivalent.

## 5. Input normalization

Review every transformation applied to input:

* Trim
* Lowercase
* Uppercase
* Accent removal
* Mask removal
* Space collapsing
* Number conversion
* Date conversion
* Boolean conversion
* Unicode normalization
* Empty-to-null conversion

Ask:

* Can valid data be changed?
* Can two different values become identical?
* Does normalization happen before uniqueness validation?
* Is normalization consistent between create, update, search, and authentication?
* Is the original value needed later?
* Does frontend behavior match backend behavior?
* Can normalization break signatures or provider identifiers?

Never normalize blindly:

* Passwords
* Tokens
* API keys
* Hashes
* Cryptographic signatures
* Secrets
* Encoded payloads
* File contents
* Values compared byte-for-byte

## 6. Numeric values

Consider:

* Zero
* Negative values
* Minimum accepted value
* Maximum accepted value
* Just below the minimum
* Just above the maximum
* Decimal values
* Excess decimal precision
* Very large integers
* Floating-point precision
* Scientific notation
* Numeric string
* Empty numeric field
* `NaN`
* Infinity
* Negative zero
* Leading zeros

Examples:

```text
0
-1
1
0.01
0.1 + 0.2
999999999999999999
"00123"
"1e6"
```

For money:

* Avoid binary floating-point for authoritative calculations.
* Define rounding rules.
* Define accepted precision.
* Define currency.
* Define behavior for negative values.
* Define behavior for partial amounts.
* Verify totals after repeated calculations.

Do not assume rounding behavior.

## 7. Dates and time

Consider:

* Invalid date
* Leap year
* February 29
* End of month
* End of year
* Midnight
* Timezone conversion
* Daylight saving transitions
* UTC versus local time
* Date without timezone
* Date-only values
* Future date
* Past date
* Same start and end date
* End before start
* Maximum allowed range
* Locale-specific parsing
* Server and client in different timezones

Examples:

```text
2024-02-29
2025-02-29
2026-12-31T23:59:59Z
2026-01-01
```

Do not parse ambiguous dates such as:

```text
01/02/2026
```

without a defined locale or format.

Verify whether date boundaries are inclusive or exclusive.

## 8. Collections

Consider:

* Empty collection
* One item
* Maximum allowed items
* One item above the limit
* Duplicate items
* Same items in different order
* Null items
* Invalid item among valid items
* Large collection
* Repeated identifiers
* Missing identifiers
* Partial failure
* Collection modified during processing

Ask:

* Is ordering meaningful?
* Are duplicates allowed?
* Is processing atomic?
* Can part of the collection succeed?
* Is rollback required?
* Are results returned in the same order?
* Is collection size bounded?

Avoid unbounded processing.

## 9. Pagination, search, filtering, and sorting

Review:

* First page
* Last page
* Page beyond available results
* Page zero
* Negative page
* Excessive page size
* Empty search
* Whitespace-only search
* Special characters
* Accents
* Case sensitivity
* Multiple filters
* Conflicting filters
* Invalid sort field
* Invalid sort direction
* Stable ordering
* Duplicate sort values
* Data changing between pages

Verify that pagination has deterministic ordering.

Without stable ordering, records may be duplicated or skipped between pages.

## 10. Identifiers

Consider:

* Missing identifier
* Invalid format
* Valid format but nonexistent resource
* Identifier belonging to another user
* Reused identifier
* Case differences
* Leading zeros
* Very long identifier
* Provider identifier with unexpected characters
* Empty identifier
* Identifier modified by normalization

Verify resource ownership separately from identifier validity.

Do not expose whether a sensitive resource exists when enumeration is a risk.

## 11. Authentication

Review:

* Missing credentials
* Empty credentials
* Invalid token
* Expired token
* Revoked token
* Wrong token type
* Token for another environment
* Token with missing claims
* Token with extra claims
* Clock skew
* User disabled after token issuance
* Password with surrounding spaces
* Multiple active sessions
* Reused reset token
* Reused verification code
* Brute-force attempts

Verify that failures do not expose sensitive details.

## 12. Authorization

Consider:

* Authenticated but unauthorized
* Correct role, wrong resource owner
* Lower role manipulating identifiers
* User changing their own role
* Admin acting across tenants
* Missing tenant context
* Modified tenant identifier
* Permission removed during an active session
* Bulk operation containing unauthorized resources
* Indirect access through related resources

Do not rely only on hidden frontend controls.

Authorization must be enforced at the backend.

## 13. State transitions

For workflows with statuses, test:

* Valid transition
* Repeated transition
* Transition from the wrong state
* Skipping states
* Returning to a previous state
* Transition after final state
* Concurrent transitions
* Transition during external processing
* Transition after timeout
* Transition after cancellation
* Partial side effects
* Manual and automated transitions occurring together

Represent allowed transitions explicitly when the workflow is important.

Do not rely only on scattered conditional statements.

## 14. Duplicate execution

Assume the same operation may happen more than once because of:

* Double-click
* Network retry
* Client retry
* Queue redelivery
* Webhook redelivery
* Timeout
* User refreshing the page
* Multiple application instances
* Scheduled retry
* Provider callback duplication

Verify:

* Whether the operation is idempotent
* Whether a unique key exists
* Whether duplicate state is detected
* Whether duplicate side effects are prevented
* Whether the same response can be safely returned
* Whether duplicate execution is logged

Critical examples include:

* Payment
* Refund
* Transfer
* Sending messages
* Creating users
* Generating credentials
* Inventory deduction
* Applying discounts
* External provider calls

## 15. Concurrency

Consider two or more operations happening at nearly the same time.

Examples:

* Two users updating the same record
* Two payments for the same order
* Two stock reservations
* Two password reset attempts
* Two workers processing the same message
* Two requests using the same limited resource
* One operation reading while another deletes
* One operation validating while another changes the state

Look for:

* Lost updates
* Dirty reads
* Duplicate records
* Negative inventory
* Double processing
* Inconsistent totals
* Invalid state transitions
* Check-then-act race conditions

Potential protections include:

* Unique constraints
* Atomic updates
* Transactions
* Optimistic locking
* Pessimistic locking
* Idempotency keys
* Compare-and-set operations
* Distributed locks

Do not add locking without identifying the real race condition.

## 16. Persistence

Review:

* Record not found
* Duplicate record
* Constraint violation
* Partial update
* Failed transaction
* Connection failure
* Timeout
* Old schema data
* Missing optional fields
* Corrupted legacy data
* Soft-deleted records
* Concurrent update
* Rollback behavior
* Read-after-write consistency

Verify whether business rules are also protected by database constraints when
appropriate.

Application checks alone may not prevent concurrent duplicates.

## 17. Cache behavior

Consider:

* Cache miss
* Stale cache
* Expired cache
* Cache unavailable
* Corrupted cached value
* Different serialization versions
* Invalidation failure
* Cache updated before persistence
* Persistence updated without cache invalidation
* Concurrent cache population
* Cache stampede
* Tenant key collision
* User key collision

The system should remain correct when cache is unavailable unless the cache is
explicitly authoritative.

## 18. External integrations

Review:

* Timeout before response
* Timeout after provider processing
* Connection failure
* Provider unavailable
* Rate limit
* Invalid credentials
* Expired credentials
* Malformed provider response
* Missing expected field
* Additional unexpected fields
* Partial success
* Duplicate response
* Out-of-order callback
* Delayed webhook
* Webhook before synchronous response
* Sandbox and production differences
* Provider returning HTTP success with business failure
* Retry causing duplicate side effects

Do not assume HTTP 200 means business success.

Do not assume timeout means the provider did not process the request.

## 19. Background jobs and queues

Consider:

* Message delivered twice
* Message delivered out of order
* Consumer crashes after processing but before acknowledgment
* Consumer crashes before processing
* Poison message
* Maximum retry reached
* Dead-letter queue
* Missing correlation identifier
* Long-running processing
* Worker restarted
* Schema version mismatch
* Partial processing
* Dependency outage
* Concurrent consumers

Verify that processing is idempotent when duplicate delivery is possible.

## 20. Files and uploads

Review:

* Empty file
* Missing file
* Oversized file
* Wrong extension
* Correct extension with invalid content
* Incorrect MIME type
* Multiple extensions
* Path traversal
* Duplicate filename
* Unicode filename
* Long filename
* Executable content
* Corrupted file
* Partial upload
* Upload interrupted
* Unsupported encoding
* Malicious archive
* Archive expansion size
* Metadata containing sensitive information

Do not trust extension or MIME type alone.

## 21. Frontend interactions

Test:

* Double-click
* Rapid repeated click
* Submit using Enter
* Submit using button
* Back button
* Refresh during processing
* Modal closed during request
* Navigation during request
* Slow network
* Offline behavior
* API error
* Empty state
* Loading state
* Disabled state
* Keyboard-only flow
* Screen zoom
* Mobile viewport
* Paste into masked input
* Autocomplete
* Browser autofill
* Lost focus
* Returning to the page with stale state

Do not rely on hover for essential functionality.

## 22. Error handling

For every expected failure, verify:

* Correct error category
* Safe user-facing message
* Useful internal context
* Sensitive values are redacted
* Partial state is handled
* Retry behavior is intentional
* Duplicate retry is safe
* Logs are not duplicated excessively
* Error does not get swallowed
* Cleanup occurs when required

Test both known errors and unexpected exceptions.

## 23. Backward compatibility

Consider:

* Existing clients omitting the new field
* Existing clients sending old values
* Old records missing new properties
* New backend with old frontend
* Old backend with new frontend
* Old queue messages
* Old webhook payloads
* Old cached values
* Existing environment configuration
* Rolling deployment with mixed application versions
* Database migration running while old code is active

Do not assume all components deploy simultaneously.

## 24. Configuration

Review:

* Missing environment variable
* Empty environment variable
* Invalid format
* Invalid URL
* Wrong environment value
* Secret accidentally exposed
* Default value used in production
* Boolean parsed incorrectly
* Numeric value outside range
* Different behavior between local, test, staging, and production
* Configuration changed without restart
* Multiple configuration sources disagreeing

Fail fast for configuration required for safe application startup.

## 25. Operational limits

Consider:

* Maximum request size
* Maximum execution time
* Rate limit
* Memory usage
* CPU-intensive inputs
* Database connection limits
* Queue backlog
* Thread or worker exhaustion
* External provider quotas
* Large response payloads
* Unbounded logs

A valid request can still be operationally unsafe.

## 26. Generate a focused edge-case matrix

Produce a matrix containing only relevant scenarios.

Use:

| Scenario             | Expected behavior            | Current coverage | Risk | Action                |
| -------------------- | ---------------------------- | ---------------- | ---- | --------------------- |
| Empty value          | Reject with validation error | Covered          | Low  | None                  |
| Password with spaces | Preserve exactly             | Missing          | High | Add regression test   |
| Duplicate request    | Return existing result       | Partial          | High | Add idempotency check |

Do not create hundreds of low-value scenarios.

Prioritize:

1. Critical business rules
2. Security boundaries
3. Data integrity
4. Duplicate execution
5. External failures
6. Boundary values
7. User-visible failures

## 27. Ask material questions

Ask the developer when expected behavior cannot be safely inferred.

Good question structure:

### Question

What should happen when the same request is submitted twice?

### Why this matters

A timeout or repeated click may cause the operation to execute more than once.

### Current behavior

The implementation creates a new record on every request and has no uniqueness
or idempotency check.

### Options

1. Return the previously created result.
2. Reject the duplicate.
3. Allow duplicates.
4. Use another domain-specific rule.

Do not ask questions when existing tests, contracts, or established project
behavior already provide the answer.

## 28. Apply corrections

After edge cases are confirmed:

1. Apply the smallest complete correction.
2. Add validation at the correct boundary.
3. Add regression tests.
4. Add database constraints when needed.
5. Add idempotency protection when needed.
6. Improve error handling.
7. Preserve confirmed behavior.
8. Avoid unrelated refactoring.
9. Re-run the affected validation commands.
10. Review the final diff.

Do not change valid business behavior based only on speculation.

## 29. Required tests

When applicable, add tests for:

* Missing values
* Null values
* Empty values
* Boundary values
* Invalid values
* Oversized values
* Unicode
* Duplicate execution
* Concurrent execution
* Unauthorized access
* Wrong state
* External timeout
* External rejection
* Partial failure
* Old data
* Backward compatibility
* Regression scenarios

Prefer a smaller set of high-value tests over a large set of repetitive tests.

## 30. Review output

Provide:

## Reviewed flow

Describe the behavior and boundaries reviewed.

## Highest-risk edge cases

List the most important scenarios first.

## Edge-case matrix

Include expected behavior, current coverage, risk, and required action.

## Questions

Ask only questions that materially affect the correction.

## Safe corrections

List corrections that can be applied without unclear business decisions.

## Missing tests

List the highest-value tests that should be added.

## 31. Final report after corrections

After applying corrections, provide:

## Edge cases covered

List the scenarios now protected.

## Corrections applied

Describe implementation changes.

## Tests added or updated

Describe the behavior covered.

## Validation executed

List only commands actually executed.

## Remaining risks

State accepted, deferred, or unverified scenarios.

## Final status

Use exactly one:

* Validated
* Partially validated
* Not validated

## 32. Anti-patterns

Do not:

* Generate random edge cases unrelated to the domain.
* Focus only on null values.
* Ignore duplicate execution.
* Ignore concurrency.
* Assume external timeouts are safe.
* Treat every theoretical case as a blocker.
* Reject Unicode without a domain reason.
* Normalize sensitive values.
* Add broad abstractions for one edge case.
* Add locking without a demonstrated race condition.
* Ask questions already answered by the repository.
* Claim coverage without tests.
* Claim validation without running commands.
* Produce an enormous checklist with no prioritization.

The review is valuable only when it identifies realistic failure scenarios and
leads to concrete protection.
