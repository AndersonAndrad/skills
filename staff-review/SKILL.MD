---

name: staff-pr-review
description: Reviews the complete current change as a Staff Engineer before final validation. Evaluates architecture, security, performance, maintainability, readability, complexity, test coverage, bugs, regressions, production impact, compatibility, observability, and operational risk. Use after implementation and before applying final validation. By default, review only and do not modify files unless explicitly instructed.
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Staff PR Review

Review the complete current change as if it were being evaluated by a Staff
Engineer before production approval.

This review is not limited to compilation, linting, tests, or formatting.

The objective is to identify problems that automated validation and existing
tests may not detect.

By default, do not modify files.

Only review, explain findings, classify risks, and recommend corrections unless
the developer explicitly requests implementation.

## 1. Review the complete change

Before producing findings:

1. Read the complete diff.
2. Identify all changed files.
3. Read the surrounding code, not only changed lines.
4. Trace the affected execution paths.
5. Inspect related interfaces, contracts, services, adapters, and persistence.
6. Inspect existing tests.
7. Inspect similar implementations in the repository.
8. Identify public contracts affected by the change.
9. Identify business rules affected by the change.
10. Identify production flows affected by the change.

Do not review isolated snippets when the complete diff is available.

Do not assume a changed function is safe without examining its callers and
consumers.

## 2. Reconstruct the intent

Determine:

* What problem the change is attempting to solve
* Which behavior is intentionally changing
* Which behavior must remain unchanged
* Which assumptions were made
* Which failure modes were considered
* Which failure modes were ignored
* Whether the implementation actually satisfies the requirement

Separate findings internally into:

### Confirmed

Supported directly by code, tests, contracts, or documented behavior.

### Inferred

Likely based on surrounding implementation.

### Unknown

Cannot be determined safely from the available evidence.

Do not report speculative problems as confirmed defects.

## 3. Architecture

Review:

* Responsibility boundaries
* Separation of concerns
* Layering
* Domain leakage
* Infrastructure leakage
* Coupling
* Cohesion
* Dependency direction
* Abstraction quality
* Module boundaries
* Service boundaries
* Reuse versus duplication
* Provider-specific code
* Public contract placement
* State ownership
* Side-effect ownership
* Error ownership
* Transaction boundaries

Ask:

* Is the rule implemented in the correct layer?
* Does the change create unnecessary coupling?
* Does a low-level module know too much about business behavior?
* Does provider-specific behavior leak into the domain?
* Is an abstraction hiding complexity or merely moving it?
* Is the solution unnecessarily generic?
* Is important behavior distributed across unrelated files?
* Could this design make future changes harder?

Do not recommend architecture changes merely for stylistic preference.

Report only changes with practical impact.

## 4. Security

Review:

* Authentication
* Authorization
* Tenant isolation
* Resource ownership
* Input validation
* Output exposure
* Injection risks
* XSS
* SSRF
* Path traversal
* Unsafe redirects
* Deserialization
* File handling
* Secret management
* Token handling
* Cryptographic verification
* Replay attacks
* Brute-force protection
* Rate limiting
* Sensitive logging
* Personal data exposure
* Mass assignment
* Privilege escalation
* Insecure defaults

Check both direct and indirect access paths.

Do not assume authorization is safe because the frontend hides an action.

Do not assume a token is valid merely because it can be decoded.

## 5. Performance

Review:

* N+1 operations
* Repeated network calls
* Repeated database calls
* Unbounded loops
* Unbounded collections
* Large payloads
* Expensive serialization
* Blocking operations
* Synchronous work in critical paths
* Unnecessary re-renders
* Inefficient state updates
* Missing pagination
* Missing indexes
* Excessive memory use
* Cold-start impact
* Bundle impact
* Cache misuse
* Repeated parsing or transformation
* Excessive logging
* High-cardinality metrics

Distinguish actual risks from theoretical micro-optimizations.

Do not report minor performance preferences without measurable or likely
production impact.

## 6. Maintainability

