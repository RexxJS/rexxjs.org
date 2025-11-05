---
layout: default
title: Function Discovery and Metadata
---

# Function Discovery and Metadata System

The Function Discovery System provides runtime introspection of all available REXX functions, including metadata about modules, categories, parameters, and examples. This enables self-documenting libraries and powerful function exploration capabilities.

## Overview

RexxJS includes two reflection functions that let you explore the 500+ available functions at runtime:

- **`INFO(functionName)`** - Get detailed metadata about a specific function
- **`FUNCTIONS([query])`** - List functions by module, category, or search for specific functions

Both return REXX stem arrays for seamless integration with REXX code.

## Function Discovery with INFO()

Get comprehensive metadata about any function:

```rexx
info = INFO("UPPER")
SAY "Module: " info.1          /* string-functions.js */
SAY "Category: " info.2        /* String */
SAY "Description: " info.3     /* Convert string to uppercase */
SAY "Parameters: " info.4      /* ["string"] */
SAY "Returns: " info.5         /* string */
SAY "Examples: " info.6        /* ["UPPER(\"hello\") => \"HELLO\""] */
```

### Return Structure

`INFO()` returns a REXX stem array:
- `.0` - Number of properties (always 6)
- `.1` - **Module** - Source module name (e.g., "string-functions.js")
- `.2` - **Category** - Function category (e.g., "String", "Math", "Array")
- `.3` - **Description** - Human-readable description
- `.4` - **Parameters** - JSON array of parameter names
- `.5` - **Returns** - Return type (e.g., "string", "number", "array")
- `.6` - **Examples** - JSON array of usage examples

### Error Handling

If function not found, returns error information:

```rexx
info = INFO("NOSUCHFUNCTION")
SAY info.error    /* Function 'NOSUCHFUNCTION' not found... */
SAY info.hint     /* Use FUNCTIONS() to list all available functions */
```

### Examples

**Exploring a math function:**
```rexx
info = INFO("MATH_SQRT")
SAY info.1  /* math-functions.js */
SAY info.2  /* Math */
SAY info.3  /* Calculate square root */
```

**Learning about an array function:**
```rexx
info = INFO("ARRAY_FLATTEN")
SAY info.3  /* Flatten nested array */
SAY info.4  /* ["array", "depth"] */
```

**Getting DOM function info:**
```rexx
info = INFO("ELEMENT")
SAY info.1  /* dom-functions.js */
SAY info.2  /* DOM */
```

## Function Listing with FUNCTIONS()

### List All Functions by Module

With no arguments, lists all functions grouped by module:

```rexx
allFuncs = FUNCTIONS()
SAY "Total modules: " allFuncs.0
/* Shows lines like: string-functions.js: UPPER, LOWER, LENGTH, ... */
DO i = 1 TO MIN(5, allFuncs.0)
  SAY allFuncs.(i)
END
```

### Filter by Category

List all functions in a specific category:

```rexx
stringFuncs = FUNCTIONS("String")
SAY "String functions: " stringFuncs.0
DO i = 1 TO MIN(10, stringFuncs.0)
  SAY "  - " stringFuncs.(i)
END
```

**Available categories:**
- String, Math, Array, DOM, DOM Pipeline
- Shell, Path, File, JSON, Regex
- Logic, Validation, Interpreter
- Custom (for user-defined functions)

### Filter by Module

List all functions from a specific module:

```rexx
arrayFuncs = FUNCTIONS("array-functions.js")
SAY "Array functions: " arrayFuncs.0
/* Returns: ARRAY_GET, ARRAY_SET, ARRAY_LENGTH, ARRAY_PUSH, ... */
```

**Core modules:**
- string-functions.js, math-functions.js, array-functions.js
- dom-functions.js, dom-pipeline-functions.js
- shell-functions.js, path-functions.js, file-functions.js
- json-functions.js, regex-functions.js, and more

### Quick Function Lookup

Get quick info about a specific function by name:

```rexx
info = FUNCTIONS("UPPER")
SAY info.1
/* Output: string-functions.js - String: Convert string to uppercase */
```

### Return Structure

`FUNCTIONS()` returns different formats depending on arguments:

**No arguments** - Modules with functions:
```json
{
  "0": 23,
  "1": "string-functions.js: UPPER, LOWER, LENGTH, ...",
  "2": "math-functions.js: ABS, INT, MAX, ...",
  ...
}
```

**With category** - Function names:
```json
{
  "0": 18,
  "1": "UPPER",
  "2": "LOWER",
  "3": "LENGTH",
  ...
}
```

**With module** - Function names:
```json
{
  "0": 22,
  "1": "ARRAY_GET",
  "2": "ARRAY_SET",
  "3": "ARRAY_LENGTH",
  ...
}
```

**With function name** - Quick info:
```json
{
  "0": 1,
  "1": "shell-functions.js - Shell: Print output"
}
```

## Practical Usage Examples

### Discover String Functions

```rexx
/* Find all string manipulation functions */
stringFuncs = FUNCTIONS("String")
SAY "Available string functions: " stringFuncs.0
SAY "First 5:"
DO i = 1 TO MIN(5, stringFuncs.0)
  func = stringFuncs.(i)
  info = INFO(func)
  SAY "  " func " - " info.3
END
```

### Document a Function

