# Basic Syntax

Fundamental language constructs and variable management in RexxJS Rexx.

## Variable Management

### Variable Assignment
```rexx
LET variable = value
LET result = functionName param=value
```

**Examples:**
```rexx
LET name = "John"
LET age = 30
LET total = calculateTax amount=100 rate=0.08
```

### Variable Substitution
Variables are automatically resolved in function parameters and expressions:

```rexx
LET base = 10
LET multiplier = 3
LET result = base + multiplier * 4    -- Evaluates to 22
createMeal potatoes=base*2 chicken=result/5
```

## Operations vs Functions

RexxJS distinguishes between two types of callable code:

### Operations (Imperative Commands)
Operations are side-effect actions called **without parentheses**:

```rexx
-- Load a library with operations
REQUIRE "cwd:libs/bathhouse.js"

-- Operations: imperative commands (no parentheses)
SERVE_GUEST guest="river_spirit" bath="herbal"
CLEAN_BATHHOUSE area="main_hall" intensity="deep"
ISSUE_TOKEN worker="chihiro" task="cleaning"
```

**Characteristics:**
- ❌ No parentheses in call syntax
- ✅ Named parameters only (passed as object)
- ✅ Used for state changes and side effects
- ❌ Cannot be used in expressions or pipes
- ✅ Loaded via REQUIRE alongside functions

### Functions (Expressions)
Functions are pure/query operations called **with parentheses**:

```rexx
-- Functions: expressions (with parentheses, return values)
LET capacity = BATHHOUSE_CAPACITY()
LET count = COUNT_TOKENS()
LET spirit = IDENTIFY_SPIRIT(description="muddy")

-- String functions
LET upper = UPPER(string="hello world")
LET length = LENGTH(string=upper)

-- Math functions
LET maximum = MAX(x=10, y=25, z=15)
LET absolute = ABS(value=-42)

-- Utility functions
LET today = DATE()
LET timestamp = NOW()
```

**Characteristics:**
- ✅ Always use parentheses (even if no params)
- ✅ Support both positional AND named parameters
- ✅ Can be used in expressions, assignments, pipes
- ✅ Parameters converted via parameter-converter
- ✅ Work with pipe operator: `data |> FUNC(param=val)`

### Named vs Positional Parameters

Functions support **both parameter styles** interchangeably:

```rexx
-- Positional parameters
LET sub1 = SUBSTR("hello world", 7, 5)    -- "world"
LET spirit1 = IDENTIFY_SPIRIT("muddy")     -- "river_spirit"

-- Named parameters
LET sub2 = SUBSTR(start=7, length=5)       -- Works with piped data
LET spirit2 = IDENTIFY_SPIRIT(description="hungry")  -- "no_face"

-- Named parameters in pipes
LET result = "hello world" |> SUBSTR(start=7, length=5)  -- "world"
LET cleaned = "  text  " |> STRIP() |> SUBSTR(start=2, length=3)  -- "ext"
```

**Parameter Conversion:**
- The parameter-converter translates named params to positional args
- Functions like `SUBSTR`, `POS`, `WORDPOS` designed for data-first piping
- Piped value becomes first argument, named params fill remaining positions

### Parameter Types
Functions support multiple parameter types:

```rexx
-- Strings (quoted)
DOM_TYPE(selector="#input", text="Hello World")

-- Numbers (unquoted)
LET result = MATH_POWER(base=2, exponent=10)

-- Booleans
LET valid = IS_EMAIL(email="user@example.com")

-- Expressions
LET calculated = MAX(x=base*2, y=multiplier+5, z=10)
```

## Mathematical Expressions

### Full Arithmetic Support
- **Basic Operators**: `+`, `-`, `*`, `/`
- **Operator Precedence**: Proper mathematical precedence
- **Parentheses Support**: `(expression)` for grouping
- **Variable Integration**: Use variables in expressions

**Examples:**
```rexx
LET base = 10
LET multiplier = 3
LET result = base + multiplier * 4    -- Evaluates to 22
LET withParens = (base + multiplier) * 4  -- Evaluates to 52

-- In function parameters
createMeal potatoes=base*2 chicken=result/5

-- In loop ranges
DO i = 1 TO result/4
  prepareDish servings=i*2+1
END
```

