# Tests

> Complete reference for Omi native test syntax and test runner.

---

## Navigation

- [Documentation](Documentation.md) - language syntax and core features
- [Modules](Modules.md) - built-in modules and APIs
- [Linter](Linter.md) - static analysis and lint configuration
- [Tests (this page)](Tests.md) - test DSL, runner, flags and reports
- [Architecture](Architecture.md) - parser/interpreter internals

---

## Overview

Omi includes a native test DSL for files with `.test.omi` extension and a built-in runner:

```bash
python shell.py test <file.test.omi|directory> [flags]
```

The runner supports:

- nested suites
- synchronous and asynchronous tests
- suite/test hooks
- skipped tests
- expectation assertions
- fail-fast mode
- JSON report output and file saving

---

## Test File Rules

- single test file must end with `.test.omi`
- directory run includes matching test files recursively
- test keywords are only valid in test context

---

## Test DSL

### Suite

```js
suite "Math":
	// tests/hook blocks
end
```

Suites can be nested.

### Test

```js
test "adds two numbers":
	expect (1 + 1) == 2
end
```

### Async Test

```js
async test "waits for network":
	var<future<http.req>> fut = async http.get("https://example.com")
	var<http.req> res = async fut
	expect res.status == 200
end
```

### Skip Test

```js
skip test "not ready yet":
	expect false
end
```

Marked as skipped and not executed.

### Hooks

Available hook blocks inside suites:

- `before`: run once before suite tests
- `after`: run once after suite tests
- `before_each`: run before each test in suite scope
- `after_each`: run after each test in suite scope

Example:

```js
suite "Users API":
	before:
		println("suite setup")
	end

	before_each:
		println("per test setup")
	end

	test "status endpoint":
		expect true
	end

	after_each:
		println("per test cleanup")
	end

	after:
		println("suite cleanup")
	end
end
```

### Expectations

Base form:

```js
expect <expression>
```

With message:

```js
expect <expression> ~ "Custom failure message"
```

Expectation fails when expression is falsy.

---

## CLI Flags

```bash
python shell.py test <target> [--failfast] [--json] [--save[=path]]
```

Directive aliases for `.test.omi` files:

```js
@use json
@use failfast
@use save
@use save as "report.json"
```

`@use save` is valid only in `.test.omi` files. Using it in a regular `.omi` file raises a runtime error.

### `--failfast`

Stops execution after first failed test (or fatal suite failure).

### `--json`

Prints machine-readable JSON report to stdout.

### `--save[=path]`

Saves JSON report to file.

Behavior:

- `--save=report.json`: explicit file path
- `--save` with single file target: auto-name from test file
- `--save` with directory target: `<directory>-test.json`
- `--save` with multi-file context: fallback consolidated filename

Examples:

```bash
python shell.py test tests
python shell.py test tests --failfast
python shell.py test tests --json
python shell.py test tests --json --save=tests-report.json
python shell.py test tests/api --save
```

---

## Output Format

### Text output

Includes:

- suite boundaries
- each test status marker (`PASS`, `FAIL`, `SKIP`)
- duration per test
- formatted runtime/assertion errors

### JSON output

Contains per-file results:

- test records with description/status/duration
- suite path for each test
- suite-level errors
- fatal errors
- summary counters (`passed`, `failed`, `skipped`)
- `stopped_early` flag for fail-fast behavior

---

## Typical Workflow

### Local development

```bash
python shell.py test tests
```

### Debugging a failing subset

```bash
python shell.py test tests/auth --failfast
```

### CI mode

```bash
python shell.py test tests --json --save=test-report.json
```

---

## Lint + Test Pipeline

A practical strict pipeline:

```bash
python shell.py lint src --level=error
python shell.py test tests --failfast
```

Or machine-readable artifacts:

```bash
python shell.py lint src --json --level=error
python shell.py test tests --json --save=test-report.json
```