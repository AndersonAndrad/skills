---

name: logging-observability
description: Reviews and improves logs, metrics, tracing, correlation identifiers, auditability, error categorization, production diagnostics, sensitive data redaction, and operational visibility. Use when implementing or modifying APIs, business flows, integrations, background jobs, queues, authentication, payments, persistence, error handling, or any behavior that may need investigation in production.
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Logging and Observability

Ensure that the implementation can be understood, monitored, and investigated
in production.

The objective is not to add logs everywhere.

The objective is to create useful operational signals without exposing
sensitive information, creating excessive noise, or increasing infrastructure
cost unnecessarily.

## 1. Understand the affected operation

Before adding logs or metrics:

1. Identify the operation being performed.
2. Identify the entry point.
3. Identify the main business steps.
4. Identify external dependencies.
5. Identify persistence operations.
6. Identify expected failures.
7. Identify unexpected failures.
8. Identify duplicate execution risks.
9. Identify what an operator would need to know during an incident.
10. Inspect existing observability patterns in the project.

Do not introduce a new logging format if the project already has a standard.

## 2. Define operational questions

Observability should answer practical questions.

Examples:

* Did the request reach the application?
* Which operation was attempted?
* Which resource was affected?
* Which user or tenant initiated the operation?
* Which step failed?
* Was the database operation completed?
* Was an external provider called?
* What response category did the provider return?
* Was the operation retried?
* Was a duplicate prevented?
* How long did the operation take?
* How often is this failure happening?
* Is the issue limited to one tenant, provider, route, or environment?
* Can the same request be traced across services?
* Was the final business operation successful?

Do not log information that does not help answer a real operational question.

## 3. Structured logging

Prefer structured logs over concatenated text.

Good:

```json
{
  "event": "payment_processing_failed",
  "operation": "process_payment",
  "orderId": "order-123",
  "provider": "payment-provider",
  "errorCategory": "provider_timeout",
  "correlationId": "corr-456",
  "durationMs": 3200
}
```

Avoid:

```text
Payment failed for order order-123 because provider timed out after 3200ms
```

Structured fields improve:

* Filtering
* Aggregation
* Dashboards
* Alerts
* Correlation
* Automated analysis
* Incident investigation

Use stable field names across the project.

## 4. Event naming

Use clear and consistent event names.

Prefer a predictable format such as:

```text
resource_action_result
```

Examples:

```text
user_created
user_creation_failed
payment_processing_started
payment_processing_completed
payment_processing_failed
webhook_duplicate_ignored
authentication_failed
inventory_reservation_conflict
```

Avoid vague events such as:

```text
error
something_failed
request_problem
service_issue
```

Event names should communicate:

* What happened
* To which domain
* At which stage
* With which outcome

## 5. Log levels

Use log levels intentionally.

### Trace

Use for extremely detailed diagnostic information.

Usually disabled in production.

Examples:

* Internal decision branches
* Low-level protocol details
* Detailed flow tracing

Do not log sensitive payloads even at trace level.

### Debug

Use for development and temporary diagnostics.

Examples:

* Internal state transitions
* Cache decisions
* Non-sensitive request metadata
* Adapter behavior
* Feature flag evaluation

Debug logs should not be required for normal production monitoring.

### Info

Use for meaningful successful business or operational events.

Examples:

* Service started
* User creation completed
* Payment processed
* Job completed
* Integration authenticated
* State transition completed

Do not log every internal function call as info.

### Warn

Use when something unexpected happened but the operation may continue or recover.

Examples:

* Retry scheduled
* Deprecated field received
* Rate limit approaching
* Cache unavailable with database fallback
* Duplicate request ignored
* Optional provider unavailable
* Unexpected but handled state

Warnings should indicate a condition that deserves attention.

### Error

Use when an operation failed and requires investigation.

Examples:

* Business operation failed unexpectedly
* Database operation failed
* External provider failed after retries
* Message moved to dead-letter queue
* Unhandled exception
* Persistent inconsistency detected

Do not use error for expected validation failures unless project policy
explicitly requires it.

### Fatal

Use only when the process or service cannot safely continue.

Examples:

* Required configuration missing
* Database unavailable at startup
* Critical dependency initialization failed
* Corrupted application state

Do not use fatal for a failed individual request.

## 6. Avoid log level inflation

