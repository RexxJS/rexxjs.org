# Interpolation Patterns Reference

Comprehensive guide to string interpolation patterns and pattern switching in RexxJS.

## Overview

RexxJS supports configurable string interpolation patterns that allow you to customize how variables are embedded within strings. You can switch between predefined patterns at runtime or create custom patterns for specific use cases.

## Quick Reference

```rexx
-- Switch between predefined patterns
SET_INTERPOLATION('handlebars')  -- {{variable}} (default)
SET_INTERPOLATION('shell')       -- ${variable}
SET_INTERPOLATION('batch')       -- %variable%
SET_INTERPOLATION('doubledollar') -- $$variable$$

-- Create custom patterns by example
SET_INTERPOLATION('<<v>>')       -- <<variable>>
SET_INTERPOLATION('{v}')         -- {variable}
```

## Important Notes

### String Quote Types Matter

Only **double-quoted strings** support interpolation:

```rexx
LET name = "Alice"

-- ✅ Double quotes: interpolation works
SAY "Hello {{name}}"  -- Output: Hello Alice

-- ❌ Single quotes: literal text only
SAY 'Hello {{name}}'  -- Output: Hello {{name}}
```

### Pattern Scope

Pattern changes affect all subsequent string interpolation globally:

```rexx
SET_INTERPOLATION('shell')
SAY "Name: ${name}"      -- Uses shell pattern

-- All strings after this use shell pattern
LET message = "User ${name} logged in"
SAY message              -- Uses shell pattern
```

## Predefined Patterns

### HANDLEBARS (Default)
**Pattern:** `{{variable}}`
**Default:** Yes

```rexx
LET name = "Alice"
SAY "Hello {{name}}"  -- Output: Hello Alice
```

### SHELL
**Pattern:** `${variable}`

```rexx
SET_INTERPOLATION('shell')
LET path = "/usr/local/bin"
SAY "Installing to ${path}"  -- Output: Installing to /usr/local/bin
```

### BATCH
**Pattern:** `%variable%`

```rexx
SET_INTERPOLATION('batch')
LET username = "admin"
SAY "User: %username%"  -- Output: User: admin
```

### DOUBLEDOLLAR
**Pattern:** `$$variable$$`

```rexx
SET_INTERPOLATION('doubledollar')
LET service = "web-api"
SAY "Service: $$service$$"  -- Output: Service: web-api
```

## Creating Custom Patterns

You can create custom interpolation patterns by providing an example:

```rexx
-- Single brace pattern
SET_INTERPOLATION('{v}')
LET name = "Bob"
SAY "Hello {name}"  -- Output: Hello Bob

-- Angle bracket pattern
SET_INTERPOLATION('<<v>>')
SAY "Hello <<name>>"  -- Output: Hello Bob

-- Square bracket pattern
SET_INTERPOLATION('[v]')
SAY "Hello [name]"  -- Output: Hello Bob

-- Custom delimiter pattern
SET_INTERPOLATION('@@v@@')
SAY "Hello @@name@@"  -- Output: Hello Bob
```

**Pattern Example Rules:**
- Must contain a single variable placeholder (typically `v`)
- The placeholder is replaced with the actual variable name
- Start and end delimiters are extracted from the example

## Pattern Switching

### Basic Pattern Switching

```rexx
LET name = "Alice"

-- Start with default handlebars
SAY "Handlebars: {{name}}"  -- Output: Handlebars: Alice

-- Switch to shell
SET_INTERPOLATION('shell')
SAY "Shell: ${name}"         -- Output: Shell: Alice

-- Switch to batch
SET_INTERPOLATION('batch')
SAY "Batch: %name%"          -- Output: Batch: Alice

-- Back to handlebars
SET_INTERPOLATION('handlebars')
SAY "Handlebars: {{name}}"   -- Output: Handlebars: Alice
```

### Context-Specific Patterns

```rexx
-- Shell script generation
SET_INTERPOLATION('shell')
LET script = "#!/bin/bash\necho ${message}"

-- Windows batch file generation
SET_INTERPOLATION('batch')
LET batch = "@echo off\necho %message%"

-- Template generation
SET_INTERPOLATION('handlebars')
LET template = "<h1>{{title}}</h1>"
```

## Integration with ADDRESS and HEREDOC

Interpolation patterns work seamlessly with ADDRESS commands and HEREDOC blocks:

### ADDRESS Command Interpolation

```rexx
LET container = "my-app"

-- Docker commands with handlebars
ADDRESS DOCKER
"create image=alpine:latest name={{container}}"
"start name={{container}}"
SAY "Container {{container}} started"
```

### HEREDOC with Custom Patterns

```rexx
-- SQL with custom pattern
SET_INTERPOLATION('[v]')
LET tableName = "users"
LET userName = "alice"

ADDRESS sqlite3 <<QUERY
SELECT * FROM [tableName] WHERE name = '[userName]'
QUERY

-- JSON API with handlebars
SET_INTERPOLATION('handlebars')
LET userId = "12345"
LET action = "login"

ADDRESS api <<JSON_REQUEST
{
  "endpoint": "/users/{{userId}}/actions",
  "body": {
    "action": "{{action}}",
    "timestamp": "{{NOW()}}"
  }
}
JSON_REQUEST
```

