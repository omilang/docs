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

---

## How to Import a Module

Built-in modules are imported with `@import` using the `omi:` prefix:

```js
@import "omi:system" as sys
@import "omi:json" as json
@import "omi:http" as http
```

After import, all module functions are available through the alias via dot notation.

> Optional arguments are shown in square brackets, for example `[fmt<string>]`.

---

## system

Module for OS interaction: command execution, environment variables, platform info, and process control.

```js
@import "omi:system" as sys
```

| Function | Accepted arguments | Description |
|---------|---------------------|-------------|
| `sys.exec(command<string>)` | `command`: `string` | Runs a shell command and returns the output as `string` |
| `sys.env(name<string>)` | `name`: `string` | Gets an environment variable value |
| `sys.set_env(name<string>, value<string>)` | `name`: `string`, `value`: `string` | Sets an environment variable |
| `sys.platform()` | — | Returns OS name: `"Windows"`, `"Linux"`, `"Darwin"` |
| `sys.username()` | — | Returns the current username |
| `sys.cwd()` | — | Returns the current working directory |
| `sys.exit([code<number>])` | `[code]`: `number`, optional, default `0` | Exits the script with a status code |

```js
@import "omi:system" as sys

println(sys.platform())
println(sys.username())
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
var<every> res = http.get("https://httpbin.org/get")
println(res.status)   // 200
println(res.text)     // raw body

// Parse JSON response
var<dict> data = res.json()
println(data.origin)  // the caller IP

// POST with JSON body
var<dict> body = {"name": "Omi", "version": 2}
var<every> res2 = http.post("https://httpbin.org/post", body)
println(res2.status)

// Custom headers
var<dict> headers = {"Authorization": "Bearer mytoken"}
var<every> res3 = http.get("https://api.example.com/data", headers)
var<dict> result = res3.json()

// Download a file
http.download("https://example.com/file.zip", "downloads/file.zip")

// Generic request
var<every> res4 = http.request("DELETE", "https://api.example.com/items/1")
println(res4.status)
```
