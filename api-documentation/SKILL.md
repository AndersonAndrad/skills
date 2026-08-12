---

name: api-documentation
description: Creates and reviews complete API and application documentation for every new or modified endpoint, command, integration, public contract, DTO, request, response, error, authentication requirement, filter, pagination rule, webhook, or user-facing technical flow. Uses the documentation tooling already adopted by the project, such as Swagger, OpenAPI, AsyncAPI, GraphQL schema descriptions, generated API references, or project documentation. Use whenever implementing or modifying an endpoint or public integration contract.
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# API Documentation

Create complete, accurate, and implementation-ready documentation for every new
or modified public contract.

The objective is that a client, frontend developer, mobile developer, external
partner, QA engineer, or integrator can understand and implement the flow
without needing to ask basic questions that should already be answered by the
documentation.

Documentation is part of the implementation.

An endpoint is not complete when the code works but its consumers cannot safely
understand:

* What the endpoint does
* Who may call it
* How to authenticate
* Which data must be sent
* Which data may be sent
* Which formats are accepted
* Which validations apply
* Which response is returned
* Which errors may occur
* Which side effects happen
* Whether retries are safe
* Whether the operation is idempotent
* How pagination, filtering, sorting, and dates behave
* How to test the integration

## 1. Use the project's existing documentation system

Before creating documentation:

1. Inspect the framework and stack.
2. Identify the documentation tooling already used by the project.
3. Inspect existing documented endpoints.
4. Identify project conventions.
5. Reuse existing decorators, helpers, schemas, examples, and response wrappers.
6. Inspect how authentication and authorization are documented.
7. Inspect how errors are documented.
8. Inspect whether DTO metadata is used to generate schemas.
9. Inspect whether documentation is generated automatically.
10. Inspect whether the project publishes or exports an OpenAPI specification.

Possible documentation systems include:

* Swagger
* OpenAPI
* NestJS Swagger
* Springdoc OpenAPI
* Swashbuckle
* FastAPI OpenAPI
* GraphQL schema descriptions
* AsyncAPI
* Postman collections
* API Blueprint
* Markdown API references
* Generated SDK documentation
* Internal developer portals

Do not introduce another documentation tool when the project already has an
established solution.

Do not create documentation that conflicts with generated schemas.

## 2. Inspect the complete public contract

Before documenting an endpoint, inspect:

* Route
* HTTP method
* Controller or handler
* Request DTO
* Response DTO
* Query DTO
* Path parameters
* Headers
* Authentication
* Authorization
* Guards
* Validation decorators
* Transformation behavior
* Normalization behavior
* Service behavior
* Persistence behavior
* External integrations
* Error mapping
* Tests
* Existing clients
* Public interfaces
* Serialization rules

Do not document only what is visible in the controller signature.

The real contract may be distributed across DTOs, pipes, guards, interceptors,
services, serializers, and exception filters.

## 3. DTO-first documentation

When the project uses DTOs, documentation must be derived from the actual DTO
contract.

For every DTO field, document:

* Field name
* Meaning
* Type
* Required or optional status
* Nullable behavior
* Format
* Minimum value
* Maximum value
* Minimum length
* Maximum length
* Allowed values
* Enum values
* Pattern
* Default value
* Example
* Normalization behavior
* Whether surrounding spaces are removed
* Whether casing is meaningful
* Whether the field is immutable
* Whether the field is sensitive
* Whether the value is returned in responses
* Cross-field rules
* Conditional requirements

The documented schema must match runtime validation.

Do not document a field as optional if runtime validation requires it.

Do not document a field as required if the request succeeds without it.

Do not document a numeric field when runtime accepts only a numeric string.

Do not use TypeScript type declarations alone as proof of runtime behavior.

## 4. Maximum useful documentation

Provide the maximum useful documentation, not the maximum amount of text.

Every description should help the consumer make a correct implementation
decision.

Document:

* Business purpose
* Expected usage
* Preconditions
* Permissions
* Accepted formats
* Validation rules
* Side effects
* Important defaults
* Ordering guarantees
* Idempotency
* Retry behavior
* Date and timezone behavior
* Pagination
* Filtering
* Sorting
* Rate limits when known
* File limits
* State transitions
* Compatibility constraints
* Error recovery
* Security restrictions
* External-provider behavior when relevant