Incorrect log levels cause:

* Alert fatigue
* Increased costs
* Hidden real incidents
* Misleading dashboards
* Difficult investigation

Examples:

A user entering an invalid email is normally not an error-level event.

A provider timing out after all retries may be an error.

A first failed attempt followed by a successful retry may be a warning or debug,
depending on project policy.

A duplicated webhook safely ignored may be info or warning, not necessarily
an error.

Choose the level based on operational impact.

## 7. Correlation identifiers

Preserve correlation across the complete flow.

Possible identifiers include:

* Correlation ID
* Request ID
* Trace ID
* Span ID
* Operation ID
* Job ID
* Message ID
* Event ID
* External provider request ID
* Idempotency key

When receiving an existing identifier:

* Validate its format if necessary.
* Preserve it.
* Propagate it to downstream calls.
* Include it in relevant logs.
* Return it in responses when project conventions allow.

When none exists:

* Generate one at the system boundary.
* Reuse the same value throughout the operation.

Do not generate a new correlation identifier at every internal layer.

## 8. Business identifiers

Include identifiers that help locate the affected resource.

Examples:

* User ID
* Account ID
* Order ID
* Payment ID
* Transaction ID
* Organization ID
* Tenant ID
* Job ID
* Webhook event ID
* External protocol
* Provider operation ID

Prefer internal stable identifiers.

Be careful with:

* Email
* CPF
* CNPJ
* Telephone
* Full name
* Address
* Card information
* Bank information

Use these only when necessary and allowed by project policy.

Prefer masked, hashed, or indirect identifiers.

## 9. Sensitive data

Never log:

* Passwords
* Password hashes
* Access tokens
* Refresh tokens
* API keys
* Private keys
* Authentication cookies
* Session tokens
* OTP codes
* Verification codes
* Cryptographic signatures
* Full authorization headers
* Database connection strings
* Secrets from environment variables
* Full card numbers
* CVV
* Full bank credentials
* Complete biometric data
* Sensitive documents
* Private file contents

Do not log full request and response objects by default.

Objects may contain sensitive nested fields.

## 10. Personally identifiable information

Treat personal data carefully.

Examples:

* Name
* Email
* CPF
* CNPJ when linked to individuals
* Telephone
* Address
* Date of birth
* Location
* IP address
* Device identifier
* Financial information
* Documents

Before logging personal data, ask:

* Is it necessary for investigation?
* Can an internal identifier replace it?
* Can it be masked?
* Can it be hashed?
* Can only part of it be logged?
* What is the retention period?
* Who can access the logs?

Examples of masking:

```text
Email: a***@example.com
CPF: ***.***.***-12
Telephone: ******7890
Card: **** **** **** 1234
```

Do not assume hashing always anonymizes data.

Low-entropy values may still be reversible through guessing.

## 11. Redaction

Use centralized redaction where possible.

Common fields to redact:

```text
password
passwordConfirmation
token
accessToken
refreshToken
authorization
cookie
apiKey
secret
privateKey
otp
verificationCode
signature
clientSecret
connectionString
```

Redaction should work recursively.

Verify:

* Nested objects
* Arrays
* Request headers
* Query parameters
* Error metadata
* Provider payloads
* Serialized objects
* Exception causes

Do not depend only on developers remembering to omit fields manually.

## 12. Request logging

Request logs may include:

* HTTP method
* Route template
* Status code
* Duration
* Request ID
* Correlation ID
* Authenticated principal ID
* Tenant ID
* User agent category
* Response size
* Error category

Prefer route templates:

```text
/users/:id
```

instead of full paths containing identifiers:

```text
/users/123456
```

Avoid logging:

* Complete body
* Complete query string
* Authorization headers
* Cookies
* Multipart file content
* Raw personal data
* Raw provider responses

## 13. Response logging

Do not log complete responses by default.

Instead log:

* Result category
* Status code
* Business outcome
* Resource ID
* Record count
* Duration
* Error category
* Provider status category

Example:

```json
{
  "event": "account_creation_completed",
  "accountId": "acc-123",
  "status": "created",
  "durationMs": 418
}
```

Avoid:

```json
{
  "event": "account_creation_completed",
  "response": {
    "name": "Full Name",
    "document": "00000000000",
    "address": {},
    "token": "secret"
  }
}
```

