# Modules

> Reference for built-in modules

---

## Navigation

- [Documentation](Documentation.md) - syntax, types, functions, imports
- [Modules (this page)](Modules.md) - built-in modules
- [Architecture](Architecture.md) - project structure and interpreter internals

---

## Contents

- [How to Import a Module](#how-to-import-a-module)
- [system](#system)
- [files](#files)
- [paths](#paths)
- [time](#time)
- [math](#math)
- [json](#json)
- [http](#http)
- [txt](#txt)
- [string](#string)
- [regex](#regex)
- [log](#log)

---

## How to Import a Module

Built-in modules are imported with `@import` using the `omi:` prefix:

```js
@import "omi:system" as sys
@import "omi:json" as json
@import "omi:http" as http
```

After import, all module functions are available through the alias via dot notation.

### Sync and Async Calls

All module functions have a single API surface. You choose execution mode at call site:

- Sync call: `module.func(...)`
- Async call (scheduled): `async module.func(...)` -> returns `future<T>`
- Await form (inside `async func`): `async futureExpr`
- `@use noasync` disables async scheduling and await form in the current file

Example:

```js
@import "omi:time" as t

async func<void> job():
  var<future<null>> fut = async t.sleep(0.2)
  async fut
  println("done")
end
```

> Optional arguments are shown in square brackets, for example `[fmt<string>]`.

**Type-like definitions from modules** are automatically imported: any `type`, `trait`, or `enum` defined inside the module file is available in the importing file without any extra step.

```js
@import "models" as m

var<m.UserType> u = {"name": "Alice", "age": 30}
var<m.Color> favorite = m.Red
var<m.Result<int, string>> result = m.Ok(200)
```

---

## system
Module for OS interaction: command execution, environment variables, platform info, and process control.

```js
@import "omi:system" as sys
```

### Constants

| Constant | Type | Description |
|----------|------|-------------|
| `sys.platform` | string | OS name: `"Windows"`, `"Linux"`, `"Darwin"` |
| `sys.username` | string | Current username |

### Functions

| Function | Accepted arguments | Description |
|---------|---------------------|-------------|
| `sys.exec(command<string>)` | `command`: `string` | Runs a shell command and returns the output as `string` |
| `sys.env(name<string>)` | `name`: `string` | Gets an environment variable value |
| `sys.set_env(name<string>, value<string>)` | `name`: `string`, `value`: `string` | Sets an environment variable |
| `sys.cwd()` | — | Returns the current working directory |
| `sys.exit([code<number>])` | `[code]`: `number`, optional, default `0` | Exits the script with a status code |

```js
@import "omi:system" as sys

println(sys.platform)
println(sys.username)
println(sys.cwd())
var<string> out = sys.exec("echo hello")
println(out)
sys.exit(0)
```

---

## files

Module for file system operations: create/remove files and directories, copy, move, list.

```js
@import "omi:files" as fs
```

| Function | Accepted arguments | Description |
|---------|---------------------|-------------|
| `fs.cwd()` | — | Returns the current working directory |
| `fs.mkdir(path<string>, [parents<bool\|number>])` | `path`: `string`, `[parents]`: `bool | number`, optional, default `true` | Creates a directory; when `parents` is true, nested directories are created automatically |
| `fs.rm(path<string>)` | `path`: `string` | Removes a file |
| `fs.rmdir(path<string>)` | `path`: `string` | Removes a directory recursively |
| `fs.list([path<string>])` | `[path]`: `string`, optional, default `"."` | Returns directory entries as `list<string>` |
| `fs.cp(src<string>, dst<string>)` | `src`: `string`, `dst`: `string` | Copies a file or directory |
| `fs.mv(src<string>, dst<string>)` | `src`: `string`, `dst`: `string` | Moves or renames a file/directory |

```js
@import "omi:files" as fs

println(fs.cwd())
fs.mkdir("out/logs")
fs.cp("main.omi", "out/main.omi")
var<list> entries = fs.list()
println(entries)
fs.rm("out/main.omi")
fs.rmdir("out")
```

---

## paths

Module for file and directory path utilities.

```js
@import "omi:paths" as p
```

| Function | Accepted arguments | Description |
|---------|---------------------|-------------|
| `p.join(parts<list<string>>)` | `parts`: `list<string>` | Joins path parts from a list of strings |
| `p.abs(path<string>)` | `path`: `string` | Converts a relative path to an absolute path |
| `p.exists(path<string>)` | `path`: `string` | Returns `true` if the file or directory exists |
| `p.ext(path<string>)` | `path`: `string` | Returns the file extension, including the dot |
| `p.name(path<string>)` | `path`: `string` | Returns the file name from a path |

```js
@import "omi:paths" as p

var<string> full = p.join(["src", "stdlib", "files.py"])
println(full)  // src/stdlib/files.py

var<string> absolute = p.abs(".")
println(absolute)

if is p.exists("main.omi"):
  println("main.omi exists")
end

println(p.ext("main.omi"))          // .omi
println(p.name("src/run/run.py"))   // run.py
```

---

## time

Module for time operations: current time, formatting, parsing, sleeping.

```js
@import "omi:time" as t
```

| Function | Accepted arguments | Description |
|---------|---------------------|-------------|
| `t.now()` | — | Returns the current Unix timestamp |
| `t.format(timestamp<number>, [fmt<string>])` | `timestamp`: `number`, `[fmt]`: `string`, optional, default `%Y-%m-%d %H:%M:%S` | Formats a timestamp using `strftime` syntax |
| `t.parse(string<string>, [fmt<string>])` | `string`: `string`, `[fmt]`: `string`, optional, default `%Y-%m-%d %H:%M:%S` | Parses a date/time string into a Unix timestamp |
| `t.sleep(seconds<number>)` | `seconds`: `number` | Pauses execution for N seconds |
| `t.timezone()` | — | Returns the timezone offset in hours |

**Formatting codes** use Python `strftime` codes:

| Code | Description | Example |
|------|-------------|---------|
| `%Y` | Year (4 digits) | `2026` |
| `%m` | Month | `04` |
| `%d` | Day | `02` |
| `%H` | Hour (24h) | `21` |
| `%M` | Minutes | `45` |
| `%S` | Seconds | `00` |

```js
@import "omi:time" as t

var<number> ts = t.now()
println(t.format(ts))
println(t.format(ts, "%d/%m/%Y"))

var<number> ts2 = t.parse("2026-01-01 00:00:00")
println(ts2)

println(t.timezone())
t.sleep(1)
```

---

## math

Module for mathematical operations. Includes constants and functions.

```js
@import "omi:math" as m
```

### Constants

| Constant | Value |
|---------|-------|
| `m.pi` | 3.141592653589793 |
| `m.e` | 2.718281828459045 |
| `m.inf` | Infinity |

### Functions

| Function | Accepted arguments | Description |
|---------|---------------------|-------------|
| `m.abs(n<number>)` | `n`: `number` | Absolute value |
| `m.round(n<number>)` | `n`: `number` | Rounds to the nearest integer |
| `m.floor(n<number>)` | `n`: `number` | Rounds down |
| `m.ceil(n<number>)` | `n`: `number` | Rounds up |
| `m.sqrt(n<number>)` | `n`: `number` | Square root |
| `m.log(n<number>, [base<number>])` | `n`: `number`, `[base]`: `number`, optional | Logarithm; if `base` is omitted, natural logarithm is used |
| `m.exp(n<number>)` | `n`: `number` | Exponential (`e^n`) |
| `m.min(lst<list<number>>)` | `lst`: `list<number>` | Minimum value from a list of numbers |
| `m.max(lst<list<number>>)` | `lst`: `list<number>` | Maximum value from a list of numbers |
| `m.random()` | — | Random float in `[0.0, 1.0)` |
| `m.randint(a<number>, b<number>)` | `a`: `number`, `b`: `number` | Random integer in `[a, b]` inclusive |
| `m.randfloat(a<number>, b<number>, [digits<number>])` | `a`: `number`, `b`: `number`, `[digits]`: `number`, optional | Random float; `digits` controls decimal precision when provided |
| `m.choice(lst<list>)` | `lst`: `list` | Random item from a list |

```js
@import "omi:math" as m

println(m.pi)
println(m.sqrt(2))
println(m.log(m.e))
println(m.log(100, 10))
println(m.randfloat(1, 5))
println(m.randint(1, 6))
println(m.choice(["rock", "paper", "scissors"]))

var<list> nums = [5, 3, 9, 1]
println(m.min(nums))
println(m.max(nums))
```

---

## json

Module for JSON encoding, decoding, and file operations.

```js
@import "omi:json" as json
```

| Function | Accepted arguments | Description |
|---------|---------------------|-------------|
| `json.parse(text<string>)` | `text`: `string` | Parses a JSON string into an Omi value |
| `json.stringify(value<every>, [indent<number>])` | `value`: `every`, `[indent]`: `number`, optional | Serializes a value to JSON; `indent` enables pretty-print formatting |
| `json.read(path<string>)` | `path`: `string` | Reads and parses a JSON file |
| `json.write(path<string>, value<every>, [indent<number>])` | `path`: `string`, `value`: `every`, `[indent]`: `number`, optional | Writes a value to a JSON file |
| `json.append(path<string>, value<every>)` | `path`: `string`, `value`: `every` | Appends a value to a JSON array file |
| `json.exists(path<string>)` | `path`: `string` | Returns `true` if the file exists |

`json.parse` returns a `dict`, `list`, number, string, or boolean depending on the JSON content.
Dict fields are accessed with dot notation.

```js
@import "omi:json" as json

// Parse from string
var<dict> data = json.parse("{\"name\": \"Omi\", \"version\": 2}")
println(data.name)     // Omi
println(data.version)  // 2

// Serialize to string
var<string> s = json.stringify({"code": 200, "ok": true})
println(s)

// Read from file
var<dict> config = json.read("config.json")
println(config.host)

// Write to file
json.write("output.json", {"result": "ok"})
json.write("pretty.json", {"result": "ok"}, 2)

// Append to JSON array file
json.append("log.json", {"event": "start"})

// Check existence
if is json.exists("config.json"):
  println("config found")
end
```

---

## http

Module for making HTTP requests. Uses only the Python standard library — no external dependencies required.

```js
@import "omi:http" as http
```

### Functions

| Function | Accepted arguments | Description |
|---------|---------------------|-------------|
| `http.get(url<string>, [headers<dict>])` | `url`: `string`, `[headers]`: `dict`, optional | Sends a GET request |
| `http.post(url<string>, [body<every>], [headers<dict>])` | `url`: `string`, `[body]`: `every`, optional, `[headers]`: `dict`, optional | Sends a POST request |
| `http.put(url<string>, [body<every>], [headers<dict>])` | `url`: `string`, `[body]`: `every`, optional, `[headers]`: `dict`, optional | Sends a PUT request |
| `http.patch(url<string>, [body<every>], [headers<dict>])` | `url`: `string`, `[body]`: `every`, optional, `[headers]`: `dict`, optional | Sends a PATCH request |
| `http.delete(url<string>, [headers<dict>])` | `url`: `string`, `[headers]`: `dict`, optional | Sends a DELETE request |
| `http.request(method<string>, url<string>, [body<every>], [headers<dict>])` | `method`: `string`, `url`: `string`, `[body]`: `every`, optional, `[headers]`: `dict`, optional | Generic HTTP request |
| `http.download(url<string>, path<string>)` | `url`: `string`, `path`: `string` | Downloads a file to disk |
| `http.upload(url<string>, path<string>, [field_name<string>])` | `url`: `string`, `path`: `string`, `[field_name]`: `string`, optional, default `"file"` | Uploads a file via multipart form |

Any `http.*` call can be scheduled asynchronously with `async` at call site.

`http.req` is a built-in request shape for typed request payloads. It is a dict-like type with `method<string>`, `url<string>`, optional `body<every>`, and optional `headers<dict>` fields.

### Response Object

All request functions return a **Response** object with the following members accessible via dot notation:

| Member | Type | Description |
|--------|------|-------------|
| `.status` | int | HTTP status code (`200`, `404`, etc.) |
| `.text` | string | Raw response body as text |
| `.headers` | dict | Response headers |
| `.json()` | any | Parses the response body as JSON |

```js
@import "omi:http" as http

// Simple GET
var<http.req> res = http.get("https://httpbin.org/get")
println(res.status)   // 200
println(res.text)     // raw body

// Parse JSON response
var<http.req> data = res.json()
println(data.origin)  // the caller IP

// POST with JSON body
var<dict> body = {"name": "Omi", "version": 2}
var<http.req> res2 = http.post("https://httpbin.org/post", body)
println(res2.status)

// Custom headers
var<dict> headers = {"Authorization": "Bearer mytoken"}
var<http.req> res3 = http.get("https://api.example.com/data", headers)
var<dict> result = res3.json()

// Download a file
http.download("https://example.com/file.zip", "downloads/file.zip")

// Generic request
var<http.req> res4 = http.request("DELETE", "https://api.example.com/items/1")
println(res4.status)
```

---

## txt

Module for text file operations: reading, writing, appending, and managing plain-text files.

```js
@import "omi:txt" as txt
```

| Function | Accepted arguments | Description |
|---------|---------------------|-------------|
| `txt.read(path<string>, [encoding<string>])` | `path`: `string`, `[encoding]`: `string`, optional, default `"utf-8"` | Reads entire file contents as a string |
| `txt.write(path<string>, content<string>, [encoding<string>])` | `path`: `string`, `content`: `string`, `[encoding]`: `string`, optional | Writes a string to a file (overwrites) |
| `txt.append(path<string>, content<string>, [encoding<string>])` | `path`: `string`, `content`: `string`, `[encoding]`: `string`, optional | Appends a string to a file |
| `txt.lines(path<string>, [encoding<string>])` | `path`: `string`, `[encoding]`: `string`, optional | Returns file lines as `list<string>` (newlines stripped) |
| `txt.write_lines(path<string>, lines<list>, [encoding<string>])` | `path`: `string`, `lines`: `list`, `[encoding]`: `string`, optional | Writes each list element as a line |
| `txt.size(path<string>)` | `path`: `string` | Returns file size in bytes |
| `txt.exists(path<string>)` | `path`: `string` | Returns `true` if the file exists |
| `txt.backup(path<string>)` | `path`: `string` | Creates a timestamped `.bak` copy; returns the backup path |

Any `txt.*` call can be scheduled asynchronously with `async` at call site.

```js
@import "omi:txt" as txt

// Write and read back
txt.write("notes.txt", "Hello, Omi!\n")
var<string> content = txt.read("notes.txt")
println(content)

// Append a line
txt.append("notes.txt", "Second line\n")

// Read as lines
var<list> lines = txt.lines("notes.txt")
for i = 0 to len(lines):
  println(lines[i])
end

// File info
println(txt.size("notes.txt"))     // bytes
println(txt.exists("notes.txt"))   // true

// Backup before overwrite
var<string> bak = txt.backup("notes.txt")
println("Backed up to: ~bak")
```

---

## string

Module for string manipulation: slicing, splitting, padding, and more.

```js
@import "omi:string" as str
```

| Function | Accepted arguments | Description |
|---------|---------------------|-------------|
| `str.len(str<string>)` | `str`: `string` | Returns the number of characters |
| `str.slice(str<string>, start<int>, end<int>)` | `str`: `string`, `start`: `int`, `end`: `int` | Returns a substring from `start` (inclusive) to `end` (exclusive) |
| `str.split(str<string>, delimiter<string>)` | `str`: `string`, `delimiter`: `string` | Splits by delimiter, returns `list<string>` |
| `str.join(list<list>, delimiter<string>)` | `list`: `list`, `delimiter`: `string` | Joins list elements with the delimiter |
| `str.replace(str<string>, old<string>, new<string>, [count<int>])` | `str`, `old`, `new`: `string`; `[count]`: `int`, optional, default `-1` (all) | Replaces occurrences of `old` with `new` |
| `str.trim(str<string>)` | `str`: `string` | Strips leading and trailing whitespace |
| `str.trim_left(str<string>)` | `str`: `string` | Strips leading whitespace |
| `str.trim_right(str<string>)` | `str`: `string` | Strips trailing whitespace |
| `str.upper(str<string>)` | `str`: `string` | Converts to uppercase |
| `str.lower(str<string>)` | `str`: `string` | Converts to lowercase |
| `str.contains(str<string>, substring<string>)` | `str`, `substring`: `string` | Returns `true` if the substring is found |
| `str.starts_with(str<string>, prefix<string>)` | `str`, `prefix`: `string` | Returns `true` if `str` starts with `prefix` |
| `str.ends_with(str<string>, suffix<string>)` | `str`, `suffix`: `string` | Returns `true` if `str` ends with `suffix` |
| `str.index_of(str<string>, substring<string>)` | `str`, `substring`: `string` | Returns the index of the first match, or `-1` |
| `str.format(template<string>, values<list\|dict>)` | `template`: `string`, `values`: `list` or `dict` | Replaces `{}` positionally (list) or `{key}` by name (dict) |
| `str.repeat(str<string>, count<int>)` | `str`: `string`, `count`: `int` | Repeats the string `count` times |
| `str.pad_left(str<string>, length<int>, [char<string>])` | `str`: `string`, `length`: `int`, `[char]`: `string`, optional, default `" "` | Right-justifies string in field of `length` |
| `str.pad_right(str<string>, length<int>, [char<string>])` | `str`: `string`, `length`: `int`, `[char]`: `string`, optional, default `" "` | Left-justifies string in field of `length` |
| `str.reverse(str<string>)` | `str`: `string` | Reverses the string |

```js
@import "omi:string" as str

println(str.upper("hello"))             // HELLO
println(str.slice("hello world", 6, 11))  // world
println(str.split("a,b,c", ","))        // ["a", "b", "c"]
println(str.join(["x", "y", "z"], "-")) // x-y-z
println(str.replace("aabbcc", "b", "X")) // aaXXcc
println(str.trim("  hi  "))             // hi
println(str.pad_left("5", 3, "0"))      // 005
println(str.index_of("hello", "ll"))    // 2
println(str.format("Hello, {}!", ["Omi"]))          // Hello, Omi!
println(str.format("{name} is {age}", {"name": "Omi", "age": "2"}))
```

---

## regex

Module for regular expressions. Uses Python's `re` engine.

```js
@import "omi:regex" as rx
```

| Function | Accepted arguments | Description |
|---------|---------------------|-------------|
| `rx.test(str<string>, pattern<string>)` | `str`, `pattern`: `string` | Returns `true` if `pattern` matches anywhere in `str` |
| `rx.match(str<string>, pattern<string>)` | `str`, `pattern`: `string` | Returns the first match as a string, or `null` |
| `rx.find_all(str<string>, pattern<string>)` | `str`, `pattern`: `string` | Returns all non-overlapping matches as `list<string>` |
| `rx.replace(str<string>, pattern<string>, replacement<string>)` | `str`, `pattern`, `replacement`: `string` | Replaces all matches with `replacement` |
| `rx.split(str<string>, pattern<string>)` | `str`, `pattern`: `string` | Splits `str` by the pattern, returns `list<string>` |

```js
@import "omi:regex" as rx

println(rx.test("hello world", "\\bworld\\b"))   // true
println(rx.match("foo 123 bar", "\\d+"))          // 123
println(rx.find_all("cat bat sat", "[a-z]at"))    // ["cat", "bat", "sat"]
println(rx.replace("2024-01-15", "-", "/"))       // 2024/01/15
println(rx.split("one  two   three", "\\s+"))     // ["one", "two", "three"]
```

---

## log

Module for structured logging to stdout and files.

```js
@import "omi:log" as log
```

### Functions

| Function | Accepted arguments | Description |
|---------|---------------------|-------------|
| `log.debug([message<string>])` | `[message]`: `string`, optional, default `""` | Emits a DEBUG log |
| `log.info([message<string>])` | `[message]`: `string`, optional, default `""` | Emits an INFO log |
| `log.warning([message<string>])` | `[message]`: `string`, optional, default `""` | Emits a WARNING log |
| `log.error([message<string>])` | `[message]`: `string`, optional, default `""` | Emits an ERROR log |
| `log.critical([message<string>])` | `[message]`: `string`, optional, default `""` | Emits a CRITICAL log |
| `log.set_level(level<string>)` | `level`: `string` | Sets level (`DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`) |
| `log.set_file(path<string>, [mode<string>])` | `path`: `string`, `[mode]`: `string`, optional (`append` or `write`) | Enables file logging |
| `log.rotate([max_size<string>], [backup_count<number>])` | `[max_size]`: `string` (for example `10MB`), `[backup_count]`: `number` | Enables rotating file logs |
| `log.with_context(data<dict>)` | `data`: `dict` | Adds shared context fields to subsequent logs |
| `log.json_mode()` | — | Switches output format to JSON |
| `log.trace()` | — | Logs and returns current source location |

```js
@import "omi:log" as log

log.set_level("DEBUG")
log.debug("debug-msg")
log.info("info-msg")
log.with_context({"user_id": 42, "request_id": "req-1"})
log.warning("warn-msg")

log.set_file("tests/log_runtime.log", "write")
log.error("error-msg")
log.rotate(max_size="1KB", backup_count=2)
log.critical("critical-msg")

var<string> trace_loc = log.trace()
println("TRACEVAL: " + trace_loc)

log.json_mode()
log.info("json-msg")
```
