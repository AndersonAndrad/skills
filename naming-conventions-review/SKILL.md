---
name: naming-conventions-review
description: Reviews naming consistency across files, folders, variables, constants, functions, methods, classes, interfaces, types, enums, DTOs, components, hooks, services, repositories, controllers, tests, routes, database objects, events, logs, metrics, environment variables, and public contracts. Use whenever creating, renaming, or modifying code, files, modules, APIs, schemas, or domain concepts.
---

---

# Naming Conventions Review

Review all names created or modified during the current task.

The objective is that a developer can understand the role, domain meaning, and
behavior of an identifier immediately, without needing to inspect distant code,
types, comments, callers, or implementation details.

Good naming reduces cognitive load, prevents incorrect reuse, exposes domain
boundaries, and makes inconsistencies easier to detect.

Do not optimize names for minimum character count.

Do not make names unnecessarily long.

Prefer the shortest name that communicates the complete relevant meaning.

## 1. Inspect project conventions first

Before creating or renaming identifiers:

1. Inspect neighboring files.
2. Inspect similar modules.
3. Inspect naming patterns already established by the project.
4. Inspect framework conventions.
5. Inspect language conventions.
6. Inspect test naming.
7. Inspect public API terminology.
8. Inspect database and external-provider terminology.
9. Identify inconsistent existing conventions.
10. Reuse the established convention when it is clear and valid.

Do not introduce a new naming convention in one isolated file.

When the project already contains conflicting conventions:

1. Identify the dominant convention.
2. Identify whether the difference is intentional.
3. Determine the canonical terminology.
4. Avoid adding a third convention.
5. Propose a focused migration when inconsistency creates real risk.

Do not copy an existing name blindly when it is misleading or incorrect.

## 2. General naming principles

Every name should answer at least one of these questions:

- What is this?
- What does it do?
- Which domain concept does it represent?
- Which state does it describe?
- Which unit or format does it use?
- Which scope does it belong to?
- Which direction does data flow?
- Is it input, output, request, response, command, query, event, or persistence?

A good name should be:

- Specific
- Accurate
- Consistent
- Searchable
- Pronounceable
- Domain-oriented
- Stable over time
- Appropriate for its scope
- Distinguishable from related concepts

Avoid names that require interpretation.

## 3. Avoid unclear abbreviations

Do not abbreviate a domain concept merely to make the name shorter.

Prefer:

```ts
const loadAuditList = async (filter: FitbankWebhookAuditFilter) => {};
```

Avoid:

```ts
const loadList = async (f: FitbankWebhookAuditFilter) => {};
```

Prefer:

```ts
const authenticatedBackofficeClient = request.backOfficeClient;
```

Avoid:

```ts
const boc = request.backOfficeClient;
```

A name is too abbreviated when the reader must:

- Inspect the type
- Read the body
- Search the caller
- Translate a team-specific acronym
- Guess which concept the letter represents
- Remember surrounding context

Generally avoid:

```text
a
b
c
d
e
f
x
y
z
arr
obj
tmp
val
res
req
ctx
cfg
usr
usrId
svc
repo
mgr
util
info
data
item
thing
stuff
```

Exceptions may exist when the abbreviation is:

- Universally established
- Standard in the language or framework
- Clearer than the expanded form
- Used in a very small conventional scope

Commonly acceptable examples:

```text
id
url
uri
api
http
https
html
css
sql
jwt
dto
ui
ux
cpu
ip
uuid
```

Context-dependent abbreviations such as `req`, `res`, `ctx`, `repo`, `config`,
and `auth` should follow explicit project conventions.

Do not expand established terms mechanically.

`userId` is better than `userIdentifier`.

`url` is better than `uniformResourceLocator`.

## 4. Variables

Variable names must describe the value's meaning, not merely its type.

Prefer:

```ts
const auditRecords = await service.getAuditList(filter);
const authenticatedUser = request.authenticatedUser;
const failedTransactions = transactions.filter(
  (transaction) => !transaction.success,
);
```

