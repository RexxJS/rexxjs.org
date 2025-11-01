# ADDRESS: The Escape Hatch to Other Domains

The `ADDRESS` statement is REXX's fundamental mechanism for breaking out of the REXX language itself and delegating work to specialized external systems—what we call an **"escape hatch to another domain."**

This concept is central to REXX's philosophy: **REXX orchestrates; other systems specialize.**

## What is an Escape Hatch?

Think of REXX as a control center and ADDRESS as an exit tunnel:

```
┌─────────────────────────────────────────┐
│        REXX Script (Orchestration)      │
│                                         │
│  LET data = READ_FILE("input.txt")    │
│  ADDRESS sql                           │◄──────┐
│  "SELECT * FROM table WHERE id IN..." │       │
│  LET result = /* SQL result */        │       │ Escape Hatch:
│                                         │       │ Leave REXX,
│  ADDRESS docker                         │◄──────┤ Enter SQL Domain
│  "run image=myapp command=process"     │       │ (run command)
│  ADDRESS                               │       │ Return to REXX
│  SAY "Done: " || result                │       │ (back to host)
└─────────────────────────────────────────┘       │
                                                   │
                    ┌──────────────────────────────┘
                    │
                    ▼
        ┌──────────────────────────────┐
        │   SQL Database Engine        │
        │   (Specialized Domain)       │
        │   - Understands SQL syntax   │
        │   - Manages data/queries     │
        │   - Returns result sets      │
        └──────────────────────────────┘
```

Without ADDRESS, you'd need:
- A full SQL parser in REXX (duplication)
- Re-implementation of Docker CLI (reinvention)
- Bindings to 50 different APIs (bloat)

With ADDRESS, you **escape** to the specialized system and **return** with results.

## The Core Insight: Delegation, Not Reimplementation

Classic languages force you to implement everything—or nothing:
- **C**: Write everything in C
- **Python**: Use Python libraries or external commands (awkward)
- **JavaScript**: Bound to browser/Node.js environment

**REXX with ADDRESS** inverts this:
- Write orchestration logic in REXX
- Escape to specialized domains for domain-specific work
- Bring results back to REXX for coordination

### Example: A Simple Data Pipeline
```rexx
-- Orchestration layer (REXX)
LET data = FILE_READ "raw_data.csv"

-- Escape to data processing domain
ADDRESS python
EXECUTE script=`
import pandas as pd
df = pd.read_csv('data.csv')
print(df.describe())
`

-- Back in REXX to coordinate results
LET summary = /* Python output */
ADDRESS email
sendReport to="analytics@company.com" body=summary

-- Continue coordinating
SAY "Pipeline complete"
```

No need to:
- Rewrite pandas in REXX
- Rewrite SMTP in REXX
- Rewrite file I/O for Python

Each domain handles its specialty; **REXX coordinates the flow.**

## Historical Perspective: From Amiga to Cloud

### The ARexx Era (1985–2010s)

ADDRESS originated in **ARexx** (Amiga Rexx), developed by William S. Hawes. It solved a real problem in the Amiga ecosystem: **how to glue together diverse applications.**

Amiga applications exposed **scripting ports**—essentially addressable interfaces. ARexx scripts could:

```rexx
-- DPaint: Graphics Domain
ADDRESS DPAINT
FILL X=100 Y=50 COLOR=3
BRUSH "mybrush.brush"
DRAW LINE FROM 0 0 TO 320 200

-- Back in ARexx
SAY "Graphics command sent"

-- Directory Opus: File Management Domain
ADDRESS DOPUS
COPY SOURCE="Projects/*" DEST="Archive/"
MAKEDIR "Archive/2024"

-- PageStream: Desktop Publishing Domain
ADDRESS PAGESTREAM
IMPORT "document.txt"
FONT "Helvetica" SIZE=12
PARAGRAPH ALIGN=CENTER
```

**Key insight**: The same REXX syntax—`ADDRESS target` + `command`—worked across completely different applications. The ADDRESS handler was the "escape hatch" specific to each domain.

### Why This Was Powerful

1. **Unified Scripting**: One language (REXX) could orchestrate multiple applications
2. **Loose Coupling**: Applications didn't need to know about each other
3. **Extensibility**: New applications could expose ADDRESS handlers; scripts automatically worked with them
4. **User Empowerment**: Non-programmers could script with simple, readable syntax

### Modern Parallels

Today's ADDRESS handlers follow the same pattern:

```rexx
-- Cloud Services Domain (Google Cloud Platform)
ADDRESS gcp
"SHEET 1BxiMVs0... SELECT * FROM 'Orders' WHERE date = TODAY()"
"BIGQUERY INSERT INTO analytics.daily_orders SELECT * FROM SHEETS_RESULT"
"FIRESTORE SET /metrics/today {\"orders\": 342, \"revenue\": 125000}"
"PUBSUB PUBLISH daily-metrics MESSAGE 'Dashboard updated'"

-- Containerization Domain (Docker)
ADDRESS docker
"create image=node:18 name=app-container"
"exec container=app-container command='npm install'"

-- AI/LLM Domain (Anthropic Claude)
ADDRESS claude
"messages role=user text='Analyze this dataset'"
LET analysis = /* Claude's response */

-- Database Domain (SQLite)
ADDRESS sqlite
"CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT)"
"INSERT INTO users (name) VALUES ('Alice')"
```

