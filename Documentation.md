# Documentation

> Full documentation of syntax, functions, operators, etc.

---

## Navigation

- [Documentation (this page)](Documentation.md) - syntax, types, functions, imports
- [Modules](Modules.md) - built-in modules
- [Architecture](Architecture.md) - project layout and interpreter internals

---

## Contents

- [Basics](#basics)
  - [Comments](#comments)
  - [Variables](#variables)
  - [Data Types](#data-types)
  - [Built-in Constants](#built-in-constants)
- [Operators](#operators)
  - [Arithmetic Operators](#arithmetic-operators)
  - [Comparison Operators](#comparison-operators)
  - [Logical Operators](#logical-operators)
  - [Ternary Operator](#ternary-operator)
- [Conditions](#conditions)
  - [if / elif / else](#if--elif--else)
  - [Single-line Form](#single-line-form)
- [Loops](#loops)
  - [for](#for-loop)
  - [while](#while-loop)
  - [break and continue](#break-and-continue)
- [Functions](#functions)
  - [Function Declaration](#function-declaration)
  - [Arrow Functions](#arrow-functions)
  - [return](#return)
  - [Default Arguments](#default-arguments)
  - [Keyword Arguments](#keyword-arguments)
  - [Functions as Arguments](#functions-as-arguments)
- [Lists](#lists)
- [Dictionaries](#dictionaries)
- [Strings](#strings)
  - [F-strings](#f-strings)
- [Type Annotations](#type-annotations)
  - [Variable Annotations](#variable-annotations)
  - [Function Annotations](#function-annotations)
  - [Union Types](#union-types)
  - [Type Aliases](#type-aliases)
  - [The void Type](#the-void-type)
  - [The null Type](#the-null-type)
  - [The every Type](#the-every-type)
  - [Disabling Type Checking](#disabling-type-checking)
- [Built-in Functions](#built-in-functions)
- [Directives](#directives)
  - [@import](#import)
  - [@use](#use)
  - [@set](#set)
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

Variables are declared with the `var` keyword. Type annotations are recommended:

```js
var<string> name = "OmiLang"
var<int> age = 1
var<float> pi_approx = 3.14
```

Reassignment also uses `var`:

```js
var<int> x = 10
var<int> x = x + 5
```

### Data Types

| Type name | Example | Description |
|-----------|---------|-------------|
| `int` | `42` | Whole numbers |
| `float` | `3.14` | Floating-point numbers |
| `bool` | `true` / `false` | Boolean values |
| `string` | `"hello"` | Text in double quotes |
| `list` | `[1, 2, 3]` | Ordered collection |
| `dict` | `{"key": value}` | Key-value mapping |
| `null` | `null` | Absence of value |

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
  print("x is in range 0..100")
end

var<bool> exists = true
if is exists:
  print("exists")
end
```

### Ternary Operator

Syntax: ``true_value ~ condition ~ false_value``

```js
var<int> age = 25
var<string> label = "adult" ~ (age >= 18) ~ "minor"
print(label)  // adult

var<int> score = 55
var<string> result = "passed" ~ (score >= 60) ~ "failed"
print(result)  // failed
```

The condition is evaluated; if truthy the left value is returned, otherwise the right value.
The ternary can also be used inline inside f-strings:

```js
var<int> score = 75
var<string> grade = "passed" ~ (score >= 60) ~ "failed"
print("Score ~score: ~grade")  // Score 75: passed
```

---

## Conditions

### if / elif / else

Conditional blocks use `:` after the condition and are closed with ``end``:

```js
var<int> score = 85

if score >= 90:
  print("Excellent")
elif score >= 70:
  print("Good")
else
  print("Can be better")
end
```

> **Important:** `if` and `elif` require `:`, while `else` does not.

### Single-line Form

For simple cases, you can use one line:

```js
var<int> x = 42
if x > 0: print("positive")
```

---

## Loops

### for loop

The `for` loop iterates from a start value to an end value:

```js
for i = 0 to 5:
  print(i)
end
```

Output: `0`, `1`, `2`, `3`, `4`

With custom step:

```js
for i = 0 to 10 step 2:
  print(i)
end
```

With negative step:

```js
for i = 10 to 1 step -1:
  print(i)
end
```

### while loop

```js
var<int> i = 1
while i <= 5:
  print(i)
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
  print(i)
end
```

Output: `0`, `1`, `2`, `4`

---

## Functions

### Function Declaration

Functions are declared with `func`, closed with `end`. The return type goes in `<>` after `func`; parameter types go after each parameter name:

```js
func<every> greet(name<string>):
  print("Hello, ~name!")
end

greet("World")
```

Use `every` as the return type when the function does not meaningfully return a value (i.e. returns `null`).

### Arrow Functions

Use `->` for compact single-expression functions. `end` is not required:

```js
func<int> add(a<int>, b<int>) -> a + b
print(add(2, 3))  // 5

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

print(factorial(5))  // 120
```

If `return` is omitted, the function returns `null`.

### Default Arguments

Parameters can have default values. Callers can omit them:

```js
func<int> increment(n<int>, by<int> = 1) -> n + by

print(increment(10))      // 11
print(increment(10, 5))   // 15
```

### Keyword Arguments

Arguments can be passed by name in any order:

```js
func<int> sub(a<int>, b<int>) -> a - b
print(sub(b=3, a=10))  // 7

func<string> describe(label<string>, count<int> = 0) -> "~label x~count"
print(describe(count=7, label="things"))  // things x7
```

Named and positional arguments can be mixed: positional must come first.

### Functions as Arguments

Use the `call` type to accept a function as an argument:

```js
func<int> double(n<int>) -> n * 2

func<int> apply(fn<call>, x<int>) -> fn(x)
print(apply(double, 5))  // 10
```

---

## Lists

Lists are created with square brackets:

```js
var<list> fruits = ["apple", "banana", "orange"]
var<list> numbers = [1, 2, 3, 4, 5]
var<list> mixed = [1, "two", 3]
var<list> empty = []
```

List operations:

```js
var<list> items = [1, 2, 3]

append(items, 4)           // items = [1, 2, 3, 4]
pop(items, 0)              // removes item at index 0, returns it
var<int> size = len(items) // 3

var<list> a = [1, 2]
var<list> b = [3, 4]
extend(a, b)               // a = [1, 2, 3, 4]
```

List concatenation and repetition:

```js
var<list> result = [1, 2] + [3, 4]  // [1, 2, 3, 4]
var<list> zeros = [0] * 5            // [0, 0, 0, 0, 0]
```

Element access uses `/`:

```js
var<list> items = [10, 20, 30]
print(items / 0)   // 10
print(items / 2)   // 30
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

Access fields with dot notation:

```js
print(user.name)   // Alice
print(user.age)    // 30
```

Nested dictionaries:

```js
var<dict> data = {
    "db": {
        "host": "localhost",
        "port": 5432,
    }
}

print(data.db.host)  // localhost
print(data.db.port)  // 5432
```

Check type with `is_dict()`:

```js
print(is_dict(user))   // true
print(is_dict(42))     // false
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

print("~name version ~ver")    // Omi version 2
print("Hello, ~name!")         // Hello, Omi!
```

Use parentheses for arbitrary expressions:

```js
var<int> a = 10
var<int> b = 32
print("~a + ~b = ~(a + b)")   // 10 + 32 = 42
```

Combining f-strings and the ternary operator:

```js
var<int> score = 75
var<string> grade = "passed" ~ (score >= 60) ~ "failed"
print("Score ~score: ~grade")  // Score 75: passed
```

To include a literal tilde, escape it:

```js
print("Hello\~world")  // Hello~world
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
var<list> items = [1, 2, 3]
var<dict> config = {"key": "value"}
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
  print("[LOG] ~msg")
end
```

A `void` function may also use `return` without a value for early exit:

```js
func<void> check(flag<bool>):
  if flag: return
  print("not set")
end
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

### Type Aliases

Use the `type` keyword to define reusable types:

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

### The void Type

`void` is the return type for functions that do not return a value. A `void` function always returns `null` implicitly:

```js
func<void> greet(name<string>):
  print("Hello, ~name!")
end
```

Use `return` without a value for early exit from a `void` function:

```js
func<void> check(flag<bool>):
  if flag: return
  print("not set")
end
```

### The null Type

`null` is both a type and the built-in constant that represents the absence of a value. Use `null` as a return type for functions that explicitly return `null`:

```js
func<null> find(key<string>) -> null   // always null in this example

var<null | string> result = null
```

Check for null with `is_null()` or equality:

```js
var x = null
print(is_null(x))    // true
print(x == null)     // true
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
| `print(value)` | Prints a value to the console |
| `print_ret(value)` | Prints a value and returns it as a string |
| `input()` | Reads a line from stdin as a string |
| `input_int()` | Reads an integer from stdin |

### Type Checks

All functions return `true` or `false`:

| Function | Description |
|---------|-------------|
| `is_number(value)` | Whether argument is any number (int or float) |
| `is_int(value)` | Whether argument is an integer |
| `is_float(value)` | Whether argument is a float |
| `is_bool(value)` | Whether argument is a boolean |
| `is_string(value)` | Whether argument is a string |
| `is_list(value)` | Whether argument is a list |
| `is_dict(value)` | Whether argument is a dict |
| `is_function(value)` | Whether argument is a function |
| `is_null(value)` | Whether argument is `null` |

### List Utilities

| Function | Description |
|---------|-------------|
| `append(list, value)` | Appends an element to a list |
| `pop(list, index)` | Removes and returns element by index |
| `extend(listA, listB)` | Appends all elements from listB to listA |
| `len(list)` | Returns list length |

### Other

| Function | Description |
|---------|-------------|
| `clear()` / `cls()` | Clears the console |
| `eval(code)` | Executes a string as Omi code (requires `@use eval`) |

---

## Directives

Directives start with `@` and are processed before execution.

### @import

Loads a module (built-in or from a file) and binds it to an alias:

```js
@import "omi/system" as sys
@import "omi/json" as json
@import "omi/http" as http
@import "omi/math" as math
@import "omi/files" as fs
@import "omi/paths" as paths
@import "omi/time" as time
```

Built-in modules use the `omi/` prefix. User files are imported by relative path without extension:

```js
@import "utils" as u
@import "./lib/helpers" as h
u.my_function()
```

All module members are accessed via dot notation.

### @use

Enables or disables interpreter features for the current file:

| Flag | Description |
|------|-------------|
| `notypes` | Disable type checking (not recommended) |
| `eval` | Enable the `eval()` built-in function |
| `debug` | Enable debug mode |
| `noecho` | Suppress `print()` output |
| `module` | Mark this file as a module |

```js
@use eval
@use debug
```

### @set

Creates compile-time constants or aliases.

**Named constant:**

```js
@set VERSION as 2
@set AUTHOR as "Kenyka"

print("Version: ~VERSION")
print("Author: ~AUTHOR")
```

**Keyword alias** (replace a keyword with a custom name):

```js
@set var as let

let<int> x = 10
let<string> name = "Omi"
```

**Function alias** (bind a module function to a short name):

```js
@import "omi/system" as sys
@set sys.exec as shell

var<string> out = shell("echo hello")
print(out)
```

---

## eval

`eval` executes a string as Omi source code and returns the result. Must be enabled with `@use eval`:

```js
@use eval

var<string> code = "10 + 5"
var<int> result = eval(code)
print(result)  // 15
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
