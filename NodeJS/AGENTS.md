# Node.js — AGENTS.md Example

These are suggested project rules to customize and adopt alongside the shared root AGENTS.md and the applicable JavaScript or TypeScript supplement. They supplement, rather than replace, the shared Git, human review, TDD, documentation, security, architecture, coverage, CI, and resource-efficiency requirements. MUST means required once adopted; SHOULD means recommended with a documented reason for departures.

Replace every `<...>` placeholder with verified project facts. Paths, tools, and commands below are examples, not claims about an existing repository. Preserve existing compatible tooling; do not migrate the runtime, module system, or application framework merely to match this example. Only adopt sections applicable to the application's role: HTTP service, worker, CLI, or another Node.js application.

## Runtime, Package Manager, and Application Profile

Record: supported Node.js versions, package manager/version, lockfile, module format, operating systems, application framework, and deployment model.

Example project facts:

```text
Application: <HTTP service / background worker / CLI>
Node.js: <supported LTS major and pinned development/CI version>
Package manager: <npm / pnpm / Yarn and version>
Module format: <ESM / CommonJS>
Framework: <none / Express / Fastify / NestJS / other and version>
Source and tests: <source directory>, <test directory>
Deployment: <container / VM / serverless / desktop CLI>
Production entry point: <verified executable or built file>
```

- Production applications SHOULD use a supported Node.js LTS release. Development, CI, and deployment MUST use the documented compatible runtime; do not assume the newest documentation matches the installed version.
- Declare runtime/package-manager expectations in the project's existing configuration and enforce them in CI. A declaration alone is not a runtime check.
- Keep the application lockfile versioned. CI MUST use the package manager's reproducible installation mode, such as `npm ci` for an npm project with a valid lockfile.
- Use pinned project-local tooling. Agents MUST NOT silently install global tools, switch package managers, or run unreviewed remote installation scripts.

## Module System and Build Contract

- Make ESM/CommonJS behavior explicit using the project's `package.json` and file-extension conventions. New projects SHOULD prefer ESM when dependencies and deployment support it; existing projects MUST retain their established contract unless migration is in scope.
- Avoid accidental import-time side effects. Importing application modules SHOULD NOT automatically open a port, start workers, or connect to production services.
- Separate application construction from process startup so tests can supply configuration and adapters without starting the production entry point.
- TypeScript applications MUST document whether they compile, bundle, or use runtime TypeScript support. Running TypeScript or stripping its types MUST NOT be treated as type checking; run the configured compiler check separately.
- Verify the actual production artifact and entry point. Test-runner aliases or development loaders MUST NOT hide module-resolution failures in production.

Example layout to adapt: `src/app` constructs the application, `src/main` owns startup/shutdown, `src/domain` contains business behavior, and `src/adapters` contains HTTP/storage integrations. File extensions depend on the selected language and module system.

## Services, Adapters, and Public Contracts

- HTTP handlers, CLI commands, and queue consumers MUST translate external inputs into validated DTOs and delegate business behavior to services.
- Domain entities and services MUST remain independent of framework request/response objects, database clients, and process globals. DAO interfaces MUST support the required interchangeable implementations without leaking persistence models.
- Inject configuration, clocks, and external clients where needed for explicit ownership and testability; do not introduce speculative abstractions.
- Document exported functions, service interfaces, HTTP endpoints, queue messages, and CLI inputs using the shared contract requirements. Include async rejection, cancellation, retry, and resource-ownership behavior where applicable.

Example service contract: `submitOrder(dto, { signal })` resolves to an order-view DTO; quantity must be a positive integer; monetary units and rounding are explicit; missing optional fields have documented defaults; abort and validation failures have documented outcomes.

## Configuration and Startup Validation

Record each configuration key's type, unit, valid range, default, required status, sensitivity, and owner. Keep actual secret values external.

Example configuration contract:

| Illustrative key | Suggested documented behavior |
| --- | --- |
| `PORT` | Integer from 1 through 65535; default `<port>`; HTTP services only. Tests may use port 0 for automatic allocation. |
| `REQUEST_TIMEOUT_MS` | Positive integer milliseconds; default `<timeout>`; identifies exactly which request phase it limits. |
| `DATABASE_URL` | Required only for the selected database adapter; sensitive; never logged. |
| `LOG_LEVEL` | One of the logger's supported levels; default `<level>`. |

- Parse environment strings explicitly; the string `false` MUST NOT be interpreted as true merely because it is nonempty.
- Validate required configuration before accepting work. Report invalid key names and remediation without echoing sensitive values.
- Application code SHOULD consume validated configuration rather than reading `process.env` throughout business logic. Environment files containing secrets MUST NOT be committed.

## Event Loop, Async Work, and Streams

- Request and job-processing paths MUST avoid synchronous filesystem, subprocess, or other blocking operations. CPU-intensive work SHOULD be bounded, partitioned, or moved to an appropriate worker/job system when measurements or workload requirements justify it.
- An `async` function does not make CPU-bound work nonblocking. Bound input sizes and concurrency; do not launch an unlimited `Promise.all` over external input.
- Each Promise, timer, listener, and background task MUST have an explicit owner for failures and cleanup.
- External operations MUST have documented timeouts and cancellation where supported. Use compatible `AbortSignal` APIs when appropriate; racing a timeout against an operation does not by itself stop the underlying work.
- Large payloads SHOULD be streamed when practical. Respect stream backpressure and handle failures across the entire pipeline, using facilities such as `node:stream/promises` where supported.
- Retries MUST be bounded and restricted to appropriate transient failures. Non-idempotent operations need a defined duplication-prevention strategy before automatic retry.