## 14. Error logging

An error log should contain enough context to investigate the failure.

Include when applicable:

* Event
* Operation
* Error category
* Error code
* Internal error type
* Safe error message
* Resource identifiers
* Correlation ID
* Provider
* Provider request ID
* Attempt number
* Duration
* Retry decision
* Stack trace for unexpected errors

Do not expose stack traces to end users.

Do not duplicate the same error at every layer.

## 15. Avoid duplicate logging

A common anti-pattern is logging and rethrowing the same error in every layer.

Example:

```text
Controller logs error
Service logs same error
Repository logs same error
Global handler logs same error
```

This produces four logs for one failure.

Prefer:

* Lower layer adds context to the error.
* Boundary or global handler records the final failure.
* Specific layers log only when they make a meaningful decision.

Meaningful decisions include:

* Retry scheduled
* Fallback activated
* Error transformed
* Partial recovery completed
* Duplicate ignored
* Compensating action executed

## 16. Error categories

Create stable error categories that support dashboards and alerts.

Examples:

```text
validation_failed
authentication_failed
authorization_failed
resource_not_found
resource_conflict
database_timeout
database_constraint_violation
provider_timeout
provider_rejected
provider_unavailable
rate_limited
duplicate_operation
internal_error
```

Do not use raw exception messages as metric labels or categories.

Exception messages may contain:

* Dynamic identifiers
* Sensitive data
* High cardinality
* Implementation details

## 17. Metrics

Use metrics for aggregate behavior.

Common metric types:

### Counter

Use for events that accumulate.

Examples:

```text
requests_total
payments_processed_total
payments_failed_total
webhooks_received_total
duplicate_operations_total
authentication_failures_total
```

### Gauge

Use for values that increase and decrease.

Examples:

```text
queue_depth
active_connections
open_orders
running_jobs
available_workers
```

### Histogram or timer

Use for distributions and duration.

Examples:

```text
request_duration_ms
provider_duration_ms
database_query_duration_ms
job_processing_duration_ms
payload_size_bytes
```

Do not use logs as a replacement for every metric.

Do not create a metric for every minor internal event.

## 18. Metric labels

Metric labels must have controlled cardinality.

Usually safe labels:

* Environment
* Service
* Route template
* HTTP method
* Status category
* Provider
* Operation
* Error category
* Queue name
* Result

Dangerous high-cardinality labels:

* User ID
* Request ID
* Transaction ID
* Email
* Full URL
* Exception message
* Timestamp
* Free-form text
* Payload content

High-cardinality labels can make monitoring expensive and unstable.

Identifiers belong in logs and traces, not usually in metric labels.

## 19. Tracing

Use distributed tracing when a request crosses:

* Multiple services
* Databases
* Queues
* External providers
* Background workers
* Serverless functions

A trace should reveal:

* Entry point
* Internal steps
* Database calls
* External calls
* Queue publication
* Queue consumption
* Duration of each stage
* Failure location

Propagate trace context through:

* HTTP headers
* Message metadata
* Queue attributes
* Background job context
* Scheduled processing

Do not include sensitive payloads in span attributes.

## 20. Spans

Create spans around meaningful operations.

Examples:

```text
database.user.find
database.payment.create
provider.fitbank.execute
queue.payment.publish
queue.payment.consume
business.order.finalize
```

Avoid spans around trivial local functions.

Add safe attributes such as:

* Operation
* Provider
* Resource type
* Result category
* Retry count
* Status
* Tenant ID when policy permits

Avoid high-cardinality or sensitive attributes.

## 21. External integrations

For external provider calls, record:

* Provider
* Operation
* Start time
* Duration
* Result category
* HTTP status
* Provider business status
* Provider request ID
* Internal correlation ID
* Retry count
* Timeout
* Circuit breaker state when applicable

Do not assume HTTP success means business success.

Example:

```json
{
  "event": "provider_request_completed",
  "provider": "fitbank",
  "operation": "create_account",
  "httpStatus": 200,
  "businessSuccess": false,
  "providerCode": "ISE0014",
  "durationMs": 824,
  "correlationId": "corr-123"
}
```

Do not log the complete provider payload unless it is explicitly sanitized.

## 22. Provider error mapping

Map provider-specific failures into stable internal categories.

Example:

```text
Provider code: ISE0014
Internal category: provider_validation_failed
```

