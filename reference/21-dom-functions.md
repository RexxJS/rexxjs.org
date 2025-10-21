---
layout: default
title: DOM Functions
---

# DOM Functions

Modern DOM manipulation and query capabilities for web automation, gray-box testing, UI interaction, and browser-based application development in Autonomous Web Mode.

## Autonomous Web Mode Only

DOM functions are only available in Autonomous Web Mode (direct browser execution). They require the `document` object and will not be available in Command-line mode or Controlled Web Mode worker contexts.

## ELEMENT() - Unified DOM Interface

`ELEMENT()` is the primary function for all DOM operations. It provides a clean, chainable interface for querying elements, manipulating properties, extracting data, and interacting with the DOM.

### Basic Syntax

```rexx
-- Query single element
LET element = ELEMENT(selector="CSS_SELECTOR", operation="OPERATION")

-- Query all matching elements
LET elements = ELEMENT(selector="CSS_SELECTOR", operation="all")

-- Chain operations with pipe operator
LET value = ELEMENT("input.email", "value")
LET text = ELEMENT("#title", "text")
```

### Query Operations

#### Getting Single Elements

```rexx
-- Get element reference
LET button = ELEMENT("#submit-btn", "get")
LET input = ELEMENT("input.email", "get")

-- Returns element reference for use in further operations
LET email = button |> ELEMENT("value")  -- Nested query
```

#### Getting All Elements

```rexx
-- Get all matching elements (returns REXX stem array)
LET buttons = ELEMENT("button", "all")
LET paragraphs = ELEMENT("p.content", "all")

-- Use with pipeline functions
LET emails = ELEMENT("input.email", "all")
  |> GET_VALUES()        -- Extract values from form inputs
  |> JOIN(",")           -- Join with separator
```

#### Count Elements

```rexx
-- Count matching elements
LET button_count = ELEMENT("button", "count")
LET form_count = ELEMENT("form", "count")
SAY "Found " button_count " buttons"
```

#### Check Existence

```rexx
-- Check if element exists
LET has_modal = ELEMENT("#modal", "exists")
IF has_modal THEN
    SAY "Modal is present"
ENDIF
```

### Extracting Data

#### Get Text Content

```rexx
-- Get text content
LET title = ELEMENT("#page-title", "text")
LET message = ELEMENT(".error-message", "text")

-- From all elements (returns REXX stem array)
LET all_text = ELEMENT("p", "all")
  |> GET_TEXT()
  |> JOIN(" | ")
```

#### Get Element Values

```rexx
-- Get input value
LET username = ELEMENT("#username", "value")
LET selected_country = ELEMENT("#country-select", "value")

-- From all form inputs (returns REXX stem array)
LET form_values = ELEMENT("input.required", "all")
  |> GET_VALUES()
```

#### Get Attributes

```rexx
-- Get specific attribute
LET data_id = ELEMENT(".product", "attr", attribute="data-product-id")
LET aria_label = ELEMENT("button", "attr", attribute="aria-label")

-- Extract attributes from multiple elements (returns REXX stem array)
LET all_ids = ELEMENT("div[data-id]", "all")
  |> GET_ATTRS("data-id")
  |> JOIN(",")
```

#### Get CSS Classes

```rexx
-- Get element's CSS classes
LET classes = ELEMENT("#container", "class")
-- Returns: "main active highlighted"

-- Check for specific class
LET is_active = ELEMENT("#item", "has_class", class="active")
```

### Filtering and Transformation

#### Filter by Attribute

```rexx
-- Filter elements by attribute value
LET required_inputs = ELEMENT("input", "all")
  |> FILTER_BY_ATTR("required", "true")
  |> GET_VALUES()
```

#### Filter by CSS Class

```rexx
-- Filter elements by CSS class
LET active_items = ELEMENT("li", "all")
  |> FILTER_BY_CLASS("active")
  |> GET_TEXT()
```

### Mutation Operations (Chainable)

All mutation operations return the element(s) for chainable operations:

