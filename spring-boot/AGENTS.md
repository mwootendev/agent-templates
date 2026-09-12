# Spring Boot — AGENTS.md Example

These are suggested project rules to customize and adopt alongside the shared root AGENTS.md. They supplement, rather than replace, its Git, review, TDD, documentation, security, architecture, coverage, CI, and resource-efficiency requirements. MUST means required once adopted; SHOULD means recommended with a documented reason for departures.

Replace every `<...>` placeholder with verified project facts. Named directories, commands, and tools below are examples, not claims about an existing repository. Preserve existing compatible tooling; do not migrate versions, frameworks, or build systems merely to match this example. Record commands, their working directories, configuration files, and required services before enabling them in CI or hooks.

## Version, Build, and Deployment Profile

Adopt the Java supplement for Java projects; record separate Kotlin rules if the project uses Kotlin.

Record: Spring Boot/JDK versions, Maven or Gradle wrapper, servlet versus reactive stack, database, migration tool, deployment profile, and test services.

Example: `<Boot version>`; `<JDK>`; Spring MVC; `<database>`; `<Flyway/Liquibase>`; controller/service/adapter packages under `<base package>`.

- Agents MUST use the project's Boot dependency management and compatible libraries. Do not mix incompatible `javax`/`jakarta` APIs or copy imports from another major version.
- Do not introduce blocking work into a reactive execution path. Choose libraries matching the existing stack.

## Controllers, Domain Services, and Persistence

- Controllers MUST translate request DTOs, validate presentation inputs, delegate to services, and map results to response DTOs. They MUST NOT expose persistence entities.
- Business rules and domain invariants MUST reside in domain/services code. Prefer constructor injection.
- Persistence adapters MUST implement application-owned DAO interfaces. Spring Data/JPA types and storage-specific exceptions MUST NOT leak across those boundaries.
- Transaction boundaries SHOULD follow service use cases. Document rollback behavior and avoid relying on proxy-based transaction interception for self-invocation.
- Schema changes MUST use the repository's versioned migration mechanism; assess compatibility and rollback/recovery before changes.

## HTTP Contracts, Errors, and Security

- Document request/response schemas, units, nullability, validation, status codes, errors, authentication, and examples using the repository's OpenAPI/documentation tools.
- Use centralized, safe error mapping such as the existing controller advice. User errors MUST include actionable messages without stack traces or internal query details.
- Enforce authorization server-side. Configure CORS and CSRF according to the application's authentication and client model; do not blanket-disable security to make tests pass.
- Bind external configuration through typed properties where appropriate. Document required settings and defaults; credentials remain external.

Example contract: `POST /orders` accepts an order-create DTO; invalid quantity produces the documented validation response; successful creation returns the order-view DTO; authorization is checked before the operation.

## Test Layers and Coverage

- Unit-test domain/services without starting Spring when unnecessary. Use framework test slices for focused web or persistence behavior and full-context tests only when their integration is under test.
- Use database integration tests against a representative engine, optionally through compatible Testcontainers support. In-memory substitutes MUST NOT be assumed to reproduce production SQL/transaction semantics.
- Verify HTTP contracts, security, migrations, transaction behavior, and DAO interchangeability. Configure separate JaCoCo line and branch gates >=80%.
- Record required container runtime, images, startup costs, and offline behavior. Missing integration services are an unrun check, not a pass.

## Operations, Commands, and Hooks

- Document health/readiness checks, safe logging fields, profile selection, and externally exposed management endpoints. Do not expose sensitive Actuator endpoints by default.
- Example Maven commands after plugin configuration: `./mvnw spotless:check` and `./mvnw verify`; Windows uses `mvnw.cmd`. Gradle projects MUST list their actual equivalent tasks.
- CI MUST bind formatting, lint/static analysis, compilation, unit/integration tests, coverage gates, secret scanning, and packaging checks. List each bound task so `verify` is not mistaken for automatic coverage of missing plugins.
- Pre-commit formats before lint; pre-push runs lint/lightweight validation. Full application startup is not required in a hook unless the repository deliberately chooses it.

## Official References

- [Spring Boot testing](https://docs.spring.io/spring-boot/reference/testing/)
- [Spring Boot Testcontainers integration](https://docs.spring.io/spring-boot/reference/testing/testcontainers.html)