```rexx
/* Create documentation for a function */
func = "ARRAY_FLATTEN"
info = INFO(func)
SAY "Function: " func
SAY "Module: " info.1
SAY "Category: " info.2
SAY "Description: " info.3
SAY "Parameters: " info.4
SAY "Returns: " info.5
IF info.6 \= "" THEN
  SAY "Examples: " info.6
```

### List All Math Functions with Details

```rexx
/* Find all math functions and show their descriptions */
mathFuncs = FUNCTIONS("Math")
SAY "Math Functions (" mathFuncs.0 " total):"
DO i = 1 TO mathFuncs.0
  func = mathFuncs.(i)
  info = INFO(func)
  SAY "  " LEFT(func, 20) " - " info.3
END
```

### Find Functions from a Module

```rexx
/* List all functions in the math module */
mathModule = FUNCTIONS("math-functions.js")
SAY "Functions in math-functions.js: " mathModule.0
DO i = 1 TO mathModule.0
  SAY "  " i ". " mathModule.(i)
END
```

## Dynamic Metadata Registration

When you load custom libraries with `REQUIRE`, their functions can register metadata for discovery:

### Creating a Self-Documenting Library

```javascript
// mylib.js
function GREET(name) {
  return `Hello, ${name}!`;
}

const __metadata__ = {
  GREET: {
    module: 'mylib.js',
    category: 'Custom',
    description: 'Generate personalized greeting',
    parameters: ['name'],
    returns: 'string',
    examples: ['GREET("Alice") => "Hello, Alice!"']
  }
};

module.exports = { GREET, __metadata__ };
```

### Using the Library

```rexx
REQUIRE 'mylib' AS lib_(.*)

/* Your functions are now discoverable! */
FUNCTIONS('Custom')     /* Lists lib_GREET and other custom functions */
INFO('lib_GREET')       /* Returns full metadata */

/* Use the function normally */
SAY lib_GREET("World")  /* Output: Hello, World! */
```

### Metadata Properties

Required fields for each function:
- **`module`** - Source module name
- **`category`** - Function category
- **`description`** - Human-readable description
- **`parameters`** - Array of parameter names
- **`returns`** - Return type

Optional fields:
- **`examples`** - Array of usage examples

## Case Sensitivity

All lookups are **case-insensitive**:

```rexx
/* All equivalent */
INFO("upper")
INFO("UPPER")
INFO("Upper")

/* Category lookups */
FUNCTIONS("string")       /* Same as */
FUNCTIONS("STRING")       /* or */
FUNCTIONS("String")

/* Module lookups */
FUNCTIONS("string-functions.js")     /* Same as */
FUNCTIONS("STRING-FUNCTIONS.JS")
```

## Function Categories Reference

### Core Categories (Built-in)

| Category | Example Functions | Count |
|----------|------------------|-------|
| **String** | UPPER, LOWER, LENGTH, SUBSTR, REPLACE | 18 |
| **Math** | ABS, SQRT, SIN, COS, MAX, MIN | 20 |
| **Array** | ARRAY_PUSH, ARRAY_SORT, ARRAY_MAP, JOIN | 22 |
| **DOM** | ELEMENT | 1 |
| **DOM Pipeline** | FILTER_BY_ATTR, GET_VALUES, GET_TEXT | 5 |
| **Shell** | SAY, PASTE, CUT, SHUF | 4 |
| **Path** | PATH_JOIN, PATH_RESOLVE | 2 |
| **File** | FILE_READ, FILE_WRITE | 2 |
| **JSON** | JSON_STRINGIFY, JSON_PARSE | 2 |
| **Regex** | REGEX_MATCH, REGEX_EXTRACT | 2 |
| **Validation** | IS_NUMBER, IS_STRING | 2 |
| **Interpreter** | INFO, FUNCTIONS, TYPEOF, SYMBOL | 4+ |

### User-Defined Categories

When loading custom libraries with metadata, you can create new categories:

```rexx
REQUIRE 'mylib' AS custom_(.*)
FUNCTIONS('MyCategory')    /* Lists functions with category='MyCategory' */
```

## Integration with REQUIRE

The metadata system automatically discovers functions from loaded modules:

```rexx
/* Load library */
REQUIRE 'statistics-lib' AS stats_(.*)

/* Discover what's available */
stats_funcs = FUNCTIONS('Statistics')
SAY "Available stats functions: " stats_funcs.0

/* Get details on a specific function */
info = INFO('stats_MEAN')
SAY info.3    /* Description of stats_MEAN */

/* Use it */
LET average = stats_MEAN(data)
```

## Performance Characteristics

- **Lookup time:** O(1) for function info, O(n) for category/module listing
- **Memory:** ~50KB for built-in metadata registry
- **Dynamic registration:** Negligible overhead when loading modules
- **Function discovery:** No impact on function execution performance

## Related Functions

- `TYPEOF(value)` - Get type of a value
- `SYMBOL(varName)` - Check if symbol is variable or literal
- `SUBROUTINES()` - List available subroutines
- `FUNCTIONS()` - Function discovery (this guide)
- `INFO(functionName)` - Function metadata (this guide)

## See Also

- [DOM Functions](21-dom-functions.md) - DOM pipeline functions
- [REQUIRE System](23-require-system.md) - Dynamic library loading
- [Function Reference](35-function-reference.md) - Comprehensive function catalog
