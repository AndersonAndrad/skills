# Change Log

## 0.1.0 — Planned

Date: 2026-09-28
Author: Anderson Andrade

Initial planned version for this skills collection; no application package
manifest or prior release version is present.

- Extend existing skills with English code and developer-owned documentation
  review, responsive UI requirements, mandatory behavior tests, mocked end-to-end
  boundaries, and frontend/backend input security checks.
- Require user-owned commits and English commit-message suggestions.
- Add release-changelog for monotonic application versions and release records.
- Coordinate formatting, lint, read-only Staff review, correction review, and
  final validation across the development workflow.
- Standardize the Staff review entrypoint and replace conflicting presentation
  rules with a concise Claude format that omits file locations.
- Define acceptance criteria, preserve existing developer edits and staging,
  and scale review depth to affected risks without weakening review gates.
- Expand persistence, migration, idempotency, concurrency, and recovery checks.
- Require meaningful behavioral assertions, realistic mock boundaries, and
  deliberate snapshot use; evolve skills from observed task failures.
- Convert API documentation authoring requirements into developer-facing review
  and correct discovery metadata for critical-code-review and dependency-review.
- Repair malformed YAML closing delimiters in requirements-review,
  edge-case-review, logging-observability, and node-vulnerability-review.
- Add implementation-plan to require a presented action plan, tradeoff/risk
  assessment, explicit developer approval, and an append-only approval audit
  before future implementations; connect standalone skills to the same approval
  gate and preserve security and validation gates.

### Approval-record status for this policy introduction

This ongoing skills change was requested before the prospective plan-approval
policy was introduced. No contemporaneous approval of a numbered plan or
explicit acknowledgment of a presented risk register was captured. The user
requested implementation of the recommendations and then requested this audit
policy; those instructions are not evidence of retrospective informed consent.
No approver identity or risk acknowledgment is inferred from the change author.
The new approval workflow applies to subsequent implementations.

### PLAN-ROCKY-001 revision 1 — Approved scope, attribution pending

Recorded at: 2026-09-29T00:00:02-03:00 (recording time, not source-message time).
Change author: Anderson Andrade (repository Git identity).
Approver: requesting developer in this conversation; display-name confirmation
has been requested and is pending. Git identity is not approval evidence.

Approval evidence, quoted in the original language:

> pode seguir sua recomendação. e puxar tudo que for relevante e adaptar o que for necessário

This explicitly approves the preceding Rocky adoption recommendation. The plan
identifier was assigned when recording that approval, not presented as an ID
beforehand. There is no independent verification of the approver's identity and
no separate acknowledgment of individual risk IDs. Do not interpret this entry
as blanket acceptance of unknown risks. Append confirmed attribution separately.

Approved scope and sequence:

1. Organize existing skills into concise entrypoints and contextual references.
2. Add collection validation, meaningful tests, and CI checks.
3. Link requirements, approved plans, tasks, risks, and validation evidence.
4. Strengthen factual documentation review and approved-plan conformance.
5. Add a small incident-pattern base from verified, locally observed defects.
6. Adapt relevant visual evidence, rollout, incremental delivery, and operational
   practices within existing skills; assess distribution without installing a
   marketplace or importing platform-specific tooling.

Benefits presented: less duplication, selective context loading, traceability,
review of real contracts, and prevention of recurring defects.
Drawbacks presented: additional reference/tooling maintenance; importing a large
CLI, many agents, or a marketplace would add unnecessary complexity.
Alternative presented: retain the current skills and adopt improvements gradually
instead of importing the entire Rocky architecture.

Previously presented concerns, assigned IDs here for traceability (these IDs
were not individually acknowledged by the developer):

- R1: automatic commits conflict with user-owned commits. Exclude those flows.
- R2: automatic documentation authorship conflicts with developer ownership.
  Adapt to read-only factual review and report gaps.
- R3: hooks are not complete security boundaries or backups. Do not import them
  or claim instruction files technically enforce all agent behavior.
- R4: CLI bootstrap, integrations, and unpinned dependencies add side effects.
  Use explicit local checks, pinned development tools, and no external services.
- R5: source instructions can expose credentials or contain stale claims.
  Generalize concepts, validate references, and copy no corporate payloads.
- R6: mobile-specific assumptions, host-specific tools, and fixed models reduce
  portability. Keep generic procedures and preserve existing host output rules.

Acceptance: core gates remain discoverable; referenced material exists; new
validator behavior is tested; the complete diff receives Staff review before
final validation; existing staged work and the source folder remain unchanged.
Rollback: revert only this adaptation's unstaged changes after inspecting the
existing work. No commits, deployment, or production-data changes are involved.
Validation results and completed outcomes will be appended after execution.
