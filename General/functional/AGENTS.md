# Functional Programming — AGENTS.md Example

This language-neutral supplement provides suggested project rules to adopt alongside the shared root AGENTS.md and relevant language/framework supplements. It does not replace their Git, human review, TDD, documentation, security, coverage, CI, or resource-efficiency requirements. MUST means required once adopted; SHOULD means recommended with a documented reason for departures.

Replace `<...>` placeholders with verified project facts. Examples are illustrative contracts or pseudocode, not production APIs. Apply idioms supported by the host language; adopting functional practices does not authorize a rewrite, require a functional library, or prohibit useful object-oriented boundaries.

## Project Functional Profile

Record which modules use functional design, how values/errors/effects are represented, and where application state is owned.

Example project facts:

```text
Language/runtime: <name and supported version>
Pure domain modules: <paths/packages>
Effectful adapters and orchestration: <paths/packages>
Data model: <records / immutable objects / tagged unions / other>
Expected errors: <Result/Either equivalent / established convention>
Optional values: <Option/Maybe equivalent / explicit nullability>
State owner: <request / service / reducer / actor / other>
Async/effect model: <native tasks/promises / chosen abstraction>
Property-testing tool: <existing compatible tool, if used>
```

Prefer native language facilities and established libraries. Agents MUST NOT introduce unfamiliar abstractions solely to make code appear more functional.

## Pure Functions and Explicit Dependencies

- Domain calculations SHOULD be pure: the same inputs produce the same result without externally visible side effects. Inputs include all values that influence the decision.
- Functions documented as pure MUST NOT read changing process globals, clocks, randomness, network, or storage, or perform logging and mutation visible to callers.
- Pass time, generated identifiers, and other nondeterministic values as explicit inputs when they influence a pure calculation. Injecting a clock function makes the dependency visible but does not make calling that clock pure.
- Keep functions focused, named for their transformation or decision, and within the shared readability/size guidance. Prefer explicit intermediate names over dense expressions.

Example contract:

```text
calculateExpiry(createdAt, lifetimeSeconds) -> expiryInstant
  Inputs have documented timezone/units and valid ranges.
  Returns the same instant for the same valid inputs.
  Does not read the current time or change either input.
```