The pattern is **identical** to ARexx:
- Switch to a domain via ADDRESS
- Send domain-specific commands
- Return results to REXX
- Coordinate next steps

## ADDRESS as a Protocol Bridge

ADDRESS is fundamentally a **protocol bridge**: it translates between REXX's imperative syntax and a domain's native language/API.

```
REXX Code                ADDRESS Handler          Target System
──────────────────       ───────────────────       ─────────────

ADDRESS docker          ┌─ Parse command
"run image=node"    ───►│  ┌─ Translate to Docker CLI Docker Engine
                         │  │ "docker run image=node"    │
                         │  └─ Execute                    │
                         └─ Return result            ◄────┘

ADDRESS sql             ┌─ Parse command
"SELECT * FROM t"   ───►│  ┌─ Build SQL AST          SQL Engine
                         │  │ "SELECT * FROM t"          │
                         │  └─ Execute                    │
                         └─ Return result set        ◄────┘
```

The ADDRESS handler is the **translator** that:
1. **Parses** the REXX command or function call
2. **Translates** to the target system's native format
3. **Executes** in the target system
4. **Returns** results to REXX

## Four Types of Escape Hatches

### 1. Command-Based Escape
The handler receives raw commands and routes them to an interpreter:

```rexx
ADDRESS shell
"ls -la /home"               -- Sent as-is to shell interpreter
"docker ps | grep running"   -- Sent as-is to shell interpreter
```

**Use cases**: OS shell, SQL engines, code interpreters

### 2. Function-Based Escape
The handler receives function calls with named/positional parameters:

```rexx
ADDRESS calculator
clear                                    -- Escape: call clear()
press button="5"                         -- Escape: call press("5")
LET result = getDisplay                  -- Escape: call getDisplay(), return result
```

**Use cases**: Application APIs, library functions, object methods

### 3. Heredoc-Based Escape
The handler receives multiline content blocks—useful for structured input:

```rexx
ADDRESS sqlite <<SQL
CREATE TABLE users (
  id INTEGER PRIMARY KEY,
  name TEXT NOT NULL
)
SQL

ADDRESS python <<PYTHON_SCRIPT
import math
result = math.sqrt(144)
PYTHON_SCRIPT
```

**Use cases**: SQL/Python/JSON/XML generation, configuration DSLs, code generation

### 4. Pattern-Based Escape
The handler receives lines matching a regex pattern, enabling natural DSLs:

```rexx
ADDRESS expectations MATCHING("^[ \t]*\. (.*)$")
. result should equal 42
. name should match "^[A-Z]"
```

**Use cases**: Test frameworks, logging, domain-specific assertions

## The REXX Orchestration Model

ADDRESS enables a specific architectural pattern: **REXX as the orchestration layer.**

```
┌────────────────────────────────────────────────────┐
│  REXX Orchestration Script                         │
│  ────────────────────────────────────────────      │
│  • Control flow (IF/ELSE, DO loops)               │
│  • Data transformation (string/array functions)    │
│  • Decision logic (which domain to invoke next?)    │
│  • Error handling (try/catch across domains)       │
└────────┬──────────────┬──────────────┬─────────────┘
         │              │              │
         ▼              ▼              ▼
    ┌────────┐    ┌────────┐    ┌────────┐
    │ Domain │    │ Domain │    │ Domain │
    │   1    │    │   2    │    │   3    │
    │        │    │        │    │        │
    │ SQL    │    │ Docker │    │ Cloud  │
    │Database│    │ Engine │    │  API   │
    └────────┘    └────────┘    └────────┘
```

This inversion of control has benefits:

| Traditional | REXX + ADDRESS |
|---|---|
| Script language bound to one domain | Script language coordinates many domains |
| Must hardcode domain logic | Domain logic in specialized systems (updated independently) |
| Tight coupling (language knows all APIs) | Loose coupling (language knows only syntax/protocol) |
| Difficult to test (domain code intertwined with logic) | Easier to test (mock domains by providing alternative handlers) |
| Hard to evolve (touching orchestration breaks domain code) | Easier to evolve (swap handlers without changing orchestration) |

## Where Address Handlers Live

An ADDRESS handler is simply:
1. A **JavaScript function** (in the RexxJS implementation) that receives commands/method calls
2. A **registry entry** exposing it to REXX scripts
3. A **protocol** defining how it translates REXX syntax to its target domain

### Built-in Handlers (Core)
- `sqlite` – SQLite database operations
- `system` / `shell` – OS command execution
- `echo` – Echo/testing
- `expectations` – Test assertions

