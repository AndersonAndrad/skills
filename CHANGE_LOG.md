# Change Log

## 0.2.0 — Planned

Date: 2026-09-30
Author: Anderson Andrade

- Make `CHANGE_LOG.md` optional: when a project has no changelog, skip release
  and approval records instead of creating the file.
- Use the configured Git `user.name` as changelog author and approver name,
  optionally complemented by the authenticated AI-host user identity.
- Transfer documentation authoring to the AI agent using the project's existing
  documentation tooling; the developer reviews all changes and creates commits.
- Limit documentation, tests, and corrections to modified behavior; pre-existing
  content is out of scope unless the user requests it.
- Integrate the developer-authored `security-log` skill (secure API error
  responses and sensitive logging) into implementation, Staff review, and final
  validation, limited to the requested implementation.

### PLAN-002 revision 1 — Approved for implementation

Recorded at: 2026-09-30T09:18:03-03:00 (recording time, not source-message time).
Change author: Anderson Andrade (Git `user.name`).
Approver: Anderson Andrade (Git `user.name`, adopted as the approver-name source
by this plan; not independent identity verification).

Objective: optional changelog, Git-based attribution, and AI-authored
documentation limited to modified behavior.

Approved steps: update `release-changelog`, `implementation-plan`,
`api-documentation`, `code-quality`, `development-workflow`, `final-validation`,
`requirements-review`, `staff-review`, `CLAUDE.md`, and `AGENTS.md`; record this
plan here; run the complete Staff review and final validation; suggest an
English commit message without committing.

Alternatives presented: keep always creating the changelog (more traceability,
unwanted files); keep documentation developer-owned (smaller diffs, stale
contracts).

Risks presented and acknowledged, with conditions:

- R1 (Medium): projects without a changelog keep approval evidence only in the
  conversation. Acknowledged.
- R2 (Low): Git `user.name` does not prove who approved. Acknowledged; condition:
  the authenticated AI-host user identity may also be used when available.
- R3 (Medium): a project without documentation tooling could lead to adding
  one. Condition: add nothing; notify the developer that documentation could not
  be produced and why.
- R4 (Low): AI-authored documentation could be inaccurate or expand the diff.
  Condition: documentation, tests, and corrections cover only modified behavior;
  existing project content is out of scope unless the user requests it.

Open question presented: whether the `package.json` version still increases
when no changelog exists. The suggested default (yes, independent bookkeeping)
was not separately answered; it is applied as the presented default under the
general approval.

Supersedes for future work: PLAN-ROCKY-001 R2 ("adapt to read-only factual
review") regarding documentation ownership. The original record is preserved.

Approval evidence, quoted in the original language:

> pode fazer, R2 -> se conseguir pegar o usuário da IA pode ser usado também, R3 -> sim, se não tiver nenhuma ferramenta de documentação não é feito nada apenas notifica o dev que não pode fazer nada e o motivo, R4 -> sim documentação assim como testes e correções é somente de coisas que foram modificadas, o que já existe no projeto já existe no projeto, não é do nosso interesse a menos que o usuário fale

Decision status: Approved for implementation.

#### Amendment 2026-09-30 — Implementation outcome

Implemented within the approved scope. `dependency-review` was also updated
because it assigned documentation gaps to the developer, which is covered by the
approved ownership change. Staff review found no Critical or High issues; Low
findings (pre-existing blockers versus scoped corrections, regression tests for
affected existing behavior, a missing changelog condition in final validation,
and line wrapping) were corrected and the complete diff re-reviewed. Validation:
YAML frontmatter and relative skill links checked by script, and whitespace
checked with `git diff --check`. No formatter, linter, or executable test suite
exists in this repository. No commit was created.

### PLAN-003 revision 1 — Approved for implementation, with conditions

Recorded at: 2026-10-01T11:37:21-03:00 (recording time, not source-message time).
Change author: Anderson Andrade (Git `user.name`).
Approver: Anderson Andrade (Git `user.name`; attribution source, not independent
identity verification).

Objective: make `security-log` discoverable and applied during implementation
and Staff review, without project-wide audits.

Approved steps: add frontmatter, the approval-gate notice, and a scope section
to `security-log`; reference it from `development-workflow`, `staff-review`,
`security-review`, `error-handling`, `logging-observability`,
`final-validation`, the forms reference of `frontend-user-experience`,
`CLAUDE.md`, and `AGENTS.md`; record under the planned 0.2.0 release without a
second version increase; run the complete Staff review and final validation.

Alternatives presented: integrate only through other skills without editing
`security-log` (no discovery, scope conflict remains); merge its content into
`security-review` (single location, loses the standalone skill).

Risks presented:

- R1 (Medium): project-wide scanning during small tasks. Condition: apply only
  to the requested implementation; never inspect the whole project unless the
  developer explicitly requests it and accepts the presented risks.
- R2 (Medium): generic messages could degrade form UX or break frontend
  expectations. Mitigation in plan; not explicitly acknowledged.
- R3 (Low): changing existing error responses could break consumers.
  Mitigation in plan; not explicitly acknowledged.
- R4 (Low): overlapping rules across skills could diverge. Condition: other
  skills reference `security-log` instead of copying its rules.

Approval evidence, quoted in the original language:

> faz a correção do R4 e R1 para que seja feita apenas na implementação que foi solicitada, não deve olhar o projeto todo a menos que o dev tenha solicitado explicitamente e aceite os riscos

Decision status: Approved for implementation.

#### Amendment 2026-10-01 — Implementation outcome

Implemented within the approved scope. In `security-log`, the Implementation
Workflow steps were also scoped to the requested change, and the impact of
changing a shared error handler was added to planning; both follow the R1
condition. Staff review found no Critical or High issues. One Medium finding
remains open for the developer: the `security-log` primary example sanitizes a
`CreateUser` "email is required" validation, while its Validation Errors
section allows documented validations to stay descriptive. The skill body was
not changed beyond the approved edits. Validation: frontmatter, skill and
reference links, and whitespace were checked. No commit was created.

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