Avoid descriptions that merely repeat field names.

Weak:

```text
User ID.
```

Better:

```text
Unique identifier of the user whose account will be updated. The authenticated
caller must have permission to manage this user.
```

Weak:

```text
Start date.
```

Better:

```text
Start of the audit period in ISO 8601 format. The value is interpreted in UTC
and the boundary is inclusive.
```

## 5. Endpoint summary and description

Every endpoint must have:

* A concise summary
* A complete description

The summary should describe the action.

Examples:

```text
Create a customer account
List webhook audit records
Cancel an open order
Generate a password reset request
```

The description should explain:

* What the operation does
* Who should use it
* Required preconditions
* Important side effects
* Business constraints
* Whether the operation is synchronous or asynchronous
* Whether the response represents final completion
* What the consumer should do next
* Whether the operation can be retried safely

Example:

```text
Creates a customer account and starts the external identity-verification flow.

The authenticated backoffice client must have permission to create accounts.
A successful HTTP response confirms that the account record was created, but
does not necessarily mean that external verification has completed.

Use the returned account identifier to query the current onboarding status.
Repeated requests with the same idempotency key return the previously created
operation instead of creating another account.
```

Do not use vague descriptions such as:

```text
Endpoint to create account.
```

## 6. Authentication documentation

For every protected endpoint, document:

* Authentication method
* Required header
* Token type
* Required scope
* Required role
* Required permission
* Tenant restrictions
* Resource ownership rules
* Administrative-only behavior
* Relevant token freshness limitations

Examples:

```text
Requires a Bearer access token.
```

```text
Requires the `super_admin` role.
```

```text
The authenticated user may access only records belonging to their current
organization.
```

Do not document only that authentication is required when authorization rules
also apply.

Do not expose internal token implementation details that consumers do not need.

## 7. Authorization documentation

Document the actual policy, not only the technical guard name.

Weak:

```text
Protected by BackofficeSuperAdminGuard.
```

Better:

```text
Only authenticated backoffice users with the `super_admin` role may access this
operation.
```

When authorization depends on resource ownership, document:

* Which resource is checked
* Which actor relationship is required
* Whether administrators bypass ownership
* Whether tenant boundaries apply
* Which error is returned when access is denied

## 8. Path parameters

For every path parameter, document:

* Meaning
* Format
* Example
* Validation
* Resource relationship
* Whether it is case-sensitive
* Whether it is an internal or provider identifier

Example:

```text
`auditId`

Unique identifier of the webhook audit record.

Format: UUID v4

Example: `5c5c6d21-9627-4d41-b05a-151b9848c071`
```

Never use a real production identifier.

## 9. Query parameters

For every query parameter, document:

* Meaning
* Required or optional status
* Type
* Default
* Allowed values
* Multiple-value behavior
* Filtering semantics
* Exact, partial, or case-insensitive matching
* Date boundary behavior
* Pagination limits
* Sorting behavior
* Combination behavior
* Invalid-value behavior

Example:

```text
`success`

Filters audit records by processing result.

- `true`: returns successful records only
- `false`: returns failed records only
- omitted: returns both successful and failed records
```

This distinction matters because `false` must not be interpreted as absence.

## 10. Pagination

For paginated endpoints, document:

* Pagination model
* Page numbering start
* Default page
* Default page size
* Maximum page size
* Stable ordering
* Total count behavior
* Empty-page behavior
* Cursor format when applicable
* Cursor expiration when applicable
* Changes during pagination
* Response metadata

Example:

```text
Pagination is zero-based.

- `pageIndex=0` returns the first page.
- `limitPerPage` defaults to `20`.
- The maximum allowed page size is `100`.
- Results are ordered by creation date descending and then by identifier
  descending to preserve deterministic ordering.
```

Do not assume consumers know whether page numbering starts at zero or one.

## 11. Sorting

Document:

* Supported sort fields
* Default sort field
* Default direction
* Allowed directions
* Stable tie-breaker
* Invalid-field behavior
* Null ordering when relevant