Preserve the provider code as a field when safe.

This allows:

* Provider-specific investigation
* Stable internal dashboards
* Better user-facing messages
* Easier migration between providers

Do not expose raw provider errors directly to users unless they are safe and
understandable.

## 23. Retries

Every retry decision should be observable.

Record:

* Operation
* Attempt number
* Maximum attempts
* Retry reason
* Delay
* Whether the error is retryable
* Provider or dependency
* Idempotency protection
* Final outcome

Example:

```json
{
  "event": "provider_retry_scheduled",
  "provider": "fitbank",
  "operation": "create_transfer",
  "attempt": 2,
  "maxAttempts": 3,
  "retryReason": "provider_timeout",
  "delayMs": 1000,
  "idempotencyKeyPresent": true
}
```

Do not log retries as errors when recovery is expected.

Log the final exhausted retry as an error.

## 24. Queues and background jobs

For queues and jobs, record:

* Job ID
* Message ID
* Event ID
* Queue name
* Operation
* Attempt
* Maximum attempts
* Processing start
* Processing completion
* Duration
* Duplicate detection
* Retry
* Dead-letter routing
* Correlation ID
* Result category

Important events:

```text
job_received
job_processing_started
job_completed
job_failed
job_retry_scheduled
job_duplicate_ignored
job_moved_to_dead_letter
```

Do not log the full message payload by default.

## 25. Webhooks

For webhooks, record:

* Provider
* Event type
* Provider event ID
* Internal correlation ID
* Signature validation result
* Duplicate detection
* Processing result
* Duration
* Retry or redelivery information

Do not log:

* Full signature
* Complete payload
* Secrets
* Sensitive personal or financial data

A webhook should be traceable even when delivered multiple times.

## 26. Database operations

Do not log every database query in production by default.

Log or trace:

* Slow queries
* Failed operations
* Constraint violations
* Timeouts
* Transaction rollback
* Deadlocks
* Retry decisions
* Unexpected record counts
* Data consistency failures

Include:

* Operation type
* Entity or collection
* Duration
* Result category
* Safe resource identifiers
* Correlation ID

Do not log raw queries containing sensitive values.

## 27. Transactions

For important transactional operations, make observable:

* Transaction started
* Transaction committed
* Transaction rolled back
* Rollback reason
* Compensating action
* Partial external side effect
* Recovery decision

Do not log normal internal transaction details excessively.

Focus on outcomes that affect data integrity.

## 28. Cache observability

Record when relevant:

* Cache hit
* Cache miss
* Cache fallback
* Cache invalidation
* Cache error
* Stale value detected
* Cache stampede protection
* Serialization failure

Avoid info-level logging for every cache hit in high-volume systems.

Use metrics for aggregate cache behavior.

Useful metrics:

```text
cache_hits_total
cache_misses_total
cache_errors_total
cache_operation_duration_ms
```

## 29. Authentication observability

Record authentication events carefully.

Possible events:

```text
authentication_succeeded
authentication_failed
token_expired
token_revoked
session_terminated
password_reset_requested
password_reset_completed
mfa_challenge_failed
```

Avoid exposing:

* Whether a specific email exists
* Password content
* Token content
* OTP codes
* Internal security rules

Use safe identifiers and rate-limit relevant events.

Authentication failure logs should support detecting brute-force attempts
without enabling user enumeration.

## 30. Authorization observability

Record important authorization failures when useful.

Include:

* Principal ID
* Resource type
* Operation
* Permission category
* Tenant ID
* Correlation ID
* Result

Avoid logging unnecessary sensitive resource details.

Differentiate:

* Unauthenticated
* Authenticated but unauthorized
* Resource ownership violation
* Tenant boundary violation
* Administrative policy denial

## 31. Audit logs

Audit logs are different from application logs.

Application logs answer:

> Why did the system fail?

Audit logs answer:

> Who did what, to which resource, and when?

Audit events may include:

* User created
* User disabled
* Permission changed
* Payment approved
* Refund executed
* Sensitive data accessed
* Configuration changed
* Credential generated
* Resource deleted
* Administrative action performed

An audit record should include:

* Actor
* Action
* Resource
* Timestamp
* Result
* Source
* Correlation ID
* Relevant before and after values when safe and required

