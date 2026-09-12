# HTML — AGENTS.md Example

These are suggested project rules to customize and adopt alongside the shared root AGENTS.md. They supplement, rather than replace, its Git, review, TDD, documentation, security, architecture, coverage, CI, and resource-efficiency requirements. MUST means required once adopted; SHOULD means recommended with a documented reason for departures.

Replace every `<...>` placeholder with verified project facts. Named directories, commands, and tools below are examples, not claims about an existing repository. Preserve existing compatible tooling; do not migrate versions, frameworks, or build systems merely to match this example. Record commands, their working directories, configuration files, and required services before enabling them in CI or hooks.

## Rendering Context and Supported Browsers

Record: plain HTML or template engine, server/client rendering, page routes, browser matrix, localization, and accessibility target.

Example: templates in `<templates directory>`; generated pages in `<build directory>`; language `<locale>`; accessibility target `<project standard>`.

- Agents MUST edit source templates rather than generated HTML unless the repository identifies generated output as the source of truth.
- Record escaping conventions and the approved location for shared layouts and partials.

## Semantic Structure and Accessibility

- Use native semantic elements, meaningful document titles, a declared document language, logical headings, and appropriate landmarks.
- Actions MUST use buttons; navigation MUST use links. Interactive controls MUST be keyboard accessible and have accessible names.
- Inputs MUST have associated labels; related controls SHOULD use suitable grouping. Errors MUST identify the affected field and provide a correction path.
- Images MUST have purpose-appropriate alternatives; decorative images SHOULD use empty alternative text. Do not add redundant ARIA where native HTML already supplies the needed semantics.

Example:

```html
<label for="email">Email address</label>
<input id="email" name="email" type="email" autocomplete="email"
       aria-describedby="email-help" required>
<p id="email-help">Enter the address where you want order updates.</p>
```

## Template Contracts and Security

- Document template input DTOs, required/optional fields, escaping, slots/partials, and output semantics. Keep business decisions in services rather than duplicating them in templates.
- Untrusted content MUST be escaped using the template engine's context-aware mechanism. Raw HTML insertion requires the project's approved sanitization boundary.
- Secret values MUST NOT appear in hidden inputs, comments, page source, or embedded scripts.

## Validation and Coverage Applicability

- Write failing DOM/accessibility assertions first when changing observable behavior or structure. Then implement and verify representative rendered pages.
- Suggested checks: HTML validation, automated accessibility scanning, link/form behavior, and manual keyboard/focus review. Automated accessibility tools do not establish full conformance by themselves.
- Pure HTML has no executable line/branch coverage metric. Mark it not applicable; validate structure and behavior instead. Associated JavaScript and template logic MUST retain applicable executable-code coverage gates.
- Do not add artificial business or persistence layers to a static document; define its data contract with the application that renders it.

## Commands and Hooks

Example package scripts to define: `format:check` (Prettier with the correct template parser), `lint:html` (HTML validator), `test:a11y`, `test:browser`, and `build`.

Pre-commit formats staged templates before validation; pre-push runs the configured validator/lightweight checks. CI validates generated representative pages as well as source syntax. Record browser viewport, form-state, localization, and assistive-technology checks required for release.

## Official Reference

- [Semantic HTML and accessibility](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Accessibility/HTML)