Review:

* Responsibility clarity
* Function size
* Module size
* Naming
* Hidden behavior
* Implicit contracts
* Duplicated rules
* Magic values
* Hard-coded business rules
* Excessive branching
* Fragile conditionals
* Temporal coupling
* Configuration spread
* Testability
* Dependency isolation
* Documentation needs
* Future change cost

Ask:

* Can another engineer safely change this in six months?
* Is the business rule visible?
* Is the implementation relying on undocumented assumptions?
* Will a small change require editing many unrelated places?
* Does the code make invalid states easy to represent?
* Does the implementation expose too many internal details?

## 7. Readability and naming

Names must communicate intent without requiring the reader to inspect distant
context.

Review:

* Single-letter variables
* Unnecessary abbreviations
* Generic names
* Misleading names
* Names that describe type instead of purpose
* Names that no longer match behavior
* Boolean names without clear polarity
* Functions that hide side effects
* Functions with vague verbs
* Parameters whose meaning requires inspecting their types

Prefer:

```ts
const loadAuditList = async (
    filter: FitbankWebhookAuditFilter,
) => {};
```

Avoid:

```ts
const loadList = async (
    f: FitbankWebhookAuditFilter,
) => {};
```

Avoid names such as:

```text
f
d
obj
arr
tmp
val
data
info
result
item
```

when a specific domain name is available.

Established abbreviations such as `id`, `url`, `api`, `http`, and project-wide
terms may be accepted.

The goal is not longer names.

The goal is immediate comprehension with minimal cognitive effort.

## 8. Complexity

Review:

* Nested conditions
* Excessive branching
* Repeated boolean expressions
* Large functions
* Excessive parameters
* Hidden state
* Multiple responsibilities
* Complex generic types
* Complex inheritance
* Excessive indirection
* Premature abstractions
* Clever code
* Repeated transformations
* Difficult control flow

Ask:

* Can the implementation be understood from top to bottom?
* Are branches mutually exclusive and complete?
* Are invalid states explicit?
* Is complexity inherent to the domain or introduced by the solution?
* Could a simpler implementation preserve the same behavior?

Do not recommend extracting functions merely to reduce line count.

Extraction must improve meaning, reuse, testing, or responsibility boundaries.

## 9. Correctness and possible bugs

Actively attempt to prove the implementation wrong.

Review:

* Incorrect conditions
* Inverted conditions
* Missing branches
* Incorrect defaults
* Null and undefined behavior
* Empty values
* Boundary values
* Incorrect transformations
* Stale state
* Incorrect asynchronous behavior
* Missing await
* Swallowed errors
* Incorrect error propagation
* Incorrect state transition
* Incorrect query filters
* Partial updates
* Duplicate execution
* Race conditions
* Transaction boundaries
* Timezone behavior
* Floating-point behavior
* Incorrect type assumptions
* Incorrect provider response interpretation
* Incorrect success criteria

Do not trust tests as proof that every behavior is correct.

Tests may encode the same incorrect assumption as the implementation.

## 10. Regressions

Identify behavior that may have been unintentionally changed.

Review:

* Existing consumers
* Existing API clients
* Old records
* Optional fields
* Default behavior
* Error formats
* Status codes
* Ordering
* Pagination
* Caching
* Authentication behavior
* Authorization behavior
* Existing UI state
* Existing event payloads
* Existing queue messages
* Existing integrations
* Existing configuration
* Rolling deployment compatibility

Search for all usages of changed contracts, types, functions, and fields.

Do not assume all services and clients deploy simultaneously.

## 11. Tests

Evaluate whether tests prove the behavior rather than merely execute the code.

Review:

* Happy path
* Failure paths
* Invalid input
* Boundary values
* Permissions
* Ownership
* Duplicate execution
* Concurrency
* External timeouts
* Provider rejection
* Database failure
* Partial failure
* Existing-data behavior
* Backward compatibility
* Regression coverage
* State transitions
* Error mapping
* Sensitive data redaction