Reference: [Microsoft's functional programming concepts: functions, immutable values, and purity](https://learn.microsoft.com/en-us/dotnet/fsharp/tutorials/functional-programming-concepts).

## Immutability and State Transitions

- Prefer immutable domain values and transformations that return new state. Functions MUST NOT unexpectedly mutate caller-owned inputs or captured state.
- Document whether immutability is shallow or deep. Read-only bindings and wrappers do not necessarily protect nested collections or referenced objects.
- Use structural sharing or suitable persistent collections where available and worthwhile; do not deep-copy entire application graphs by default.
- Local mutation MAY be used inside a function when it cannot escape, preserves its contract, and improves clarity or measured performance. Document non-obvious ownership assumptions.
- Stateful operations SHOULD make old state, input event/command, and new state explicit. The orchestrator owns committing that state and handling concurrency.

Example pseudocode:

```text
cancelOrder(order, reason) -> Result<newOrder, CancelError>
  if reason is empty: return Error(InvalidReason)
  if order is not cancellable: return Error(InvalidState)
  return Ok(copy of order with status Cancelled and cancellationReason reason)
```

The example never mutates `order`; storage and notifications occur at an effect boundary. The project's result syntax and copying semantics MUST be defined before implementation.

## Data Modeling and Total Behavior

- Represent meaningful alternatives explicitly with tagged/discriminated unions or idiomatic equivalents. Avoid unrelated boolean flags that permit contradictory states.
- Validate external values before constructing domain values. Units, ranges, and invariants MUST remain explicit even when the language cannot enforce them statically.
- Functions SHOULD define behavior for every input in their declared domain, including empty collections and absent values. Avoid unchecked indexing, unsafe unwrapping, and incomplete case handling.
- Expected absence and expected failure SHOULD have explicit representations. They MUST NOT be conflated with success values such as empty strings or zero when those values are valid results.
- Use the repository's established error model. Do not broadly convert programming defects into normal results or introduce a second error convention without a defined boundary.

Example model: `LoadState = Idle | Loading | Loaded(OrderView) | Failed(UserError)` makes loading, success, and failure alternatives explicit. It does not by itself define the async cancellation or ownership policy.

## Composition and Collection Processing

- Compose small transformations when the data flow remains easy to read. Use named helpers for important domain steps.
- Use map for transformation, filter for selection, and fold/reduce for accumulation where these improve clarity. Do not hide network calls or mutation inside a pipeline advertised as pure.
- Higher-order functions SHOULD accept narrow, documented callbacks. Specify callback invocation, ordering, failure propagation, and whether evaluation is deferred.
- Avoid excessive currying, point-free expressions, deeply nested lambdas, and custom operators that make routine maintenance harder.
- Recursion SHOULD be used only where clear and safe for the runtime and input size. Do not assume tail-call optimization; iteration is appropriate when needed for stack safety.

Reference: [Functions, composition, and partial application in F#](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/functions/). These illustrate options rather than requirements for every language.

## Effect Boundaries and Layered Architecture

- Keep I/O, logging, persistence, and external communication in explicit adapters/orchestration. Pure domain functions SHOULD return decisions or values for those boundaries to act upon.
- Preserve the shared presentation/domain/persistence separation. Domain entities can be immutable records with identity and validated transition functions; they need not be mutable classes.
- DAO contracts may be interfaces or well-defined records/modules of functions. In-memory, file, and database implementations MUST satisfy the same supported contract; persistence models MUST NOT leak into the pure core.
- Use the host language's resource cleanup, cancellation, and transaction facilities at the effect boundary. Pure calculations do not guarantee that external operations are atomic or repeatable.
- Retrying effects MUST follow an explicit idempotency/duplication policy. A pure decision function does not make the database write or message send idempotent.
- Describing an effect as data SHOULD be used when orchestration or testing benefits; do not invent a general-purpose effect interpreter for a single straightforward operation.

Example flow: presentation validates a request DTO; an adapter loads data; pure domain functions calculate a transition; a service persists it with concurrency checks; presentation maps the result to a response DTO.

## Evaluation, Async Work, and Performance

- Document eager versus lazy evaluation, iteration repeatability, and resource lifetime for streams/sequences. Re-enumerating a lazy sequence may repeat work or effects.
- Bound concurrency and buffering. Immutable inputs alone do not make external resources safe to access concurrently.
- Avoid repeated collection traversals and excessive intermediate allocation when measurements show a problem. Preserve readability and behavior when optimizing.
- Memoization MUST have an appropriate key, lifetime, and memory bound. Only memoize computations whose validity and inputs are understood; do not cache hidden nondeterministic behavior as though it were pure.

## Documentation and Examples

Public functions and types MUST follow the shared contract requirements: purpose, types, ranges, units, missing/null behavior, returns, errors, deprecations, relationships, and usage examples.

Add functional-specific details: purity/effects, input ownership, returned value sharing, evaluation order when guaranteed, laziness, and callback semantics. State algebraic laws only when they actually hold for the domain and representation.

Example: a normalization function may promise idempotence, `normalize(normalize(x)) = normalize(x)`. Floating-point addition MUST NOT be assumed associative merely because a mathematical idealization is associative.

## Tests and Verification

- Follow the shared TDD workflow. Pure functions SHOULD be tested directly with input/output assertions; mocks usually belong at effect boundaries.
- Cover every modeled alternative, empty/invalid input, expected failure, and input immutability where relevant.
- Property-based tests SHOULD complement examples when meaningful laws exist. Define valid generators and record reproducible failing seeds/cases.
- Useful properties include normalization idempotence, valid-state preservation, and documented encode/decode round trips. Do not assert laws without their preconditions or use a property that merely restates the implementation.
- Integration/contract tests MUST verify adapters, effect ordering when guaranteed, errors, cancellation, and persistence semantics. Pure unit tests do not prove orchestration correctness.
- Retain separate line and branch coverage gates >=80% for executable code. Property tests do not replace coverage or meaningful assertions.

## Tooling, Hooks, and Combining Styles

Record verified `<formatter>`, `<linter>`, `<type checker>`, `<unit/property tests>`, `<integration tests>`, and `<coverage gate>` commands using existing language tooling. Type exhaustiveness checks and mutation rules SHOULD be enabled where compatible and useful.

Pre-commit MUST format before lint; pre-push runs lint/lightweight validation; CI independently runs required checks. Do not add a new library solely to obtain a functional vocabulary.

When combined with the object-oriented supplement, objects may own configuration, resource lifetimes, and interfaces while delegating calculations to pure functions. Document each module's state/ownership contract and avoid imposing incompatible mutation models on the same API.

The rules above are suggested project policy informed by the linked concepts. They are not a mandate to use F# or to adopt every functional technique. Examples and commands require adaptation; no application tests were executed to create this document.
