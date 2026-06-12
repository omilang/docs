# Omi Language Specification

This document defines the guaranteed language rules for Omi. The detailed reference pages are `Documentation.md`, `Modules.md`, and `Tests.md`; this specification is the behavior contract when a rule needs to be stated briefly and precisely.

## Files and Execution

- Omi source files use the `.omi` extension.
- Native test files use the `.test.omi` extension.
- The CLI supports `omi run <file.omi> [flags] [-- args...]`, `omi test <file.test.omi|directory>`, and `omi lint <path>`.
- Source files are read as `utf-8-sig`, then `utf-8`, then `cp1251`.
- Execution passes through lexer, parser, preprocessor for `@import`, `@use`, and `@set`, then interpreter.

## Syntax

- Statements are separated by newlines.
- Block constructs use `:` after the header and are closed with `end`.
- Single-line comments start with `//`.
- Multiline comments start with `/{` and end with `}/`.
- Strings are written in double quotes.
- String interpolation uses `~name` and `~(expr)`.
- Variables are declared with `var`; constants are declared with `const`.
- A constant must have an initial value and cannot be reassigned or changed through mutating array operations.

```omi
var<int> count = 0
const<string> name = "Omi"

if count == 0:
    println("zero")
end
```

## Types

- Type checking is enabled by default.
- `@use notypes` disables type checks for the current file.
- Built-in types are `int`, `float`, `number`, `bool`, `string`, `array`, `dict`, `null`, `void`, `every`, `call`, and `future<T>`.
- Variable annotations are written as `var<T> name` or `var[T] name` for typed arrays.
- Array size limits are written after the annotation: `var<int>(5) ids = []`.
- Union types use `|`; nullable types use `?`.
- `int?` is equivalent to `int | null`.
- `void` is allowed only as the return type of a function that does not return a value.
- `null` is both a value and the type of explicit absence.
- `every` accepts any value.
- User-defined types are declared with `type`, `enum`, and `trait`.
- Generic types are written as `Name<T>` or `Name<T, E>`.

```omi
type Status = "ok" | "error"
type User = {
    name<string>,
    age<int?>,
}

enum Result<T, E> = {
    Ok(T),
    Err(E)
}
```

## Values and Operators

- Arithmetic supports `+`, `-`, `*`, `/`, `%`, and `^`.
- Comparison supports `==`, `!=`, `<`, `>`, `<=`, and `>=`.
- Logic supports `and`, `or`, `is`, and `isnt`.
- Membership uses `in` for arrays, strings, and dictionaries.
- The ternary operator is written as `value_if_true ~ condition ~ value_if_false`.
- Null coalescing is written as `left ?? fallback`.
- Compound assignment is guaranteed for `+=`, `-=`, `*=`, `/=`, and `%=`.
- Increment/decrement is written as `value++`, `value--`, `++value`, or `--value` on variables and indexed list/dictionary elements; postfix returns the old value and prefix returns the updated value.
- Indexing and slicing apply to supported collections and strings.

## Scope

- Each file has its own global scope.
- Functions, `try/catch/final` blocks, `match case` blocks, tests, and hooks create local scopes for their declarations.
- Function parameters and captures such as `case Ok(value)` are available only inside their corresponding block.
- An inner scope can read names from an outer scope.
- A local declaration with the same name hides the outer name in the current scope.
- Imported modules are available only through their declared alias.

## Functions

- A function is declared as `func<Return> name(arg<Type>): ... end`.
- The short form is written as `func<Return> name(args) -> expr`.
- `func<void>` does not return a value; bare `return` is allowed.
- `func<null>` must explicitly return `null`.
- Parameters may have default values.
- Calls support positional and named arguments; positional arguments must come first.

```omi
func<int> add(a<int>, b<int> = 1) -> a + b
println(add(b=2, a=3))
```

## Errors

- Syntax, type, import, indexing, arithmetic, and runtime errors stop the current run unless they are caught.
- `try/catch/final` catches runtime errors.
- `final` always runs after `try` or `catch`.
- If `final` raises an error, that error becomes the result of the block.
- `throw expr` creates a runtime error with text produced from `expr`.
- The `catch err` variable is an error object with guaranteed fields `type`, `msg`, and `trace`.
- The external Python API returns errors as `err`; callers must check the result of `run()`.

```omi
try:
    throw "bad value"
catch err:
    println(err.type)
    println(err.msg)
    println(err.trace)
final:
    println("done")
end
```

## Imports and Directives

- Directives start with `@` and are processed before execution.
- Built-in modules are imported as `@import "omi:name" as alias`.
- User modules are imported by relative path without the extension.
- A user file can be imported only when it declares `@use module`.
- Module members are accessed with dot notation: `alias.member`.
- Types, enums, and traits from a module are available through the module alias prefix.
- `@set Source as Alias` creates a local alias or name substitution for following code.
- Guaranteed `@use` modes are `notypes`, `eval`, `debug`, `noecho`, `noasync`, `module`, `nolint`, `json`, `fix`, `failfast`, `level`, `rules`, `config`, and `save`.
- `@use nolint` disables the automatic pre-run lint check for the current file.
- Script launch arguments are exposed by `omi:system` as `args()` and `argv()`.

```omi
@import "omi:system" as sys
@import "models" as models
@set models.User as User
```

## Async

- An async function is declared as `async func<T> name(...)`.
- An async call is written as `async name(...)` and returns `future<T>`.
- Await is written as `async future_expr` and returns the value from the future.
- Await is allowed only inside `async func`, `async test`, or an async group body.
- Calling a synchronous function with `async` is an error.
- Calling an async function synchronously is an error, except under `@use noasync`.
- `@use noasync` disables scheduling and runs async code synchronously with a warning.
- An async group is written as `async name:` or `async name(timeout: seconds):`.
- `cancel(future_or_group)` cancels a future or group.

```omi
@import "omi:time" as t

async func<void> wait():
    var<future<null>> fut = async t.sleep(0.1)
    async fut
end
```

## Match

- `match expr:` selects the first matching `case`.
- `case literal:` compares by value.
- `case Variant(name):` checks an enum variant and captures its payload.
- `case _:` is the fallback case.
- If no `case` matches and no fallback exists, Omi raises a `Non-exhaustive match` runtime error.

## Tests

- The native test DSL is available only in `.test.omi` files.
- A test file is built from `suite`, `test`, `async test`, `skip test`, and regular Omi code.
- `suite "name": ... end` groups tests and may be nested.
- Hooks are declared only inside `suite`.
- `test "description": ... end` passes when its body completes without errors and all `expect` statements are truthy.
- `async test` allows the await form `async future_expr` inside the test body.
- `skip test` is registered as skipped and does not execute its body.
- Hooks `before`, `after`, `before_each`, and `after_each` are available inside `suite`.
- `expect expr` fails when the expression is falsy.
- `expect expr ~ "message"` adds a message to the assertion error.
- The runner supports `--failfast`, `--json`, `--save[=path]`, and `--nocolors`.

```omi
suite "Math":
    test "adds":
        expect (2 + 3) == 5
    end
end
```
