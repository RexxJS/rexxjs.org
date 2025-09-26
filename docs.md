---
layout: default
title: Docs
---

# Docs

Start here for a guided overview, then dive into the full reference.

## Getting Started

- What is RexxJS? A modern REXX targeting Node.js, browsers, and a standalone binary, with rich built‑in libraries.
- Try it in your browser: [Online REPL](https://repl.rexxjs.org)
- Browse the full [Reference](reference/00-INDEX.html)

## Key Topics

- Language Basics — [Basic Syntax](reference/01-basic-syntax.html), [Control Flow](reference/02-control-flow.html), [Advanced Statements](reference/03-advanced-statements.html)
- Everyday Functions — [Strings](reference/04-string-functions.html), [Math](reference/05-math-functions.html), [Arrays](reference/06-array-functions.html), [Date/Time](reference/07-datetime-functions.html)
- Data & Web — [JSON](reference/08-json-functions.html), [Web](reference/09-web-functions.html), [Filesystem](reference/13-filesystem-functions.html), [SQLite Address](reference/26-sqlite-address.html)
- Reliability — [Error Handling](reference/24-error-handling.html), [Assertions & Expect](reference/31-assertions-expect-documentation.html), [Testing with rexxt](reference/32-testing-rexxt.html)
- Orchestration — [Application Addressing](reference/19-application-addressing.html), [ADDRESS Patterns](reference/27-address-heredoc-patterns.html), [Require System](reference/23-require-system.html)

## Examples

Hello world with arrays and mapping:

```rexx
LET nums = [1,2,3,4]
LET doubled = MAP(nums, "x * 2")
SAY doubled
```

Fetch JSON and validate a field:

```rexx
LET data = JSON_PARSE(URL_READ("https://api.example.com/todo/1"))
IF IS_OBJECT(data) & data.id = 1 THEN SAY "ok"
```

## Next Steps

- Explore the [Function Reference](reference/35-function-reference.html)
- Learn the [REPL](reference/34-repl-guide.html)
- See [Interpolation Patterns](reference/36-interpolation-patterns.html)

