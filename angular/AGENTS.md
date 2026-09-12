# Angular — AGENTS.md Example

These are suggested project rules to customize and adopt alongside the shared root AGENTS.md. They supplement, rather than replace, its Git, review, TDD, documentation, security, architecture, coverage, CI, and resource-efficiency requirements. MUST means required once adopted; SHOULD means recommended with a documented reason for departures.

Replace every `<...>` placeholder with verified project facts. Named directories, commands, and tools below are examples, not claims about an existing repository. Preserve existing compatible tooling; do not migrate versions, frameworks, or build systems merely to match this example. Record commands, their working directories, configuration files, and required services before enabling them in CI or hooks.

## Framework Version and Workspace

Adopt the applicable TypeScript, HTML, and CSS/SCSS supplements.

Record: Angular/CLI/Node versions, workspace projects, standalone versus NgModule conventions, test builder, package manager, and CSR/SSR/hydration mode.

Example: application `<project name>`; source `<sourceRoot>` from `angular.json`; tests colocated with the feature; build configuration `<production configuration>`.

- Agents MUST use the pinned framework and workspace commands. Standalone components SHOULD be used for new work where consistent with the repository; do not convert existing modules incidentally.
- Match the repository's supported APIs rather than assuming the newest Angular guide applies to its version.

## Components, Services, and Data Contracts

- Organize by feature. Components SHOULD own presentation; services/domain modules MUST own business rules, and adapters MUST isolate transport and persistence.
- Inputs, outputs, service APIs, and route data MUST document types, required/default behavior, units, validation, and errors.
- Templates SHOULD avoid complex expressions and repeated expensive computation. Public template contracts MUST remain accessible and type checked.
- Use the established signals/RxJS state convention; do not introduce another store without a demonstrated task need.

Example component contract: input `order` is an order-view DTO; output `cancelRequested` carries the order identifier; disabled state prevents cancellation; the service remains responsible for authorization and domain validation.

## Lifecycle, Reactivity, and Security

- Use lifecycle-safe cleanup for subscriptions/listeners, such as the async pipe or compatible destruction helpers.
- Prefer derived state over duplicate writable state. Document observable ownership and cancellation.
- Use Angular's normal binding/escaping mechanisms. Security-bypass APIs MUST NOT be used merely to silence sanitization warnings.
- Route guards MUST NOT be treated as server authorization. SSR code MUST guard browser-only APIs and avoid request data in shared mutable state.

## Tests and Compiler Checks

- Use the test runner configured for the installed Angular version. Current new-project defaults MUST NOT trigger an unrelated migration of an existing test setup.
- Unit-test services/domain rules; use TestBed or component harnesses for component behavior, inputs/outputs, forms, and accessible interaction. Add router/HTTP contract tests and browser tests for critical flows.
- Enable template strictness according to the project policy. CI MUST separately enforce line and branch coverage >=80% for executable application code.

## Commands, Budgets, and Hooks

Example workspace commands, adapted to actual project and builder configuration:

```text
ng build <project> --configuration <configuration>
ng test <project> --watch=false
```

Record the actual lint command; `ng lint` requires a configured lint target. Suggested tools: Prettier, angular-eslint, and framework-compatible TypeScript checks. Document bundle budgets, browser test commands, and SSR checks where applicable. Pre-commit formats before lint; pre-push runs lint/lightweight validation; CI also runs builds, template compilation, tests, coverage, and budgets.

## Official References

- [Angular style guide](https://angular.dev/style-guide)
- [Angular testing setup and version-sensitive defaults](https://angular.dev/guide/testing)
