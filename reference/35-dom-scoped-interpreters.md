# DOM Scoped Interpreters

## Overview

DOM Scoped Interpreters allow multiple independent REXX scripts to run on the same web page with completely isolated function registrations. Instead of all functions being registered to the global `window` object, each interpreter can register functions to a specific DOM element's scope, preventing namespace collisions and global pollution.

## Scope Detection

When a `RexxInterpreter` is created in a web environment, it automatically searches the DOM for an ancestor element with the `RexxScript` CSS class:

```javascript
const interpreter = new RexxInterpreter(null, {
    output: console.log
});
// Automatically detects RexxScript class in parent elements
// Falls back to window if not found
```

**Detection Strategy:**
1. If running in an iframe, traverse up from `window.frameElement`
2. Check `document.documentElement` (HTML root)
3. If no `RexxScript` class found, default to `window` scope (backward compatible)

## Explicit Scope Configuration

Specify a scope element explicitly:

```javascript
const container = document.getElementById('script1-container');
const interpreter = new RexxInterpreter(null, {
    scopeElement: container,
    output: console.log
});
```

## Scope Registry System

Each interpreter maintains a `scopeRegistry` object with methods for managing function registration:

| Method | Purpose |
|--------|---------|
| `registerFunction(name, func)` | Register function to scope |
| `getFunction(name)` | Retrieve function from scope |
| `hasFunction(name)` | Check if function exists |
| `registerMetadata(name, meta)` | Register function metadata |
| `getMetadata(name)` | Retrieve function metadata |

## Storage Locations

**Isolated Scope (with RexxScript class):**
- Functions: `domElement.__rexxFunctions[name]`
- Metadata: `domElement.__rexxMetadata[name]`

**Global Scope (without RexxScript class):**
- Functions: `window[name]` (legacy behavior)
- Metadata: `window[name]` (legacy behavior)

## Usage Examples

### Single Script with Isolated Scope

```html
<div class="RexxScript" id="script1">
    <textarea id="code1">SAY "Hello from isolated scope"</textarea>
    <button onclick="runScript1()">Run</button>
</div>

<script>
    const container = document.getElementById('script1');
    const interpreter = new RexxInterpreter(null, {
        scopeElement: container,
        output: console.log
    });

    async function runScript1() {
        const code = document.getElementById('code1').value;
        const commands = parse(code);
        await interpreter.run(commands);
    }
</script>
```

### Multiple Independent Scripts

```html
<!-- Script 1: Isolated -->
<div class="RexxScript" id="script1">
    <!-- Functions in: element.__rexxFunctions -->
</div>

<!-- Script 2: Isolated -->
<div class="RexxScript" id="script2">
    <!-- Functions in: element.__rexxFunctions -->
</div>

<!-- Script 3: Global -->
<div id="script3">
    <!-- Functions in: window -->
</div>

<script>
    const interp1 = new RexxInterpreter(null, {
        scopeElement: document.getElementById('script1')
    });

    const interp2 = new RexxInterpreter(null, {
        scopeElement: document.getElementById('script2')
    });

    const interp3 = new RexxInterpreter(); // Auto-detects or uses window
</script>
```

### Automatic Scope Detection

```javascript
// Auto-detects RexxScript class in ancestors
const interpreter = new RexxInterpreter();
console.log('Scope element:', interpreter.scopeElement?.id || 'window');
```

## Scope Isolation Benefits

| Feature | Isolated Scope | Global Scope |
|---------|---|---|
| Function Namespace | ✓ Isolated per element | Shared globally |
| Multiple Scripts | ✓ No conflicts | Can overwrite |
| Global Pollution | ✓ None | Namespace pollution |
| Runtime Overhead | Minimal | None |
| Backward Compatibility | ✓ Opt-in | ✓ Default |

## Verification with INTERPRET_JS

Verify scope isolation using `INTERPRET_JS`:

```rexx
INTERPRET_JS "
    // Check if functions are in window or element scope
    window.scopeCheck = {
        hasGlobalFilter: typeof window.FILTER_BY_ATTR !== 'undefined',
        varValue: x.value
    }
"
```

## Use Cases