Example:

```text
Supported values for `sortBy`:

- `createdAt`
- `updatedAt`
- `status`

Supported values for `sortDirection`:

- `asc`
- `desc`

The default is `createdAt desc`.
```

Do not allow undocumented arbitrary database fields as sort parameters.

## 12. Filtering

Document:

* Supported filters
* Exact or partial matching
* Case sensitivity
* Accent sensitivity
* Multiple-filter combination
* Inclusive or exclusive date boundaries
* Whether empty values are ignored or rejected
* Whether arrays use AND or OR semantics
* Whether filters are normalized

Example:

```text
When multiple filters are supplied, all filters are combined using AND
semantics.

The `route` filter performs a case-insensitive partial match.

The `method` filter performs an exact match against the documented HTTP method
values.
```

## 13. Dates and time

Every documented date field must define:

* Expected format
* Timezone
* Whether timezone is required
* Whether it is date-only or date-time
* Inclusive or exclusive boundaries
* Accepted future or past values
* Precision
* Example

Prefer ISO 8601.

Example:

```text
`startDate`

Inclusive start of the search period.

Format: ISO 8601 UTC date-time

Example: `2026-08-01T00:00:00Z`
```

Never use ambiguous examples such as:

```text
01/08/2026
```

unless the project explicitly defines that format.

## 14. Boolean values

Document whether boolean fields accept:

* JSON boolean
* String boolean
* Numeric boolean
* Omission
* Null

Example:

````text
Send a JSON boolean:

```json
{
  "active": false
}
````

The strings `"true"` and `"false"` are not accepted.

````

Do not assume consumers know whether query parsing converts string values.

## 15. Numeric values

For numeric fields, document:

- Integer or decimal
- Unit
- Currency
- Decimal precision
- Minimum
- Maximum
- Whether numeric strings are accepted
- Rounding behavior
- Whether negative values are allowed
- Whether zero is allowed

For money, clearly document whether the value uses major or minor units.

Example:

```text
`amount`

Transaction amount in minor currency units.

For BRL, `1050` represents R$10.50.

The value must be a positive integer.
````

Never use a realistic customer balance or transaction value from production.

## 16. String normalization

Document transformations that affect consumer input.

Examples:

* Leading and trailing spaces removed
* Email converted to lowercase
* Mask removed before persistence
* Internal spaces preserved
* Case preserved
* Unicode accepted
* Empty string converted to null

Be explicit.

Example:

```text
Leading and trailing spaces are removed from `name`.

Internal spaces are preserved.

Passwords, tokens, secrets, signatures, and free-form message fields are never
silently trimmed.
```

Do not document normalization that does not actually occur.

Do not hide transformations that may alter consumer input.

## 17. Masks and formatted values

For masked fields such as CPF, CNPJ, CEP, telephone, currency, and dates,
document:

* Accepted formatted value
* Accepted unformatted value
* Value returned by the API
* Value stored internally when relevant to consumers
* Country assumptions
* Validation behavior
* Example

Example:

```text
`document`

Brazilian CPF.

Accepted formats:

- `12345678909`
- `123.456.789-09`

The response returns the normalized value without punctuation.
```

Use structurally valid but explicitly fictional examples.

When checksums are relevant, use generated non-production examples or clearly
document that the example is illustrative and not associated with a real
person.

## 18. Request body examples

Every request body should include at least one complete valid example.

Add additional examples when materially useful:

* Minimum valid request
* Complete request
* Optional-field example
* Alternative enum state
* Bulk request
* File upload metadata
* Idempotent request
* Filtered request

Examples must:

* Match the documented schema
* Pass runtime validation when intended as valid
* Use fictional information
* Avoid real personal information
* Avoid real credentials
* Avoid real tokens
* Avoid real account numbers
* Avoid real provider protocols
* Avoid real company identifiers
* Avoid production URLs
* Avoid secrets

Example:

```json
{
  "name": "Alex Example",
  "email": "alex.example@example.test",
  "document": "12345678909",
  "phone": "+5511999999999"
}
```