### Extensible Handlers (Extras)
- **Cloud**: `gcp` (Google Cloud), `aws` (Lambda), `anthropic` (Claude), `openai` (GPT)
- **Containers**: `docker`, `podman`, `systemd-nspawn`
- **VMs**: `qemu`, `virtualbox`, `proxmox`, `firecracker`, `lxd`
- **Data**: `duckdb`, `pyodide` (Python in browser)
- **APIs**: Custom handlers for any REST/RPC service

### User-Defined Handlers
You can register your own:

```javascript
// my-handler.js
function MY_HANDLER_ADDRESS_META() {
  return {
    canonical: "my.org/my-handler",
    provides: { addressTarget: "myapp" }
  };
}

async function ADDRESS_MY_HANDLER(method, params) {
  // Handle REXX calls to ADDRESS myapp
  return { success: true, result: /* ... */ };
}

// Then in REXX:
// ADDRESS myapp
// doSomething arg1=value1
```

## From Concept to Practice

### A Complete Example: Photo Processing Pipeline

```rexx
-- REXX orchestration layer
REQUIRE "gcp-address"
REQUIRE "claude-address"

-- Read list of image files
LET imageList = FILE_READ "images-to-process.txt"
LET images = SPLIT text=imageList delimiter="\\n"

-- Process each image
DO i = 1 TO LENGTH images
  LET imagePath = ARRAY_GET array=images index=i

  -- Escape to GCP: Upload to Cloud Storage
  ADDRESS gcp
  "upload path={imagePath} to gs://my-bucket/raw/"

  -- Escape to GCP: Trigger image analysis via Cloud Vision
  "vision analyze image=gs://my-bucket/raw/{imagePath}"
  LET visionResult = /* GCP response */

  -- Escape to Claude: Interpret results
  ADDRESS claude
  LET interpretation = "messages role=user text='Analyze this image data: {visionResult}'"

  -- Back in REXX: Coordinate results
  LET report = "Image: {imagePath}, Vision: {visionResult}, Interpretation: {interpretation}"

  -- Escape to database: Store analysis
  ADDRESS sqlite
  "INSERT INTO image_analysis (path, vision_data, interpretation) VALUES (?, ?, ?)"

  SAY "Processed: " || imagePath
END

-- Final coordination: Generate summary report
ADDRESS gcp
"firestore set /reports/daily path={reportPath}"

SAY "Pipeline complete!"
```

This script:
- **Orchestrates** in REXX (loops, conditionals, data flow)
- **Escapes to GCP** for cloud operations
- **Escapes to Claude** for AI analysis
- **Escapes to SQLite** for storage
- **Returns** with results at each step

No REXX script reimplements cloud services, AI models, or database engines. Each domain stays specialized; REXX stays focused on orchestration.

## Key Principles

1. **ADDRESS is NOT a library system** – it's a context switch mechanism
   - A library (`REQUIRE`) loads code into your REXX environment
   - An ADDRESS target (`ADDRESS name`) delegates to an external system

2. **ADDRESS is NOT just for APIs** – it's for any external domain
   - Shell commands (OS domain)
   - SQL queries (database domain)
   - Docker commands (container domain)
   - Python code (interpreter domain)

3. **ADDRESS is NOT limited to strings** – modern RexxJS supports function calls with named parameters
   - `function param1=value param2=value`
   - Results can be complex objects, not just strings

4. **ADDRESS is composable** – you can chain ADDRESS calls to build complex workflows
   - REXX glues everything together
   - Each ADDRESS handler is independently testable

## Migration from ARexx

If you're coming from **ARexx** scripting:

| ARexx | RexxJS |
|---|---|
| `ADDRESS DPAINT` → paint commands | `ADDRESS canvas` → canvas operations |
| `ADDRESS DOPUS` → file operations | `ADDRESS docker` → container operations |
| `ADDRESS PAGESTREAM` → document layout | `ADDRESS cloudStorage` → cloud operations |
| Amiga ports (binary protocols) | Web handlers (JSON/RPC protocols) |
| System-specific persistence | Cloud-based persistence |

The **conceptual model is identical**: use ADDRESS to escape REXX and operate in another domain. The **target domains have evolved** from Amiga applications to cloud services, databases, and AI systems.

## See Also

- [Application Addressing](19-application-addressing.md) – Technical reference for ADDRESS syntax
- [ADDRESS Handlers Reference](36-address-handlers.md) – Complete list of available handlers
- [History and Patterns](37-address-history-and-patterns.md) – Deep dive into ADDRESS evolution
- [HEREDOC Patterns](27-address-heredoc-patterns.md) – Multi-line content handling
- [MATCHING Patterns](27-address-matching-patterns.md) – Regex-based routing

---

**Core Takeaway**: ADDRESS is REXX's answer to the question "How do I use something outside REXX?" The answer: **escape hatch.** Leave REXX when you need a specialist; bring results back when done. Coordinate everything in REXX.
