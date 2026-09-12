# Java — AGENTS.md Example

These are suggested project rules to customize and adopt alongside the shared root AGENTS.md. They supplement, rather than replace, its Git, review, TDD, documentation, security, architecture, coverage, CI, and resource-efficiency requirements. MUST means required once adopted; SHOULD means recommended with a documented reason for departures.

Replace every `<...>` placeholder with verified project facts. Named directories, commands, and tools below are examples, not claims about an existing repository. Preserve existing compatible tooling; do not migrate versions, frameworks, or build systems merely to match this example. Record commands, their working directories, configuration files, and required services before enabling them in CI or hooks.

## JDK, Build, and Module Configuration

Record: JDK/toolchain version, supported runtime, Maven or Gradle wrapper, module boundaries, and artifact type.

Example: `src/main/java`, `src/test/java`; packages under `<organization>.<product>`; compiler release `<version>`; build tool `<Maven/Gradle>`.

- Agents MUST use the repository's wrapper and configured toolchain. Preview features MUST NOT be enabled incidentally.
- Dependency and plugin versions MUST follow existing dependency-management conventions.

## API Design, Types, and Documentation

- Public types, constructors, methods, and fields MUST have Javadoc describing applicable parameters, units, nullability, returns, exceptions, deprecation, and relationships.
- Record `<nullability annotation library>` and the policy for `Optional`. Example: use `Optional<T>` for an intentionally absent method result; do not return `null` from such a method.
- Immutable value objects and records SHOULD represent values/DTOs when supported by the pinned JDK. Persistence entities MUST remain separate from public DTOs and domain entities under the shared architecture.
- Use `java.time` types with an explicit timezone policy; monetary values SHOULD use decimal arithmetic with documented currency and rounding.

Example contract: `Duration timeout` MUST be non-null and positive; invalid duration raises `IllegalArgumentException`; the method documents whether it owns the supplied resource.

## Resource Ownership and Concurrency

- Use try-with-resources for owned `AutoCloseable` resources. Do not swallow exceptions.
- Document thread-safety, executor ownership, interruption, and cancellation for concurrent APIs. Preserve interrupt status when an interrupt cannot be propagated.
- Prefer interfaces and constructor-supplied dependencies over global mutable state.

## Common Best Practices

- Implement `equals(Object)` based on the Object's fields.
- Agents MUST implement `hashCode()` if overriding `equals()`
- Agents SHOULD implement `toString()`.

## Tests and Analysis

- Suggested stack: compatible JUnit Jupiter, Mockito, focused test doubles, and JaCoCo. Pin versions compatible with the project's JDK.
- Contract tests MUST exercise interfaces across DAO implementations. Integration tests SHOULD verify actual serialization and storage behavior.
- Test Javadocs should identify which method is being tested.
- Test data and data creation SHOULD be defined in `Fixture` classes. For instance, an `OrdersFixture` mayb provide utilities for creating sample orders, with constants such as `ORDER_ID`.
- Configure independent JaCoCo `LINE` and `BRANCH` `COVEREDRATIO` rules, each with minimum `0.80`; document exclusions rather than hiding application code.
- Suggested formatting: google-java-format through Spotless; static analysis: existing Checkstyle, SpotBugs, or equivalent.

## Commands and Hooks

Example Maven profile, only if the named plugins and lifecycle bindings are configured:

```text
./mvnw spotless:check
./mvnw verify
```

Windows equivalent: `mvnw.cmd`. A Gradle project SHOULD document its existing equivalent tasks instead. `verify` MUST actually bind tests, independent coverage gates, and selected analysis; it does not supply these automatically. Pre-commit formats staged Java files before lint; pre-push runs configured lint/lightweight checks.

## Official References

- [Java language and API learning material](https://dev.java/learn/)
- [JaCoCo check rules](https://www.jacoco.org/jacoco/trunk/doc/check-mojo.html)