Prefer reserved or clearly non-production domains such as:

```text
example.com
example.org
example.net
example.test
```

Do not copy request examples from production logs.

## 19. Example data policy

All documentation examples must be fictional.

Never use:

* Real names
* Real email addresses
* Real telephone numbers
* Real CPF or CNPJ values
* Real bank details
* Real customer identifiers
* Real tokens
* Real hashes
* Real API keys
* Real addresses
* Real transaction identifiers
* Real provider payloads
* Real production URLs
* Real internal secrets
* Real audit records

Use clearly artificial values.

Examples:

```text
Name: Alex Example
Email: alex.example@example.test
Order ID: 7ab83c13-82a5-4d8c-aec4-96d4a8cc2a15
Correlation ID: corr-example-001
API key: example_key_not_valid
Token: example-token-not-valid
```

When an identifier format requires checksum validity, generate a fictional test
value and ensure it is not copied from a real user or production system.

Never label real information as fake merely to include it in documentation.

## 20. Response documentation

For every success response, document:

* HTTP status
* Response type
* Field descriptions
* Required and optional fields
* Nullable behavior
* Enum values
* Timestamps
* Units
* Nested objects
* Collection behavior
* Pagination metadata
* Example
* Side-effect completion status

Example:

```text
HTTP 201 Created

The response confirms that the account record was created.

External identity verification may still be pending.
```

Do not imply that asynchronous work has completed when the response only
confirms acceptance or record creation.

## 21. Response examples

Provide realistic but fictional success examples.

Example:

```json
{
  "id": "7ab83c13-82a5-4d8c-aec4-96d4a8cc2a15",
  "status": "pending_verification",
  "createdAt": "2026-08-05T03:00:00Z"
}
```

Ensure:

* Every field exists in the real schema.
* Every enum value is valid.
* Dates match documented format.
* Monetary values use documented units.
* Optional values are represented intentionally.
* Examples do not contradict field descriptions.

Do not generate examples automatically without checking them against the DTO.

## 22. Error documentation

Document every relevant error response.

At minimum, consider:

* 400 Bad Request
* 401 Unauthorized
* 403 Forbidden
* 404 Not Found
* 409 Conflict
* 413 Payload Too Large
* 415 Unsupported Media Type
* 422 Unprocessable Entity when adopted
* 429 Too Many Requests
* 500 Internal Server Error
* 502 Bad Gateway
* 503 Service Unavailable
* 504 Gateway Timeout

Document only errors that the flow may actually return.

For every error, document:

* HTTP status
* Error code
* Meaning
* Trigger conditions
* Retry guidance
* Example response
* Whether the caller should modify the request
* Whether the caller should contact support
* Whether a correlation identifier is returned

## 23. Error format

Document the project's standard error contract.

Example:

```json
{
  "statusCode": 400,
  "code": "VALIDATION_FAILED",
  "message": "The request contains invalid fields.",
  "errors": [
    {
      "field": "email",
      "code": "INVALID_EMAIL",
      "message": "Provide a valid email address."
    }
  ],
  "correlationId": "corr-example-001"
}
```

Document each error field.

Do not expose:

* Stack traces
* SQL errors
* Internal exception class names
* Provider credentials
* Raw provider responses
* Infrastructure details

## 24. Validation error documentation

For validation errors, document:

* Field
* Constraint
* Error code
* User-safe message
* Multiple-error behavior
* Nested-field path behavior
* Array-item path behavior
* Whether unknown fields are rejected
* Whether validation stops on first error

Example:

```text
Invalid nested fields use dot notation.

Example:

`address.postalCode`
```

Consumers should be able to map validation errors back to their form fields.

## 25. Conflict documentation

For `409 Conflict`, explain the conflicting state.

Examples:

* Email already registered
* Resource already exists
* Operation already completed
* Invalid state transition
* Duplicate idempotency key with different payload
* Concurrent update conflict

Do not use a generic `Conflict` description.

Example:

```text
Returned when the idempotency key was previously used with a different request
body.

The client must generate a new idempotency key for a new logical operation.
```

## 26. Retry guidance

For errors and asynchronous operations, document whether the caller may retry.

