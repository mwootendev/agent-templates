# Object-Oriented Programming — AGENTS.md Example

This language-neutral supplement provides suggested project rules to adopt alongside the shared root AGENTS.md and relevant language/framework supplements. It does not replace their Git, human review, TDD, documentation, security, coverage, CI, or resource-efficiency requirements. MUST means required once adopted; SHOULD means recommended with a documented reason for departures.

Replace `<...>` placeholders with verified project facts. Examples are illustrative contracts or pseudocode, not production APIs. Use the host language's idioms; adopting this supplement does not authorize a rewrite or require converting every function into a class.

## Project Design Profile

Record the object model, module boundaries, dependency construction mechanism, error convention, and thread-safety policy.

Example project facts:

```text
Language/framework: <name and supported version>
Domain boundary: <module/package>
Public API and DTO boundary: <module/package>
Persistence adapters: <module/package>
Dependency construction: <manual composition / existing DI framework>
Domain errors: <typed exceptions / result objects / established convention>
Mutability policy: <immutable values; controlled entity transitions>
Concurrency model: <single owner / locks / actors / other>
```

Agents MUST preserve established compatible conventions. New abstractions and patterns SHOULD address a concrete responsibility, contract, or substitution need.

## Responsibilities and Encapsulation

- Give each class a cohesive responsibility. Separate domain decisions from transport, formatting, configuration, and storage concerns.
- Public operations MUST preserve documented invariants. Constructors or factories MUST establish a valid state or report why creation failed.
- Keep implementation details private or module-internal. Avoid public writable fields and unrestricted setters for invariant-bearing state.
- Methods SHOULD express domain intent, such as `cancel(reason)`, rather than expose arbitrary changes such as `setStatus(value)`.
- Returning a collection or nested object MUST NOT accidentally allow callers to violate the owner's invariants. Use immutable views, copies, or explicit ownership contracts appropriate to the language.
- Do not build a large utility or manager class that accumulates unrelated responsibilities. Apply the shared small-function guidance without mechanically splitting cohesive behavior.

## Entities, Values, and Relationships

- Distinguish entities with persistent identity from value objects compared by their contents. Document identity and equality semantics explicitly.
- Value objects SHOULD be immutable. Equality and hashing MUST agree; values used as hash keys MUST remain stable while stored in the collection.
- Domain entities MUST control their state transitions. DTOs may be simple data carriers; persistence models describe storage. Neither MUST substitute for a domain entity merely because fields look similar.
- Document ownership, cardinality, lifecycle, and consistency boundaries for relationships. Avoid unrestricted bidirectional object graphs when simpler references satisfy the use case.
- Use aggregates when a real domain consistency boundary requires them. Do not add repositories, factories, or aggregates solely to imitate a pattern catalog.

Example model:

```text
Money: immutable amount + currency, with documented precision and rounding.
Order: identity OrderId; owns order lines and allowed state transitions.
OrderView: presentation DTO; exposes only the client's required data.
OrderDAO: storage interface; concrete adapters map persistence models.
```

## Composition, Inheritance, and SOLID

Apply the shared SOLID principles through these design checks:

| Principle | Suggested concrete rule |
| --- | --- |
| Single Responsibility | A policy change should affect the component that owns that policy, without coupling it to unrelated infrastructure. |
| Open/Closed | Use a focused extension point for an actual variation, rather than repeatedly branching on implementation types. Avoid speculative plugin frameworks. |
| Liskov Substitution | Implementations MUST honor the abstraction's accepted inputs, promised outputs, errors, and observable invariants. |
| Interface Segregation | Clients SHOULD depend only on operations they need; separate read/write capabilities where useful. |
| Dependency Inversion | Business behavior MUST depend on explicit contracts rather than concrete transport or storage implementations. |

- Prefer composition and delegation for reuse. Use inheritance when the subtype genuinely satisfies the parent contract, not merely to share code.
- Subtypes MUST NOT strengthen preconditions or weaken postconditions. An implementation that rejects an operation promised by its interface is not a valid substitute without an explicit contract allowing that result.
- Keep inheritance shallow and intentional. Avoid type checks and downcasts that make clients depend on concrete implementations.
- Interfaces may be language interfaces, protocols, abstract types, or other idiomatic contracts; a separate interface file is not mandatory for every class.

