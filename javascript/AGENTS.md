# JavaScript — AGENTS.md Example

These are suggested project rules to customize and adopt alongside the shared root AGENTS.md. They supplement, rather than replace, its Git, review, TDD, documentation, security, architecture, coverage, CI, and resource-efficiency requirements. MUST means required once adopted; SHOULD means recommended with a documented reason for departures.

Replace every `<...>` placeholder with verified project facts. Named directories, commands, and tools below are examples, not claims about an existing repository. Preserve existing compatible tooling; do not migrate versions, frameworks, or build systems merely to match this example. Record commands, their working directories, configuration files, and required services before enabling them in CI or hooks.

## Execution Environment and Dependencies

Record: browser or server target, Node/runtime version if relevant, supported browsers, package manager, lockfile, and module format.

Example: browser ESM application; `src/` and `tests/`; `<package manager>`; browser support defined in `<configuration path>`.

- Agents MUST preserve the existing ESM/CommonJS boundary. New modules SHOULD use ESM when compatible with the project.
- Client code MUST NOT import server-only modules or secrets. Package scripts MUST use pinned local tools rather than silently downloading executables.

## Contracts and Data Boundaries

- Public exports MUST have JSDoc describing types, units, valid values, null/undefined behavior, returns, thrown errors, and Promise rejection behavior.
- Inputs from network, storage, and user interaction MUST be validated at boundaries. Static documentation is not runtime validation.
- Presentation code SHOULD consume explicit DTO shapes through service interfaces, with storage/browser API adapters isolated from domain behavior.

Example contract: `fetchOrder(id, { signal })` resolves to an order DTO; a missing order produces `<documented result/error>`; aborting the signal cancels the request and produces the documented cancellation result.

## Language and Async Rules

- Prefer `const`, strict equality, and explicit conversions. Do not rely on ambiguous truthiness when zero, empty string, null, and missing are distinct.
- Every Promise MUST be awaited, returned, or have an intentional error-handling owner. Avoid unhandled rejections and uncontrolled parallel requests.
- Timers, event listeners, and subscriptions MUST have clear cleanup ownership.
- Untrusted content MUST NOT be passed to `eval` or unsafe HTML injection sinks.

## Testing and Browser Behavior

- Use the existing Jest, Vitest, Node test runner, or equivalent. Test values, errors, asynchronous outcomes, and public contracts.
- Configure separate line and branch coverage thresholds of at least 80%. Browser behavior SHOULD have real-browser tests in the supported browser matrix.
- Mock at stable boundaries; do not replace the behavior the test is intended to validate.

## Commands, Checks, and Hooks

Suggested tools: Prettier and ESLint. Optional JavaScript type checking via JSDoc and `checkJs` MUST be explicitly configured.

Example package-script contract to define and verify:

```text
npm run format:check
npm run lint
npm run test:coverage
npm run build
```

Record each script's implementation and working directory. Pre-commit: Prettier on staged files, then ESLint. Pre-push: ESLint and selected fast checks. CI: all configured checks and applicable browser tests; never infer success from a script name alone.

## Official Reference

- [JavaScript modules and environment boundaries](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules)
