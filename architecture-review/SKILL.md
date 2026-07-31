---
name: architecture-review
description: Reviews code changes for separation of concerns, dependency direction, domain boundaries, coupling, public contracts, persistence leakage, external integrations, and architectural consistency. Use for features, refactors, new modules, integrations, or changes spanning multiple layers.
---

# Architecture Review

Follow the architecture already established by the project unless the task
explicitly requires changing it.

## Responsibilities

Verify that:

- Controllers or handlers coordinate requests rather than implement business rules.
- Business rules remain independent from delivery mechanisms when practical.
- Persistence details do not leak unnecessarily into business logic.
- External providers are isolated behind clear boundaries.
- Validation occurs at the appropriate boundary.
- Shared modules contain genuinely shared concepts.
- Domain-specific logic stays close to its domain.

## Dependency direction

Check that higher-level business rules do not depend unnecessarily on:

- HTTP framework details
- Database-specific models
- Provider-specific response formats
- UI concerns
- Environment access scattered throughout the code

## Authentication boundary architecture

The authentication layer must expose a stable internal contract to the rest of
the application.

The architecture should separate:

### Credential validation

Responsible for:

* Reading the credential
* Verifying authenticity
* Validating required claims
* Validating token state
* Rejecting invalid credentials

### Authenticated context creation

Responsible for:

* Mapping validated claims into an internal type
* Removing unnecessary raw token data
* Establishing the canonical principal representation
* Attaching the context to the request or execution context

### Authorization policy

Responsible for:

* Roles
* Permissions
* Tenant boundaries
* Ownership
* Resource-specific access decisions

### Business execution

Responsible for the operation after access has been granted.

Controllers should orchestrate these components, not recreate them.

Avoid architecture where each controller:

* Reads different request properties
* Interprets raw JWT claims
* Checks roles inline
* Decides authentication failure semantics
* Duplicates ownership checks
* Builds its own authenticated-user shape

When the same authenticated actor appears under multiple request properties,
treat this as an architectural inconsistency that requires consolidation or an
explicit compatibility plan.


## Contracts

Before changing a public contract, inspect:

- Existing callers
- Tests
- Documentation
- Serialized formats
- Database compatibility
- Message schemas
- External consumers

Avoid breaking changes unless explicitly approved.

## Abstractions

Create an abstraction only when it:

- Represents a stable domain concept
- Removes meaningful duplication
- Is used by more than a speculative future case
- Improves testing or dependency isolation
- Makes ownership clearer

Do not create repositories, factories, adapters, or generic services merely to
follow a pattern without solving a real problem.

## Cross-layer changes

For changes spanning multiple layers, verify consistency between:

- Input contract
- Validation
- Business logic
- Persistence
- Output contract
- Tests
- Documentation