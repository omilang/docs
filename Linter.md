# Linter

> Complete reference for Omi static analysis and lint workflow.

---

## Navigation

- [Documentation](Documentation.md) - language syntax and core features
- [Modules](Modules.md) - built-in modules and APIs
- [Tests](Tests.md) - test syntax and test runner
- [Linter (this page)](Linter.md) - rules, config, CLI and outputs
- [Architecture](Architecture.md) - implementation and project structure

---

## Overview

Omi linter is an AST-based static analyzer.

It performs:

- lexical + parser-driven checks (syntax-aware)
- scope/symbol checks (unused/undefined/shadowing)
- type-oriented checks (where available from annotations)
- style checks on source lines
- security-oriented checks for dangerous patterns

The linter is available in two modes:

- standalone command: `python shell.py lint <path>`
- pre-run check: `python shell.py run <file.omi> --lint`

---

## CLI Usage

### 1) Standalone lint mode

```bash
python shell.py lint <file.omi|directory> [flags]
```

Supported flags:

- `--fix`: apply auto-fix operations for fixable rules
- `--json`: print machine-readable JSON report
- `--failfast`: in lint mode affects exit strategy when errors are present
- `--level=<error|warning|style|security>`: minimum severity to include
- `--rules=<rule1,rule2,...>`: only include selected rules
- `--config[=path]`: load lint config from `.omilint`

Examples:

```bash
python shell.py lint src
python shell.py lint tests/test_features.omi --json
python shell.py lint tests --rules=undefined-var,unused-var
python shell.py lint example.omi --level=error
python shell.py lint src --fix --config=.
```

### 2) Run mode with lint

```bash
python shell.py run <file.omi> --lint [lint-flags]
```

Behavior:

- linter executes before interpreter runtime
- report is printed first
- script execution continues unless lint abort conditions are met
- when `--fix` applies edits, updated source is used for execution

Example:

```bash
python shell.py run app.omi --lint --level=warning
```

### 3) Directive aliases (`@use`)

When lint runs against a single file (`run <file.omi> --lint` or `lint <file.omi>`), you can set lint flags in source via directives:

```js
@use json
@use fix
@use failfast
@use level as error
@use rules as "undefined-var,unused-var"
@use config
@use config as "./.omilint"
```

Rules:

- `level` and `rules` require `as <value>`
- `config` accepts both `@use config` and `@use config as <path>`
- CLI flags override directive values when both are present

---

## Configuration (.omilint)

### Filename rules

- config files must have `.omilint` extension (e.g., `.omilint`, `config.omilint`, `lint.omilint`)
- with `--config=<dir>`, linter searches for any `.omilint` file (will find first match)
- with `--config=<file>`, file must have `.omilint` extension
- if explicitly provided config is missing, linter raises an error
- special case: if no `--config` is provided, linter looks for `.omilint` first, then any `*.omilint` file

### Lookup behavior

- without `--config`, linter checks current base directory for `.omilint` first, then any `*.omilint` file
- if no file exists, default settings are used
- when multiple `*.omilint` files exist, the first match is used

### Supported sections

```ini
[general]
level = warning
max_line_length = 120
exclude = tests/_tmp*.omi, **/*.bak

[rules]
undefined-var = true
spacing-operators = style
unsafe-import = security

[auto-fix]
enabled = true
rules = trailing-whitespace, empty-lines
```

### General options

- `level`: `error | warning | style | security`
- `max_line_length`: integer
- `exclude`: comma-separated glob patterns

### Rule values

Each key in `[rules]` accepts:

- `true` / `false` to enable or disable rule
- severity override: `error`, `warning`, `style`, `security`

### Auto-fix options

- `enabled`: `true` or `false`
- `rules`: optional comma-separated allow-list for fixable rules

---

## Rule Catalog

### Error-focused

- `undefined-var`: symbol used before declaration
- `type-mismatch`: incompatible assignment/usage relative to annotation
- `const-reassign`: reassignment of `const`
- `missing-return`: non-`void` function declares return type but has no return path
- `invalid-import`: unresolved import target
- `duplicate-param`: duplicated function parameter name
- `unreachable-code`: code after terminating flow (return/break/continue)

### Warning-focused

- `unused-var`: declared variable never read
- `unused-import`: imported alias never used
- `prefer-const`: variable assigned once and never reassigned
- `no-shadow`: declaration shadows symbol from outer scope
- `prefer-nullable`: nullable intent mismatch suggestions
- `division-by-zero-risk`: suspicious divisor patterns

### Style-focused

- `naming-convention`: identifier naming issues
- `max-line-length`: line exceeds configured limit
- `trailing-whitespace`: trailing spaces/tabs at EOL
- `spacing-operators`: missing spacing around arithmetic/assignment/comparison operators
- `empty-lines`: excessive empty line sequences

Notes for `spacing-operators`:

- type/generic angle brackets are ignored (for example `func<void>`, `future<req>`)
- it targets operator spacing, not annotation syntax

### Security-focused

- `eval-usage`: dynamic eval patterns
- `unsafe-import`: sensitive module import usage (for example process/system side effects)
- `hardcoded-secret`: likely secret/token literals in source

---

## Exit Codes

- `0`: no issues
- `1`: errors present
- `2`: non-error issues present (warning/style/security depending on filters)

---

## Report Output

### Text mode (default)

Contains:

- filename + location (`line:column`)
- severity marker (`[ERROR]`, `[WARN]`, `[STYLE]`, `[SECURITY]`)
- rule identifier and message
- source snippet with caret markers
- optional hint
- final summary counts

### JSON mode (`--json`)

Returns structured object with:

- `files`: linted files list
- `issues[]`: each issue with rule, level, message, start/end positions, suggestion, auto-fixable flag
- `summary`: counts by severity and fixable issues

---

## Lint + Test Workflow Recommendations

- During local development:
	- `python shell.py lint src --level=warning`
	- `python shell.py test tests`
- For CI:
	- `python shell.py lint src --json --level=error`
	- `python shell.py test tests --json --save=report.json`
