---
layout: default
title: Home
---

# RexxJS

REXX interpreter implemented in JavaScript, designed to run both in Node.js and browsers with comprehensive cross-application communication capabilities.

## Getting Started

RexxJS provides a complete REXX environment with modern extensions, making it suitable for everything from simple scripting to complex distributed applications.

### Quick Example

```rexx
-- Modern variable assignment
LET data = [1, 2, 3, 4, 5]
LET processed = MAP(data, "x * 2")
SAY processed
```

## Key Features

- **Complete REXX Language Implementation** - Classic syntax with modern extensions
- **Cross-Application Communication** - ADDRESS mechanism for external system integration  
- **Browser Integration** - PostMessage-based communication between iframes
- **Function Libraries** - 200+ built-in functions across multiple domains
- **Statistical Computing** - R/SciPy-compatible functions for data analysis
- **Database Operations** - SQLite integration with full CRUD capabilities

## Distribution

- **Standalone Binary** (`./rexx`) - 49MB, no Node.js required
- **Node.js CLI** (`node core/src/cli.js`) - For development
- **Test Runner** (`./rexxt`) - With TUI experience

## Use Cases

- Scientific Computing and Data Analysis
- Web Automation and Browser Control
- Database Operations and API Integration
- System Administration
- Cross-Application Testing

---

*RexxJS brings the power of REXX to modern JavaScript environments.*