Check whether tests:

* Assert meaningful outcomes
* Could pass while behavior remains wrong
* Mock too much
* Reproduce production behavior
* Cover the actual regression
* Are deterministic
* Verify side effects
* Verify absence of forbidden side effects

Do not consider line coverage sufficient evidence.

## 12. Production impact

Review what happens after deployment.

Consider:

* Database migrations
* Existing data
* Rolling deployment
* Mixed application versions
* Cache compatibility
* Queue backlog
* Old queue messages
* External provider behavior
* Rate limits
* Timeouts
* Retry amplification
* Log volume
* Metric cardinality
* Deployment configuration
* Environment variables
* Resource consumption
* Failure recovery
* Rollback
* Monitoring
* Support investigation

Ask:

* Can the change be rolled back safely?
* Does rollback also require reverting data?
* Can a timeout create duplicate side effects?
* Will operators know when the flow fails?
* Can one tenant or user affect others?
* Could the change increase production cost significantly?
* Are dashboards or alerts needed?
* Will old data break the new implementation?

## 13. Observability

Review:

* Structured logs
* Correlation IDs
* Trace propagation
* Provider identifiers
* Stable error categories
* Retry visibility
* Duplicate-operation visibility
* Audit requirements
* Sensitive-data redaction
* Alertability
* Production diagnostics

Determine whether an engineer could answer:

* What failed?
* Where did it fail?
* Which operation was affected?
* Was the provider called?
* Was the operation retried?
* Was duplicate execution prevented?
* Which version was deployed?
* Can the issue be correlated across services?

Do not demand logs in every function.

Require only signals that materially improve investigation.

## 14. Dependency impact

When dependencies changed, review:

* Necessity
* Version
* Lockfile
* Transitive dependencies
* Security advisories
* Install scripts
* Runtime compatibility
* License
* Bundle impact
* Native code
* Telemetry
* Provider lock-in
* Upgrade path

Do not approve unexplained lockfile changes.

## 15. Severity classification

Classify every finding using practical impact.

### Critical

Likely to cause:

* Unauthorized access
* Secret exposure
* Financial loss
* Irreversible data corruption
* Remote code execution
* Severe tenant isolation failure
* Major production outage
* Critical compliance breach

### High

Likely to cause:

* Incorrect business processing
* Duplicate financial or external operations
* Significant data inconsistency
* Breaking public contracts
* Major regression
* Persistent production failure
* Serious security weakness
* Unsafe migration or rollback

### Medium

Likely to cause:

* Edge-case failures
* Operational difficulty
* Incomplete error handling
* Weak validation
* Maintainability problems
* Performance degradation
* Missing important tests
* User-visible inconsistency

### Low

Includes:

* Local readability issues
* Minor naming problems
* Small maintainability improvements
* Non-blocking simplifications
* Minor consistency issues

Do not inflate severity to make findings appear important.

## 16. Finding format

For every finding, provide:

### Title

A concise statement of the problem.

### Severity

Use exactly one:

* Critical
* High
* Medium
* Low

### Evidence

Identify:

* File
* Function or relevant location
* Current behavior
* Supporting code or flow

### Technical explanation

Explain why the implementation is problematic.

### Practical impact

Explain what may happen in production, development, testing, or maintenance.

### Recommended correction

Provide a concrete correction.

### Suggested validation

Explain how the correction should be verified.

Do not report vague findings such as:

> This could be improved.

A finding must describe a concrete problem or risk.

## 17. Avoid false positives

Do not report:

* Pure stylistic preferences
* Hypothetical problems unsupported by the code
* Framework conventions as defects
* Intentional behavior supported by tests and contracts
* Micro-optimizations without impact
* Missing abstraction when duplication is trivial
* Missing comments for self-explanatory code
* Longer names solely because they are longer
* Tests for impossible states
* Problems already prevented by another layer

When evidence is incomplete, classify the item as a question or uncertainty,
not as a confirmed defect.

## 18. Questions