Avoid:

```ts
const data = await service.getAuditList(filter);
const obj = request.authenticatedUser;
const arr = transactions.filter((item) => !item.success);
```

Review whether:

- The name describes the actual value.
- Singular and plural forms are correct.
- Collection names are plural.
- Boolean names communicate true and false meaning.
- Units are represented when relevant.
- Raw and transformed values are distinguishable.
- Previous and next state are distinguishable.
- Similar values are not given nearly identical names.

Examples:

```ts
const amountInCents = 1050;
const amountInReais = 10.5;

const rawDocument = input.document;
const normalizedDocument = normalizeDocument(rawDocument);

const previousStatus = order.status;
const nextStatus = OrderStatus.Cancelled;
```

Avoid:

```ts
const amount = 1050;
const document2 = normalizeDocument(document);
const statusNew = OrderStatus.Cancelled;
```

## 5. Boolean names

Boolean names should read naturally as true or false.

Prefer prefixes such as:

- `is`
- `has`
- `can`
- `should`
- `was`
- `did`
- `will`
- `requires`
- `supports`

Examples:

```ts
const isActive = user.status === UserStatus.Active;
const hasPermission = permissions.includes(requiredPermission);
const canRetry = attempt < maxAttempts;
const shouldShowError = touched && Boolean(error);
const requiresAuthentication = route.isProtected;
```

Avoid:

```ts
const active = true;
const permission = false;
const retry = true;
const status = false;
```

Avoid negative booleans when they create double negatives.

Prefer:

```ts
if (isEnabled) {
}
```

Instead of:

```ts
if (!isDisabled) {
}
```

When a negative concept is part of the domain, use it carefully:

```ts
const isUnavailable = provider.status === ProviderStatus.Unavailable;
```

## 6. Functions and methods

Function names should describe behavior and side effects.

Use verbs.

Examples:

```ts
loadWebhookAudits;
createCustomer;
updateAccountStatus;
validatePermission;
calculateOrderTotal;
normalizeDocument;
publishPaymentEvent;
mapProviderError;
```

Avoid vague names:

```ts
handle;
process;
execute;
run;
doWork;
manage;
perform;
action;
method;
helper;
utils;
```

Generic verbs may be acceptable when the surrounding abstraction makes the
meaning explicit.

For example:

```ts
paymentProcessor.process(payment);
```

may be clear because `PaymentProcessor` defines the context.

Avoid names that hide side effects.

Bad:

```ts
getUser();
```

when it also updates access time, writes an audit record, and refreshes cache.

Better:

```ts
getUserAndRecordAccess();
```

or separate responsibilities when appropriate.

Function names should distinguish:

- Fetching versus loading
- Reading versus creating
- Validating versus transforming
- Calculating versus persisting
- Publishing versus handling
- Starting versus completing
- Attempting versus guaranteeing

Examples:

```text
findUser
getUserOrThrow
createUser
saveUser
validateUser
normalizeUserInput
publishUserCreatedEvent
handleUserCreatedEvent
```

Do not use `get` for every operation.

## 7. Command-query distinction

Names should clarify whether a function reads data or changes state.

Queries usually use names such as:

- `find`
- `get`
- `list`
- `search`
- `count`
- `exists`
- `can`
- `has`

Commands usually use names such as:

- `create`
- `update`
- `delete`
- `remove`
- `cancel`
- `approve`
- `publish`
- `send`
- `reserve`
- `revoke`

Avoid a read-like name for a write operation.

Bad:

```ts
getOrCreateUser();
```

when the creation side effect is not obvious to the caller.

Use an explicit name or separate operations.

## 8. Classes

Class names should represent a clear responsibility or domain concept.

Use nouns.

Examples:

```text
PaymentService
CustomerRepository
BackofficeSuperAdminGuard
WebhookAuditController
CreateCustomerCommand
GetAuditListQuery
FitbankWebhookAdapter
JwtAuthenticationStrategy
```

Avoid:

