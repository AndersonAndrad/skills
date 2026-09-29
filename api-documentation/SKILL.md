---
name: api-documentation
description: Review API and application documentation against actual contracts, identify stale descriptions and missing consumer guidance, and report gaps for the developer. Use when endpoints, DTOs, integrations, configuration, or public behavior change. Author documentation only when explicitly requested.
---

# API Documentation Review

Before implementing changes from this skill, follow the approval gate in
`../implementation-plan/SKILL.md`. Read-only analysis and review may proceed
without implementation approval.

Application documentation is the developer's responsibility. This skill checks
whether consumers can safely understand a changed contract; it does not create
or rewrite documentation as part of ordinary feature implementation.

## Ownership and scope

Review existing comments, API metadata, schemas, examples, and relevant guides.
Report inaccuracies, missing explanations, and their practical impact. Do not
add a documentation tool, generate checked-in guides, or insert comments merely
to satisfy a checklist. A missing explanation is a developer follow-up unless
it exposes a concrete unsafe contract or prevents meaningful verification.

An explicit request to write documentation authorizes that work within the
requested scope. In that case use English, preserve business terminology and
external API names/values, and follow existing project tooling. Do not treat
invoking a review as authorization to write documentation.

Executable validation schemas, types, serializers, and configuration remain
implementation responsibilities even when they also generate documentation.
Do not leave runtime behavior incorrect under the documentation ownership rule.
Mandatory changelog and version bookkeeping is handled by `release-changelog`.

## Establish the actual contract

Trace the route or entry point, DTOs, validators, authentication, authorization,
business operation, persistence, external adapters, serialization, errors, and
existing tests. Inspect callers and similar implementations before deciding
which contract is authoritative. Do not assume that prose or static types prove
runtime behavior. When intent is genuinely ambiguous, report the uncertainty
rather than inventing a contract or changing code to fit stale prose.

Identify the existing documentation system, such as OpenAPI, AsyncAPI, GraphQL
schema descriptions, or project guides. Review only the sections affected by the
change; do not require every possible documentation field for every endpoint.

## Review criteria

Check applicable consumer-facing details:

- Purpose, actors, prerequisites, authentication method, permissions, tenant
  boundaries, ownership, and administrative restrictions.
- Request body, path/query/header inputs, types, required versus optional and
  nullable fields, defaults, formats, enums, bounds, cross-field constraints,
  unknown fields, and immutable values.
- Normalization, masks, preserved whitespace/case, password and signature
  integrity, units, monetary precision, date format, and timezone semantics.
- Success status and response schema, error status and safe error payloads,
  validation feedback, and the distinction between asynchronous acceptance and
  completed processing.
- Pagination limits and ordering, filtering combinations, sorting allowlists,
  boolean false versus omission, empty results, and binary/download responses.
- State transitions, side effects, transactions visible to consumers,
  idempotency scope, retry safety, duplicate delivery, webhooks, timeouts,
  partial failure, and recovery behavior.
- Upload limits and accepted content, configuration requirements/defaults,
  versioning, deprecation, compatibility, and migration or operational guidance.
- Accurate examples for success and important failure/alternative scenarios,
  consistent operation identifiers, resolved schema references, discriminated
  unions, nested DTOs, and generated clients when affected.

Do not invent provider guarantees, rate limits, retirement dates, permission
rules, or delivery semantics. Distinguish observed behavior from unresolved
business intent. An external provider's field names and response values must
remain intact where they are part of the contract.

## Safety and useful explanations

Flag documentation containing real credentials, tokens, personal data,
production payloads, private infrastructure details, or instructions that could
encourage unsafe use. Examples must be fictional and schema-compatible; never
copy production logs and label them fictional.

Prefer explanations of non-obvious constraints, side effects, and rationale.
Do not request comments that narrate syntax, duplicate field names, or repeat
self-explanatory code. Report stale claims that could mislead a maintainer even
when the implementation itself is correct.

## Validation

Run applicable existing contract checks, schema validation, example validation,
or documentation generation in a temporary location when supported. Do not
publish output or overwrite developer-authored artifacts during a review.
Inspect generated output before relying on it. A large snapshot alone does not
prove a contract; verify relevant semantics against validators and consumers.

Do not add dependencies merely to perform this review. Report unavailable tools
and unverified behavior. Read-only validation may identify work for the developer
without forcing automatic documentation edits.

## Findings and handoff

For each distinct gap, identify the incorrect or missing contract information,
the supporting behavior, why a consumer or maintainer could be misled, and the
recommended correction and validation. Classify severity by practical impact;
do not make missing prose automatically Critical or High.

When included in Staff review, use `../staff-review/SKILL.md` for presentation
and deduplication. In Claude, use only Category, Problem, and Description, without
file locations. Separate documented defects from questions about unknown intent.
If the review found no material gaps, say so without claiming authoring occurred.

For explicitly requested authoring, update only the authorized material and
report what changed and what validation actually ran. The developer retains
ownership of the documentation.
