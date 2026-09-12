# Python — AGENTS.md Example

These are suggested project rules to customize and adopt alongside the shared root AGENTS.md. They supplement, rather than replace, its Git, review, TDD, documentation, security, architecture, coverage, CI, and resource-efficiency requirements. MUST means required once adopted; SHOULD means recommended with a documented reason for departures.

Replace every `<...>` placeholder with verified project facts. Named directories, commands, and tools below are examples, not claims about an existing repository. Preserve existing compatible tooling; do not migrate versions, frameworks, or build systems merely to match this example. Record commands, their working directories, configuration files, and required services before enabling them in CI or hooks.

## Runtime, Dependencies, and Layout

Record: supported Python versions, dependency manager, lockfile policy, package layout, and deployment target.

Example project facts:

- Python: `<supported versions>`; dependency manager: `<pip/uv/Poetry/other>`.
- Source: `src/example_app/`; tests: `tests/`; configuration: `pyproject.toml`.
- Projects MUST have an isolated runtime environment, preferably using `venv`. 
- Agents MUST use the project's isolated environment and declared dependencies. They MUST NOT install globally or change the dependency manager as part of unrelated work.
- Any dependencies and their versions should be recorded in `requirements.txt` or `pyproject.toml`.
- Projects should be architected into nested packages. 

## Types and Public Documentation

- Public functions and methods MUST have type annotations and docstrings covering the shared documentation contract. Use the project's chosen Google, NumPy, or reStructuredText docstring style consistently.
- Use `T | None` only where the minimum Python version supports it. Document whether `None` means omitted, unknown, or intentionally empty; use a sentinel when missing and explicit `None` differ.
- Domain models SHOULD use suitable dataclasses/value objects. DAO boundaries SHOULD use `Protocol` or abstract interfaces as appropriate; persistence objects MUST NOT become public response models.

Example contract: `timeout_seconds` is a positive float; omitted means the documented default; `None` disables the timeout only if explicitly supported; invalid values raise `ValueError`.

## Language-Specific Safety and Concurrency

- Agents MUST avoid mutable default arguments and wildcard imports in application code.
- Use context managers for files and other owned resources. Catch specific exceptions and preserve causes when translating failures.
- Async code MUST NOT perform blocking I/O on the event loop. Document cancellation and resource cleanup where async work is used.
- Use parameterized database operations. Do not deserialize untrusted pickle data.

## Tests and Coverage

- Suggested stack: pytest, reusable fixtures, and coverage.py; retain an existing suitable unittest setup.
- Use temporary directories and controlled clocks/network doubles. Test exception types, boundary values, and async cancellation when applicable.
- CI MUST enforce line >=80% and branch >=80% separately. A combined coverage.py percentage or a single `--fail-under=80` is not evidence that both independent thresholds passed. Use branch measurement and a repository check of each metric in the machine-readable report.

## Tooling, Commands, and Hooks

Example commands, after installing and configuring these tools in the project environment:

```text
python -m black --check src tests
python -m ruff check src tests
python -m mypy src
python -m pytest
python -m coverage run --branch -m pytest
python -m coverage json
```

Choose one formatter: existing Black, or Ruff's formatter if adopted. Record `<independent coverage gate command>`, packaging checks, and supported-version matrix. Pre-commit runs the chosen formatter on staged files before Ruff; pre-push runs lint and the documented lightweight validation. CI performs non-mutating checks plus the full suite.

## Official References

- [Python virtual environments](https://docs.python.org/3/library/venv.html)
- [Ruff formatter and linter](https://docs.astral.sh/ruff/)
- [coverage.py branch measurement](https://coverage.readthedocs.io/en/latest/branch.html)