```text
PaymentManager
CustomerHelper
CommonUtils
GeneralService
DataProcessor
SystemHandler
BaseManager
```

Class names should not rely on vague suffixes to create meaning.

A class ending in `Service`, `Manager`, `Helper`, or `Processor` still needs a
specific responsibility.

Review whether the class name matches what it actually does.

A class named `CustomerService` that handles authentication, payment, email,
and reports may be hiding excessive responsibility.

## 9. Interfaces and types

Use names that communicate the represented contract.

Examples:

```ts
interface AuthenticatedBackofficeContext {}
interface WebhookAuditFilter {}
interface CreateCustomerInput {}
interface CustomerResponse {}
type PaymentStatus = "pending" | "completed" | "failed";
```

Avoid meaningless prefixes or suffixes unless project conventions require them.

Potentially avoid:

```text
IUserService
TUser
UserInterface
UserType
UserData
```

when the language and project already make the construct clear.

Do not create two names for the same concept, such as:

```text
BackofficeUser
BackOfficeClient
AuthenticatedClient
CurrentUser
```

without an explicit distinction.

When names represent different concepts, document and preserve the difference.

## 10. DTO naming

DTO names should make direction and use case explicit.

Prefer:

```text
CreateCustomerRequestDto
CreateCustomerResponseDto
UpdateCustomerRequestDto
ListWebhookAuditsQueryDto
WebhookAuditResponseDto
```

or the project's established equivalent.

Avoid:

```text
CustomerDto
CustomerDataDto
CustomerPayload
CustomerModel
```

when the same object could mean request, response, persistence, or domain data.

Do not reuse the same DTO blindly for:

- Create
- Update
- Response
- Persistence
- External-provider requests
- Internal domain commands

The name should reveal the DTO's boundary.

## 11. Controller and route naming

Controller names should match the route resource or business capability.

Examples:

```text
WebhookAuditController
CustomerController
PaymentRefundController
```

Route names should:

- Use consistent pluralization.
- Use domain terminology.
- Avoid technical implementation details.
- Avoid verbs when a resource-oriented route is appropriate.
- Distinguish actions only when they represent domain commands.

Prefer:

```text
GET /webhook-audits
POST /customers
PATCH /orders/{orderId}
POST /orders/{orderId}/cancel
```

Avoid:

```text
GET /getWebhookAuditList
POST /createCustomer
POST /updateOrderStatus
```

unless the project intentionally uses RPC-style routes.

Do not rename public routes without compatibility analysis.

## 12. Service naming

Service names should identify the capability they own.

Prefer:

```text
WebhookAuditService
PaymentAuthorizationService
CustomerOnboardingService
```

Avoid:

```text
CommonService
SharedService
GenericService
MainService
UtilsService
```

A service name that could apply to most of the application usually lacks a clear
boundary.

## 13. Repository naming

Repository names should match the aggregate or persisted entity they manage.

Examples:

```text
CustomerRepository
PaymentRepository
WebhookAuditRepository
```

Methods should communicate query semantics:

```text
findById
findByOrganizationId
findActiveByEmail
existsByDocument
save
deleteById
```

Avoid:

```text
find
get
query
searchData
execute
doSave
```

when the condition or result is not clear.

Do not use one repository method name for materially different query behavior.

## 14. Guard, policy, and authorization naming

Authorization component names must describe the actual policy.

Prefer:

```text
BackofficeSuperAdminGuard
OrganizationMemberGuard
CanApproveRefundPolicy
CustomerOwnershipPolicy
```

Avoid:

```text
AuthGuard2
AdminGuard
PermissionGuard
RoleCheck
```

when the protected actor, role, permission, tenant, or resource is ambiguous.

Names must reflect the source and scope of authorization.

For example:

```text
BackofficeSuperAdminGuard
```

is clearer than:

```text
SuperAdminGuard
```

when the system has more than one kind of administrator.

Do not create multiple names for the same authorization rule.

## 15. Event naming

Events should use stable domain language and indicate completed facts.

Prefer past tense for facts that already occurred:

