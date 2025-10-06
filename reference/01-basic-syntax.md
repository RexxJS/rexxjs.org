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

## Function Calls

### Basic Function Calls
```rexx
functionName param1=value1 param2=value2
```

**Examples:**
```rexx
-- String functions
LET upper = UPPER string="hello world"
LET length = LENGTH string=upper

-- Math functions  
LET maximum = MAX x=10 y=25 z=15
LET absolute = ABS value=-42

-- Utility functions
LET today = DATE
LET timestamp = NOW
```

### Parameter Types
Functions support multiple parameter types:

```rexx
-- Strings (quoted)
DOM_TYPE selector="#input" text="Hello World"

-- Numbers (unquoted)
LET result = MATH_POWER base=2 exponent=10

-- Booleans
LET valid = IS_EMAIL email="user@example.com"

-- Expressions
LET calculated = MAX x=base*2 y=multiplier+5 z=10
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
LET result = "hello" |> UPPER
-- Equivalent to: UPPER string="hello"
-- Result: "HELLO"

-- Multi-stage pipeline
LET cleaned = "  hello world  " |> TRIM |> UPPER |> LENGTH
-- Equivalent to: LENGTH string=UPPER(string=TRIM(string="  hello world  "))
-- Result: 11

-- Piping with function arguments
LET words = "a,b,c" |> SPLIT separator=","
-- Equivalent to: SPLIT string="a,b,c" separator=","
-- Result: ["a", "b", "c"]
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