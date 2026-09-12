# CSS — AGENTS.md Example

These are suggested project rules to customize and adopt alongside the shared root AGENTS.md. They supplement, rather than replace, its Git, review, TDD, documentation, security, architecture, coverage, CI, and resource-efficiency requirements. MUST means required once adopted; SHOULD means recommended with a documented reason for departures.

Replace every `<...>` placeholder with verified project facts. Named directories, commands, and tools below are examples, not claims about an existing repository. Preserve existing compatible tooling; do not migrate versions, frameworks, or build systems merely to match this example. Record commands, their working directories, configuration files, and required services before enabling them in CI or hooks.

## Browser, Design System, and Style Ownership

Record: browser matrix, styling strategy, design-token source, breakpoints, themes, and stylesheet entry points.

Example: tokens in `styles/tokens.css`; components in `styles/components/`; naming `<BEM/CSS Modules/other>`; approved cascade order `<list>`.

- Agents MUST preserve the project's styling architecture and approved tokens. Avoid adding a competing styling system.
- Document ownership of global resets and global selectors; component styles SHOULD remain scoped.

## Cascade and Maintainability

- Prefer low-specificity selectors and explicit component states. Avoid IDs for styling and unexplained `!important` overrides.
- Use existing cascade layers where supported and configured; do not introduce them without considering precedence against unlayered rules.
- Shared custom properties MUST document purpose, valid values, units, fallback behavior, inheritance, theme overrides, and intended consumers.

Example token contract: `--control-gap` accepts a non-negative CSS length, defaults to `0.5rem`, and controls spacing between sibling form actions.

## Responsive and Accessible Presentation

- Layout MUST support the project's viewport range, text zoom, long content, and keyboard focus visibility.
- Prefer logical properties where direction-aware layout is required. Use intrinsic layout techniques before adding device-specific breakpoints.
- Honor reduced-motion preferences for nonessential animation. Color MUST NOT be the only way to communicate status.
- Test contrast and focus indicators in all supported themes; do not remove outlines without an accessible replacement.

Example:

```css
.actions { display: flex; flex-wrap: wrap; gap: var(--control-gap, 0.5rem); }
@media (prefers-reduced-motion: reduce) {
  .status-indicator { animation: none; }
}
```

## Visual Tests and Coverage Applicability

- Define expected layout or state assertions before changing styles where practical. Test responsive boundaries, overflow, focus, disabled/error states, themes, and reduced motion.
- Screenshot baselines MUST be reviewed for meaningful changes; do not blindly accept new images to make tests pass.
- CSS does not have executable line/branch coverage. Do not equate browser CSS-usage reports with the shared code-coverage thresholds. Use a documented visual/state matrix; associated script logic retains code-coverage gates.
- Measure CSS size and performance against `<project budget>` when relevant; avoid speculative micro-optimization.

## Tooling and Hooks

Suggested tools: Prettier, Stylelint, and existing browser/visual testing tooling. Example scripts to define: `format:check`, `lint:css`, `test:visual`, `test:a11y`, and `build`.

Pre-commit formats staged CSS before Stylelint; pre-push runs lint and lightweight validation. CI checks styles and the required visual/accessibility matrix. Stylelint configuration MUST match the syntax and support policy.

## Official References

- [Reduced-motion media query](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/At-rules/%40media/prefers-reduced-motion)
- [Stylelint setup](https://stylelint.io/user-guide/get-started/)