Classify when relevant:

* Safe to retry immediately
* Retry with exponential backoff
* Retry only with the same idempotency key
* Do not retry without changing input
* Do not retry automatically
* Query operation status before retrying
* Contact support after repeated failure

Example:

```text
A `504 Gateway Timeout` does not guarantee that the provider failed to process
the operation.

Before retrying, query the operation status using the returned correlation
identifier or reuse the same idempotency key.
```

Do not leave retry behavior implicit for operations with external side effects.

## 27. Idempotency

For idempotent or potentially duplicated operations, document:

* Whether idempotency is supported
* Header or field name
* Format
* Scope
* Expiration
* Reuse behavior
* Same-key same-payload behavior
* Same-key different-payload behavior
* Response behavior
* Retry requirements

Example:

```text
Header: `Idempotency-Key`

Use one unique value per logical operation.

Reusing the same key with the same payload returns the original result.

Reusing the same key with a different payload returns `409 Conflict`.
```

Never claim an endpoint is idempotent when no protection exists.

## 28. Asynchronous operations

When the response does not represent final completion, document:

* What the response confirms
* Current operation status
* How to query status
* Expected state transitions
* Webhook behavior
* Polling recommendations
* Failure state
* Retry behavior
* Timeout expectations
* Whether duplicate requests are safe

Example:

```text
HTTP 202 Accepted confirms that processing was scheduled.

Use `GET /operations/{operationId}` to retrieve the final status.

Possible statuses:

- `pending`
- `processing`
- `completed`
- `failed`
```

Do not describe `202 Accepted` as successful completion.

## 29. State transitions

For stateful resources, document allowed transitions.

Example:

```text
Allowed order transitions:

- `received` → `preparing`
- `preparing` → `ready`
- `ready` → `delivered`

A delivered order cannot return to preparing.
```

Document:

* Who may trigger each transition
* Preconditions
* Side effects
* Invalid-transition error
* Whether the transition can be repeated

## 30. File uploads

For uploads, document:

* Content type
* Multipart field name
* Allowed file types
* MIME types
* Maximum size
* Maximum number of files
* Filename handling
* Virus scanning when applicable
* Processing behavior
* Storage behavior relevant to consumers
* Failure responses
* Example request

Do not document only the file extension.

Example:

```text
Accepted formats:

- PDF: `application/pdf`
- PNG: `image/png`
- JPEG: `image/jpeg`

Maximum size: 10 MB per file
```

## 31. Webhooks

For webhooks, document:

* Event types
* Delivery method
* Authentication
* Signature algorithm
* Signature header
* Timestamp header
* Replay window
* Payload schema
* Example payload
* Retry schedule
* Duplicate delivery
* Ordering guarantees
* Timeout expectation
* Required response
* Event identifier
* Versioning
* Failure handling

Use fictional signing secrets and payload data.

Document that consumers must treat delivery as at-least-once when duplicates
are possible.

## 32. External integration behavior

When the endpoint calls an external provider, document consumer-visible
behavior:

* Synchronous or asynchronous
* Provider failure mapping
* Timeout behavior
* Retry implications
* Partial success
* Status codes
* Provider reference fields returned safely
* Eventual consistency
* Sandbox differences when relevant

Do not expose unnecessary provider implementation details.

Do not hide behavior that affects client correctness.

## 33. Headers

Document every relevant header:

* Authorization
* Content-Type
* Accept
* Idempotency-Key
* Correlation-ID
* Tenant identifier
* API version
* Locale
* Conditional request headers
* Signature headers

For each header, document:

* Required or optional
* Format
* Example
* Source
* Security considerations
* Default behavior

Never include a real bearer token.

Example:

```text
Authorization: Bearer example-token-not-valid
```

## 34. Content types

Document:

* Request content type
* Response content type
* File download content type
* Multipart requirements
* Vendor-specific media types
* Character encoding
* Binary response behavior

Example:

```text
Request:

`Content-Type: application/json`

Response:

`Content-Type: application/pdf`
```

For binary downloads, document filename behavior and error response content
types.

## 35. Versioning

Document API version behavior:

* URL version
* Header version
* Media-type version
* Schema version
* Default version
* Deprecation policy
* Sunset date when defined
* Backward compatibility
* Migration notes

Do not change a public contract without updating version documentation.

## 36. Deprecated endpoints and fields

When deprecating:

* Mark the endpoint or field as deprecated.
* Explain the replacement.
* Explain migration steps.
* Define timeline when available.
* Preserve old behavior during the supported period.
* Document compatibility differences.

Example:

```text
Deprecated.

Use `POST /v2/customers` instead.

This endpoint will remain available until the documented retirement date.
```

Do not silently remove deprecated behavior.

## 37. Rate limits

When rate limits are known, document:

* Limit
* Time window
* Scope
* Relevant headers
* `429` behavior
* Retry guidance
* Burst behavior
* Whether different endpoints have different limits

Do not invent rate-limit values.

When exact limits are intentionally private or dynamic, document general
behavior without fabricating numbers.

## 38. Permissions and scopes

Document exact required permissions.

Example:

```text
Required permission:

`webhook_audit:read`
```

If multiple permissions are accepted, explain whether they use AND or OR
semantics.

Example:

```text
The caller must have either:

- `customer:read_all`
- `customer:read_own` for a resource they own
```

## 39. Examples must compile conceptually

Every example must be reviewed against:

* DTO types
* Runtime validation
* Enum values
* Required fields
* Response serializers
* Error format
* Date format
* Monetary units
* Authentication requirements
* Path and query parameters

An example that does not pass validation is a documentation defect.

When possible, add automated tests that validate OpenAPI examples against their
schemas.

## 40. Swagger and OpenAPI metadata

When Swagger or OpenAPI is used, document with the project's equivalent
features:

* Operation summary
* Operation description
* Tags
* Security
* Path parameters
* Query parameters
* Headers
* Request body
* Request examples
* Response schemas
* Response examples
* Error responses
* Deprecated state
* External documentation
* Enum descriptions
* Nullable fields
* Discriminators
* File upload schemas
* Pagination schemas

For NestJS Swagger, inspect whether the project uses:

* `@ApiOperation`
* `@ApiTags`
* `@ApiBearerAuth`
* `@ApiParam`
* `@ApiQuery`
* `@ApiHeader`
* `@ApiBody`
* `@ApiProperty`
* `@ApiPropertyOptional`
* `@ApiResponse`
* `@ApiOkResponse`
* `@ApiCreatedResponse`
* `@ApiBadRequestResponse`
* `@ApiUnauthorizedResponse`
* `@ApiForbiddenResponse`
* `@ApiNotFoundResponse`
* `@ApiConflictResponse`
* `@ApiTooManyRequestsResponse`
* `@ApiConsumes`
* `@ApiProduces`
* `@ApiExtraModels`

Use equivalent tools for other frameworks.

Do not add decorators that contradict runtime behavior.

## 41. DTO property documentation

For a required property, document:

```ts
@ApiProperty({
    description: 'Human-readable name displayed to application users.',
    example: 'Example Organization',
    minLength: 2,
    maxLength: 120,
})
```

For an optional property:

```ts
@ApiPropertyOptional({
    description:
        'Optional external reference used by the client to correlate records.',
    example: 'client-reference-001',
    maxLength: 100,
})
```

The documentation metadata must match runtime validators such as:

* `@IsString`
* `@IsOptional`
* `@IsEmail`
* `@MinLength`
* `@MaxLength`
* `@IsEnum`
* `@Matches`
* `@Min`
* `@Max`
* `@IsUUID`
* `@ValidateNested`

Do not duplicate constraints inconsistently across validators and documentation.

## 42. Enum documentation

For enums, document:

* All accepted values
* Meaning of each value
* Default
* Transition restrictions when applicable
* Whether values may expand in the future

Example:

```text
Possible values:

- `pending`: accepted but not yet processed
- `completed`: processed successfully
- `failed`: processing ended with an error
```

Do not expose enum names without explaining their business meaning.

## 43. Polymorphic schemas

When request or response shape depends on a discriminator, document:

* Discriminator field
* Allowed discriminator values
* Schema for each value
* Required fields per variant
* Examples per variant
* Invalid combinations