When expected behavior cannot be determined safely, ask a targeted question.

Use:

### Question

What behavior must be confirmed?

### Why it matters

What practical outcome changes based on the answer?

### Current evidence

What does the code currently indicate?

### Options

List likely alternatives when useful.

Do not classify unanswered business questions as implementation bugs.

## 19. Review-only behavior

Unless explicitly asked to modify files:

* Do not edit code.
* Do not add tests.
* Do not apply formatting.
* Do not update dependencies.
* Do not change configuration.
* Do not create commits.

The review should remain independent from the implementation.

This prevents the reviewer from silently hiding findings by correcting them
during analysis.

## 20. Required output

Provide:

# Staff PR Review

## Scope reviewed

List the changed areas and flows inspected.

## Overall assessment

Summarize whether the change appears safe, risky, incomplete, or ready.

### Authentication and authorization source tracing

For every protected flow, trace the complete identity path:

```text
Credential
    ↓
Validation strategy or middleware
    ↓
Validated claims
    ↓
Authenticated context creation
    ↓
Request or execution-context injection
    ↓
Guard or policy
    ↓
Controller
    ↓
Business operation
```

Verify:

* The credential is verified before claims are trusted.
* The injected context has an explicit type and purpose.
* There is one canonical property for the authenticated actor.
* Guards read the property actually populated by authentication.
* Controllers do not reconstruct or duplicate role checks.
* The same authorization policy is not copied across endpoints.
* Client input cannot replace JWT-derived identity or permissions.
* Changes to roles, tenants, or permissions have an intentional freshness
  strategy.
* Guards and policies have isolated tests.

A mismatch such as:

```text
Authentication populates request.backOfficeClient
Guard reads request.backofficeUser
```

must be reported because it may produce:

* Incorrect denial
* Authorization bypass when fallback behavior exists
* Divergent endpoint behavior
* Untested production-only failure
* Continued duplication of inline checks

Do not classify this only as naming inconsistency.

It is an authentication-context contract defect.


## Findings

Order findings by severity:

1. Critical
2. High
3. Medium
4. Low

For every finding include:

* Severity
* Evidence
* Technical explanation
* Practical impact
* Recommended correction
* Suggested validation

## Missing tests

List important scenarios not currently proven.

## Production considerations

Describe deployment, compatibility, observability, rollback, and operational
concerns.

## Questions

List unresolved behavior questions separately.

## Positive observations

Mention relevant strengths, but do not use them to dilute important findings.

## Recommendation

Use exactly one:

* Reject until corrected
* Approve after required corrections
* Approve with follow-up improvements
* Approve

Do not approve while unresolved Critical or High findings remain.

## 21. Anti-patterns

Do not:

* Review only changed lines.
* Trust compilation as proof of correctness.
* Trust tests as proof that requirements are correct.
* Ignore surrounding code.
* Ignore public consumers.
* Ignore production behavior.
* Ignore old data.
* Ignore retries and duplicate execution.
* Ignore permissions.
* Report speculation as fact.
* Inflate severity.
* Focus only on formatting.
* Recommend broad refactoring unrelated to the change.
* Hide findings by modifying files during review.
* Approve because automated checks passed.
* Produce findings without evidence.
* Approve with unresolved Critical or High findings.

The purpose of this review is to discover what ordinary implementation and
automated validation may have missed.

## GitHub-style inline review

The final review output must resemble a GitHub pull request review.

Whenever a finding can be associated with a specific code location, report it
as an inline review comment using:

* File path
* Start line
* End line
* Severity
* Review comment
* Practical impact
* Suggested correction
* Suggested validation

Use the smallest line range that contains enough context to understand the
finding.

Prefer:

```text
application/users/users.controller.ts
Lines 42-47
```

Instead of:

```text
application/users/users.controller.ts
Lines 1-250
```

Do not reference the entire file when the finding belongs to a specific
condition, function, decorator, parameter, query, or statement.

### Line reference source

Use line numbers from the current diff or current file content.

Before reporting a line:

1. Open the current version of the file.
2. Confirm the relevant line range.
3. Confirm that the referenced code still exists.
4. Confirm that the line range includes the problematic implementation.
5. Avoid referencing stale line numbers from an earlier version of the diff.

When exact line numbers cannot be determined, use the nearest stable code
location:

```text
application/users/users.controller.ts
Function: updateUser
Near: inline role validation
```

Do not invent line numbers.

### Review comment format

Use the following structure for every inline finding:

```text
### `path/to/file.ts`

**Lines:** 42-47  
**Severity:** High

**Review comment**

The authorization rule is implemented inline in this controller even though
the same `super_admin` policy exists in another controller. This creates two
independent copies of the same security rule.

The duplicated implementations already use different authenticated-context
properties: this controller reads `request.backOfficeClient`, while the
existing guard reads `request.backofficeUser`.

**Impact**

A future endpoint may copy either implementation, causing inconsistent access
control, incorrect denials, or an authorization bypass if one request property
is populated differently.

**Suggested correction**

Define one canonical authenticated backoffice context, update the existing
`BackofficeSuperAdminGuard` to read that context, and apply the guard with
`@UseGuards(BackofficeSuperAdminGuard)` to every `super_admin`-only endpoint.

Remove the inline role comparisons from the controllers.

**Suggested validation**

- Add isolated tests for the guard.
- Verify missing authentication context returns the expected authentication
  error.
- Verify a non-super-admin user is rejected.
- Verify a super administrator is allowed.
- Verify legacy request properties are not trusted.
- Verify both controllers use the same guard.
```

### Concise GitHub comment

The review comment itself should be concise enough to appear as an inline
GitHub comment.

Do not repeat the complete review report inside every finding.

The comment should explain:

1. What is wrong
2. Why it matters
3. What should change

Use the Impact and Suggested correction sections for additional detail.

### Suggested patch

When the correction is local and can be represented accurately, include an
optional suggested patch.

Use:

````text
**Suggested patch**

```suggestion
const loadAuditList = async (
    filter: FitbankWebhookAuditFilter,
) => {
````

````

Only provide a suggested patch when:

- The exact correction is known.
- The patch does not depend on unanswered business rules.
- The patch does not hide additional required changes.
- The replacement is valid for the referenced line range.
- The correction is small enough for an inline suggestion.

Do not provide a misleading one-line patch for an architectural issue that
requires changes across multiple files.

For larger corrections, provide a proposed implementation outline instead.

### Multi-file findings

When one defect spans multiple files, create one primary finding and list the
related locations.

Example:

```text
### Primary location

`application/audit/controllers/webhook-audit.controller.ts`

**Lines:** 38-44

### Related locations

- `application/audit/controllers/balance-audit.controller.ts`, lines 31-37
- `infrastructure/auth/guards/backoffice-super-admin.guard.ts`, lines 18-25
- `infrastructure/auth/strategies/backoffice.strategy.ts`, lines 51-62
````

The primary location should be the most useful place for the developer to begin
the correction.

Do not create several identical comments for the same root cause.

Create separate comments only when each location has a distinct defect or
requires a different correction.

### Duplicated code

When reporting duplication:

1. Identify every relevant copy.
2. Determine which implementation is canonical, if any.
3. Verify whether the copies already behave differently.
4. Identify the underlying rule being duplicated.
5. Recommend the correct reusable abstraction.
6. Avoid recommending abstraction for trivial structural similarity.

For duplicated authorization logic, always trace:

* Where authentication is validated
* Which authenticated context is injected
* Which request property is canonical
* Which guards already exist
* Which controllers repeat the policy
* Whether the duplicated copies use different identity sources
* Whether isolated policy tests exist

A duplicated security rule must not be reported only as a DRY or maintainability
issue.

Report the authorization-consistency impact.

### Finding title

Every finding must have a short, actionable title.

Examples:

* `Centralize the duplicated super-admin authorization policy`
* `Preserve false values when building the audit filters`
* `Add a timeout to the external provider request`
* `Avoid detaching the background operation from request cancellation`
* `Use the authenticated JWT context instead of request input`
* `Add regression coverage for duplicate webhook delivery`

Avoid vague titles:

* `Improve this code`
* `Possible issue`
* `Refactor`
* `Code quality`
* `Check this`

### Severity label

Use exactly one:

* Critical
* High
* Medium
* Low

Severity must be based on practical impact, not on the amount of code affected.

### GitHub-style grouping

Order comments by:

1. Critical
2. High
3. Medium
4. Low

Within the same severity, order comments by file path and line number.

### Review summary comments

Not every finding belongs to a specific line.

Use a general PR comment only for concerns such as:

* Missing deployment strategy
* Missing rollback plan
* Repository-wide architecture inconsistency
* Missing documentation spanning several endpoints
* Missing environment configuration
* Missing observability strategy
* Missing migration plan
* Missing test category across the complete change

General comments must use:

```text
## General PR comment

**Severity:** High  
**Area:** Deployment and rollback

The migration removes a column before all application instances are guaranteed
to stop reading it. During a rolling deployment, old instances may fail while
the migration is already active.

**Suggested correction**

Use a two-phase expand-and-contract migration.
```

Do not use a general comment when the issue has a clear inline location.

## Review output contract

The final response must use the following structure:

```text
# Staff PR Review

## Overall assessment

Briefly summarize the safety and quality of the PR.

## Recommendation

Use exactly one:

- Request changes
- Comment
- Approve

Use `Request changes` when unresolved Critical or High findings exist.

Use `Comment` when only Medium or Low findings remain and they do not block the
change.

Use `Approve` only when no material findings remain.

## Review comments

### `path/to/file.ts`

#### Centralize the duplicated authorization policy

**Lines:** 42-47  
**Severity:** High

**Review comment**

...

**Impact**

...

**Suggested correction**

...

**Suggested validation**

...

---

### `path/to/another-file.ts`

#### Preserve explicit false filter values

**Lines:** 58-61  
**Severity:** Medium

**Review comment**

...

## General PR comments

Include only findings that cannot be placed on a specific line.

## Missing tests

List missing scenarios and associate each scenario with the relevant file,
function, endpoint, or flow.

## Questions

List unresolved questions that depend on business intent.

## Positive observations

Mention relevant strengths briefly.

## Review statistics

Report:

- Files reviewed
- Files with findings
- Critical findings
- High findings
- Medium findings
- Low findings
- General comments
- Questions
```

## GitHub recommendation mapping

Map the previous recommendation model to GitHub terminology:

| Engineering assessment              | GitHub review   |
| ----------------------------------- | --------------- |
| Reject until corrected              | Request changes |
| Approve after required corrections  | Request changes |
| Approve with follow-up improvements | Comment         |
| Approve                             | Approve         |

Do not approve while unresolved Critical or High findings remain.

## Comment quality requirements

Every review comment must be:

* Specific
* Evidence-based
* Actionable
* Attached to the smallest relevant line range
* Clear about practical impact
* Clear about the expected correction
* Free from personal criticism
* Independent from stylistic preference

Do not write:

```text
This is wrong.
```

Write:

```text
This controller reads `request.backOfficeClient`, while the existing
`BackofficeSuperAdminGuard` reads `request.backofficeUser`. Because the
authentication pipeline populates only one of these properties, authorization
behavior may differ between endpoints.
```

Do not write:

```text
Use a guard.
```

Write:

```text
Update the existing super-admin guard to consume the canonical authenticated
context and apply it to both controllers with `@UseGuards`. Add isolated tests
for missing context, invalid role, and allowed role.
```

## Root-cause deduplication

Do not produce multiple comments that repeat the same root cause.

Example:

If two controllers duplicate the same authorization policy and an existing
guard uses the wrong request property:

* Create one primary High finding on the first duplicated policy.
* List the second controller and guard as related locations.
* Explain the shared authentication-context defect.
* Recommend one consolidated correction.