Do not place secrets or complete sensitive payloads in audit logs.

Audit records may require stronger retention and access controls.

## 32. Before and after values

When auditing changes, do not blindly store complete objects.

Prefer explicit relevant fields.

Example:

```json
{
  "event": "user_role_changed",
  "userId": "user-123",
  "previousRole": "user",
  "newRole": "admin",
  "changedBy": "admin-456"
}
```

Avoid:

```json
{
  "before": { "completeUserObject": "..." },
  "after": { "completeUserObject": "..." }
}
```

Complete objects may contain sensitive or irrelevant data.

## 33. Frontend observability

Frontend monitoring may include:

* Page load failures
* API request failures
* Unhandled exceptions
* Failed form submission
* Critical user flow abandonment
* Performance metrics
* Resource loading failures
* Feature availability

Avoid recording:

* Passwords
* Full form contents
* Tokens
* Complete URLs with sensitive query parameters
* Sensitive DOM content
* User keystrokes

Do not add analytics to sensitive actions without explicit product and privacy
requirements.

## 34. User-facing error references

For unexpected errors, the interface may show a safe reference.

Example:

```text
Não foi possível concluir a operação.
Código de referência: 8F2K-19A
```

The reference should map to:

* Correlation ID
* Error ID
* Trace ID
* Incident lookup identifier

Do not expose:

* Stack trace
* Database error
* Provider credentials
* Internal architecture
* Sensitive resource IDs

## 35. Sampling

High-volume logs and traces may require sampling.

Consider sampling for:

* Successful repetitive requests
* Health checks
* Cache hits
* Polling
* High-frequency background jobs
* Repeated expected validation errors

Do not aggressively sample:

* Errors
* Security events
* Financial operations
* Rare failures
* Critical state transitions
* Audit events

Sampling must not make important incidents impossible to investigate.

## 36. Log volume and cost

Review whether the implementation may create excessive telemetry.

Risks include:

* Logging inside large loops
* Logging every item in a batch
* Logging complete payloads
* Logging repeated retries
* Duplicate logs across layers
* Large stack traces for expected errors
* High-cardinality metric labels
* Debug logs enabled in production

Prefer aggregated logs.

Example:

```json
{
  "event": "batch_processing_completed",
  "total": 1000,
  "succeeded": 980,
  "failed": 20
}
```

Instead of one info log per item.

Log individual failures when investigation requires it.

## 37. Health checks

Health checks should distinguish:

* Process alive
* Application ready
* Critical dependency available
* Optional dependency degraded

Common categories:

### Liveness

Is the process running?

### Readiness

Can the application safely receive traffic?

### Dependency health

Are required dependencies working?

Do not make liveness fail because an optional provider is unavailable.

Avoid placing sensitive infrastructure details in public health responses.

## 38. Alerts

Alerts should represent actionable conditions.

Good alert candidates:

* Sustained error rate increase
* High provider timeout rate
* Queue backlog growth
* Dead-letter messages
* Database saturation
* Authentication abuse
* Repeated transaction rollback
* Payment inconsistency
* Missing scheduled processing
* Service unavailable
* Significant latency degradation

Avoid alerts for:

* Single expected validation failure
* One transient retry
* Normal cache miss
* Routine user error

An alert should answer:

* What happened?
* Why does it matter?
* Which service or flow is affected?
* What should the responder inspect first?

## 39. Dashboards

For relevant features, identify whether dashboards should include:

* Request volume
* Success rate
* Failure rate
* Latency
* Error categories
* Provider behavior
* Retry count
* Queue depth
* Processing duration
* Duplicate operations
* User-facing failures
* Business outcome counts

Do not create dashboards automatically for trivial changes.

Recommend them when the feature is operationally important.

## 40. Feature flags and rollout

When a feature uses a flag, record when useful:

* Flag name
* Evaluated result
* Variation
* Rollout cohort
* Fallback behavior
* Evaluation failure

Do not log user-sensitive targeting data unnecessarily.

During rollout, observability should allow comparison between:

* Enabled and disabled populations
* Old and new behavior
* Success and failure rates
* Performance differences

## 41. Deployment observability

For risky changes, consider whether telemetry can identify:

* Deployment version
* Commit hash
* Build version
* Environment
* Region
* Instance
* Feature flag state

This supports comparison before and after deployment.