Do not document a vague object with broad optional fields when the contract is
actually variant-specific.

## 44. Nested DTOs

For nested objects, document:

* Object purpose
* Required status
* Nested required fields
* Array item rules
* Maximum depth when relevant
* Example
* Validation path
* Null behavior

Ensure nested DTOs are correctly included in generated documentation.

Do not let generated Swagger show `{}` because nested metadata was omitted.

## 45. Arrays

For arrays, document:

* Item type
* Minimum items
* Maximum items
* Duplicate behavior
* Ordering significance
* Partial failure behavior
* Empty-array behavior
* Validation of each item
* Example

Example:

```text
The array must contain between 1 and 50 identifiers.

Duplicate identifiers are rejected.

Processing is atomic: if one identifier is invalid, no item is processed.
```

## 46. Create and update differences

Document create and update DTOs separately when behavior differs.

For update endpoints, clarify:

* Partial or full replacement
* Meaning of omitted fields
* Meaning of null
* Immutable fields
* Fields that can be cleared
* Validation after merge
* Concurrency behavior

Example:

```text
This is a partial update.

Omitted fields remain unchanged.

Sending `null` for `description` clears the field.

The `document` field is immutable and cannot be changed.
```

Do not reuse create documentation blindly for update behavior.

## 47. Delete behavior

For deletion, document:

* Hard or soft delete
* Cascading behavior
* Reversibility
* Required permissions
* Preconditions
* Conflict states
* Response status
* Idempotent behavior
* Existing-reference behavior

Example:

```text
The resource is soft-deleted.

Subsequent reads return `404 Not Found`.

Repeating the delete request returns `204 No Content`.
```

Do not claim deletion is permanent unless that is true.

## 48. Security-sensitive documentation

Document safe usage without exposing implementation details that weaken
security.

Document:

* Required authentication
* Signature verification requirements
* Allowed algorithms when consumers must configure them
* Replay protection
* Token expiration behavior
* Secret storage expectations
* Required HTTPS
* Sensitive-data restrictions

Do not document:

* Real secrets
* Internal private keys
* Internal network locations
* Exploitable infrastructure details
* Bypass mechanisms
* Production credentials

## 49. Application documentation beyond endpoints

When the task changes application behavior, update related documentation such
as:

* Setup instructions
* Environment variables
* Authentication flow
* Permission model
* Feature flags
* Operational procedures
* Webhook setup
* Queue behavior
* Background jobs
* File limits
* State machines
* Deployment requirements
* Migration instructions
* Troubleshooting
* Examples
* SDK usage

Do not update endpoint decorators while leaving central integration guides
incorrect.

## 50. Environment variables

For every new public configuration variable, document:

* Name
* Purpose
* Required or optional
* Default
* Format
* Example
* Secret status
* Environment scope
* Failure behavior
* Restart requirement

Never include a real secret.

Example:

```text
`EXTERNAL_PROVIDER_BASE_URL`

Base URL used for provider API requests.

Required: yes

Example: `https://sandbox-provider.example.test`