## Pipe Operator

### Function Chaining with `|>`

The pipe operator `|>` enables elegant left-to-right data flow by passing the result of one expression as the first argument to the next function:

```rexx
-- Basic piping
LET result = "hello" |> UPPER()
-- Equivalent to: UPPER(string="hello")
-- Result: "HELLO"

-- Multi-stage pipeline
LET cleaned = "  hello world  " |> TRIM() |> UPPER() |> LENGTH()
-- Equivalent to: LENGTH(string=UPPER(string=TRIM(string="  hello world  ")))
-- Result: 11

-- Piping with named parameters
LET words = "a,b,c" |> SPLIT(separator=",")
-- Equivalent to: SPLIT(string="a,b,c", separator=",")
-- Result: ["a", "b", "c"]

-- Named parameters in chained pipes
LET result = "hello world" |> SUBSTR(start=7, length=5)
-- Piped value becomes first arg, named params fill rest
-- Equivalent to: SUBSTR(string="hello world", start=7, length=5)
-- Result: "world"

-- Complex pipeline with named parameters
LET processed = "  hello  " |> STRIP() |> SUBSTR(start=2, length=3)
-- Result: "ell"
```

### Complex Data Transformations

The pipe operator excels at building readable data transformation pipelines:

```rexx
-- String processing pipeline
LET text = "  HELLO WORLD  "
LET result = text |> TRIM |> LOWER |> SPLIT separator=" " |> ARRAY_LENGTH
-- Result: 2

-- Combining arithmetic and piping
LET value = 5 + 3 |> ABS |> MATH_POWER exponent=2
-- Evaluates: (5 + 3) = 8, then ABS(8) = 8, then MATH_POWER(8, 2) = 64

-- Array processing
LET data = "apple,banana,cherry"
LET count = data |> SPLIT separator="," |> ARRAY_LENGTH
-- Result: 3
```

### Operator Precedence

The pipe operator has the **lowest precedence** of all operators, meaning arithmetic expressions are evaluated first:

```rexx
LET result = 10 * 2 |> ABS
-- Evaluates as: (10 * 2) |> ABS = 20 |> ABS = 20

LET value = 5 + 3 |> UPPER
-- ❌ Error: (5 + 3) = 8, then UPPER(8) - numbers can't be uppercased

-- Use parentheses to control precedence
LET text = ("Hello" |> UPPER) || " World"
-- Result: "HELLO World"
```

### Pipe-Friendly Functions

Functions where data is the first parameter work naturally with piping:

**String Functions** (see [String Functions](04-string-functions.md)):
- `UPPER`, `LOWER`, `TRIM`, `REVERSE`, `LENGTH`
- `SLUG`, `WORD_FREQUENCY`, `SENTIMENT_ANALYSIS`

**Array Functions** (see [Array Functions](06-array-functions.md)):
- `ARRAY_LENGTH`, `ARRAY_REVERSE`, `ARRAY_UNIQUE`, `ARRAY_FLATTEN`
- `ARRAY_MIN`, `ARRAY_MAX`, `ARRAY_SUM`, `ARRAY_AVERAGE`

**Encoding Functions** (see [Web Functions](09-web-functions.md)):
- `BASE64_ENCODE`, `BASE64_DECODE`
- `URL_ENCODE`, `URL_DECODE`

**Validation Functions** (see [Validation Functions](11-validation-functions.md)):
- `IS_EMAIL`, `IS_URL`, `IS_NUMBER`, `IS_INTEGER`

### String Concatenation vs Addition

**Important:** RexxJS follows strict REXX semantics for operators:

```rexx
-- ✅ Arithmetic addition (numeric operands only)
LET sum = 100 + 11          -- Result: 111
LET total = "50" + "25"     -- Result: 75 (automatic numeric coercion)

-- ❌ String concatenation with + is NOT supported
LET text = "Hello" + " World"  -- Error: "Hello" is not a number

-- ✅ Use || for string concatenation
LET greeting = "Hello" || " World"  -- Result: "Hello World"
LET fullName = firstName || " " || lastName

-- Combining pipes and concatenation
LET result = "hello" |> UPPER || " WORLD"
-- Result: "HELLO WORLD"
```