Create separate findings only when:

* One controller has an additional independent defect.
* The guard has a separate security flaw.
* The authentication strategy injects unvalidated claims.
* Different corrections or validation strategies are required.

The number of comments must reflect distinct engineering problems, not the
number of files containing the same symptom.

## Review-only mode

The first execution of `staff-pr-review` must remain review-only unless the
developer explicitly requests corrections.

Do not:

* Modify files
* Apply suggestions automatically
* Format code
* Add tests
* Change configuration
* Update dependencies

After the developer requests corrections:

1. Apply the agreed changes.
2. Run the applicable validations.
3. Execute `staff-pr-review` again.
4. Use the same GitHub-style output format.
5. Mark previous findings as resolved, partially resolved, or unresolved.
6. Report any new findings introduced by the corrections.

# Staff Review Finding Presentation

The final Staff Review must organize findings by severity and make each finding
immediately understandable without requiring the reader to inspect the code
first.

## Severity sections

Use this exact severity order:

1. Critical
2. High
3. Medium
4. Low

Each severity section must restart its numbering from `1`.

Example:

```text
🔴 CRITICAL (2)

1. Missing authentication validation
2. Duplicate transaction processing

🟠 HIGH (3)

1. Hardcoded provider configuration
2. Missing timeout
3. Incorrect error propagation

🟡 MEDIUM (2)

1. Ambiguous variable naming
2. Missing edge-case coverage

🔵 LOW (1)

1. Minor duplication
```

Do not continue numbering between severity groups.

Incorrect:

```text
CRITICAL
1. Finding

HIGH
2. Finding
3. Finding

MEDIUM
4. Finding
```

Correct:

```text
CRITICAL
1. Finding

HIGH
1. Finding
2. Finding

MEDIUM
1. Finding
```

## Finding structure

Every finding must follow this structure:

```text
1. Finding title

Brief description explaining what is wrong and why the reviewer considers it
a problem.

- Location: path/to/file.ext:line
- Impact: practical consequence
- Recommendation: concrete correction
```

The description must appear immediately below the title.

It must explain:

* What is currently happening
* Why that behavior is problematic
* Why the suggested correction improves the implementation

Keep this explanation concise.

Prefer approximately 1 to 3 sentences.

Do not repeat the same information from Impact or Recommendation.

## Example

Use:

```text
1. Missing nil validation for MessagingService

The constructor validates `EmploymentRepository`, but accepts a nil
`MessagingService`. This creates an inconsistent dependency contract and allows
the application to start successfully only to panic later when messaging is
used.

- Location: `impl.go:26`
- Impact: Runtime panic when the invite flow attempts to send a message.
- Recommendation: Validate `params.MessagingService` in the constructor and fail
  fast when the dependency is missing.
```

Avoid:

```text
1. Missing nil validation for MessagingService
- impl.go:26
- Can panic
- Add nil check
```

The second format communicates the symptom but not the engineering reasoning.

## Description quality

The short explanation should answer:

> What is wrong here and why should I care?

Examples:

### Hardcoded value

```text
1. Hardcoded sender phone number

The sender number is embedded directly in the use case even though it belongs
to external messaging configuration. This couples business logic to one
environment and makes testing or provider changes harder.

- Location: `send_employee_invite.go:52`
- Impact: Environment changes require code modifications and tests depend on
  production-like configuration.
- Recommendation: Inject the sender number through configuration or the
  messaging adapter.
```

### Duplicate authorization

```text
1. Duplicated super-admin authorization rule

The same role check is implemented directly in multiple controllers instead of
using one authorization policy. These copies can diverge over time and already
make it harder to know which authenticated request context is authoritative.

- Location: `webhook-audit.controller.ts:42-47`
- Related: `balance-audit.controller.ts:31-36`
- Impact: Different endpoints may enforce the same permission differently.
- Recommendation: Centralize the policy in a dedicated guard using the canonical
  authenticated context.
```

