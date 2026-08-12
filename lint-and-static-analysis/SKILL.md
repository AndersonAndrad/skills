# Lint and Static Analysis

---

name: lint-and-static-analysis
description: Detects, analyzes, and fixes lint and static-analysis errors in any project that provides linting or static-analysis tooling. Use during every implementation when lint tooling exists. A task must not be considered complete while lint errors introduced or reachable in the affected project remain unresolved.
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Objective

Lint and static analysis are mandatory quality gates when the project provides
the necessary tooling.

The agent must not consider a task complete only because:

* The code compiles.
* Tests pass.
* The implementation appears correct.
* The changed files have no obvious syntax errors.

If the project has a lint or static-analysis command, it must be discovered and
executed before completion.

A task must not be reported as validated while applicable lint checks are
failing.

## 1. Discover the project's lint tooling

Before running commands, inspect the project configuration.

Possible sources include:

* `package.json`
* `Makefile`
* `Taskfile.yml`
* `pyproject.toml`
* `.eslintrc`
* `eslint.config.js`
* `eslint.config.mjs`
* `biome.json`
* `ruff.toml`
* `.golangci.yml`
* `pom.xml`
* `build.gradle`
* `checkstyle.xml`
* CI workflow files
* Project documentation

Identify:

* Lint command
* Static-analysis command
* Auto-fix command
* Scope
* File patterns
* Ignored paths
* Configuration inheritance
* CI behavior

Do not invent command names.

Use commands already defined by the project whenever possible.

## 2. Common tooling

Examples include:

### JavaScript / TypeScript

* ESLint
* Biome
* TypeScript compiler
* Stylelint

Possible commands:

```bash
npm run lint
npm run lint:fix
pnpm lint
yarn lint
npx eslint .
```

### Go

* golangci-lint
* go vet
* staticcheck

### Python

* Ruff
* Flake8
* Pylint
* mypy

### Java

* Checkstyle
* SpotBugs
* PMD

### C#

* Roslyn analyzers
* dotnet format

Use the tooling already adopted by the project.

Do not introduce another linter merely because the current one reports errors.

## 3. Mandatory execution

When lint tooling exists:

1. Run the applicable lint command.
2. Capture all reported errors and warnings.
3. Determine whether auto-fix is available.
4. Apply safe automatic fixes when appropriate.
5. Review all automatic changes.
6. Fix remaining issues manually.
7. Run lint again.
8. Repeat until no blocking lint errors remain.
9. Run the broader validation commands afterward.

Do not stop after the first lint execution.

Do not report the task as complete with known lint errors.

## 4. Existing lint debt

The repository may already contain lint failures before the current task.

Examples:

```text
219 problems
180 errors
39 warnings
```

Do not ignore them automatically.

First determine:

* Which errors are in files changed by the current task.
* Which errors existed before the task.
* Which errors prevent the project lint command from succeeding.
* Which errors are safe to correct within scope.
* Which errors represent repository-wide technical debt.

### Rule for changed files

All lint errors in files touched by the current task must be corrected.

The task must not introduce new lint errors.

The task must not leave existing lint errors in a modified area when they can be
safely corrected without unrelated behavioral changes.

### Repository-wide failures

When the configured project lint command fails because of pre-existing errors
outside the current task:

1. Record the baseline.
2. Separate pre-existing errors from newly introduced errors.
3. Fix all errors caused by the current implementation.
4. Fix nearby or directly related existing errors when safe.
5. Report remaining repository-wide lint debt explicitly.

If project policy requires zero lint errors globally, then continue correcting
the repository until lint passes completely.

Do not hide existing lint debt by narrowing the lint command unless the project
explicitly uses changed-file linting.

## 5. Default project policy

Unless the project explicitly defines another policy:

* New lint errors are forbidden.
* Modified files must finish with zero lint errors.
* Auto-fixable lint errors should be corrected.
* Errors that reveal real code defects must be corrected.
* Existing repository-wide lint debt must be reported.
* A project-wide lint command that is expected to pass in CI must pass before
  final validation.

When the repository currently contains large lint debt and the user explicitly
requires all lint errors fixed, treat the cleanup as part of the task.

## 6. Do not disable rules to make lint pass

Do not solve lint errors by weakening lint configuration without analyzing the
root cause.

Avoid:

```ts
// eslint-disable-next-line
```

```ts
/* eslint-disable */
```

or configuration changes such as:

```js
rules: {
    'important-rule': 'off',
}
```

unless there is a specific and justified exception.

Before suppressing a rule:

1. Understand why the rule exists.
2. Determine whether the code can be corrected instead.
3. Determine whether the rule conflicts with project architecture.
4. Check existing project conventions.
5. Limit suppression to the smallest possible scope.
6. Document why suppression is necessary.

Do not globally disable a rule because one implementation violates it.

## 7. Do not change behavior only to satisfy lint

Lint corrections must preserve intended behavior.

Be careful when fixing:

* Missing dependencies in hooks
* Promise handling
* Async callbacks
* Equality comparisons
* Nullable values
* Unused parameters
* Type coercion
* Exhaustive dependencies
* Mutation warnings
* Unsafe optional chaining
* Accessibility rules
* React hooks
* Resource cleanup

A lint error may expose a real defect.

Do not silence it without understanding the behavior.

## 8. Unused variables and imports

Remove unused:

* Imports
* Variables
* Functions
* Parameters
* Types
* Interfaces
* Constants
* Components
* Helpers

unless they are required by a framework contract.

Do not rename unused values to `_value` merely to silence lint unless:

* The framework requires the parameter.
* The interface requires the signature.
* Project convention explicitly uses this pattern.

Prefer deleting unnecessary code.

## 9. Naming lint findings

When lint exposes unclear naming, apply the `naming-conventions-review` skill.

Examples:

```ts
const f = filter;
const d = data;
const x = result;
```

Do not keep poor names merely because the linter does not reject them.

Lint defines the minimum mechanical standard.

Naming and readability skills define the engineering standard.

## 10. Type-related lint findings

For rules such as:

* `no-explicit-any`
* unsafe assignment
* unsafe member access
* unsafe return
* unsafe argument

do not solve them using type assertions blindly.

Avoid:

```ts
value as any
value as unknown as SomeType
```

unless explicitly justified.

Instead:

1. Identify the real runtime type.
2. Validate external input.
3. Create the correct type.
4. Narrow unknown values.
5. Preserve boundary safety.

Do not trade lint cleanliness for weaker type safety.

## 11. Promise and async rules

Review findings involving:

* Floating promises
* Missing await
* Misused promises
* Async callbacks
* Promise rejection
* Error propagation

Do not fix a floating promise by adding:

```ts
void somePromise();
```

without confirming that fire-and-forget behavior is intentional.

Determine:

* Who owns the promise lifecycle?
* Does failure matter?
* Should it be awaited?
* Does it need timeout?
* Does it require retry?
* Does it need logging?
* Can it survive request cancellation?

Lint may expose an architectural issue.

## 12. React and frontend lint rules

When applicable, inspect:

* React Hooks rules
* Missing effect dependencies
* Invalid hook usage
* Accessibility rules
* Unstable references
* Missing keys
* Unsafe JSX
* Interactive element semantics

Do not silence:

```text
react-hooks/exhaustive-deps
```

without understanding why the dependency is missing.

A lint warning about effect dependencies may reveal:

* Infinite loops
* Stale closures
* Repeated network calls
* Incorrect state synchronization
* Unnecessary renders

Apply relevant frontend skills when needed.

## 13. Security lint findings

Treat findings involving security as more than style.

Examples:

* Unsafe eval
* Dynamic code execution
* Unsafe regex
* Unsanitized HTML
* Weak randomness
* Unsafe child process execution
* Path traversal
* Unsafe deserialization
* Missing promise handling around security operations

Apply `security-review` when lint exposes a security-sensitive issue.

## 14. Complexity lint findings

When the linter reports:

* Excessive cognitive complexity
* Excessive cyclomatic complexity
* Function too long
* Too many parameters
* Excessive nesting

do not split code mechanically.

First determine:

* Which responsibilities are mixed?
* Which conditions represent domain rules?
* Whether repeated logic should be extracted.
* Whether the complexity belongs to the domain.
* Whether a state machine or policy would be clearer.

Apply `code-quality` and `architecture-review`.

## 15. Auto-fix behavior

Auto-fix may be used for deterministic changes such as:

* Formatting
* Import ordering
* Semicolons
* Quotes
* Some unused imports
* Whitespace
* Safe syntax transformations

After auto-fix:

1. Inspect the diff.
2. Confirm no unintended behavior changed.
3. Run lint again.
4. Run type checking.
5. Run tests.
6. Run build when applicable.

Never assume `--fix` is behavior-preserving without reviewing the diff.

## 16. ESLint-specific guidance

For ESLint projects:

Inspect:

* Flat config versus legacy config
* Type-aware linting
* Parser configuration
* Project references
* Ignore patterns
* React plugins
* Import plugins
* Security plugins
* Accessibility plugins

Prefer the configured project command over direct ESLint invocation.

If:

```bash
npm run lint
```

runs:

```bash
eslint .
```

use the project script.

Do not bypass project configuration by linting only one file unless doing an
intermediate diagnostic run.

Final validation should use the actual project lint command.

## 17. Warnings

Determine the project's warning policy.

Possible policies:

* Warnings allowed
* Warnings fail CI
* `--max-warnings=0`
* Specific warning threshold

When the project treats warnings as failures, they must be fixed before
completion.

Even when warnings do not fail CI, review them.

Do not ignore security, correctness, or React Hooks warnings merely because
their severity is `warning`.

## 18. Changed-file validation

During implementation, targeted lint may be used for speed.

Example:

```bash
eslint src/changed-file.ts
```

This is useful for iteration.

However, before final completion:

* Run the project-level lint command when practical.
* Do not rely only on changed-file lint when CI runs the entire repository.
* Compare results with the baseline if pre-existing debt exists.

## 19. Baseline comparison

When the repository already has lint failures, record:

```text
Before:
219 lint errors

After implementation:
223 lint errors
```

This is a regression and must not be accepted.

A valid progression may be:

```text
Before:
219 lint errors

After implementation:
214 lint errors
```

but if project policy requires lint to pass globally, continue until:

```text
0 lint errors
```

Report both before and after when existing debt is relevant.

## 20. CI consistency

Inspect CI workflows when possible.

Determine:

* Which lint command CI runs.
* Which Node or runtime version CI uses.
* Whether warnings fail CI.
* Whether formatting is checked separately.
* Whether generated code is linted.
* Whether multiple workspaces have separate lint commands.

Local validation must match CI expectations.

Do not claim lint validation is complete using a command different from the
one used by CI without explaining the difference.

## 21. Monorepos

In monorepos, determine:

* Root lint command
* Workspace-specific lint commands
* Affected applications
* Shared packages
* Dependency graph
* CI affected-project behavior

Run all applicable lint commands.

Do not lint only the application while modified shared packages remain
unchecked.

## 22. Generated files

Do not manually fix generated files unless the project expects them to be
edited.

If generated files fail lint:

1. Identify the generator.
2. Determine whether they should be ignored.
3. Determine whether the generated template is wrong.
4. Fix the source or generator when appropriate.

Do not add broad ignore rules without justification.

## 23. Lint configuration changes

Changes to lint configuration are engineering changes.

Review:

* Why the rule is changing.
* Which files are affected.
* Whether severity is being reduced.
* Whether new ignores are introduced.
* Whether a plugin is removed.
* Whether type-aware analysis is disabled.
* Whether CI behavior changes.

Any change that makes lint less strict requires explicit justification.

Do not weaken lint to make an implementation pass.

## 24. Required task workflow

For every task in a project with lint configured:

### Before implementation

When useful, run lint to establish a baseline.

### During implementation

Run targeted lint on changed areas.

### Before completion

Run the full applicable lint command.

### After automatic or manual fixes

Run lint again.

### Before final validation

Lint must be green according to project policy.

## 25. Blocking behavior

A lint failure blocks completion when:

* It was introduced by the current task.
* It exists in a modified file and can be safely corrected.
* It represents a correctness issue.
* It represents a security issue.
* It represents a type-safety issue.
* It represents a React Hooks violation.
* CI requires the lint command to pass.
* Project policy requires zero lint errors.

Do not mark the implementation `Validated` while a blocking lint failure
remains.

## 26. Required output

After lint review, report:

### Lint tooling

State which linter or static analyzer was discovered.

### Command executed

List the actual command.

### Baseline

When applicable, report the original number of:

* Errors
* Warnings

### Findings fixed

Summarize the categories corrected.

### Remaining findings

List any remaining findings and why they remain.

### After

Report the final:

* Error count
* Warning count

### Validation status

Use:

* Validated
* Partially validated
* Not validated

Use `Validated` only when the project's expected lint command passes according
to project policy.

## 27. Anti-patterns

Do not:

* Ignore lint because tests pass.
* Ignore lint because the task is unrelated.
* Introduce new lint errors.
* Leave lint errors in modified files.
* Run lint only once.
* Run only auto-fix and assume the task is complete.
* Use `eslint-disable` indiscriminately.
* Disable rules globally to make the build green.
* Replace real types with `any`.
* Add unsafe type assertions.
* Add `void` to promises without reviewing lifecycle.
* Remove dependencies from React hooks without analysis.
* Modify generated files blindly.
* Narrow the lint command only to hide repository failures.
* Ignore the command used by CI.
* Claim lint passed when it was not executed.
* Mark the task validated while blocking lint errors remain.

Lint is a mandatory engineering gate when the project provides it.