```text
CustomerCreated
PaymentAuthorized
RefundRequested
WebhookReceived
AccountDisabled
```

Commands should express intent:

```text
CreateCustomer
AuthorizePayment
RequestRefund
DisableAccount
```

Do not confuse commands and events.

Avoid:

```text
CustomerCreate
PaymentProcess
HandleRefund
EventData
GenericEvent
```

For serialized event names, use a consistent project format:

```text
customer.created
payment.authorized
refund.requested
```

Do not change event names without compatibility review.

## 16. Error naming

Error classes and codes should describe the failed rule.

Prefer:

```text
CustomerNotFoundError
InsufficientPermissionError
DuplicateWebhookError
InvalidOrderTransitionError
PROVIDER_TIMEOUT
CUSTOMER_ALREADY_EXISTS
```

Avoid:

```text
BusinessError
CustomError
GenericException
OperationFailed
ERROR_001
INVALID_DATA
```

Error names should help:

- Developers investigate
- Consumers react correctly
- Logs aggregate failures
- Tests assert specific behavior

Do not expose internal class names as public API error codes unless this is an
intentional convention.

## 17. Constants

Constants should explain meaning and units.

Prefer:

```ts
const MAX_LOGIN_ATTEMPTS = 5;
const PROVIDER_TIMEOUT_MS = 15_000;
const DEFAULT_PAGE_SIZE = 20;
const PASSWORD_RESET_TOKEN_TTL_MINUTES = 30;
```

Avoid:

```ts
const MAX = 5;
const TIMEOUT = 15000;
const DEFAULT = 20;
const TTL = 30;
```

Do not include mutable values in names that may become inaccurate.

Bad:

```ts
const THREE_RETRIES = config.maxRetries;
```

Better:

```ts
const maxRetries = config.maxRetries;
```

Use uppercase constants according to project and language conventions.

## 18. Enums

Enum names should represent one concept.

Enum values should use consistent grammar and abstraction level.

Good:

```ts
enum PaymentStatus {
  Pending = "pending",
  Processing = "processing",
  Completed = "completed",
  Failed = "failed",
}
```

Avoid mixing:

```ts
enum PaymentStatus {
  New = "new",
  IsProcessing = "processing",
  PaymentCompleted = "completed",
  Error = "failed",
}
```

Review whether:

- Values are mutually understandable.
- Names represent state, not actions.
- Serialization values are stable.
- Public values match documentation.
- Similar enums are not duplicated.

## 19. Files

File names must communicate their responsibility and follow project casing.

Examples for kebab-case projects:

```text
webhook-audit.controller.ts
webhook-audit.service.ts
webhook-audit.repository.ts
backoffice-super-admin.guard.ts
create-customer.request.dto.ts
```

Examples for snake_case projects:

```text
webhook_audit_controller.py
payment_repository.py
```

Do not mix casing styles without a framework reason.

Avoid vague filenames:

```text
utils.ts
helpers.ts
common.ts
shared.ts
misc.ts
temp.ts
new.ts
index2.ts
service-new.ts
controller-old.ts
```

A utility file should communicate its domain and purpose.

Prefer:

```text
document-normalization.ts
currency-formatting.ts
provider-error-mapping.ts
```

Avoid placing unrelated helpers into one broad file.

## 20. Folder naming

Folders should represent:

- Domain
- Feature
- Layer
- Boundary
- Capability

Use one dominant organizational model.

Examples:

```text
customers/
payments/
webhook-audits/
authentication/
```

or:

```text
controllers/
services/
repositories/
```

Avoid mixing feature-first and layer-first organization unpredictably within the
same area.

Avoid vague folders:

```text
common/
shared/
utils/
misc/
others/
new/
old/
temp/
```

These folders are acceptable only when their ownership and inclusion criteria
are explicit.

## 21. Component naming

Frontend components should describe the UI responsibility.

Prefer:

```text
WebhookAuditTable
CustomerCreationForm
PaymentStatusBadge
DeleteCustomerDialog
```

Avoid:

```text
AuditComponent
FormComponent
CustomTable
CommonModal
MainCard
Wrapper
Container
```

A component name should help a developer find where a user-facing behavior
lives.

## 22. Hooks and composables

Hooks should use the framework convention and describe the capability.

Examples:

```text
useWebhookAudits
useCustomerForm
usePaymentStatus
useDebouncedSearch
```

Avoid:

```text
useData
useFetch
useLogic
useHelper
useCommon
```

when the hook owns a specific domain behavior.

Names should indicate whether the hook:

- Fetches remote data
- Manages local form state
- Exposes an action
- Observes a status
- Coordinates a workflow

## 23. Test files and test names

Test file names should clearly map to the tested unit or flow.

Examples:

```text
backoffice-super-admin.guard.spec.ts
webhook-audit.controller.spec.ts
create-customer.e2e-spec.ts
```

Test names should describe:

- Initial condition
- Action
- Expected result

Prefer:

```ts
it(
  "rejects a request when the authenticated user is not a super administrator",
);
```

Avoid:

```ts
it("works");
it("test guard");
it("should return false");
```

A good test name should explain the business behavior even when the assertion
fails.

## 24. Database naming

Review names for:

- Tables
- Collections
- Columns
- Indexes
- Constraints
- Foreign keys
- Migrations

Use consistent singular or plural conventions.

Names should describe business meaning.

Prefer:

```text
webhook_audits
organization_id
created_at
uq_customers_email
idx_webhook_audits_created_at
fk_orders_customer_id
```

Avoid:

```text
data
type
value
info
ref
field1
index1
constraint_new
```

Do not rename persisted objects without migration and compatibility analysis.

## 25. Migration naming

Migration names should describe the schema change.

Prefer:

```text
add_status_to_webhook_audits
create_customer_email_unique_index
rename_backoffice_user_context
```

Avoid:

```text
update_database
fix_table
changes
migration_42
new_fields
```

The name should remain useful when investigating migration history years later.

## 26. Environment variables

Environment variable names should:

- Use uppercase snake case.
- Identify the system or provider.
- Identify the value purpose.
- Indicate units when relevant.
- Avoid ambiguous generic names.

Prefer:

```text
FITBANK_API_BASE_URL
FITBANK_REQUEST_TIMEOUT_MS
JWT_ACCESS_TOKEN_TTL_MINUTES
DATABASE_CONNECTION_POOL_SIZE
```

Avoid:

```text
URL
TIMEOUT
TOKEN_TIME
DB_SIZE
KEY
SECRET
```

For sensitive values, the name may include:

```text
SECRET
TOKEN
API_KEY
PRIVATE_KEY
```

but documentation and logs must still protect the value.

## 27. Logs and metrics

Log events and metric names should use stable, searchable terminology.

Prefer:

```text
webhook_audit_processing_failed
payment_provider_timeout
authentication_failed
```

Avoid:

```text
error
failure
something_wrong
process_problem
```

Metric names should describe:

- Domain
- Operation
- Measurement
- Unit when relevant

Examples:

```text
webhook_processing_duration_ms
provider_requests_total
authentication_failures_total
```

Do not use variable resource identifiers in metric names.

## 28. Public contract naming

Public API fields must use consistent domain terminology.

Do not expose multiple names for the same concept.

Example inconsistency:

```text
organizationId
ownerId
employerId
accountOwnerId
```

when all refer to the same entity.

Before adding a public field:

1. Identify the real domain concept.
2. Search existing contracts.
3. Confirm whether similar names represent distinct concepts.
4. Select the canonical term.
5. Preserve compatibility when changing existing fields.
6. Document aliases or deprecations when necessary.

Do not select a field name based only on the current database column or provider
payload.

## 29. External-provider naming

Do not leak external-provider terminology into generic domain code unless the
concept is provider-specific.

Prefer:

```text
externalTransactionId
providerRequestId
paymentProvider
```

inside generic domain boundaries.

Use provider-specific names inside adapters:

```text
fitbankProtocol
fitbankBusinessUnitId
```

Do not call a generic domain identifier `fitbankId` when another provider may be
used later.

## 30. Singular and plural

Use singular names for one item and plural names for collections.

Prefer:

```ts
const customer = customers[0];
const auditRecords = await listAuditRecords();
```

Avoid:

```ts
const customers = customers[0];
const auditRecord = await listAuditRecords();
```

Method names should match result cardinality:

```text
findCustomerById
listCustomers
countCustomers
```

Do not use `getCustomers` when the function returns one customer.

## 31. Units and formats

Include units in names when ambiguity can cause errors.

Examples:

```text
timeoutMs
durationSeconds
amountInCents
fileSizeBytes
tokenTtlMinutes
percentageRate
createdAtUtc
```

Avoid:

```text
timeout
duration
amount
size
ttl
rate
date
```

when more than one interpretation is possible.

Do not add units redundantly when the type makes them impossible to confuse and
the project convention is already clear.

## 32. Scope-sensitive naming

Name length may depend on scope.

A short name can be acceptable in a tiny conventional scope:

```ts
coordinates.map(([x, y]) => ({ x, y }));
```

A broader scope requires a more descriptive name.

Avoid single-letter domain variables even inside callbacks:

```ts
filters.map((f) => f.method);
```

Prefer:

```ts
filters.map((filter) => filter.method);
```

The fact that a scope is short does not remove domain meaning.

## 33. Avoid type-only names

Do not name values only by their structure.

Avoid:

```text
stringValue
numberValue
objectData
arrayItems
responseObject
requestData
```

Prefer the business meaning:

```text
email
attemptCount
authenticatedContext
auditRecords
customerResponse
createCustomerRequest
```

Types already explain structure.

Names should explain purpose.

## 34. Avoid redundant context

Do not repeat context already guaranteed by the enclosing scope.

Inside `CustomerService`, this may be sufficient:

```ts
create();
findById();
```

rather than:

```ts
createCustomer();
findCustomerById();
```

provided the call site remains clear:

```ts
customerService.create();
```

However, exported standalone functions may require more context:

```ts
createCustomer();
```

Balance local clarity with call-site clarity.

## 35. Avoid misleading legacy names

When behavior changes, review whether names remain accurate.

Examples:

- A method named `create` now also updates.
- A field named `userId` now stores an organization ID.
- A variable named `ownerId` actually represents an employer ID.
- A guard named `SuperAdminGuard` protects only backoffice administrators.
- A function named `validate` also mutates values.

Misleading names are defects because they cause future incorrect use.

Rename local identifiers when safe.

For public contracts or persisted data, use a compatibility plan.

## 36. Source-of-truth terminology

For concepts that appear across layers, define one canonical term.

Examples:

```text
employer
organization
account
backoffice client
authenticated principal
webhook audit
```

Before naming a value:

1. Trace the source.
2. Identify its actual entity.
3. Verify whether a one-to-one relation makes two identifiers appear
   interchangeable.
4. Use the identifier for the actual concept required by the flow.
5. Do not use a convenient related ID merely because it currently maps 1:1.
6. Preserve domain distinction even when current data relationships overlap.

Example:

If tracing requires `employer_id`, do not name or populate it with
`organizationId` merely because the current relationship is one-to-one.

A name must describe what the value is, not what it happens to correlate with.

## 37. Naming review severity

Classify naming findings by practical impact.

### Critical

Rare for naming alone, but applicable when misleading names contribute directly
to:

- Authorization bypass
- Cross-tenant access
- Financial misrouting
- Secret exposure
- Data corruption

### High

Use when names may cause:

- Incorrect identity source usage
- Confusion between security principals
- Wrong identifier sent to an external system
- Incorrect permission checks
- Persistent contract misuse
- Data written to the wrong entity

### Medium

Use when names cause:

- Significant maintainability risk
- Repeated incorrect reuse
- Hidden side effects
- Confusing public contracts
- Duplicate concepts
- Difficult incident investigation

### Low

