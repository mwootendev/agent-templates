# Web Components — AGENTS.md Example

These are suggested project rules to customize and adopt alongside the shared root AGENTS.md. They supplement, rather than replace, its Git, review, TDD, documentation, security, architecture, coverage, CI, and resource-efficiency requirements. MUST means required once adopted; SHOULD means recommended with a documented reason for departures.

Replace every `<...>` placeholder with verified project facts. Named directories, commands, and tools below are examples, not claims about an existing repository. Preserve existing compatible tooling; do not migrate versions, frameworks, or build systems merely to match this example. Record commands, their working directories, configuration files, and required services before enabling them in CI or hooks.

## Platform and Distribution Choices

Adopt the applicable JavaScript/TypeScript, HTML, and CSS supplements.

Record: native custom elements or chosen library, supported browsers, naming prefix, shadow/light DOM policy, packaging, registration strategy, and SSR support.

Example: autonomous elements named `acme-*`; ESM package; open shadow roots; explicit registration entry `<entry point>`.

- Agents MUST follow the package's registration convention and avoid duplicate registration errors. Do not assume customized built-in elements are supported across the selected browser matrix.
- Shadow DOM MUST NOT be treated as a security boundary.

## Public Element Contract

- Document tag names, attributes, properties, defaults, reflection, slots, events, methods, CSS custom properties, and exposed parts.
- Attribute/property contracts MUST distinguish strings, booleans, numbers, absent attributes, and explicit empty values. Boolean attributes are generally represented by presence/absence; the text `false` is not absence.
- Event contracts MUST document `detail`, bubbling, composition across shadow boundaries, and cancellation behavior.

Example API specification:

```text
Tag: acme-order-picker
Property: orders: OrderView[]; default []; not reflected to an attribute.
Attribute: disabled; presence disables interaction.
Event: order-selected; detail { id: string }; bubbles true; composed true.
Slot: empty; optional content when orders is empty.
CSS part: trigger; public styling surface.
```

These values are an example design, not a requirement that all component events bubble or cross shadow boundaries.

## Lifecycle and Data Boundaries

- Keep constructors within custom-element lifecycle constraints; initialize DOM-dependent behavior at the appropriate stage.
- Connection logic MUST tolerate disconnection and reconnection without duplicating listeners. Clean up observers, subscriptions, timers, and owned resources.
- Handle property assignment before upgrade where the library's usage permits it. Avoid reflection loops between property setters and attribute callbacks.
- Domain behavior SHOULD remain in services behind explicit interfaces; elements expose presentation contracts.

## Accessibility and Forms

- Prefer native controls inside components. Verify names, focus order, keyboard behavior, and labeling across shadow boundaries.
- Form-associated elements MUST document and test value submission, reset, disabled state, validation, and restoration where supported. Use `ElementInternals` only within the documented compatibility policy.

## Tests, Tooling, and Hooks

- Use a real browser runner for lifecycle, shadow DOM, slots, focus, events, form behavior, and consumer-framework interoperability. A simulated DOM alone is insufficient for platform-specific contracts.
- Enforce JavaScript/TypeScript line and branch coverage >=80%. Add contract tests for attributes/properties/events and visual checks for public styling surfaces.
- Suggested tools: Prettier, ESLint, Stylelint where applicable, and the repository's browser test runner.

Define `format:check`, `lint`, `typecheck` if used, `test:coverage`, `test:browser`, and `build`. Document browser matrix and package smoke-test consumers. Pre-commit formats before lint; pre-push runs lightweight checks; CI runs full browser and distribution checks.

## Official References

- [Custom element lifecycle and registration](https://developer.mozilla.org/en-US/docs/Web/API/Web_components/Using_custom_elements)
- [Templates and slots](https://developer.mozilla.org/en-US/docs/Web/API/Web_components/Using_templates_and_slots)
