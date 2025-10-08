---
layout: default
title: Home
---

# RexxJS

A modern REXX for JavaScript runtimes. Run classic REXX with powerful new libraries in Node.js, browsers, and standalone binaries — with first-class cross-application messaging.

## Getting Started

- Try in browser: [Online REPL](https://repl.rexxjs.org)
- Explore the language: [Reference](reference/00-INDEX.html)
- Get the code: GitHub (coming soon)

### Quick Example

```rexx
-- Modern variable assignment
LET data = [1, 2, 3, 4, 5]
LET processed = MAP(data, "x * 2")
SAY processed

-- Pipe operator with named parameters
LET result = "  hello world  "
  |> STRIP()
  |> SUBSTR(start=7, length=5)
-- Result: "world"

-- Load libraries with operations and functions
REQUIRE "cwd:libs/bathhouse.js"
SERVE_GUEST guest="river_spirit"        -- Operation: side effect
LET count = COUNT_TOKENS()               -- Function: returns value
```

## Why RexxJS

- **Familiar REXX syntax** with modern ergonomics
- **Pipe operator (`|>`)** with named parameters for elegant data transformations
- **Operations vs Functions** architecture: imperative commands and pure functions working together
- **200+ functions** across filesystems, strings, math, time, web, and more
- **Flexible parameters**: Both positional and named styles work everywhere
- **ADDRESS-based** cross-application control for scripts that orchestrate systems
- **Browser-friendly**: postMessage channels, iframe control, automation hooks
- **Data tooling**: R- and SciPy-inspired helpers for analysis work
- **SQLite integration** for lightweight persistence

## Distribution

- Standalone binary `./rexx` — no Node.js required
- Node.js CLI `node core/src/cli.js` — great for dev
- Test runner `./rexxt` — TUI experience

## Use Cases

- Data analysis and automation
- Web/browser orchestration and testing
- Database tasks and API integration
- System administration and scripting

Looking for specifics? Jump to the [Reference](reference/00-INDEX.html).

---

*RexxJS brings the power of REXX to modern JavaScript environments.*