Secret: no
```

## 51. Documentation consistency

Ensure consistency between:

* Controller
* DTO
* Validation
* Response type
* Serializer
* Error filter
* Tests
* Swagger/OpenAPI
* README
* Postman collection
* Client examples
* Published API reference

When inconsistencies exist, determine which behavior is authoritative and fix
the documentation or implementation accordingly.

Do not preserve incorrect documentation merely because it already exists.

## 52. Documentation tests

When the project supports it, validate:

* OpenAPI generation succeeds
* Schema is valid
* Operation IDs are unique
* References resolve
* DTO schemas are present
* Examples match schemas
* Required fields match validation
* Error schemas are referenced
* Security schemes are defined
* Deprecated operations are marked
* Generated client compilation
* Documentation route loads
* No sensitive values appear in the specification

Possible checks include:

* OpenAPI schema validation
* Spectral linting
* Swagger CLI validation
* Generated SDK test
* Snapshot review
* Contract tests
* Example validation

Use only tools already adopted by the project unless a new dependency is
explicitly reviewed.

## 53. Review checklist for every endpoint

### Operation

* [ ] Summary clearly describes the action.
* [ ] Description explains purpose, preconditions, and side effects.
* [ ] Authentication is documented.
* [ ] Authorization is documented.
* [ ] Idempotency is documented when relevant.
* [ ] Async behavior is documented when relevant.

### Request

* [ ] Path parameters are documented.
* [ ] Query parameters are documented.
* [ ] Headers are documented.
* [ ] Request DTO matches runtime validation.
* [ ] Required and optional fields are correct.
* [ ] Formats and limits are documented.
* [ ] Normalization is documented.
* [ ] Valid fictional examples are included.

### Response

* [ ] Success status is documented.
* [ ] Response DTO is documented.
* [ ] Response example is included.
* [ ] Units and timestamps are clear.
* [ ] Pagination metadata is documented.
* [ ] Async completion status is clear.

### Errors

* [ ] Validation errors are documented.
* [ ] Authentication errors are documented.
* [ ] Authorization errors are documented.
* [ ] Not-found behavior is documented.
* [ ] Conflict behavior is documented.
* [ ] Provider or infrastructure failures are documented when relevant.
* [ ] Retry guidance is documented.
* [ ] Error examples use the project standard format.

### Safety

* [ ] No real personal data is used.
* [ ] No real credential is used.
* [ ] No real token is used.
* [ ] No real provider payload is used.
* [ ] No production URL is used.
* [ ] No sensitive implementation detail is exposed.

### Consistency

* [ ] Documentation matches DTOs.
* [ ] Documentation matches validators.
* [ ] Documentation matches guards.
* [ ] Documentation matches response serializers.
* [ ] Documentation matches tests.
* [ ] Generated schema was reviewed.
* [ ] Existing central documentation was updated when required.

## 54. Documentation review output

Before changing documentation, provide when useful:

## Contract summary

Describe the public behavior being documented.

## Documentation system

State which project tooling and conventions are being used.

## Missing documentation

List missing operation, DTO, response, error, security, and example details.

## Contract inconsistencies

List differences between implementation and current documentation.

## Documentation plan

Describe which files, decorators, schemas, or guides must be updated.

## 55. Final report

After documentation is updated, provide:

## Documentation added or updated

List the endpoints, DTOs, schemas, examples, guides, or generated specifications
changed.

## Consumer guidance covered

Summarize:

* Authentication
* Authorization
* Request contract
* Response contract
* Errors
* Pagination
* Filters
* Retry behavior
* Idempotency
* Examples

## Example-data safety

Confirm that examples are fictional and contain no real credentials, personal
data, or production information.

## Contract consistency

Describe how documentation was checked against DTOs, validators, guards,
serializers, tests, and implementation behavior.

## Validation executed

List only commands and checks actually executed.

## Remaining limitations

State undocumented provider behavior, unavailable generation tooling, missing
contract tests, or manual checks still required.

## Final status

Use exactly one:

* Validated
* Partially validated
* Not validated

Do not use `Validated` when generated documentation was not checked or when
material contract inconsistencies remain.

## 56. Anti-patterns

Do not:

* Add an endpoint without documenting it.
* Add only a one-line Swagger summary.
* Repeat field names as descriptions.
* Document DTO types without runtime validation rules.
* Use real customer or production data in examples.
* Copy payloads from logs into documentation.
* Include real tokens, secrets, hashes, or provider identifiers.
* Document only successful responses.
* Ignore authorization.
* Ignore retry behavior.
* Ignore idempotency.
* Describe asynchronous acceptance as final success.
* Hide important normalization behavior.
* Use ambiguous date formats.
* Omit pagination defaults and limits.
* Omit filter semantics.
* Document fields that do not exist.
* Omit fields that are returned.
* Invent rate limits or provider guarantees.
* Add documentation that contradicts tests or runtime behavior.
* Introduce a new documentation dependency without dependency review.
* Claim complete documentation without reviewing the generated output.
* Depend on consumers asking the team for information that belongs in the API
  contract.

Good documentation allows a consumer to integrate safely without reverse
engineering the implementation.
