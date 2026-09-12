# React — AGENTS.md Example

These are suggested project rules to customize and adopt alongside the shared root AGENTS.md. They supplement, rather than replace, its Git, review, TDD, documentation, security, architecture, coverage, CI, and resource-efficiency requirements. MUST means required once adopted; SHOULD means recommended with a documented reason for departures.

Replace every `<...>` placeholder with verified project facts. Named directories, commands, and tools below are examples, not claims about an existing repository. Preserve existing compatible tooling; do not migrate versions, frameworks, or build systems merely to match this example. Record commands, their working directories, configuration files, and required services before enabling them in CI or hooks.

## Framework, Rendering, and Toolchain

Adopt the applicable JavaScript/TypeScript, HTML, and CSS/SCSS supplements.

Record: React version, host framework/bundler, router, server/client component boundaries, SSR/hydration, state/data tools, and test environment.

Example: `<React version>` with `<framework>`; components in `<directory>`; data adapters in `<directory>`; runtime `<browser/server/both>`.

- Agents MUST preserve the host framework's routing and rendering conventions. Do not add a new state manager or migrate build tooling incidentally.

## Components, Hooks, and State

- Components and Hooks MUST follow React's purity and Hook rules. Do not mutate props, state, or Hook inputs; update state through the approved setters/reducers.
- Hooks MUST be called in positions allowed by the installed React version's rules. Enforce these rules with the compatible React Hooks ESLint plugin rather than inventing exemptions.
- Prefer derived values over duplicate state. Effects SHOULD synchronize with external systems; avoid effects for values that can be computed during render.
- Effects, listeners, subscriptions, and requests MUST have appropriate cleanup and stale-result handling.
- List keys MUST represent stable identity. Do not use changing/random keys or array indexes when identity can change.

## Architecture and Component Documentation

- UI components SHOULD use DTO/view models and call service interfaces; domain decisions and persistence MUST remain outside rendering code.
- Public components MUST document props, defaults, children/slots, callbacks, controlled/uncontrolled behavior, accessibility, loading/empty/error states, and examples.

Example contract: `OrderPicker` receives `orders` and `selectedId`; `onSelectionChange(id)` requests a change; the parent owns selection. `null` means no selection. Loading and failed fetches have distinct user-visible states.

## Server Boundaries and Error Handling

- Server-only credentials and modules MUST NOT enter client bundles. Document which data can cross the server/client boundary.
- Avoid browser APIs during server rendering. Initial markup MUST respect hydration requirements.
- Use error boundaries for rendering failures where appropriate; event-handler and async failures need explicit handling because an error boundary does not generally catch them.
- Raw HTML insertion requires the approved sanitization boundary.

## Tests, Tooling, and Commands

- Prefer interaction tests that query user-visible roles/names, using the repository's Testing Library or equivalent. Test callbacks, keyboard behavior, loading/failure states, and effects' cleanup.
- Test domain/services independently. Enforce executable line and branch coverage >=80% separately; use browser tests for critical flows and hydration where relevant.
- Suggested tools: Prettier, ESLint with compatible React Hooks rules, the configured test runner, and TypeScript checks where used.

Define project scripts `format:check`, `lint`, `typecheck` if applicable, `test:coverage`, `test:browser`, and `build`. Pre-commit formats before lint; pre-push runs lint/lightweight validation; CI adds production build and required runtime tests. Measure rendering or bundle regressions against documented budgets; do not add memoization without a demonstrated need.

## Official References

- [Rules of React](https://react.dev/reference/rules)
- [React Hooks lint rules](https://react.dev/reference/eslint-plugin-react-hooks/lints/rules-of-hooks)