#### Set Text Content

```rexx
-- Set text content
ELEMENT("#title", "text", value="New Title")

-- Chainable
LET element = ELEMENT("#status", "text", value="Ready")
  |> ELEMENT("class", value="success")
```

#### Set Input Value

```rexx
-- Set form input value
ELEMENT("#email", "value", value="user@example.com")

-- Chain with other operations
ELEMENT("#search", "value", value="query")
  |> ELEMENT("focus")
```

#### Set Attributes

```rexx
-- Set attribute
ELEMENT(".icon", "attr", attribute="title", value="Click to expand")

-- Chainable
ELEMENT("button", "attr", attribute="disabled", value="true")
  |> ELEMENT("class", value="inactive")
```

#### CSS Class Manipulation

```rexx
-- Add class
ELEMENT("#notification", "class", operation="add", class="visible")

-- Remove class
ELEMENT("#modal", "class", operation="remove", class="hidden")

-- Toggle class
ELEMENT(".menu-item", "class", operation="toggle", class="active")

-- Chainable
ELEMENT("#form", "class", operation="add", class="valid")
  |> ELEMENT("class", operation="remove", class="error")
```

#### Inline Style Manipulation

```rexx
-- Set inline styles
ELEMENT("#progress", "style", property="width", value="75%")
ELEMENT("#banner", "style", property="backgroundColor", value="red")

-- Chainable
ELEMENT(".overlay", "style", property="opacity", value="0.5")
  |> ELEMENT("style", property="display", value="block")
```

#### DOM Tree Manipulation

```rexx
-- Append child elements
ELEMENT("#container", "append", html="<div class='item'>New item</div>")

-- Prepend child elements
ELEMENT("#list", "prepend", html="<li>First item</li>")

-- Remove element
ELEMENT(".temporary", "remove")

-- Chainable - build content incrementally
ELEMENT("#container", "append", html="<h2>Title</h2>")
  |> ELEMENT("append", html="<p>Description</p>")
```

### User Interaction

#### Click

```rexx
-- Click element
ELEMENT("#submit-btn", "click")

-- Check if visible, then click
IF ELEMENT("#save-btn", "visible") THEN
    ELEMENT("#save-btn", "click")
ENDIF

-- Chainable
ELEMENT("button.secondary", "click")
  |> ELEMENT("class", operation="add", class="processing")
```

#### Type Text

```rexx
-- Type text into input
ELEMENT("#username", "type", text="john_doe")
ELEMENT("#email", "type", text="user@example.com")

-- Clear and type
ELEMENT("#search", "type", text="")      -- Clear
ELEMENT("#search", "type", text="query")

-- Chainable
ELEMENT("input.search", "type", text="product")
  |> ELEMENT("focus")
```

#### Focus Element

```rexx
-- Focus element
ELEMENT("#password", "focus")

-- Chainable
ELEMENT("#search-box", "type", text="")
  |> ELEMENT("focus")
```

### Visibility and State

#### Check Visibility

```rexx
-- Check if element is visible
IF ELEMENT("#popup", "visible") THEN
    SAY "Popup is visible"
ENDIF
```

#### Check Stale State

```rexx
-- Check if element is stale (removed from DOM)
IF ELEMENT("#item", "stale") THEN
    SAY "Element is no longer in DOM"
ENDIF
```

## Pipeline Operations

RexxJS provides specialized functions for data extraction from DOM elements in pipelines:

### FILTER_BY_ATTR() - Filter by Attribute

```rexx
-- Get all elements matching attribute
LET required_fields = ELEMENT("input", "all")
  |> FILTER_BY_ATTR("required", "true")
  |> GET_VALUES()
```

### FILTER_BY_CLASS() - Filter by CSS Class

```rexx
-- Get all elements with specific class
LET visible_items = ELEMENT("li", "all")
  |> FILTER_BY_CLASS("visible")
  |> GET_TEXT()
```

### GET_VALUES() - Extract Form Values