Do not log build metadata in every custom event when it is already attached
automatically by the platform.

## 42. Data retention

Telemetry should have an intentional retention policy.

Consider:

* Application logs
* Security logs
* Audit logs
* Metrics
* Traces
* Provider payload archives
* Error events

Retention depends on:

* Operational needs
* Cost
* Privacy
* Compliance
* Investigation window
* Legal requirements

Do not retain sensitive data indefinitely merely because storage is available.

## 43. Failure scenarios to review

For every relevant operation, consider:

* Validation failure
* Authentication failure
* Authorization failure
* Resource not found
* Conflict
* Database timeout
* Database constraint violation
* External provider timeout
* Provider business rejection
* Provider malformed response
* Duplicate execution
* Retry exhaustion
* Queue redelivery
* Partial completion
* Unexpected exception
* Rollback failure
* Compensating action failure

For each scenario, determine:

* Log event
* Level
* Fields
* Metric
* Trace behavior
* Alert relevance
* User-facing reference
* Redaction needs

## 44. Observability review matrix

Use a focused matrix when helpful:

| Scenario          | Signal               | Level        | Required context                              | Sensitive risk   | Action              |
| ----------------- | -------------------- | ------------ | --------------------------------------------- | ---------------- | ------------------- |
| Provider timeout  | Log + metric + trace | Error        | Provider, operation, correlation ID, duration | Provider payload | Add sanitized event |
| Duplicate webhook | Log + counter        | Info         | Event ID, provider, result                    | Signature        | Redact signature    |
| Invalid input     | Validation metric    | Info or none | Route, field category                         | User input       | Do not log value    |

Do not create a large matrix for trivial flows.

## 45. Implementation process

When applying this skill:

1. Inspect existing logger, tracing, and metrics infrastructure.
2. Identify the affected operational questions.
3. Identify existing project event conventions.
4. Add only the necessary signals.
5. Use stable structured fields.
6. Redact sensitive data.
7. Avoid duplicate logging.
8. Add tests for important log behavior when practical.
9. Validate that log output is useful.
10. Review telemetry cost and cardinality.
11. Run applicable tests.
12. Review the final diff.

Do not add a new observability dependency without checking whether existing
infrastructure already supports the requirement.

## 46. Testing observability

When practical, test:

* Correct event emitted
* Correct log level
* Correlation ID propagated
* Sensitive fields redacted
* Provider code preserved safely
* Retry attempt recorded
* Duplicate operation recorded
* Audit record created
* Unexpected error includes stack internally
* User response does not expose internal error
* Metrics use bounded labels
* Tracing context propagated

Avoid tests tightly coupled to irrelevant log formatting.

Test stable events and fields.

## 47. Review output

Provide:

## Operational questions

List what production investigation should be able to answer.

## Existing observability

Describe the signals already available.

## Missing signals

List missing logs, metrics, traces, or audit records.

## Sensitive data risks

Identify telemetry that may expose protected information.

## Recommendations

Prioritize concrete changes.

## Validation plan

Describe how the new observability will be verified.

## 48. Final report

After applying changes, provide:

## Signals added or changed

Describe logs, metrics, traces, or audit events.

## Correlation

Describe identifiers propagated through the flow.

## Redaction

Describe sensitive fields protected.

## Operational value

Explain which production questions can now be answered.

## Validation executed

List only commands and checks actually executed.

## Remaining limitations

State missing dashboards, alerts, infrastructure, or manual validation.

## Final status

Use exactly one:

* Validated
* Partially validated
* Not validated

## 49. Anti-patterns

Do not:

* Log complete request and response objects by default.
* Log passwords, tokens, secrets, or authorization headers.
* Add logs to every function.
* Log the same error in every layer.
* Use error level for expected user validation.
* Use dynamic exception messages as metric labels.
* Put user IDs or request IDs in metric labels.
* Assume HTTP success means business success.
* Ignore provider business codes.
* Generate a new correlation ID in every layer.
* Add observability without considering cost.
* Add dashboards for trivial changes.
* Expose stack traces to users.
* Store complete objects in audit logs.
* Claim a flow is observable without identifying what questions can be answered.
* Add a new monitoring dependency without checking existing infrastructure.
* Treat logs as a replacement for tests.

Good observability must improve production understanding without creating
security, privacy, noise, or cost problems.