Reference: [Node.js event-loop and worker-pool guidance](https://nodejs.org/en/learn/asynchronous-work/dont-block-the-event-loop).

## HTTP and Application Security

For HTTP services, record authentication, authorization, validation, body limits, timeout phases, rate-limit ownership, trusted proxies, CORS, and cookie/CSRF policies.

- Validate untrusted input at boundaries, including message payloads and configuration received from external sources. Type annotations are not runtime validation.
- Enforce authorization on the server. CORS settings and client checks MUST NOT be treated as authorization.
- Set bounded request sizes and appropriate server/client timeouts. Trust forwarded headers only from the configured proxy chain.
- Use parameterized database operations. Constrain user-influenced filesystem paths and outbound URLs according to the application's approved access policy.
- Avoid invoking a shell with untrusted input; use structured executable arguments and an approved executable when subprocesses are necessary. Do not evaluate untrusted JavaScript.
- Dependency changes SHOULD review maintenance, compatibility, known vulnerabilities, and install-script behavior. Run the project's dependency checks without automatically applying unrelated major-version fixes.
- Keep credentials and PII out of logs, error responses, bundled assets, and diagnostic dumps. Apply the shared secret-scanning requirement in CI.

Reference: [Node.js security best practices](https://nodejs.org/en/learn/getting-started/security-best-practices).

## Errors, Logging, and Process Lifecycle

- Map expected validation, authorization, and domain failures into documented transport or CLI errors. Responses MUST be actionable and MUST NOT expose raw stacks, queries, or internals.
- Use the existing structured logger with safe operation/error identifiers and correlation context where appropriate. Avoid duplicate logging of the same failure at every layer.
- Unexpected uncaught exceptions MUST NOT be swallowed to continue normal service. Define bounded failure handling and termination, with restart owned by the deployment supervisor where used.
- Long-running services MUST document graceful shutdown: stop accepting work, update readiness, drain in-flight requests/jobs within a deadline, close owned connections, and terminate. Make shutdown idempotent and match signal handling to the deployment platform.
- Distinguish liveness from readiness. Health endpoints MUST NOT disclose secrets or detailed internal diagnostics.
- CLI applications SHOULD reserve stdout for intended output, send diagnostics to stderr, and use documented exit codes. Do not prematurely terminate before pending output or owned cleanup completes.

## Tests, Coverage, and Integration Services

- Use the shared clarify/test-first/implement/refactor/verify workflow. Retain the existing compatible runner: Node's built-in test runner, Vitest, Jest, or another established tool.
- Unit-test domain/services with controlled clocks and appropriate fakes. Use temporary directories, isolated state, and dynamically allocated ports where practical.
- Add contract tests for public APIs and DAO adapters. Integration tests SHOULD exercise actual HTTP serialization, middleware/error handling, storage behavior, and queue semantics where those are used.
- Test timeouts, cancellation, cleanup, invalid configuration, and graceful shutdown when relevant. Tests MUST close servers, clients, listeners, and timers they own rather than forcing exit to hide leaks.
- CI MUST enforce **line coverage >=80% and branch coverage >=80% separately**, covering intended application source rather than only modules imported by tests. Document exclusions and source-map handling for compiled TypeScript.
- Record required database/container/broker services and supported runtime/OS matrix. Missing infrastructure is an unrun check, not a pass.

Reference: [Node.js test runner; select documentation for the pinned version](https://nodejs.org/api/test.html).

## Commands, CI, and Git Hooks

Record verified commands, working directories, configuration files, and prerequisite services. The following script names are suggestions to define, not built-in npm capabilities:

| Example command | Required project meaning |
| --- | --- |
| `npm ci` | Install the npm lockfile reproducibly in the intended checkout/CI environment. |
| `npm run format:check` | Check formatting without changing files. |
| `npm run lint` | Run ESLint or the existing linter/static analysis. |
| `npm run typecheck` | Run the configured TypeScript or checked-JavaScript type check, when applicable. |
| `npm run test:coverage` | Run tests and enforce independent line/branch gates. |
| `npm run test:integration` | Run required service/adapter integration checks. |
| `npm run build` | Produce the deployable artifact if a build step exists. |
| `npm start` | Start the documented application entry point with validated configuration. |

- Adapt commands to the selected package manager. Plain JavaScript projects without a build step SHOULD document that fact rather than invent a build task.
- Suggested tools: Prettier, ESLint, and the chosen test/coverage stack. Reuse repository configurations and pinned versions.
- Pre-commit MUST format staged files first, then lint. Pre-push MUST run lint and selected lightweight validation. Hooks MUST be versioned and shareable.
- CI MUST independently run applicable checks, secret scanning, coverage gates, and required integration tests, then smoke-test the deployable artifact. Local hooks do not replace CI or human review.
- Example commands were not executed against an application repository; verify their implementations before adoption.

## Deployment and Resource Budgets

Record: deployment artifact, runtime dependencies, migration ownership, resource limits, shutdown grace period, and startup/readiness expectations.

- Production packaging MUST include required runtime dependencies and built files. Verify that omission of development dependencies does not break startup.
- Containers SHOULD run with the least privileges and filesystem access the application requires. Match native dependencies to the deployment OS/architecture.
- Keep memory, connection pools, queue concurrency, and payload sizes bounded according to the workload. Define measurable latency/throughput budgets when performance is a requirement.
- Serverless applications MUST follow their host's lifecycle; do not assume process signal handlers or long-running background tasks will execute to completion.

## Additional Official References

- [Node.js module and package configuration](https://nodejs.org/api/packages.html)
- [npm reproducible installation with npm ci](https://docs.npmjs.com/cli/commands/npm-ci/)

Recommendations checked September 10, 2026. Use API and framework documentation matching the installed versions.