### Best Practices

**Use pipes for readability:**
```rexx
-- ❌ Hard to read (nested function calls)
LET result = ARRAY_LENGTH array=SPLIT string=TRIM string=UPPER string=text separator=" "

-- ✅ Easy to read (left-to-right flow)
LET result = text |> UPPER |> TRIM |> SPLIT separator=" " |> ARRAY_LENGTH
```

**Multi-line pipelines for clarity:**
```rexx
-- Format complex pipelines across multiple lines
LET result = rawData
  |> TRIM
  |> LOWER
  |> SPLIT separator=","
  |> ARRAY_UNIQUE
  |> ARRAY_LENGTH
```

**Know when NOT to use pipes:**
```rexx
-- Functions where data is NOT the first parameter
LET position = POS needle="world" haystack="hello world"  -- Can't pipe efficiently

-- Simple single function calls
LET upper = UPPER string="hello"  -- No need for pipe here
```

## String Interpolation

### Variable Interpolation in Strings
Use `{variable}` syntax for dynamic string content:

```rexx
LET mealName = 'Special Dinner'
LET guest = 'VIP'

-- Basic interpolation
prepareDish name="Today's {mealName}" servings=4

-- Multiple variables
LET greeting = "Welcome {firstName} {lastName}"

-- Complex variable paths
LET status = "Current stock: {stock.quantity} units"
```

### Configurable Interpolation Patterns

RexxJS supports multiple interpolation patterns that can be switched globally:

```rexx
-- Default RexxJS pattern: {variable} (default)
LET name = "Alice"
SAY "Hello {name}"

-- Switch to Handlebars pattern: {{variable}}
INTERPOLATION HANDLEBARS
SAY "Hello {{name}}"

-- Switch to Shell pattern: ${variable}
INTERPOLATION SHELL  
SAY "Hello ${name}"

-- Switch to Batch pattern: %variable%
INTERPOLATION BATCH
SAY "Hello %name%"

-- Reset to default
INTERPOLATION DEFAULT
```

**Available Patterns:**
- `DEFAULT` or `REXX`: `{variable}` (default)
- `HANDLEBARS`: `{{variable}}`
- `SHELL`: `${variable}`
- `BATCH`: `%variable%`
- `CUSTOM`: `$$variable$$`
- `BRACKETS`: `[variable]`

### Creating Custom Interpolation Patterns

#### INTERPOLATION PATTERN Statement

Create custom interpolation patterns with the `INTERPOLATION PATTERN` statement:

```rexx
-- Syntax: INTERPOLATION PATTERN name=NAME start="delimiter" end="delimiter"
INTERPOLATION PATTERN name=ANGLES start="<<" end=">>"
INTERPOLATION PATTERN name=PIPES start="|" end="|"
INTERPOLATION PATTERN name=RUBY start="#{" end="}"
```

#### Using Custom Patterns

After defining a custom pattern, switch to it using the pattern name:

```rexx
-- Define custom pattern
INTERPOLATION PATTERN name=ANGLES start="<<" end=">>"

-- Switch to the custom pattern
INTERPOLATION ANGLES
LET user = "Bob"
SAY "User: <<user>>"  -- Outputs: User: Bob

-- Define and use another pattern
INTERPOLATION PATTERN name=PIPES start="|" end="|"
INTERPOLATION PIPES
SAY "Status: |user| is active"  -- Outputs: Status: Bob is active

-- Switch back to default
INTERPOLATION DEFAULT
SAY "Back to {user}"  -- Outputs: Back to Bob
```

#### Custom Pattern Examples

