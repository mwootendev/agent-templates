# SCSS — AGENTS.md Example

These are suggested project rules to customize and adopt alongside the shared root AGENTS.md. They supplement, rather than replace, its Git, review, TDD, documentation, security, architecture, coverage, CI, and resource-efficiency requirements. MUST means required once adopted; SHOULD means recommended with a documented reason for departures.

Replace every `<...>` placeholder with verified project facts. Named directories, commands, and tools below are examples, not claims about an existing repository. Preserve existing compatible tooling; do not migrate versions, frameworks, or build systems merely to match this example. Record commands, their working directories, configuration files, and required services before enabling them in CI or hooks.

## Sass Compiler and CSS Baseline

Adopt the applicable CSS guidelines alongside this supplement.

Record: Sass implementation/version, build integration, entry points, import paths, generated CSS policy, and third-party style dependencies.

Example: Dart Sass `<pinned version>`; entry `styles/main.scss`; partials in `styles/`; output `<generated CSS directory>`.

- Agents MUST use the existing compiler integration and preserve CSS behavior. They MUST NOT edit generated CSS when SCSS is the source.
- Compiler migrations and dependency-wide deprecation cleanup require task scope.

## Modules and Public Style APIs

- New code SHOULD use namespaced `@use` and deliberate `@forward` exports. Avoid introducing deprecated Sass `@import`; this does not prohibit CSS imports where intentionally required.
- Shared variables, mixins, and functions MUST document units, constraints, defaults, return values, errors, and whether they emit CSS.
- Prefer private helpers and a small exported theme API. Use `!default` for intentionally configurable module variables.

Example public module:

```scss
// _spacing.scss: configurable non-negative base spacing, expressed as a CSS length.
$unit: 0.25rem !default;

// panel.scss
@use "spacing";
.panel { padding: spacing.$unit * 4; }
```

## Nesting, Composition, and Runtime Themes

- Nesting SHOULD generally remain at three levels or fewer; avoid selectors coupled to deep DOM structure.
- Use mixins for intentional reuse; review `@extend` for selector expansion and coupling before using it.
- Keep Sass compile-time values distinct from CSS custom properties needed for runtime theming.
- Use supported module functions such as `sass:math` operations where appropriate to the pinned compiler; avoid introducing deprecated arithmetic patterns.
- Check compiled CSS for duplicate output, unexpected specificity, and size growth.

## Tests and Coverage Applicability

- Compile all supported entry points and theme configurations. Use focused tests for reusable Sass functions/mixins plus browser tests of rendered states.
- Preserve the CSS accessibility, visual baseline, and responsive checks. Source-level executable line/branch coverage is generally not supplied by ordinary Sass compilation; do not fabricate equivalent metrics.
- Any build scripts or application code remain subject to their language's coverage requirements.

## Tools, Commands, and Hooks

Suggested stack: Dart Sass, Prettier, and Stylelint configured for SCSS syntax and rules. Example compile check after project configuration: `sass styles/main.scss <output.css>`; replace the output placeholder before running.

Define `format:check`, `lint:scss`, `build:styles`, and `test:visual` scripts. Pre-commit formats before SCSS lint; pre-push runs lint and a lightweight compile; CI builds all required styles and performs browser checks. Existing deprecation warnings SHOULD have documented ownership rather than broad suppression.

## Official References

- [Sass module system](https://sass-lang.com/documentation/at-rules/use/)
- [Sass import deprecation](https://sass-lang.com/documentation/at-rules/import/)
