# Documentation

> Full documentation of syntax, functions, operators, etc.

---

## Navigation

- [Documentation (this page)](Documentation.md) - syntax, types, functions, imports
- [Modules](Modules.md) - built-in modules
- [Linter](Linter.md) - static analysis, rules, config, and CLI
- [Tests](Tests.md) - test syntax, runner flags, and reports
- [Architecture](Architecture.md) - project layout and interpreter internals

---

## Contents

- [Basics](#basics)
  - [Comments](#comments)
  - [Variables](#variables)
  - [Declaring Without a Value](#declaring-without-a-value)
  - [Constants](#constants)
  - [Data Types](#data-types)
  - [Built-in Constants](#built-in-constants)
- [Operators](#operators)
  - [Arithmetic Operators](#arithmetic-operators)
  - [Comparison Operators](#comparison-operators)
  - [Logical Operators](#logical-operators)
  - [Ternary Operator](#ternary-operator)
  - [Null Coalescing Operator](#null-coalescing-operator)
- [Conditions](#conditions)
  - [if / elif / else](#if--elif--else)
  - [Single-line Form](#single-line-form)
- [Loops](#loops)
  - [for](#for-loop)
  - [while](#while-loop)
  - [break and continue](#break-and-continue)
- [Functions](#functions)
  - [Function Declaration](#function-declaration)
  - [Async Functions](#async-functions)
  - [Async Groups](#async-groups)
  - [Arrow Functions](#arrow-functions)
  - [return](#return)
  - [Default Arguments](#default-arguments)
  - [Keyword Arguments](#keyword-arguments)
  - [Functions as Arguments](#functions-as-arguments)
- [Error Handling and Pattern Matching](#error-handling-and-pattern-matching)
  - [try / catch / final](#try--catch--final)
  - [match / case](#match--case)
- [Arrays](#arrays)
  - [Typed Arrays](#typed-arrays)
  - [Size-Constrained Arrays](#size-constrained-arrays)
  - [Array Operations](#array-operations)
- [Dictionaries](#dictionaries)
  - [Access Syntax](#access-syntax)
  - [Nested Dicts](#nested-dicts)
  - [Typing Dictionaries](#typing-dictionaries)
- [Strings](#strings)
  - [F-strings](#f-strings)
- [Type Annotations](#type-annotations)
  - [Variable Annotations](#variable-annotations)
  - [Function Annotations](#function-annotations)
  - [Union Types](#union-types)
  - [Nullable Types](#nullable-types)
  - [Type Aliases](#type-aliases)
  - [Enums and Traits](#enums-and-traits)
  - [Typed and Sized Arrays](#typed-and-sized-arrays)
  - [The void Type](#the-void-type)
  - [The null Type](#the-null-type)
  - [The every Type](#the-every-type)
  - [Disabling Type Checking](#disabling-type-checking)
- [Built-in Functions](#built-in-functions)
  - [Input / Output](#input--output)
  - [Type Checks](#type-checks)
  - [List Utilities](#list-utilities)
  - [Async Utilities](#async-utilities)
  - [Other](#other)
- [Directives](#directives)
  - [@import](#import)
  - [@use](#use)
  - [@set](#set)
  - [type](#type-keyword)
- [eval](#eval)
- [Interactive Shell](#interactive-shell)

---

## Basics

### Comments

Single-line comments start with ``//``:

```js
// This is a comment
var<int> x = 10 // This is also a comment
```

### Variables

Variables are declared with the `var` keyword. Type annotations are required unless `@use notypes` is active.

Declare and assign in one line:

```js
var<string> name = "OmiLang"
var<int> age = 1
var<float> pi_approx = 3.14
```

Declare without an initial value (assign later in code):

```js
var<string> username
username = "Alice"
println(username)   // Alice
```

Accessing a declared-but-unassigned variable produces a runtime error.

Reassignment (no `var`, no type annotation):

```js
var<int> x = 10
x = x + 5
```

### Constants

Use `const` to declare an immutable variable. Constants must be initialized at the point of declaration and cannot be reassigned or mutated.

```js
const<int> MAX = 100
const<string> APP_NAME = "Omi"
```

With `@use notypes`:

```js
@use notypes
const MAX = 100
const APP_NAME = "Omi"
```

Rules:
- Constants must have an initial value — `const<int> X` without `=` is a syntax error.
- Reassignment (`MAX = 200`) raises a runtime error.
- Mutating array constants via `append`, `pop`, or `extend` raises a runtime error.

### Data Types

| Type name | Example | Description |
|-----------|---------|-------------|
| `int` | `42` | Whole numbers |
| `float` | `3.14` | Floating-point numbers |
| `bool` | `true` / `false` | Boolean values |
| `string` | `"hello"` | Text in double quotes |
| `array` | `[1, 2, 3]` | Ordered collection |
| `dict` | `{"key": value}` | Key-value mapping |
| `null` | `null` | An explicit null value |
| `void` | — | Absence of any return value (functions only) |

### Built-in Constants

| Constant | Type | Description |
|----------|------|-------------|
| `null` | `null` | The absence of a value |
| `true` | `bool` | Boolean true |
| `false` | `bool` | Boolean false |

---

## Operators

### Arithmetic Operators

| Operator | Description | Example |
|----------|-------------|---------|
| `+` | Addition | `2 + 3` -> `5` |
| `-` | Subtraction | `5 - 2` -> `3` |
| `*` | Multiplication | `3 * 4` -> `12` |
| `/` | Division | `10 / 3` -> `3.333...` |
| `^` | Exponentiation | `2 ^ 8` -> `256` |

Parentheses override precedence:

```js
var<int> result = (2 + 3) * 4  // 20
```

### Comparison Operators

| Operator | Description |
|----------|-------------|
| `==` | Equal |
| `!=` | Not equal |
| `<` | Less than |
| `>` | Greater than |
| `<=` | Less than or equal |
| `>=` | Greater than or equal |

### Logical Operators

| Operator | Description |
|----------|-------------|
| `and` | Logical AND |
| `or` | Logical OR |
| `is` | True (value is not 0 / false) |
| `isnt` | False (value is 0 / false) |

```js
var<int> x = 50

if x > 0 and x < 100:
  println("x is in range 0..100")
end

var<bool> exists = true
if is exists:
  println("exists")
end
```

### Ternary Operator

Syntax: ``true_value ~ condition ~ false_value``

```js
var<int> age = 25
var<string> label = "adult" ~ (age >= 18) ~ "minor"
println(label)  // adult

var<int> score = 55
var<string> result = "passed" ~ (score >= 60) ~ "failed"
println(result)  // failed
```

The condition is evaluated; if truthy the left value is returned, otherwise the right value.
The ternary can also be used inline inside f-strings:

```js
var<int> score = 75
var<string> grade = "passed" ~ (score >= 60) ~ "failed"
println("Score ~score: ~grade")  // Score 75: passed
```

---

### Null Coalescing Operator

The `??` operator returns its left operand if it is not `null` or `void`, otherwise it evaluates and returns the right operand:

```js
var<int?> port = null
var<int> final = port ?? 8080    // final = 8080

port = 1111
var<int> final2 = port ?? 8080   // final2 = 1111
```

The right-hand side is only evaluated when the left side is `null` or `void`:

```js
var<string?> name = null
var<string> display = name ?? "Anonymous"   // "Anonymous"

name = "Alice"
var<string> display2 = name ?? "Anonymous"  // "Alice"
```

`??` works with any nullable type and can be chained:

```js
var<string?> a = null
var<string?> b = null
var<string>  c = "default"
var<string>  result = a ?? b ?? c           // "default"
```

---

## Conditions

### if / elif / else

Conditional blocks use `:` after the condition and are closed with ``end``:

```js
var<int> score = 85

if score >= 90:
  println("Excellent")
elif score >= 70:
  println("Good")
else:
  println("Can be better")
end
```

> **Important:** `if` and `elif` require `:`, while `else` does not.

### Single-line Form

For simple cases, you can use one line:

```js
var<int> x = 42
if x > 0: println("positive")
```

---

## Loops

### for loop

The `for` loop supports two forms:

- Numeric range form: iterate over integers (inclusive start, exclusive end)

```js
for i = 0 to 5:
  println(i)
end
```

Output: `0`, `1`, `2`, `3`, `4`

You can specify a `step` (positive or negative):

```js
for i = 0 to 10 step 2:
  println(i)
end

for i = 10 to 1 step -1:
  println(i)
end
```

**Iterable form**

- iterate over the elements of an array using `to`. The loop variable receives each
element value in turn. Works with arrays (`var[...]` or `var<array>`)

```js
var[string] fruits = ["apple", "banana", "pear"]
for item to fruits:
  println(item)
end
```

Output: `apple`, `banana`, `pear`

The iterable form does not use `step` or `=`, write `for <ident> to <iterable>:`

### while loop

```js
var<int> i = 1
while i <= 5:
  println(i)
  var<int> i = i + 1
end
```

### break and continue

- `break` stops the loop
- `continue` jumps to the next iteration

```js
for i = 0 to 10:
  if i == 5: break
  if i == 3: continue
  println(i)
end
```

Output: `0`, `1`, `2`, `4`

---

## Functions

### Function Declaration

Functions are declared with `func`, closed with `end`. The return type goes in `<>` after `func`; parameter types go after each parameter name:

```js
func<void> greet(name<string>):
  println("Hello, ~name!")
end

greet("World")
```

Use `void` as the return type when the function does not return a value:

### Async Functions

Use `async func` to declare asynchronous functions.

```js
async func<int> fetch_id(url<string>):
  // schedule blocking I/O in background
  var<future<every>> r = async http.get(url)
  var<every> res = async r
  return res.status
end
```

Rules:
- `async func<T>` returns `future<T>` when called.
- `async name(...)` schedules execution and immediately returns `future<...>`.
- `async <futureExpr>` is the await form (waits for completion and unwraps value).
- `async <futureExpr>` is allowed only inside `async func`.
- `async <nonFutureExpr>` raises a runtime error.
- `@use noasync` disables async scheduling and await form for the file.

### Async Groups

Use named async groups to collect scheduled tasks and cancel them together.

```js
@import "omi:time" as t

async workers(timeout: 0.25):
  var<future<null>> a = async t.sleep(1)
  var<future<null>> b = async t.sleep(1)
end

cancel(workers)
```

Rules:
- Syntax: `async groupName:` or `async groupName(timeout: <number>):`.
- Only the `timeout` parameter is currently supported.
- Group body runs in async scope, so `async <futureExpr>` (await form) is valid inside.
- `cancel(groupName)` cancels all futures collected by the group.
- With `@use noasync`, group body executes synchronously and scheduling is disabled.

Typical usage:

```js
async func<void> worker():
  var<future<null>> t = async time.sleep(0.1)
  async t
  println("done")
end

async worker()      // fire-and-forget scheduling
println("scheduled")
```

### Arrow Functions

Use `->` for compact single-expression functions. `end` is not required:

```js
func<int> add(a<int>, b<int>) -> a + b
println(add(2, 3))  // 5

func<int> double(n<int>) -> n * 2
```

Arrow functions return the expression result automatically.

### return

Use `return` in regular functions to return a value:

```js
func<int> factorial(n<int>):
  var<int> result = 1
  while n > 1:
    var<int> result = result * n
    var<int> n = n - 1
  end
  return result
end

println(factorial(5))  // 120
```

If `return` is omitted in a regular function, it returns `void`. Use `func<void>` for such functions.

### Default Arguments

Parameters can have default values. Callers can omit them:

```js
func<int> increment(n<int>, by<int> = 1) -> n + by

println(increment(10))      // 11
println(increment(10, 5))   // 15
```

### Keyword Arguments

Arguments can be passed by name in any order:

```js
func<int> sub(a<int>, b<int>) -> a - b
println(sub(b=3, a=10))  // 7

func<string> describe(label<string>, count<int> = 0) -> "~label x~count"
println(describe(count=7, label="things"))  // things x7
```

Named and positional arguments can be mixed: positional must come first.

### Functions as Arguments

Use the `call` type to accept a function as an argument:

```js
func<int> double(n<int>) -> n * 2

func<int> apply(fn<call>, x<int>) -> fn(x)
println(apply(double, 5))  // 10
```

---

## Error Handling and Pattern Matching

### try / catch / final

Use `try` and `catch` to handle runtime errors without stopping the whole script.

```js
try:
  var<int> x = 10 / 0
catch err:
  println("ERR: ~err.msg")
end
```

Use `final` for cleanup code that must always run:

```js
try:
  println("TRY")
catch err:
  println("CATCH")
final:
  println("ALWAYS")
end
```

The `catch` variable is a dict-like error object with fields:
- `type`
- `msg`
- `trace` (array of traceback lines)

### match / case

Use `match` for value/variant dispatch.

```js
match value:
  case 0: println("zero")
  case "ok": println("ok")
  case _: println("fallback")
end
```

Enum-style variant match with payload capture:

```js
match result:
  case Ok(v): println(v)
  case Err(e): println(e)
  case _: println("unknown")
end
```

---

## Arrays

Arrays are created with square brackets:

```js
var<array> fruits = ["apple", "banana", "orange"]
var<array> numbers = [1, 2, 3, 4, 5]
var<array> empty = []
```

### Typed Arrays

Use `[type]` instead of `<array>` to restrict what element types are allowed:

```js
var[int] ids = [1, 2, 3]
var[int | bool] flags = [1, true, 0, false]
```

Assigning an element of the wrong type raises a type error.

### Size-Constrained Arrays

Add `(n)` after the annotation to limit the maximum number of elements.
This limit is enforced on declaration, `+`/`*` operators, and `append()`/`extend()`:

```js
var<array>(3) small = [1, 2, 3]     // max 3 elements (any type)
var[int](5) ids = [1, 2, 3]         // max 5 int elements
```

### Array Operations

```js
var<array> items = [1, 2, 3]

append(items, 4)            // items = [1, 2, 3, 4]
pop(items, 0)               // removes item at index 0, returns it
var<int> size = len(items)  // 3

var<array> a = [1, 2]
var<array> b = [3, 4]
extend(a, b)                // a = [1, 2, 3, 4]
```

Operator shortcuts:

| Expression | Meaning |
|------------|---------|
| `arr + val` | Append element |
| `arr - idx` | Remove element at index |
| `arr * arr2` | Concatenate two arrays |
| `arr / idx` | Access element at index |

```js
var<array> items = [10, 20, 30]
println(items / 0)   // 10
println(items / 2)   // 30
```

---

## Dictionaries

Dictionaries hold key-value pairs. All keys are strings:

```js
var<dict> user = {"name": "Alice", "age": 30}
```

Multi-line syntax with optional trailing comma:

```js
var<dict> config = {
    "host": "localhost",
    "port": 5432,
    "debug": false,
}
```

### Access Syntax

Two equivalent ways to read a key:

```js
// dot notation
println(user.name)   // Alice
println(user.age)    // 30

// bracket notation
println(user["name"])   // Alice
println(user["age"])    // 30
```

Bracket notation works with any string expression — including variables and f-string keys.
Both notations work inside f-string interpolations:

```js
var<string> key = "name"
println("~(user["name"])")        // Alice
println("host: ~(config.host)")   // localhost
```

Accessing a key that does not exist is a **runtime error**:

```
Runtime Error: Key 'missing' not found in dict
```

### Nested Dicts

```js
var<dict> data = {
    "db": {
        "host": "localhost",
        "port": 5432,
    }
}

// mixed dot + bracket access:
println(data.db.host)         // localhost
println(data.db["port"])      // 5432
println(data["db"]["host"])   // localhost
```

### Typing Dictionaries

Use the `type` keyword to define a **structured dict type** — a named schema that validates field presence and types:

```js
type PersonType = {
    name<string>,
    age<int>,
    city<string?>,      // optional — string or null; may be absent
}
```

Assign a dict with that type annotation:

```js
var<PersonType> person = {
    "name": "John",
    "age": 25
    // "city" is omitted — allowed because it is string?
}
```

If a required field is missing or has the wrong type, a runtime error is raised:

```
Runtime Error: Dict is missing required field 'age'
Runtime Error: Field 'age': Type error: expected <int>, got string ("twenty")
```

Nested dict types are defined inline with `: { ... }` syntax:

```js
type UserType = {
    name<string>,
    age<int?>,
    address: {
        street<string>,
        zip<string?>,
    }
}

var<UserType> user = {
    "name": "Bob",
    "address": {
        "street": "123 Main St"
    }
}

println(user.address.street)          // 123 Main St
println(user.address["street"])       // 123 Main St
println(user["address"]["street"])    // 123 Main St
```

Check type with `is_dict()`:

```js
println(is_dict(user))   // true
println(is_dict(42))     // false
```

---

## Strings

Strings use double quotes:

```js
var<string> greeting = "Hello, World!"
```

Concatenation:

```js
var<string> full = "Hello, " + "World!"
```

Repetition:

```js
var<string> line = "-" * 20  // "--------------------"
```

Escape sequences:

| Sequence | Result |
|----------|--------|
| `\n` | New line |
| `\t` | Tab |
| `\\` | Backslash |
| `\"` | Double quote |
| `\~` | Literal tilde (disables interpolation) |

### F-strings

Any string can embed expressions using `~`. No special prefix is needed:

```js
var<string> name = "Omi"
var<int> ver = 2

println("~name version ~ver")    // Omi version 2
println("Hello, ~name!")         // Hello, Omi!
```

Use parentheses for arbitrary expressions:

```js
var<int> a = 10
var<int> b = 32
println("~a + ~b = ~(a + b)")   // 10 + 32 = 42
```

Combining f-strings and the ternary operator:

```js
var<int> score = 75
var<string> grade = "passed" ~ (score >= 60) ~ "failed"
println("Score ~score: ~grade")  // Score 75: passed
```

To include a literal tilde, escape it:

```js
println("Hello\~world")  // Hello~world
```

---

## Type Annotations

Types are optional but checked by default. Annotations are written in angle brackets `<>`.

### Variable Annotations

```js
var<int> count = 42
var<string> name = "Alice"
var<float> pi = 3.14
var<bool> active = true
var<array> items = [1, 2, 3]
var<dict> config = {"key": "value"}
```

Typed arrays and size constraints:

```js
var[int] ids = [1, 2, 3]           // only int elements allowed
var[int | bool] flags = [1, true]  // int or bool elements
var<array>(5) buf = []             // any elements, max 5
var[string](3) names = []          // string elements, max 3
```

Declaring without an initial value (assign later):

```js
var<string> username
username = "Bob"    // assigned later
```

### Function Annotations

The return type goes after `func`; parameter types go after each parameter name:

```js
func<int> add(a<int>, b<int>) -> a + b

func<string> greet(name<string>):
  return "Hello, ~name!"
end
```

When a function does not return a value, annotate it with `void`:

```js
func<void> log(msg<string>):
  println("[LOG] ~msg")
end
```

A `void` function may use bare `return` for early exit:

```js
func<void> check(flag<bool>):
  if flag: return
  println("not set")
end
```

Use `null` when the function explicitly returns `null`:

```js
func<null> maybe(x<int>) -> null
```

`every` accepts any value (including `null`) and bypasses the return-type check entirely. Use it when the return type is genuinely variable:

```js
func<every> identity(x<every>) -> x
```

### Union Types

Use `|` to allow multiple types:

```js
var<int | string> id = 10
var<int | string> id2 = "user_42"

func<int | string> parse(raw<string>) -> raw
```

### Nullable Types

Add `?` after a type name to allow `null` as a value. `int?` is exactly equivalent to `int | null`:

```js
var<int?> port = null       // same as var<int | null>
var<string?> name = null

// You can still assign a real value:
port = 8080
name = "Alice"
```

Use `??` (null coalescing) together with nullable types:

```js
var<int?> port = null
var<int> final = port ?? 9000   // 9000
```

In typed dict schemas, a nullable field (`string?`) is **optional** — it may be absent from the dict entirely:

```js
type Config = {
    host<string>,
    port<int?>,    // optional field
}
var<Config> cfg = {"host": "localhost"}   // valid — port is omitted
```

### Type Aliases

Use the `type` keyword to define reusable type names.

**Simple aliases** — union of existing types or literal strings:

```js
type Status = "ok" | "error" | "pending"
type UserId = int | string

var<Status> code = "ok"
var<UserId> uid = 42
```

Literal types restrict to specific string values:

```js
type Direction = "north" | "south" | "east" | "west"
var<Direction> dir = "north"
```

**Structured dict types** — a named schema for dictionaries (see [Typing Dictionaries](#typing-dictionaries)):

```js
type PersonType = {
    name<string>,
    age<int>,
    city<string?>,    // optional field
}
var<PersonType> p = {"name": "Alice", "age": 30}
```

Type aliases defined in **modules** are accessed with the module alias as a prefix: `alias.TypeName`. They can be bound to a short local name with `@set`:

```js
@import "models" as m
// PersonType from models.omi is available as m.PersonType:

var<m.PersonType> user = {"name": "Bob", "age": 25}

// Bind to a short local name with @set:
@set m.PersonType as Person
var<Person> admin = {"name": "Root", "age": 40}
```

### Enums and Traits

Omi supports algebraic data types through the `enum` keyword and structural interfaces through the `trait` keyword.

Enums are desugared to dictionaries with a string `__tag` field. Variants without payloads become unit values, and payload variants store their data in `value`:

```js
enum Color = {
  Red,
  Green,
  Blue
}

enum Result<T, E> = {
  Ok(T),
  Err(E)
}

var<Color> favorite = Green
var<Result<int, string>> ok = Ok(200)

println(favorite.__tag)  // Green
println(ok.__tag)        // Ok
println(ok.value)        // 200
```

Rules for enums:
- Variant names must start with an uppercase letter and may contain letters, numbers, and underscores.
- Each variant may have at most one payload value.
- `enum` values are normal `dict` values at runtime, so regular dict access still works.
- Imported enums are available as module-scoped type aliases via `@import`, and they can be renamed with `@set`.

Traits define structural contracts:

```js
trait Serializable = {
  func<string> to_json(),
  func<string> desc()
}
```

If a value's type provides all required methods with matching signatures, it satisfies the trait automatically. Traits can also be imported from modules and renamed with `@set`.

### The void Type

`void` is the return type for functions that perform a side effect and do not return a value. A `void` function must use bare `return` (no value) for early exit, or simply fall through:

```js
func<void> greet(name<string>):
  println("Hello, ~name!")
end

greet("World")
```

Early exit from a `void` function:

```js
func<void> check(flag<bool>):
  if flag: return    // bare return — OK for void
  println("not set")
end
```

Key rules:
- `func<void>`: only bare `return` is allowed. `return null` → error.
- `func<void>` cannot be an arrow function (arrow functions always produce a value).
- `void` cannot be used as a variable type annotation.
- `typeof()` returns `"void"` for the result of a void function call.
- `is_null()` returns `false` for a void value.

### The null Type

`null` is both a type and the built-in constant that represents the explicit absence of a value. Use `null` as a return type for functions that **explicitly return `null`** with `return null`:

```js
func<null> find(key<string>) -> null   // always null in this example

var<null | string> result = null
```

> **`null` ≠ `void`**: `func<null>` must `return null` explicitly — a bare `return` is an error. Use `func<void>` for functions that return nothing.

Check for null with `is_null()` or equality:

```js
var<null> x = null
println(is_null(x))    // true
println(x == null)     // true
```

### The every Type

`every` accepts any value, bypassing the type check for that variable or parameter:

```js
var<every> anything = 42
var<every> anything2 = "hello"
```

### Disabling Type Checking

Use `@use notypes` to disable type checking for an entire file. This is **not recommended** — prefer explicit annotations:

```js
@use notypes

var x = 42
// No type errors will be raised anywhere in this file
```

---

## Built-in Functions

### Input / Output

| Function | Description |
|---------|-------------|
| `print(value)` | Prints a value **without** adding a newline. |
| `println(value, [end])` | Prints a value and ends with `"\n"` by default. Optional `end` overrides the suffix. |
| `reprint(value)` | Returns the string form of a value without printing it. |
| `output(v1, v2, ...)` | Prints multiple values separated by spaces and ends the line. |
| `input()` | Reads a line from stdin as a string |

```js
print("hello")
print(" world")
println("!")          // hello world!\n
println("bye", "\t") // bye\t
output(1, 2, "hello", 3) // 1 2 hello 3\n
var<string> s = reprint(42) // "42"
```

### Type Checks

All functions return `true` or `false`:

| Function | Description |
|---------|-------------|
| `is_number(value)` | Whether argument is any number (int or float) |
| `is_int(value)` | Whether argument is an integer |
| `is_float(value)` | Whether argument is a float |
| `is_bool(value)` | Whether argument is a boolean |
| `is_string(value)` | Whether argument is a string |
| `is_array(value)` | Whether argument is an array |
| `is_dict(value)` | Whether argument is a dict |
| `is_function(value)` | Whether argument is a function |
| `is_null(value)` | Whether argument is `null` (returns `false` for `void`) |

### List Utilities

| Function | Description |
|---------|-------------|
| `append(list, value)` | Appends an element to a list (error on `const` arrays) |
| `pop(list, index)` | Removes and returns element by index (error on `const` arrays) |
| `extend(listA, listB)` | Appends all elements from listB to listA (error on `const` arrays) |
| `len(value)` | Returns the length — number of elements for arrays, number of characters for strings |
| `range(stop)` / `range(start, stop, [step])` | Returns an array of integers from `0` to `stop` (exclusive), or from `start` to `stop` with optional `step` (default `1`) |

### Async Utilities

| Function | Description |
|---------|-------------|
| `cancel(target)` | Cancels a `future` or an async group created with `async groupName: ... end` |

### Other

| Function | Description |
|---------|-------------|
| `typeof(value)` | Returns the type name as a string: `"int"`, `"float"`, `"string"`, `"bool"`, `"array"`, `"dict"`, `"call"`, `"null"`, `"void"` |
| `to_string(value)` | Convert to string |
| `to_int(value)` | Convert to integer |
| `to_float(value)` | Convert to float |
| `to_bool(value)` | Convert to boolean |
| `clear()` | Clears the console |
| `eval(code)` | Executes a string as Omi code (requires `@use eval`) |

---

## Directives

Directives start with `@` and are processed before execution.

### @import

Loads a module (built-in or from a file) and binds it to an alias:

```js
@import "omi:system" as sys
@import "omi:json" as json
@import "omi:http" as http
@import "omi:math" as math
@import "omi:files" as fs
@import "omi:paths" as paths
@import "omi:time" as time
@import "omi:txt" as txt
@import "omi:string" as str
@import "omi:regex" as rx
@import "omi:log" as log
```

Built-in modules use the `omi:` prefix. User files are imported by relative path without extension:

```js
@import "utils" as u
@import "./lib/helpers" as h
u.my_function()
```

All module members are accessed via dot notation. Member files must declare `@use module`.

**Type aliases from modules** are automatically imported: any `type` defined inside the module file is available in the importing file without any extra step:

```js
// models.omi
@use module
type UserType = {
    name<string>,
    age<int>,
}

// main.omi
@import "models" as m
var<m.UserType> u = {"name": "Alice", "age": 30}   // m.UserType — alias prefix required
```

### @use

Enables or disables interpreter features for the current file:

| Flag | Description |
|------|-------------|
| `notypes` | Disable type checking (not recommended) |
| `eval` | Enable the `eval()` built-in function |
| `debug` | Enable debug mode |
| `noecho` | Suppress `print()` / `println()` / `output()` output |
| `noasync` | Disable async scheduling and await form in this file |
| `module` | Mark this file as a module (required for user modules) |
| `json` | Alias for CLI `--json` in `lint` / `test` flows |
| `fix` | Alias for CLI `--fix` in `lint` flows |
| `failfast` | Alias for CLI `--failfast` in `lint` / `test` flows |
| `level` | Alias for CLI `--level=<...>` in `lint` flows |
| `rules` | Alias for CLI `--rules=<...>` in `lint` flows |
| `config` | Alias for CLI `--config[=path]` in `lint` flows |
| `save` | Alias for CLI `--save[=path]` in `test` flows (`.test.omi` only) |

Forms:

- no-value flags: `@use <flag>`
- value flags: `@use level as <value>`, `@use rules as <value>`
- optional value flags: `@use config`, `@use config as <path>`, `@use save`, `@use save as <path>`

```js
@use eval
@use debug
@use json
@use fix
@use failfast
@use level as error
@use rules as "undefined-var,unused-var"
@use config
@use config as "./.omilint"
```

`@use save` is valid only in `.test.omi` files. Using it in a regular `.omi` file raises a runtime error.

### @set

Creates compile-time constants or aliases. The preprocessor substitutes the alias in all subsequent lines of source code.

**Named constant:**

```js
@set VERSION as 2
@set AUTHOR as "Kenyka"

println("Version: ~VERSION")
println("Author: ~AUTHOR")
```

**Keyword alias** (replace a keyword with a custom name):

```js
@set var as let

let<int> x = 10
let<string> name = "Omi"
```

**Function alias** (bind a module function to a short name):

```js
@import "omi:system" as sys
@set sys.exec as shell

var<string> out = shell("echo hello")
println(out)
```

**Type alias rename** (bind an imported type to a short local name):

```js
@import "models" as m
// m.PersonType is the qualified name from models.omi

@set m.PersonType as P    // create local alias P → m.PersonType

var<P> admin = {"name": "Root", "age": 0}
```

### type keyword

Defines a named type alias at module or top-level scope.

**Simple union type:**

```js
type Status = "ok" | "error" | "pending"
type ID = int | string
```

**Nullable alias** (using `?`):

```js
type MaybeInt = int?    // equivalent to int | null
```

**Structured dict type:**

```js
type PersonType = {
    name<string>,
    age<int>,
    bio<string?>,     // optional field (may be absent)
    address: {        // nested dict schema
        city<string>,
        zip<string?>,
    }
}
```

All `type` definitions are exported from module files and become available in any file that imports that module.

---

## eval

`eval` executes a string as Omi source code and returns the result. Must be enabled with `@use eval`:

```js
@use eval

var<string> code = "10 + 5"
var<int> result = eval(code)
println(result)  // 15
```

---

## Interactive Shell

Start the interactive shell (REPL):

```
python shell.py
```

Run a script file:

```
python shell.py run filename.omi
```

Flags:

| Flag | Description |
|------|-------------|
| `--version` or `-v` | Print the Omi version and exit |
| `--help` or `-h` | Show help and links, then exit |
| `--debug` or `-d` | Print the parsed AST result after execution |