```rexx
-- Ruby-style interpolation
INTERPOLATION PATTERN name=RUBY start="#{" end="}"
INTERPOLATION RUBY
LET count = 5
SAY "Found #{count} items"  -- Outputs: Found 5 items

-- Angle bracket style
INTERPOLATION PATTERN name=XML start="<%" end="%>"
INTERPOLATION XML
LET title = "Welcome"
SAY "<%title%> to our site"  -- Outputs: Welcome to our site

-- Double pipe style
INTERPOLATION PATTERN name=WIKI start="||" end="||"
INTERPOLATION WIKI
LET page = "HomePage"
SAY "Visit ||page|| for more info"  -- Outputs: Visit HomePage for more info
```

#### Pattern Validation

Custom patterns must:
- Have unique names (case-insensitive)
- Use non-empty start and end delimiters
- Not conflict with existing pattern names

```rexx
-- ✅ Valid custom patterns
INTERPOLATION PATTERN name=CUSTOM1 start="@@" end="@@"
INTERPOLATION PATTERN name=SPECIAL start="<?" end="?>"

-- ❌ Invalid patterns (will cause errors)
INTERPOLATION PATTERN name=DEFAULT start="[" end="]"  -- Reserved name
INTERPOLATION PATTERN name=EMPTY start="" end="]"     -- Empty delimiter
```

### Interpolation Features
- **Complex Variable Paths**: `{object.property}` notation
- **Missing Variable Handling**: Placeholder preserved if variable not found
- **Control Flow Integration**: Works within IF/DO/SELECT statements
- **Numeric Variable Support**: Automatic string conversion
- **Pattern Switching**: Change delimiters globally with `INTERPOLATION` statement
- **Custom Delimiters**: Create your own patterns with `INTERPOLATION PATTERN`

## HEREDOC Strings

### Multi-line String Literals
Use HEREDOC syntax for multi-line content with custom delimiters:

```rexx
LET content = <<DELIMITER
Multi-line content
can span several lines
and preserve formatting
DELIMITER
```

### JSON Auto-parsing
When the delimiter contains "JSON" (case-insensitive), the content is automatically parsed as JSON:

```rexx
-- ✅ Auto-parsed as JavaScript object
LET user = <<JSON
{
  "name": "Alice Smith",
  "age": 30,
  "settings": {
    "theme": "dark",
    "notifications": true
  }
}
JSON

-- Now you can access properties directly
LET userName = user.name           -- "Alice Smith"
LET userTheme = user.settings.theme   -- "dark"
```

**JSON Auto-parsing Rules:**
- **Delimiter matching**: Contains "json", "JSON", "Json", "MYJSON", etc.
- **Content validation**: Must be valid JSON starting with `{` or `[`
- **Fallback behavior**: Invalid JSON remains as string
- **Array support**: JSON arrays are parsed as JavaScript arrays

**Examples:**
```rexx
-- ✅ These delimiters trigger JSON parsing
LET config = <<JSON
{"theme": "dark"}
JSON

LET data = <<json  
["item1", "item2", "item3"]
json

LET settings = <<CONFIGJSON
{"enabled": true, "timeout": 5000}
CONFIGJSON

-- ❌ These remain as strings  
LET text = <<DATA
{"not": "parsed"}
DATA

LET content = <<CONFIG
{"stays": "as string"}
CONFIG
```

### Use Cases
- **Configuration files**: Store complex settings as JSON
- **API responses**: Handle JSON data from web services
- **Test data**: Define structured test fixtures
- **Templates**: Multi-line content with dynamic variables

## Escape Sequences

### JavaScript-Style Escape Sequences

RexxJS supports JavaScript-style escape sequences in all quoted strings. This is a **modern extension that breaks from classic REXX**, which doesn't support these escape sequences.

**Supported Escape Sequences:**

| Sequence | Character | Unicode | Example | Output |
|----------|-----------|---------|---------|--------|
| `\n` | Newline | U+000A | `"line1\nline2"` | line1<br/>line2 |
| `\t` | Tab | U+0009 | `"col1\tcol2"` | col1    col2 |
| `\r` | Carriage Return | U+000D | `"before\rafter"` | after |
| `\b` | Backspace | U+0008 | `"text\bspace"` | texspace |
| `\f` | Form Feed | U+000C | `"page1\fpage2"` | page1\fpage2 |
| `\v` | Vertical Tab | U+000B | `"line1\vline2"` | line1\vline2 |
| `\0` | Null Character | U+0000 | `"before\0after"` | before(null)after |
| `\'` | Single Quote | U+0027 | `"It\'s"` | It's |
| `\"` | Double Quote | U+0022 | `"Say \"hi\""` | Say "hi" |
| `\\` | Backslash | U+005C | `"C:\\path"` | C:\path |
| `\uXXXX` | Unicode (4-digit) | Any | `"\u0041"` | A |
| `\uXXXXXXXX` | Unicode (8-digit) | Any | `"\u0001F600"` | 😀 |

