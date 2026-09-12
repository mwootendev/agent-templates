# Language and Framework AGENTS.md Examples

Twelve customizable supplements to the shared agent specification. Each folder contains a file named `AGENTS.md` with suggested sections, sample normative rules, concrete project facts to record, validation/tooling examples, and official references.

## How to Use

1. Keep your shared specification at the target repository root. These examples do not replace it and are not complete standalone governance policies.
2. Select the relevant language and framework sections. Merge selected content into the target AGENTS.md, or place scoped supplements in relevant subdirectories using your agent's supported instruction-scoping behavior.
3. Replace `<...>` placeholders and illustrative paths with verified repository facts. Resolve version, runtime, architecture, accessibility, coverage, and tooling choices before adopting the rules.
4. Keep existing compatible project tools. Example script names do not imply those scripts exist; define and verify their actual implementations before relying on them.
5. Consolidate overlapping requirements when combining examples. Do not rely on sibling files being loaded automatically.

The original shared AGENTS.md has not been changed. MUST/SHOULD wording within the examples expresses the suggested policy once adopted. Recommendations were checked against linked official references on September 10, 2026; use documentation matching the installed versions.

## Select Examples

| Example | Main customization areas |
| --- | --- |
| [Python](python/AGENTS.md) | Runtime/environment, annotations/docstrings, async I/O, independent coverage gates |
| [Java](java/AGENTS.md) | JDK/build wrapper, Javadoc/nullability, resource ownership, JaCoCo rules |
| [JavaScript](javascript/AGENTS.md) | Browser/server boundaries, modules, JSDoc, Promise ownership, runtime validation |
| [TypeScript](typescript/AGENTS.md) | Compiler strictness, type narrowing, public types, separate type checking |
| [Lua](lua/AGENTS.md) | Dialect/host globals, table semantics, coroutine ownership, coverage limitations |
| [HTML](html/AGENTS.md) | Template engine, semantics, forms, escaping, accessibility validation |
| [CSS](css/AGENTS.md) | Design tokens, cascade, responsive states, motion, visual checks |
| [SCSS](scss/AGENTS.md) | Sass compiler/modules, public mixins, nesting, compiled output |
| [Angular](angular/AGENTS.md) | Workspace/version, components/services, reactivity, template checks, builders |
| [React](react/AGENTS.md) | Rendering mode, Hooks/state, component contracts, hydration, interaction tests |
| [Web Components](web-components/AGENTS.md) | Registration/lifecycle, attributes/events/slots, shadow DOM, forms, browser tests |
| [Spring Boot](spring-boot/AGENTS.md) | Boot/JDK compatibility, DTO/service/DAO boundaries, transactions, security, integration tests |

## Suggested Combinations

- Python service: shared specification + Python.
- Spring Boot service: shared specification + Java + Spring Boot.
- Angular application: shared specification + TypeScript + Angular + HTML + CSS, plus SCSS if used.
- React application: shared specification + JavaScript or TypeScript + React + HTML + chosen stylesheet language.
- Custom-element library: shared specification + JavaScript or TypeScript + Web Components + HTML + CSS.

## Coverage and Applicability

The shared 80% line and 80% branch thresholds apply to measurable executable code. HTML and CSS do not have equivalent executable branch metrics; SCSS compilation does not ordinarily provide them either. Their examples define structural, accessibility, compilation, and visual checks, while associated executable logic retains code-coverage requirements. Record this scope explicitly when adopting a supplement.

Lua tooling needs special attention: standard LuaCov output must not be presented as measured branch coverage. The Lua example retains the requirement and calls for a validated branch tool or an explicit unresolved gap.

## Validation of This Collection

Checked: all 12 named examples exist, have nonempty content, include customization sections and official reference links, and have balanced code fences. Archive contents match the generated collection. The sample project commands were not executed against application repositories: these are documentation examples, not configured projects or installed toolchains.