## Practical Examples

### Multi-Environment Configuration

```rexx
-- Development environment
LET env = "development"
LET dbHost = "localhost"
LET dbPort = "5432"

-- Use shell-style for config files
SET_INTERPOLATION('shell')

LET config = <<CONFIG
[${env}]
host=${dbHost}
port=${dbPort}
debug=true
CONFIG

FILE_WRITE filename="config.ini" content=config
```

### Template Generation

```rexx
-- HTML email template
SET_INTERPOLATION('handlebars')

LET userName = "Alice"
LET orderNumber = "ORD-12345"
LET total = "99.99"

LET emailTemplate = <<HTML
<!DOCTYPE html>
<html>
<head>
    <title>Order Confirmation</title>
</head>
<body>
    <h1>Hello {{userName}}</h1>
    <p>Your order {{orderNumber}} has been confirmed.</p>
    <p>Total: ${{total}}</p>
</body>
</html>
HTML

SAY emailTemplate
```

### Shell Script Generation

```rexx
-- Generate bash script
SET_INTERPOLATION('shell')

LET appName = "myapp"
LET version = "1.0.0"
LET installDir = "/opt/myapp"

LET script = <<BASH
#!/bin/bash
APP_NAME=${appName}
VERSION=${version}
INSTALL_DIR=${installDir}

echo "Installing ${APP_NAME} v${VERSION}"
mkdir -p ${INSTALL_DIR}
echo "Installation complete"
BASH

FILE_WRITE filename="install.sh" content=script
```

## Best Practices

### 1. Use Appropriate Patterns for Context

```rexx
-- Shell scripts → shell pattern
SET_INTERPOLATION('shell')

-- Windows batch → batch pattern
SET_INTERPOLATION('batch')

-- Templates → handlebars pattern
SET_INTERPOLATION('handlebars')

-- SQL (if needed) → custom bracket pattern
SET_INTERPOLATION('[v]')
```

### 2. Be Consistent Within Context

```rexx
-- ✅ Good: Consistent pattern usage
SET_INTERPOLATION('shell')
LET config = "host=${host}\nport=${port}"
LET script = "#!/bin/bash\nexport PATH=${path}"

-- ❌ Avoid: Mixing patterns within same context
SET_INTERPOLATION('shell')
LET mixed = "host=${host}\nport={{port}}"  -- Inconsistent
```

### 3. Document Pattern Changes

```rexx
-- Document why you're switching patterns
SAY "Generating shell scripts..."
SET_INTERPOLATION('shell')
-- ... shell script generation ...

SAY "Generating HTML templates..."
SET_INTERPOLATION('handlebars')
-- ... HTML template generation ...
```

### 4. Return to Default When Done

```rexx
-- Use custom pattern for specific task
SET_INTERPOLATION('[v]')
-- ... SQL generation ...

-- Return to default
SET_INTERPOLATION('handlebars')
-- ... rest of script ...
```

## Function Reference

### SET_INTERPOLATION(pattern)

Sets the global interpolation pattern by name or example.

**Parameters:**
- `pattern` - Pattern name (`'handlebars'`, `'shell'`, `'batch'`, `'doubledollar'`) or pattern example (e.g., `'{v}'`, `'<<v>>'`)

**Returns:** String - name of the pattern that was set (usually ignored)

**Examples:**
```rexx
-- By predefined name
SET_INTERPOLATION('shell')
SET_INTERPOLATION('batch')
SET_INTERPOLATION('handlebars')

-- By pattern example
SET_INTERPOLATION('{v}')
SET_INTERPOLATION('<<v>>')
SET_INTERPOLATION('[v]')
```

## Predefined Pattern Names

| Name | Pattern | Example Output |
|------|---------|----------------|
| `handlebars` | `{{var}}` | `Hello {{name}}` → `Hello Alice` |
| `shell` | `${var}` | `Hello ${name}` → `Hello Alice` |
| `batch` | `%var%` | `Hello %name%` → `Hello Alice` |
| `doubledollar` | `$$var$$` | `Hello $$name$$` → `Hello Alice` |

## See Also

- [Basic Syntax](01-basic-syntax.md) - String interpolation fundamentals
- [ADDRESS HEREDOC Patterns](27-address-heredoc-patterns.md) - Using interpolation with ADDRESS
- [Address Handler Utilities](29-address-handler-utilities.md) - Interpolation in handlers
- [Output & Debugging](20-output-debug.md) - SAY statement with interpolation

---

**Pattern Count:** 4 predefined patterns + unlimited custom patterns
**Delimiter Support:** Single or multi-character delimiters with automatic escaping
**Scope:** Global pattern switching with script-wide effect
**Quote Requirement:** Double quotes only (`"string"`) - single quotes (`'string'`) are literal
