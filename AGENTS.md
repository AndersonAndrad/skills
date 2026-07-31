## Mandatory Staff PR review

After implementation and before final validation, load and apply the
`staff-pr-review` skill to the complete current diff.

The review must evaluate:

* Architecture
* Security
* Performance
* Maintainability
* Readability
* Naming
* Complexity
* Correctness
* Test coverage
* Possible bugs
* Regressions
* Backward compatibility
* Observability
* Dependency impact
* Production impact
* Deployment and rollback risk

The reviewer must inspect:

* The complete diff
* Surrounding code
* Callers and consumers
* Existing tests
* Similar project implementations
* Public contracts
* Persistence and integration behavior

By default, the Staff PR review must not modify files.

For every finding, report:

* Severity
* Evidence
* Technical explanation
* Practical impact
* Recommended correction
* Suggested validation

Do not proceed to final validation while unresolved Critical or High findings
remain.

After corrections:

1. Review the complete diff again.
2. Confirm that findings were actually resolved.
3. Check whether corrections introduced new risks.
4. Only then run `final-validation`.

Automated checks are not a substitute for engineering review.


## Authentication and authorization context

Before implementing or modifying authentication or authorization:

1. Trace where the credential is received.
2. Trace where it is validated.
3. Confirm that signature, expiration, issuer, audience, token type, and
   required claims are validated when applicable.
4. Identify where the authenticated principal is created.
5. Identify the canonical request property or context accessor.
6. Search for existing guards, policies, decorators, and authorization helpers.
7. Search for duplicated inline authorization checks.
8. Verify that guards read the same property populated by authentication.
9. Verify that roles, permissions, tenant identifiers, and ownership are not
   taken from client-controlled input.
10. Add isolated tests for reusable guards and policies.

Authorization must use validated authentication context.

Do not trust raw decoded JWT payloads.

Do not introduce competing properties for the same authenticated actor.

Do not repeat role or permission checks across controllers.

When the same policy protects multiple endpoints, implement or reuse a
dedicated guard or authorization policy.

A mismatch between the property populated by authentication and the property
read by authorization is a blocking defect.

Examples:

```text
Authentication writes request.backOfficeClient
Guard reads request.backofficeUser
```

or:

```text
One controller checks request.backOfficeClient.role
Another guard checks request.backofficeUser.role
```

Before creating a new guard, verify the existing authentication pipeline and
the actual canonical context source.

Do not copy an existing guard until confirming that it reads the correct,
validated context.


## Exact Node.js dependency versions

All direct Node.js application dependencies and development dependencies must
use exact versions.

Use:

```json
{
  "dependencies": {
    "package-name": "1.2.3"
  }
}
```

Do not use:

```json
{
  "dependencies": {
    "package-name": "^1.2.3"
  }
}
```

Also avoid:

* `~1.2.3`
* `>=1.2.3`
* `*`
* `latest`
* Unpinned Git branches
* Unpinned Git tags

When installing packages, use the package-manager option that saves exact
versions.

Examples:

```bash
npm install --save-exact package-name@1.2.3
pnpm add --save-exact package-name@1.2.3
yarn add --exact package-name@1.2.3
```

Prefer configuring the repository with:

```ini
save-exact=true
```

in `.npmrc` when compatible with the project.

The lockfile does not replace exact direct dependency declarations.

A future lockfile regeneration must not be allowed to select a dependency
version that was never reviewed.

Exceptions such as `peerDependencies` in published libraries require explicit
compatibility analysis and must use the narrowest justified version range.

Any newly added or updated direct dependency using `^`, `~`, a wildcard, or an
open-ended range is a validation finding and must be corrected before approval.
