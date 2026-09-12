# Lua — AGENTS.md Example

These are suggested project rules to customize and adopt alongside the shared root AGENTS.md. They supplement, rather than replace, its Git, review, TDD, documentation, security, architecture, coverage, CI, and resource-efficiency requirements. MUST means required once adopted; SHOULD means recommended with a documented reason for departures.

Replace every `<...>` placeholder with verified project facts. Named directories, commands, and tools below are examples, not claims about an existing repository. Preserve existing compatible tooling; do not migrate versions, frameworks, or build systems merely to match this example. Record commands, their working directories, configuration files, and required services before enabling them in CI or hooks.

## Runtime and Host Customization

Record: exact Lua dialect/version, embedding host, module loader, available standard libraries, native modules, and packaging method.

Example: `<Lua 5.4/LuaJIT/other>`; modules in `src/`; tests in `spec/`; dependencies managed by `<LuaRocks/host tooling>`.

- Agents MUST target the actual dialect. Lua, LuaJIT, and Luau MUST NOT be treated as interchangeable.
- Host callbacks, sandbox restrictions, filesystem access, and allowed global variables MUST be listed explicitly.

## Modules, Tables, and Contracts

- Use local variables and module-return tables. New globals MUST NOT be introduced outside documented host requirements.
- Document table schemas, optional keys, integer/number assumptions, units, multiple return values, and error conventions using the project's LuaDoc/LDoc or annotation style.
- `nil` represents an absent table key; it cannot represent a stored null-like value. Use a documented sentinel if explicit null is required.
- Sparse tables MUST NOT be treated as reliable sequences using `#` or `ipairs`. State ordering guarantees; do not rely on `pairs` iteration order.

Example contract: `load_order(id)` returns `order, nil` on success or `nil, error_code` on expected failure; order fields and error codes are documented. Unexpected errors follow `<host exception policy>`.

## Host Boundaries and Resource Ownership

- Domain modules SHOULD depend on adapter tables/interfaces rather than host APIs directly. In-memory and real DAO adapters MUST satisfy the same contract tests.
- Handle failures at owned boundaries using appropriate `pcall`/`xpcall` behavior. Do not silently discard errors.
- Document coroutine scheduling, yield restrictions, and cleanup. Do not introduce version-specific cleanup syntax without verifying the runtime.
- Do not execute untrusted strings through `load` or expose privileged host functions to untrusted scripts.

## Tests, Coverage, and Known Gaps

- Suggested tools: Busted for tests, StyLua for formatting, Luacheck for linting, and LuaCov for coverage where compatible with the runtime.
- Configure only legitimate host globals in lint settings; do not broadly disable undefined-global checks.
- LuaCov's standard coverage is line-oriented and MUST NOT be presented as proof of branch coverage. Enforce line >=80%; name a validated branch-capable tool or report branch coverage as unverified. Manual branch test cases do not establish a measured percentage, and the shared branch requirement is not silently waived.

## Commands and Hooks

Example commands after configuring compatible tool versions:

```text
stylua --check src spec
luacheck src spec
busted
```

Record `<coverage collection command>`, `<line gate>`, and `<branch gate or unresolved gap>`. Pre-commit uses StyLua before Luacheck; pre-push runs Luacheck and fast tests; CI runs the supported host matrix and reports any unmet coverage gate.

## Official References

- [Lua reference manuals: choose the pinned runtime](https://www.lua.org/manual/)
- [StyLua supported syntax and configuration](https://github.com/JohnnyMorganz/StyLua)
- [LuaCov](https://github.com/lunarmodules/luacov)