```rexx
-- Extract values from multiple elements
LET form_data = ELEMENT("input", "all")
  |> GET_VALUES()
-- Returns REXX stem array: {0: count, 1: value1, 2: value2, ...}
```

### GET_TEXT() - Extract Text Content

```rexx
-- Extract text from multiple elements
LET messages = ELEMENT(".message", "all")
  |> GET_TEXT()
  |> JOIN(" | ")
```

### GET_ATTRS() - Extract Attributes

```rexx
-- Extract specific attribute from elements
LET product_ids = ELEMENT("div.product", "all")
  |> GET_ATTRS("data-id")
  |> JOIN(",")
```

## Real-World Examples

### Form Extraction and Validation

```rexx
-- Extract all form data
LET form_values = ELEMENT("input", "all")
  |> GET_VALUES()

-- Count required fields
LET required_fields = ELEMENT("input", "all")
  |> FILTER_BY_ATTR("required", "true")
LET required_count = required_fields.0

SAY "Form has " required_count " required fields"
```

### Data Table Processing

```rexx
-- Extract data from table rows
LET row_ids = ELEMENT("tr.data-row", "all")
  |> FILTER_BY_CLASS("visible")
  |> GET_ATTRS("data-id")
  |> JOIN("|")

-- Extract cell values
LET cell_values = ELEMENT("td.amount", "all")
  |> GET_TEXT()
  |> JOIN(",")
```

### UI State Management

```rexx
-- Build element state from UI
LET active_tabs = ELEMENT("li.tab", "all")
  |> FILTER_BY_CLASS("active")
  |> GET_ATTRS("data-tab-id")

-- Update UI state
ELEMENT("#status", "text", value="Ready")
  |> ELEMENT("class", operation="remove", class="loading")
  |> ELEMENT("class", operation="add", class="complete")
```

### Sequential Interactions

```rexx
-- Clear form, fill fields, submit
ELEMENT("#search", "type", text="")
  |> ELEMENT("#search", "type", text="product")
  |> ELEMENT("#search", "focus")

-- Wait for results would go here...

-- Click result and extract data
ELEMENT("a.result:first-child", "click")
ELEMENT("h1.title", "text")  -- Get page title after navigation
```

## Chainable Operations

All ELEMENT() mutations return element references, enabling seamless operation chaining:

```rexx
-- Build complex interaction sequences
ELEMENT("#form", "class", operation="add", class="processing")
  |> ELEMENT("#submit", "attr", attribute="disabled", value="true")
  |> ELEMENT("#status", "text", value="Submitting...")
  |> ELEMENT("#spinner", "style", property="display", value="inline")
```

## Stem Array Format

Pipeline functions return REXX stem arrays with this format:

```
Array.0 = count of elements
Array.1 = first element's value
Array.2 = second element's value
...
Array.N = Nth element's value
```

This format integrates seamlessly with REXX iteration:

```rexx
LET values = ELEMENT("input", "all") |> GET_VALUES()
DO i = 1 TO values.0
  SAY "Value " i ": " values.(i)
END
```

## Error Handling

```rexx
-- Check existence before accessing
IF ELEMENT("#required-field", "exists") THEN
    LET value = ELEMENT("#required-field", "value")
ELSE
    SAY "Field not found"
ENDIF

-- Check visibility before interaction
IF ELEMENT("#button", "visible") THEN
    ELEMENT("#button", "click")
ENDIF
```

## Performance Notes

- Element queries use native DOM selectors (CSS selectors)
- Pipeline operations (FILTER_BY_ATTR, GET_VALUES) operate on element arrays
- Chainable operations enable efficient DOM manipulation without repeated queries
- Stem arrays avoid unnecessary data conversion between REXX and JavaScript

## Related Functions

- FILTER_BY_ATTR() - Filter elements by attribute
- FILTER_BY_CLASS() - Filter elements by CSS class
- GET_VALUES() - Extract values from form elements
- GET_TEXT() - Extract text content
- GET_ATTRS() - Extract attribute values
- JOIN() - Combine values into string