### Usage Examples

**Basic Escape Sequences:**
```rexx
-- Newlines for multi-line strings
LET message = "Line 1\nLine 2\nLine 3"
SAY message
-- Output:
-- Line 1
-- Line 2
-- Line 3

-- Tabs for formatting
LET table = "Name\tAge\nAlice\t30\nBob\t25"
SAY table
-- Output:
-- Name    Age
-- Alice   30
-- Bob     25

-- Escaped quotes
LET quoted = "She said \"Hello\""
SAY quoted
-- Output: She said "Hello"
```

**Unicode Escapes:**
```rexx
-- 4-digit Unicode (Basic Multilingual Plane)
LET greek = "Alpha: \u03B1, Beta: \u03B2"
SAY greek
-- Output: Alpha: α, Beta: β

-- 8-digit Unicode (extended planes, for emoji)
LET emoji = "Smile: \u0001F600, Love: \u0001F60D"
SAY emoji
-- Output: Smile: 😀, Love: 😍

-- Mathematical symbols
LET math = "Plus-Minus: \u00B1, Multiply: \u00D7, Divide: \u00F7"
SAY math
-- Output: Plus-Minus: ±, Multiply: ×, Divide: ÷
```

**Escape Sequences in Different Contexts:**
```rexx
-- In SAY statements
SAY "Direct SAY with Unicode: \u0041\u0042\u0043"
-- Output: Direct SAY with Unicode: ABC

-- In variable assignments
LET text = "Newline test\n\u0041\u0042"
SAY text
-- Output: Newline test
-- AB

-- In function parameters
LET length = LENGTH("hello\nworld")
-- Result: 11 (5 + 1 newline + 5)

-- In concatenation
LET result = "Part1\t" || "Part2"
SAY result
-- Output: Part1   Part2

-- In conditionals
IF "text\nhere" = "text\nhere" THEN
  SAY "Match"
ENDIF
-- Output: Match
```

### Important Notes

- **Escape sequences work everywhere**: assignments, SAY statements, function parameters, concatenation operators
- **Both single and double quotes supported**: `"string with escapes"` and `'string with escapes'`
- **Invalid Unicode escapes are preserved**: If hex digits are invalid (e.g., `\uGGGG`), the backslash-u is kept as-is
- **Incomplete Unicode escapes**: If there aren't enough hex digits, the escape remains literal (e.g., `\u12` stays as `\u12`)
- **Modern extension**: This feature is not part of classic REXX and is a RexxJS-specific enhancement for modern string handling

## Comments

### Comment Syntax
Lines starting with `--` or `//` are comments:

```rexx
-- This is a comment (traditional REXX style)
// This is also a comment (C/JavaScript style)
LET value = 10        -- End of line comment
LET another = 20      // Also an end of line comment

-- Multi-line comments with --
-- can span multiple lines
-- for detailed explanations

// Multi-line comments with //
// are also supported
// using the same approach
```

Both comment styles are supported for developer convenience:
- **`--`**: Traditional REXX comment syntax
- **`//`**: C/C++/JavaScript style comments for familiarity

## Application Addressing

### ADDRESS Statement
Direct function calls to specific applications:

```rexx
-- Switch target application
ADDRESS calculator
add x=10 y=32

-- Switch to another service  
ADDRESS kitchen
prepareDish name="Pasta" servings=4

-- Switch back to calculator
ADDRESS calculator
display message="Calculation complete"
```

**See also:** [Application Addressing](16-application-addressing.md) for detailed cross-application communication patterns.