**Multi-tenant Applications**
- Each tenant's scripts run in isolation
- No cross-tenant namespace pollution

**Plugin Systems**
- Multiple plugins can safely coexist
- Each plugin has its own function namespace

**Dynamic Scripting**
- User-uploaded scripts don't interfere with each other
- Safe execution environment

**Testing Frameworks**
- Each test runs in isolated scope
- No test state leakage

**Dashboard Applications**
- Multiple independent widgets with embedded REXX
- Widgets don't conflict

**Collaborative Tools**
- Different users' scripts work in parallel
- Zero shared state

## Architecture Details

### Constructor Changes

```javascript
constructor(addressSender, optionsOrHandler = null, explicitOutputHandler = null) {
    // ... existing code ...

    // New: Scope registration system
    this.scopeElement = this.findScopeElement(optionsOrHandler?.scopeElement);
    this.scopeRegistry = this.setupScopeRegistry();
}
```

### Scope Detection Method

```javascript
findScopeElement(explicitScopeElement) {
    // If explicit scope provided, use it
    if (explicitScopeElement) return explicitScopeElement;

    // Only in web environment
    if (typeof window === 'undefined') return null;

    // Search for RexxScript class in DOM
    if (window.frameElement) {
        let element = window.frameElement;
        while (element) {
            if (element.classList?.contains('RexxScript')) {
                return element;
            }
            element = element.parentElement;
        }
    }

    // Check document root
    if (document.documentElement?.classList?.contains('RexxScript')) {
        return document.documentElement;
    }

    return null; // Default to window
}
```

### Scope Registry Setup

```javascript
setupScopeRegistry() {
    const self = this;

    return {
        registerFunction(name, func) {
            if (self.scopeElement) {
                if (!self.scopeElement.__rexxFunctions) {
                    self.scopeElement.__rexxFunctions = {};
                }
                self.scopeElement.__rexxFunctions[name] = func;
            } else {
                window[name] = func;
            }
        },

        getFunction(name) {
            if (self.scopeElement?..__rexxFunctions?.[name]) {
                return self.scopeElement.__rexxFunctions[name];
            }
            return window[name] || null;
        }

        // ... other methods ...
    };
}
```

## Integration with Web REPL

The demo page at `/core/src/repl/dom-scoped-rexx.html` showcases this feature with:

- 3 independent script editors (2 with isolated scopes, 1 with global)
- Visual scope indicators (green for isolated, blue for global)
- Real-time script execution
- Status tracking (Ready/Running/Complete/Error)
- Comprehensive documentation and examples

## Browser Support

Works in all modern browsers supporting:
- ES6+ JavaScript
- DOM classList API
- Object property storage on elements
- Array/Map APIs

Tested on Chromium via Playwright.

## Backward Compatibility

✓ **Fully backward compatible** - This is an opt-in feature:
- Existing code without `RexxScript` class continues to work unchanged
- Functions still register to `window` by default
- New code can opt-in by adding `RexxScript` class and passing `scopeElement`

## Future Enhancement: Variable Pool Isolation

The current implementation isolates **function registration**. Future phases will also isolate **variable pools**:

```javascript
// Future: Each interpreter has separate variables Map
const interp1 = new RexxInterpreter(null, { scopeElement: container1 });
const interp2 = new RexxInterpreter(null, { scopeElement: container2 });

// interp1.variables and interp2.variables are completely separate
// No variable leakage between scripts
// Perfect isolation for multi-tenant scenarios
```

This will enable:
- Separate variable namespaces per interpreter
- Zero shared state between scripts
- Full data isolation for multi-tenant applications

## Testing

Automated tests verify:
- Scope detection with `RexxScript` class
- Function registration to correct scope
- Isolation between multiple interpreters
- Sequential execution without state leakage
- INTERPRET_JS verification of scope state

Test file: `/core/tests/web/dom-scope-registration.spec.js` (11 comprehensive tests)

## Related Documentation

- [DOM Functions Reference](21-dom-functions.md) - ELEMENT() and DOM operations
- [Web Functions](09-web-functions.md) - Browser-based functionality
- [Control Bus](22-control-bus.md) - Cross-iframe communication
- [Require System](23-require-system.md) - Module loading across scopes
