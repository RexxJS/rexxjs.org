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

### Core Strengths

- **Familiar REXX syntax** with modern ergonomics (variables, loops, subroutines)
- **Multi-environment execution**: Run the same script in Node.js, browsers, or standalone binaries
- **Pipe operator (`|>`)** with named parameters for elegant data transformations
- **Operations vs Functions** architecture: imperative commands and pure functions working together
- **500+ functions** across filesystems, strings, math, time, web, DOM, and more

### Integration and Automation

- **Cross-application control** via ADDRESS statement for orchestrating systems
- **Control-bus scripting** with sandboxed iframe execution and real-time progress monitoring
- **Web automation** with gray-box testing via DOM queries, element filtering, and chainable operations
- **Remote execution** for containers and VMs (Docker, Podman, QEMU, VirtualBox, Proxmox)
- **Gray-box browser testing** - combine white-box (DOM access) with black-box (user interactions)

### Data and Functions

- **Function pipelines** - FILTER_BY_ATTR, GET_VALUES, GET_TEXT chain DOM data extraction
- **Flexible parameters**: Both positional and named styles work everywhere
- **Dynamic function discovery** - INFO() and FUNCTIONS() for runtime introspection
- **Data tooling**: R- and SciPy-inspired helpers for analysis and transformation
- **SQLite integration** for lightweight persistence
- **HTTP API** integration with automatic retry and error handling

## Distribution

- Standalone binary `./rexx` — no Node.js required
- Node.js CLI `node core/src/cli.js` — great for dev
- Test runner `./rexxt` — TUI experience

## Use Cases

**Web and Browsers**
- Browser automation with gray-box testing (DOM + user interactions)
- Cross-iframe scripting via control-bus architecture
- Real-time progress monitoring in web applications
- Web scraping and data extraction with pipeline operators

**Node.js and CLI**
- System administration and infrastructure automation
- Container and VM orchestration (Docker, Podman, QEMU, etc.)
- Local and remote script execution
- File system operations and command orchestration

**Data and Integration**
- Data analysis and transformation with piped operations
- API integration and HTTP workflows
- Database tasks with SQLite
- Statistical computing with R/SciPy-inspired functions

**Testing and Automation**
- System integration testing with gray-box capabilities
- Multi-application orchestration via ADDRESS handlers
- Resilient workflows with retry logic and error handling
- Infrastructure-as-code for VMs and containers

Looking for specifics? Jump to the [Reference](reference/00-INDEX.html).

---

*RexxJS brings the power of REXX to modern JavaScript environments.*