## Dependency Construction and Side Effects

- Required collaborators SHOULD be explicit constructor/factory inputs. Avoid service locators, hidden globals, and mutable singletons that obscure dependencies.
- A composition root SHOULD assemble concrete adapters and services. Domain objects MUST NOT construct their own database connections or reach into framework containers.
- Keep construction free of surprising network or filesystem activity. If initialization requires effects, define a separate, explicit lifecycle operation and its failure behavior.
- Document which object owns and closes resources. Use the language's deterministic cleanup mechanisms where available; do not rely on finalizers for timely cleanup.

## Public Contracts and Error Behavior

Public classes and interfaces MUST follow the shared documentation requirements, including purpose, related types, fields, parameters, units, ranges, null/missing behavior, returns, errors, deprecations, and examples.

Add object-specific contracts: invariants, valid call order, mutation, ownership, equality, thread safety, and state after failure.

Example contract:

```text
Order.cancel(reason):
  Requires a nonempty reason and an order in a cancellable state.
  Success transitions the order to Cancelled exactly once.
  Repeated cancellation follows <documented idempotency policy>.
  Invalid input/state reports the documented domain error.
  Failure leaves the order unchanged.
  Persistence and external notifications are service responsibilities.
```

Agents MUST NOT swallow failures or leave an object partially updated contrary to its contract. Document transactions, compensation, or weaker failure guarantees if an operation cannot promise unchanged state.

## Concurrency and Lifetime

- State whether instances are thread-safe, thread-confined, or require caller synchronization. A read-only reference does not guarantee deep immutability.
- Protect multi-step invariant changes as a unit under the chosen concurrency model. Do not assume separate thread-safe fields make the entire object thread-safe.
- Avoid calling unknown callbacks while holding locks unless the design explicitly handles reentrancy and deadlock risks.
- Shared caches and registries MUST have documented ownership, bounds, invalidation, and shutdown where relevant.

## Tests and Review Examples

- Follow the shared TDD workflow. Test observable behavior and invariants through supported interfaces rather than private implementation details.
- Run reusable contract tests against each implementation, especially interchangeable DAOs. Verify expected state and results; verify interactions only when their ordering or occurrence is part of the contract.
- Cover valid/invalid construction, state transitions, failure-state guarantees, equality/hash behavior, and resource cleanup where applicable.
- Use fixtures/factories to create valid objects. Avoid mocking every collaborator when a small real object or in-memory fake is clearer.
- Enforce the shared independent line and branch coverage thresholds of at least 80%; contract and state-transition tests complement those metrics.

Example acceptance tests: cancelling an eligible order changes its state; cancelling a shipped order reports the specified failure without mutation; every DAO implementation round-trips an order according to the same contract.

## Tooling, CI, and Customization

Reuse the language's formatter, linter, type checker, tests, and static analysis. Record `<format check>`, `<lint>`, `<type check>`, `<test/coverage gate>`, and any `<architecture dependency check>` with verified paths and commands.

Pre-commit MUST format before lint; pre-push runs lint/lightweight validation; CI independently runs required checks. Architecture tests SHOULD enforce meaningful layer boundaries when existing tooling makes this practical. Do not add tooling that merely counts classes or mandates inheritance depth without an actionable project reason.

When combining with the functional supplement, immutable value objects and pure domain calculations can coexist with objects that own lifecycles and effects. Document ownership per module; do not impose both a mutable-entity model and an immutable-state model on the same operation without defining their boundary.

## Official Reference

- [Microsoft: encapsulation, interfaces, inheritance, and object-oriented types](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/). Language-specific examples illustrate concepts; this supplement's policy remains language-neutral.

The rules above are suggested project policy, not a claim that every recommendation is mandated by the cited language documentation. Examples and commands require adaptation; no application tests were executed to create this document.
