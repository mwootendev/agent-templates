# TypeScript — AGENTS.md Example

These are suggested project rules to customize and adopt alongside the shared root AGENTS.md. They supplement, rather than replace, its Git, review, TDD, documentation, security, architecture, coverage, CI, and resource-efficiency requirements. MUST means required once adopted; SHOULD means recommended with a documented reason for departures.

Replace every `<...>` placeholder with verified project facts. Named directories, commands, and tools below are examples, not claims about an existing repository. Preserve existing compatible tooling; do not migrate versions, frameworks, or build systems merely to match this example. Record commands, their working directories, configuration files, and required services before enabling them in CI or hooks.

## Compiler, Runtime, and Project Boundaries

Record: TypeScript version, runtime/browser targets, bundler, package manager, module resolution, and application versus library output.

Example: `src/`, `tests/`, `tsconfig.json`; `<ESM/CommonJS>`; `<bundler>`; declaration output `<enabled/disabled>`.

- Agents MUST preserve runtime-compatible compiler and module settings. Do not copy a generic `tsconfig` without checking the host environment.
- Configure separate browser/server boundaries and project references where the repository requires them.

## Type Safety

- New projects SHOULD enable `strict`, `noUncheckedIndexedAccess`, and `exactOptionalPropertyTypes`. Existing projects MUST retain their chosen policy; any migration needs explicit task scope.
- Prefer `unknown` at untrusted boundaries and narrow it through runtime validation. Types and assertions do not validate external JSON.
- Avoid `any`, non-null assertions, and broad casts. Necessary exceptions MUST be narrow and explained.
- Use discriminated unions for distinct states and explicit DTO/domain/persistence types for layer contracts. Do not use structurally compatible types to excuse model leakage.

Example configuration fragment to evaluate against the repository:

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true
  }
}
```

## Documentation and Public Packages

- Public exports MUST use the shared contract documentation, preferably TSDoc/JSDoc. Explain semantic constraints not captured by types, such as units, limits, thrown errors, and missing versus undefined.
- Public libraries SHOULD test emitted declarations and supported consumer module formats. Keep internal types out of the public export surface.

## Tests and Tooling

- Retain JavaScript async/resource rules. Tests MUST verify runtime behavior as well as compile-time contracts when public types are part of the API.
- Suggested tools: Prettier, ESLint with typescript-eslint, the project's test runner, and separate line/branch coverage gates >=80%.
- A transpiling test runner or bundler MUST NOT be treated as a substitute for TypeScript checking.

Example package scripts to define: `format:check`, `lint`, `typecheck`, `test:coverage`, and `build`. For a non-composite application, `typecheck` might run `tsc --noEmit`; composite/build-mode projects MUST use commands suitable for their references and emission strategy. Pre-commit formats before lint; pre-push adds the configured type check when lightweight enough; CI performs all checks.

## Official References

- [TypeScript strict mode](https://www.typescriptlang.org/tsconfig/strict)
- [Unchecked indexed access](https://www.typescriptlang.org/tsconfig/noUncheckedIndexedAccess.html)
- [Compiler options reference](https://www.typescriptlang.org/tsconfig/)