### Missing test

```text
1. Missing regression coverage for duplicate processing

The implementation prevents duplicate processing, but no test proves that two
requests for the same operation cannot create duplicate side effects. A future
refactor could remove the protection without failing the test suite.

- Location: `payment.service.spec.ts`
- Impact: Duplicate financial or external operations may regress unnoticed.
- Recommendation: Add a regression test executing the same logical operation
  twice and assert that the side effect occurs only once.
```

## Location format

Prefer:

```text
- Location: `path/to/file.go:52`
```

For ranges:

```text
- Location: `path/to/file.ts:42-47`
```

For findings spanning multiple files:

```text
- Location: `path/to/primary-file.ts:42-47`
- Related: `path/to/second-file.ts:31-36`
- Related: `path/to/guard.ts:18-25`
```

Do not duplicate the same root finding once for every affected file.

## Impact

Impact must describe the practical consequence.

Good:

```text
- Impact: A missing dependency causes a runtime panic when the invite flow is
  executed.
```

Bad:

```text
- Impact: Bad practice.
```

Good:

```text
- Impact: Future endpoints may enforce the same role using different request
  properties, producing inconsistent authorization behavior.
```

Bad:

```text
- Impact: Violates DRY.
```

Engineering principles such as DRY, SOLID, or clean architecture may help
explain a finding, but they are not themselves the practical impact.

## Recommendation

Recommendations must describe the expected correction, not only the desired
result.

Prefer:

```text
- Recommendation: Move the sender number to messaging configuration and inject
  it through the use-case dependencies.
```

Avoid:

```text
- Recommendation: Refactor.
```

Prefer:

```text
- Recommendation: Add a `BackofficeSuperAdminGuard` that reads the canonical
  authenticated context and replace both inline role checks with
  `@UseGuards(...)`.
```

Avoid:

```text
- Recommendation: Use a guard.
```

## Optional validation

When the correction requires important verification, add:

```text
- Validation: Add an isolated guard test covering missing context, unauthorized
  role, and super-admin access.
```

Do not add a Validation line to every trivial finding.

Use it primarily for:

* Security
* Business rules
* Regression risks
* Concurrency
* External integrations
* Financial behavior
* Complex bug fixes

## Output template

Use the following final structure:

```text
# Staff Review - Findings 📋

The complete review found X findings.

🔴 CRITICAL (X)

1. Finding title

Short explanation of what is wrong and why the correction is recommended.

- Location: `path/file.ts:10-15`
- Impact: ...
- Recommendation: ...
- Validation: ...

2. Finding title

Short explanation.

- Location: ...
- Impact: ...
- Recommendation: ...

---

🟠 HIGH (X)

1. Finding title

Short explanation.

- Location: ...
- Impact: ...
- Recommendation: ...

2. Finding title

...

---

🟡 MEDIUM (X)

1. Finding title

...

---

🔵 LOW (X)

1. Finding title

...
```

Omit severity sections with zero findings unless the developer explicitly asks
to show them.

## GitHub inline review compatibility

When the full GitHub-style review format is requested, preserve the same short
description immediately below the finding title.

Example:

```text
### `send_employee_invite.go`

#### Hardcoded sender phone number

**Lines:** 52-55  
**Severity:** High

The sender number is embedded in business logic even though it belongs to
messaging configuration. This couples the use case to one environment and makes
testing or provider changes harder.

**Impact**

...

**Suggested correction**

...
```

The summary format and GitHub inline format must communicate the same finding
and severity.

## Anti-patterns

Do not:

* Continue numbering between severity groups.
* Provide only a title and bullet list with no explanation.
* Repeat the Impact text word-for-word in the description.
* Use principles such as DRY as the only impact.
* Write long essays under each finding.
* Hide the practical consequence.
* Recommend vague actions such as "refactor", "improve", or "fix".
* Duplicate one root cause across multiple findings only because several files
  contain the symptom.
* Mix Critical, High, Medium, and Low findings in the same numbered sequence.