Use for:

- Local abbreviations
- Minor casing inconsistency
- Generic local names
- Small searchability improvements

Do not classify every naming issue as Low automatically.

Names can expose deeper architectural or security defects.

## 38. Review process

When applying this skill:

1. Inspect all changed files.
2. List every new or modified identifier.
3. Compare names with project conventions.
4. Search for the same concept across the repository.
5. Identify abbreviations.
6. Identify generic names.
7. Identify misleading names.
8. Identify duplicate terminology.
9. Identify casing inconsistencies.
10. Identify public-contract impact.
11. Identify persisted or external compatibility impact.
12. Rename local behavior-preserving identifiers when explicitly authorized.
13. Avoid broad unrelated renaming.
14. Run applicable lint, tests, type checking, and build after changes.
15. Review the final diff.

## 39. Required review output

Provide findings using the Staff PR Review format when possible.

Example:

```text
### `src/audit/use-webhook-audits.ts`

#### Replace the abbreviated filter parameter

**Lines:** 42-51
**Severity:** Low

**Review comment**

The parameter `f` represents a complete `FitbankWebhookAuditFilter`, but its
meaning is only discoverable by inspecting the type and function body.

**Impact**

This adds unnecessary cognitive load and makes future changes easier to
misinterpret.

**Suggested correction**

Rename `f` to `filter`.

Also consider renaming `loadList` to `loadWebhookAudits` if the surrounding
scope does not already make the list type explicit.
```

For each finding include:

- File
- Line range
- Current name
- Why it is unclear or incorrect
- Practical impact
- Suggested name
- Whether the rename is local or contract-breaking
- Required validation

## 40. Naming inventory

For larger changes, provide a focused inventory:

| Current name      | Suggested name              | Kind            | Scope                  | Reason                 | Compatibility risk |
| ----------------- | --------------------------- | --------------- | ---------------------- | ---------------------- | ------------------ |
| `f`               | `filter`                    | parameter       | local                  | Removes abbreviation   | None               |
| `data`            | `auditRecords`              | variable        | local                  | Reveals returned value | None               |
| `ownerID`         | `employerId`                | trace attribute | observability contract | Matches actual entity  | Medium             |
| `SuperAdminGuard` | `BackofficeSuperAdminGuard` | class           | internal API           | Clarifies actor scope  | Low                |

Do not create an inventory for trivial changes with one or two names.

## 41. Rename safety

Before renaming, classify the identifier.

### Local rename

Examples:

- Local variable
- Private method
- Internal parameter
- Non-exported helper

Usually safe after search and validation.

### Internal contract rename

Examples:

- Exported class
- Shared interface
- Public module function
- DI token
- Event consumed inside the repository

Requires full usage search and tests.

### Public contract rename

Examples:

- API field
- Route
- Webhook field
- Queue event name
- Environment variable
- Database column
- Published package export

Requires:

- Compatibility review
- Versioning
- Migration
- Deprecation
- Documentation
- Consumer analysis
- Rollback plan

Do not rename a public contract as a minor cleanup.

## 42. Anti-patterns

Do not:

- Use one-letter domain variables.
- Use abbreviations that require interpretation.
- Rename identifiers only to make them longer.
- Expand established terms mechanically.
- Use generic names when a domain name exists.
- Use inconsistent casing.
- Mix singular and plural.
- Hide units.
- Hide side effects.
- Reuse one DTO name for unrelated boundaries.
- Call different concepts by the same name.
- Call the same concept by several names.
- Use provider terminology throughout generic domain code.
- Use database terminology as the public API by default.
- Rename public contracts without compatibility analysis.
- Create broad `utils`, `helpers`, or `common` files without ownership.
- Accept misleading names because the code currently works.
- Ignore naming inconsistencies in authentication and authorization.
- Ignore names that represent the wrong identifier.
- Perform repository-wide renaming unrelated to the task.
- Report naming preferences without practical reasoning.

Good naming should allow the reader to understand the code at first glance and
reduce the chance of incorrect future changes.
