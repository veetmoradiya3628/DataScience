# Python Day 12 — Time Module, F-Strings, Polymorphism, Modules, Packages & Libraries

**Date:** 8 February 2026 | **Class:** Day 12

---

> **Topics Covered Today:**
> 1. `time` Module — `time.time()`, `time.perf_counter()`, `time.sleep()`, `divmod()`
> 2. F-Strings (Formatted String Literals)
> 3. Polymorphism — OOP's 4th Pillar
> 4. Modules — Creating & Importing `.py` files
> 5. Packages — Folders of Modules + `__init__.py`
> 6. Library vs Module vs Package — Key Differences

---

# PART 1: The `time` Module

---

## Concept Overview

- The `time` module is a Python **built-in module** providing functions that revolve around time measurement and time-based control flow.
- It solves the problem of **measuring how long code takes to run**, which is essential for performance optimization, benchmarking, and building timers.
- **Real-world analogy:** Think of it like a stopwatch. You press start before a race, press stop after, and the difference tells you the runner's time. `time.time()` is your stopwatch.
- In Python's bigger picture: `time` is one of many standard-library modules. It's commonly used alongside profiling tools, decorators, and performance testing.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **Unix Epoch** | January 1, 1970, 00:00:00 UTC — the reference point for Unix time |
| **Epoch Time** | Total seconds elapsed since the Unix Epoch |
| **`time.time()`** | Returns current time as float (seconds since Jan 1, 1970) |
| **`time.perf_counter()`** | High-resolution timer; undefined start point, higher precision |
| **`time.sleep(n)`** | Pauses Python execution for `n` seconds |
| **`divmod(a, b)`** | Built-in that returns `(a // b, a % b)` — quotient and remainder |
| **Precision** | Number of significant decimal digits in a float value |
| **Benchmarking** | Measuring execution time of code to compare performance |

---

## Syntax & Definition

```python
import time

# Get current epoch time (seconds since Jan 1, 1970)
t = time.time()

# High-precision counter (undefined start point)
t = time.perf_counter()

# Pause execution for n seconds
time.sleep(n)

# divmod: returns (quotient, remainder) as a tuple
hours, remainder = divmod(total_seconds, 3600)
minutes, seconds = divmod(remainder, 60)
```

---

## Detailed Explanation with Examples

### Example 1: Measuring Time Difference with `time.time()`

```python
import time

t1 = time.time()     # Record start time

# --- some code or manual wait ---
time.sleep(2)        # Simulate a 2-second task

t2 = time.time()     # Record end time

elapsed = t2 - t1
print(elapsed)
```

**Output:**
```
2.0023841857910156
```

**Line-by-line breakdown:**
- `t1 = time.time()` — Captures the number of seconds elapsed since Jan 1, 1970 right now. Example value: `1738999200.45`
- `time.sleep(2)` — Tells Python to pause (block) for 2 seconds. Nothing executes during this wait.
- `t2 = time.time()` — Captures time again. This value is ~2 seconds larger than `t1`.
- `elapsed = t2 - t1` — **Latest minus older = positive elapsed time.** If you reversed this (`t1 - t2`), you'd get a negative number.

**Why this works:** `time.time()` always increases (monotonically moves forward). By storing two snapshots and subtracting, you isolate the elapsed interval.

> ⚠️ **Always subtract: `newer_time - older_time`**. Older minus newer gives a negative result, which is meaningless for durations.

---

### Example 2: `time.perf_counter()` — High-Precision Timing

```python
import time

def add(a, b):
    return a + b

t1_pc = time.perf_counter()
result = add(1, 2)
t2_pc = time.perf_counter()

elapsed_pc = t2_pc - t1_pc
print(f"perf_counter elapsed: {elapsed_pc}")

# Compare with time.time() for the same fast operation
t1 = time.time()
result = add(1, 2)
t2 = time.time()
elapsed_tt = t2 - t1
print(f"time.time elapsed:    {elapsed_tt}")
```

**Output:**
```
perf_counter elapsed: 0.0000012340000000
time.time elapsed:    0.0
```

**Line-by-line breakdown:**
- `time.perf_counter()` — Returns a float with much higher decimal precision than `time.time()`. Its starting reference point is undefined (may be system boot time).
- For ultra-fast operations like a simple addition, `time.time()` rounds to `0.0` — too coarse to measure anything.
- `time.perf_counter()` captures sub-microsecond differences, making it useful for micro-benchmarking.

**Why this works:** `perf_counter` uses the highest-resolution clock available on the OS. It sacrifices a meaningful start reference (unlike epoch time) in exchange for precision.

| Feature | `time.time()` | `time.perf_counter()` |
|---------|--------------|----------------------|
| Start reference | Jan 1, 1970 (Unix epoch) | Undefined (OS-dependent) |
| Precision (digits) | ~14 decimal places | ~20 decimal places |
| Use case | Timestamps, general timing | Micro-benchmarking, profiling |
| Can go backwards? | Theoretically (clock sync) | No — always monotonic |

---

### Example 3: Converting Seconds to Hours, Minutes, Seconds with `divmod()`

```python
import time

t1 = time.time()
# ... some code runs for a while ...
time.sleep(966)      # Simulate 966 seconds (for demonstration)
t3 = time.time()

total_seconds = t3 - t1

# Convert seconds → hours, minutes, seconds
hours, remainder = divmod(total_seconds, 3600)
minutes, seconds  = divmod(remainder, 60)

print(f"Time taken — H: {int(hours)} :: M: {int(minutes)} :: S: {seconds:.2f}")
```

**Output:**
```
Time taken — H: 0 :: M: 16 :: S: 6.96
```

**Line-by-line breakdown:**
- `divmod(total_seconds, 3600)` — Divides by 3600 (seconds per hour). Returns `(whole_hours, leftover_seconds)`.
  - 3600 = 60 minutes × 60 seconds per minute.
- `divmod(remainder, 60)` — Divides the leftover seconds by 60. Returns `(whole_minutes, leftover_seconds)`.
- `int(hours)`, `int(minutes)` — Cast to int so we don't show decimal points for hours and minutes (e.g., `0 hours`, not `0.0 hours`).
- `seconds:.2f` — Keep 2 decimal places for seconds (covered fully in F-Strings section).

**The Math (step by step):**
```
total_seconds = 966.96

Step 1: divmod(966.96, 3600)
    → quotient  = 0      (0 full hours)
    → remainder = 966.96 (all seconds remain)

Step 2: divmod(966.96, 60)
    → quotient  = 16     (16 full minutes)
    → remainder = 6.96   (6.96 seconds left over)

Result: 0 hours, 16 minutes, 6.96 seconds
```

**Real-world analogy:** Humans are bad at interpreting "966 seconds." We're good at "16 minutes and 7 seconds." `divmod` bridges that gap.

---

### Example 4: Real-World Benchmarking — Comparing Two Algorithms

```python
import time

def algorithm_slow(data):
    """Simulate slow O(n²) search."""
    result = []
    for i in data:
        for j in data:
            if i == j:
                result.append(i)
    return result

def algorithm_fast(data):
    """Simulate fast O(n) search using a set."""
    seen = set()
    result = []
    for i in data:
        if i not in seen:
            seen.add(i)
            result.append(i)
    return result

data = list(range(5000))

# Benchmark slow algorithm
t1 = time.perf_counter()
algorithm_slow(data)
t2 = time.perf_counter()
print(f"Slow algorithm: {t2 - t1:.4f} seconds")

# Benchmark fast algorithm
t1 = time.perf_counter()
algorithm_fast(data)
t2 = time.perf_counter()
print(f"Fast algorithm: {t2 - t1:.4f} seconds")
```

**Output:**
```
Slow algorithm: 2.3187 seconds
Fast algorithm: 0.0004 seconds
```

**Why this is the industry pattern:** In real projects (e.g., Google Maps routing algorithms, ML model inference), two engineers may write different solutions to the same problem. Benchmarking with `perf_counter` objectively determines which is faster — without guessing.

---

## How `time.time()` Works Internally

```
Python calls time.time()
    │
    ▼
OS system clock API (gettimeofday / clock_gettime on Linux)
    │
    ▼
Returns: total_nanoseconds_since_epoch / 1,000,000,000
    │
    ▼
Python returns a float (e.g., 1738999200.453812)
```

- The Unix Epoch clock is **not a physical clock that stops when power is off.** It is a calculated value: the OS always knows the current date/time (set via NTP or manually) and computes seconds since epoch on demand.
- `perf_counter` uses the CPU's hardware performance counter — a register that increments billions of times per second, giving nanosecond-level precision.

---

## Common Mistakes & Debugging

### Mistake 1: Subtracting in wrong order

**Wrong:**
```python
t1 = time.time()
time.sleep(5)
t2 = time.time()

elapsed = t1 - t2   # WRONG: newer minus older
print(elapsed)
```

**Error/Output:**
```
-5.003241062164307
```

**Why it fails:** `t1` was captured first; `t2` is larger. `t1 - t2` is always negative.

**Correct:**
```python
elapsed = t2 - t1   # Always: latest - older
```

---

### Mistake 2: Using `time.time()` to measure ultra-fast code

**Wrong:**
```python
t1 = time.time()
result = 1 + 1        # Executes in nanoseconds
t2 = time.time()
print(t2 - t1)        # Prints 0.0 — useless
```

**Why it fails:** `time.time()` resolution is too coarse for sub-millisecond operations.

**Correct:**
```python
t1 = time.perf_counter()
result = 1 + 1
t2 = time.perf_counter()
print(t2 - t1)   # Prints a tiny but non-zero value
```

---

### Mistake 3: Forgetting `time.sleep()` blocks everything

```python
# This will freeze your program for 10 seconds
# No other code executes during this wait
time.sleep(10)
print("This prints after 10 seconds")
```

**When NOT to use `sleep`:** In production web servers, GUIs, or async code — `sleep` blocks the entire thread. Use `asyncio.sleep()` for async programs instead.

---

## Best Practices

- **Use `time.perf_counter()` over `time.time()`** for benchmarking — it's always more accurate.
- **Use `time.time()`** when you need an actual timestamp (e.g., "what time did this event happen?").
- **Never use `time.sleep()`** in production async code. Use `asyncio.sleep()`.
- Use `divmod()` to convert raw seconds into human-readable time — it's cleaner than manual `//` and `%` operations.

---

## When to Use / When NOT to Use

| Use When... | Avoid When... |
|------------|--------------|
| Benchmarking function performance | Measuring wall-clock timestamps for logging (use `datetime` instead) |
| Building a custom timer/stopwatch | High-precision async timing (use `asyncio`) |
| Pausing execution in scripts/tests | Pausing in async/web server code |
| Converting seconds to H:M:S display | You need timezone-aware time (use `datetime`) |

---

## Related Topics & Connections

- **`datetime` module** — For date-aware, timezone-aware time operations
- **`timeit` module** — Specifically designed for micro-benchmarking code snippets
- **Decorators** — The standard pattern to wrap timing logic around any function automatically (covered in next class)
- **`asyncio.sleep()`** — Non-blocking equivalent of `time.sleep()` for async code

---

## Quick-Reference Cheat Sheet

```
TIME MODULE CHEAT SHEET
========================
import time

time.time()             → Float: seconds since Unix epoch (Jan 1, 1970)
time.perf_counter()     → Float: high-precision timer (undefined start)
time.sleep(n)           → Pauses execution for n seconds

# Timing a block of code
t1 = time.perf_counter()
... your code ...
t2 = time.perf_counter()
elapsed = t2 - t1       → ALWAYS: later - earlier

# Convert seconds to H:M:S
h, rem    = divmod(total_seconds, 3600)
m, s      = divmod(rem, 60)

# Gotchas
t1 - t2   → WRONG (gives negative result)
time.time() on fast code → Returns 0.0 (use perf_counter)
sleep() in async code   → Blocks entire thread (use asyncio.sleep)
```

---

---

# PART 2: F-Strings (Formatted String Literals)

---

## Concept Overview

- An **F-string** (formatted string literal) is a way to embed Python expressions directly inside a string, using `f"..."` syntax.
- It solves the problem of **cumbersome string concatenation** — instead of `"Hello " + name + "!"`, you write `f"Hello {name}!"`.
- **Real-world analogy:** Think of an F-string like a fill-in-the-blanks form. The form has fixed text (`"Hello ___"`) and blank spaces `{}` where values are dynamically inserted.
- In Python's bigger picture: F-strings are the modern, fastest, and most readable way to build strings. They replaced `%` formatting and `.format()` as the industry standard (introduced in Python 3.6).

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **F-string** | String prefixed with `f` or `F`; allows embedding expressions in `{}` |
| **Expression** | Any valid Python code inside `{}` — variable, calculation, function call |
| **Format spec** | Codes after `:` inside `{}` that control display format |
| **Precision** | Number of decimal places shown (e.g., `.2f` = 2 decimal places) |
| **Width** | Minimum character width for a field (e.g., `:>10` = 10 chars wide) |
| **Alignment** | Left (`<`), right (`>`), or center (`^`) within a given width |
| **Type code** | `f` for float, `%` for percentage, `d` for integer, `s` for string |

---

## Syntax & Definition

```python
# Basic syntax
f"literal text {expression} more text"

# With format specification
f"{expression:format_spec}"

# Format spec structure: [fill][align][sign][width][.precision][type]
f"{value:>10}"       # Right-align in 10 chars
f"{value:.2f}"       # 2 decimal places, float
f"{value:.2%}"       # Percentage with 2 decimal places
f"{value:+.2f}"      # Show + or - sign, 2 decimal places
f"{value:010.2f}"    # Zero-padded, width 10, 2 decimal places
```

---

## Detailed Explanation with Examples

### Example 1: Basic F-String Usage

```python
# Without F-string (old way)
name = "Monal"
greeting = "Hello " + name + "!"
print(greeting)

# With F-string (modern way)
greeting = f"Hello {name}!"
print(greeting)
```

**Output:**
```
Hello Monal!
Hello Monal!
```

**Line-by-line breakdown:**
- `"Hello " + name + "!"` — Three separate string objects concatenated with `+`. You must handle spaces manually. Type errors occur if `name` is not a string.
- `f"Hello {name}!"` — Python sees the `f` prefix, scans for `{}` blocks, evaluates `name`, and substitutes its string value inline.

**Why F-strings are better:** Single string object created in one pass. No `+` operators, no spaces to manually track, no type conversion needed for display.

---

### Example 2: Expressions Inside F-Strings

```python
a = 10
b = 3

# You can put any expression inside {}
print(f"Sum: {a + b}")
print(f"Division: {a / b:.2f}")
print(f"Is even: {a % 2 == 0}")
print(f"Upper: {'hello'.upper()}")
```

**Output:**
```
Sum: 13
Division: 3.33
Is even: True
Upper: HELLO
```

**Why this works:** Everything inside `{}` is evaluated as normal Python code. The result is converted to a string and inserted into the final string.

---

### Example 3: Formatting Numbers — Float Precision

```python
seconds = 6.9682134522

# Show only 2 decimal places (display only — original value unchanged)
print(f"Seconds: {seconds:.2f}")

# Compare — original variable is unaffected
print(f"Original: {seconds}")
```

**Output:**
```
Seconds: 6.97
Original: 6.9682134522
```

**Critical insight:** F-string formatting **does NOT modify the variable**. It only changes how the value is *displayed* in that string. `seconds` still holds full precision.

---

### Example 4: Percentage Formatting

```python
accuracy = 0.8745

# Manual way (verbose)
print(f"Accuracy: {accuracy * 100:.2f}%")

# F-string percentage shorthand (Python does ×100 automatically)
print(f"Accuracy: {accuracy:.2%}")
```

**Output:**
```
Accuracy: 87.45%
Accuracy: 87.45%
```

**Line-by-line breakdown:**
- `accuracy * 100:.2f` — Multiply by 100 yourself, show 2 decimal places, manually add `%`.
- `accuracy:.2%` — The `%` type code automatically multiplies by 100, formats with 2 decimal places, and appends `%`. Cleaner and less error-prone.

---

### Example 5: Alignment and Width — Real-World Terminal UI

```python
# Poor formatting (unaligned)
print("Name   :", "Alice")
print("Email  :", "alice@example.com")
print("Mobile :", "9876543210")

# Better: right-aligned labels using F-string width
label_width = 15
print(f"{'Name':{label_width}} : Alice")
print(f"{'Email':{label_width}} : alice@example.com")
print(f"{'Mobile Number':{label_width}} : 9876543210")
```

**Output:**
```
Name            : Alice
Email           : alice@example.com
Mobile Number   : 9876543210
```

**Line-by-line breakdown:**
- `f"{'Name':{label_width}}"` — Embeds another expression `label_width` as the width spec. The string `"Name"` is left-padded to exactly `label_width` characters, making all colons align vertically.
- This is how professional terminal UIs produce clean, form-like output.

---

### Example 6: Right-Alignment with `>` (Padding)

```python
name = "Monal"

# Right-align name in a field of 10 characters
print(f"|{name:>10}|")   # Right-aligned
print(f"|{name:<10}|")   # Left-aligned (default for strings)
print(f"|{name:^10}|")   # Center-aligned
```

**Output:**
```
|     Monal|
|Monal     |
|  Monal   |
```

**Why this is useful:** When building formatted tables in terminal output, dashboards, or log files, alignment ensures consistent, readable columns.

---

### Example 7: Sign Display and Zero-Padding

```python
num_positive =  42.5
num_negative = -42.5

# Show explicit + or - sign
print(f"Positive: {num_positive:+.2f}")
print(f"Negative: {num_negative:+.2f}")

# Zero-pad to width 8 with 2 decimal places
print(f"Zero-padded: {num_positive:08.2f}")
```

**Output:**
```
Positive: +42.50
Negative: -42.50
Zero-padded: 00042.50
```

---

### Example 8: Saving F-String Result to Variable

```python
hours   = 0
minutes = 16
seconds = 6.96

# F-string can be stored just like any string
result = f"Time taken — H: {hours} :: M: {minutes} :: S: {seconds:.2f}"
print(result)
```

**Output:**
```
Time taken — H: 0 :: M: 16 :: S: 6.97
```

> ⚠️ **Comma-separated print values produce a tuple, not a string:**
> ```python
> # This gives a tuple, not a single string!
> x = print("H:", hours, "M:", minutes)  # x is None; this doesn't store a clean string
>
> # F-string gives you a proper string variable
> x = f"H: {hours} M: {minutes}"         # x is a str
> ```

---

## Comparison Table: String Formatting Methods

| Method | Example | Readability | Speed | Python Version |
|--------|---------|-------------|-------|----------------|
| `%` formatting | `"Hello %s" % name` | Low | Slow | Python 2+ |
| `.format()` | `"Hello {}".format(name)` | Medium | Medium | Python 3.0+ |
| **F-string** | `f"Hello {name}"` | **High** | **Fastest** | **Python 3.6+** |
| Template strings | `Template("Hello $name")` | Low | Slow | Python 2.4+ |

**Industry standard: Always use F-strings** in Python 3.6+.

---

## How F-Strings Work Internally

```
Source code: f"Hello {name}!"
                │
                ▼
Parser detects f-prefix → enters f-string mode
                │
                ▼
Reads "Hello " → literal bytes stored
                │
                ▼
Reads {name} → evaluates name in current scope → gets "Monal"
                │
                ▼
Calls str("Monal") → "Monal"
                │
                ▼
Concatenates: "Hello " + "Monal" + "!"
                │
                ▼
Returns new string object: "Hello Monal!"
```

F-strings are compiled at **parse time**, not runtime — making them faster than `.format()` which is a runtime method call.

---

## Common Mistakes & Debugging

### Mistake 1: Forgetting the `f` prefix

**Wrong:**
```python
name = "Alice"
print("{name}")
```

**Output:**
```
{name}
```

**Why it fails:** Without `f`, Python treats `{name}` as literal text, not a variable reference.

**Correct:**
```python
print(f"{name}")
```

---

### Mistake 2: Missing type code `f` in float formatting

**Wrong:**
```python
seconds = 5.987654
print(f"{seconds:.2}")   # Missing 'f' type code
```

**Output:**
```
6.0
```

**Why it fails:** Without `f`, `.2` means precision of 2 *significant digits*, not 2 decimal places.

**Correct:**
```python
print(f"{seconds:.2f}")  # '2f' = 2 decimal places, float type
```

---

### Mistake 3: Trying to add string + number without F-string

**Wrong:**
```python
hours = 2
print("Hours: " + hours)
```

**Error:**
```
TypeError: can only concatenate str (not "int") to str
```

**Why it fails:** `+` for strings requires both sides to be strings. `hours` is an `int`.

**Correct:**
```python
print(f"Hours: {hours}")   # F-string handles type conversion automatically
# OR
print("Hours: " + str(hours))  # Manual conversion
```

---

## Best Practices

```python
# DO: Use F-strings for all string formatting
message = f"User {username} logged in at {timestamp:.2f}"

# DO: Use :.2f for displaying floats to users
print(f"Price: ${price:.2f}")

# DO: Use :.2% for percentages
print(f"Accuracy: {accuracy:.2%}")

# DON'T: Use % formatting (legacy)
print("Hello %s" % name)    # Outdated

# DON'T: Use + concatenation for complex strings
msg = "Hello " + name + "! You have " + str(count) + " messages."  # Verbose

# DO: The clean version
msg = f"Hello {name}! You have {count} messages."
```

---

## When to Use / When NOT to Use

| Use When... | Avoid When... |
|------------|--------------|
| Embedding variables in output strings | Python < 3.6 (use `.format()` instead) |
| Formatting numbers (decimals, %, width) | Internationalization (use `gettext` library) |
| Building clean terminal UI | Template strings with untrusted user input (security concern) |
| Logging and debugging output | Dynamic format strings known only at runtime (use `.format()`) |

---

## Quick-Reference Cheat Sheet

```
F-STRING CHEAT SHEET
========================
f"{var}"            → Insert variable
f"{var:.2f}"        → Float, 2 decimal places
f"{var:.2%}"        → Percentage (×100 + %), 2 decimal places
f"{var:>10}"        → Right-align in width 10
f"{var:<10}"        → Left-align in width 10
f"{var:^10}"        → Center in width 10
f"{var:+.2f}"       → Show +/- sign
f"{var:010.2f}"     → Zero-pad, width 10, 2 decimals
f"{a + b}"          → Any expression works inside {}
f"{func(arg)}"      → Function calls work too

Gotchas:
- Forgetting 'f' prefix → {} treated as literal text
- :.2 (no f) → significant digits, not decimal places
- string + int → TypeError; use F-string instead
```

---

---

# PART 3: Polymorphism (OOP — 4th Pillar)

---

## Concept Overview

- **Polymorphism** (Greek: *poly* = many, *morphism* = forms) is the ability of **different objects to respond to the same method name**, each with its own specific behavior.
- It solves the problem of writing repetitive, hard-coded calls to many differently-named methods that all do conceptually the same job (e.g., `start_tv()`, `start_microwave()`, `start_fan()` → all replaced by `.start()`).
- **Real-world analogy:** A universal TV remote. One button labeled "Power" turns on a TV, a DVD player, or a sound system — the same button press, but the device (object) decides what "power" means for it.
- In Python's bigger picture: Polymorphism is the 4th pillar of OOP (after Encapsulation, Inheritance, Abstraction). Together they help design systems that are **clean, scalable, and easy to extend**.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **Polymorphism** | Same method name → different behaviors based on the object |
| **Method Overriding** | Subclass provides its own implementation of a parent class method |
| **Duck Typing** | Python's informal polymorphism: "if it has `.start()`, call `.start()`" regardless of class |
| **Operator Overloading** | `+` does addition for `int`, concatenation for `str` — same operator, different behavior |
| **Inheritance-based Polymorphism** | Polymorphism enforced through a shared abstract base class |
| **Abstract Base Class (ABC)** | A class that defines a contract: subclasses *must* implement specified methods |

---

## Syntax & Definition

```python
# Basic Polymorphism — same method name, different classes
class ClassA:
    def action(self):
        # ClassA's specific implementation
        pass

class ClassB:
    def action(self):
        # ClassB's specific implementation
        pass

# Polymorphic usage — one loop, any object
objects = [ClassA(), ClassB()]
for obj in objects:
    obj.action()   # Calls the right implementation automatically
```

---

## Detailed Explanation with Examples

### Example 1: Without Polymorphism (The Problem)

```python
class TV:
    def start_tv(self):
        print("Starting TV...")

class Microwave:
    def start_microwave(self):
        print("Starting Microwave...")

class Fan:
    def start_fan(self):
        print("Starting Fan...")

# To start each appliance, need 3 different method names
tv  = TV()
mv  = Microwave()
fan = Fan()

tv.start_tv()
mv.start_microwave()
fan.start_fan()
```

**Output:**
```
Starting TV...
Starting Microwave...
Starting Fan...
```

**Problem:** You must memorize and write a different method name for each class. If you have 50 appliances, you need 50 different method calls — not scalable.

---

### Example 2: With Polymorphism (The Solution)

```python
class TV:
    def start(self):
        print("TV: Initializing display driver, loading channels...")

class Microwave:
    def start(self):
        print("Microwave: Heating element warming up, timer ready...")

class Fan:
    def start(self):
        print("Fan: Motor spinning up, setting speed...")

# Polymorphic usage — single loop, single method name
electronic_appliances = [TV(), Microwave(), Fan()]

for appliance in electronic_appliances:
    appliance.start()   # Python calls the right .start() for each object
```

**Output:**
```
TV: Initializing display driver, loading channels...
Microwave: Heating element warming up, timer ready...
Fan: Motor spinning up, setting speed...
```

**Line-by-line breakdown:**
- `electronic_appliances = [TV(), Microwave(), Fan()]` — A list of objects from *different* classes. Python stores them all as generic objects.
- `for appliance in electronic_appliances:` — Iterates over each object.
- `appliance.start()` — Python looks up the actual type of each `appliance` and calls *that class's* `start()` method. This is called **dynamic dispatch**.

**Why this works:** Python doesn't care what *class* `appliance` is at compile time. At runtime, it checks the actual object's type and calls the correct method. This is Python's **duck typing**: "If it has a `.start()` method, I'll call it."

---

### Example 3: Adding Abstraction to Enforce Polymorphism

```python
from abc import ABC, abstractmethod

class ElectronicContract(ABC):
    """Abstract base class — acts as a contract for all electronics."""

    @abstractmethod
    def start(self):
        pass  # No implementation here — just the requirement

class TV(ElectronicContract):
    def start(self):
        print("TV is starting...")

class Microwave(ElectronicContract):
    def start(self):
        print("Microwave is starting...")

class Fan(ElectronicContract):
    def start(self):
        print("Fan is starting...")

# A new developer adds AirFryer but forgets to implement start()
class AirFryer(ElectronicContract):
    pass   # Missing start() implementation!

# Error happens at object creation — fails fast, before any loop runs
try:
    fryer = AirFryer()
except TypeError as e:
    print(f"Error: {e}")
```

**Output:**
```
Error: Can't instantiate abstract class AirFryer with abstract method start
```

**Line-by-line breakdown:**
- `class ElectronicContract(ABC)` — Inherits from `ABC` (Abstract Base Class). This class cannot be instantiated directly.
- `@abstractmethod` — Decorator that marks `start()` as *required*. Any subclass must implement it.
- `class AirFryer(ElectronicContract): pass` — Inherits the contract but provides no `start()`. Python raises `TypeError` the moment you try to create an `AirFryer` object.
- This catches developer mistakes **at object creation**, before the bug reaches a production loop.

> **Key insight:** Polymorphism and Abstraction are **separate concepts**. Polymorphism is the pattern of using the same method name; Abstraction (ABC) is the enforcement tool that ensures the pattern is followed. Abstraction is optional — polymorphism works without it. But combining them produces robust, enforced designs.

---

### Example 4: Operator Overloading — Built-in Polymorphism

```python
# The + operator exhibits polymorphism
result_int = 1 + 1          # Addition
result_str = "Hello" + " World"  # Concatenation
result_list = [1, 2] + [3, 4]   # List extension

print(result_int)    # 2
print(result_str)    # Hello World
print(result_list)   # [1, 2, 3, 4]
```

**Output:**
```
2
Hello World
[1, 2, 3, 4]
```

**Why this is polymorphism:** The same `+` operator behaves differently based on the *type* (class) of its operands. Python calls `int.__add__()`, `str.__add__()`, or `list.__add__()` internally — same symbol, different behavior.

---

### Example 5: Real-World — Healthcare System

```python
from abc import ABC, abstractmethod

class HealthMetric(ABC):
    @abstractmethod
    def calculate(self):
        pass

class BMI(HealthMetric):
    def __init__(self, weight_kg, height_m):
        self.weight = weight_kg
        self.height = height_m

    def calculate(self):
        bmi = self.weight / (self.height ** 2)
        print(f"BMI: {bmi:.2f}")

class HeartRate(HealthMetric):
    def __init__(self, beats, duration_min):
        self.beats    = beats
        self.duration = duration_min

    def calculate(self):
        bpm = self.beats / self.duration
        print(f"Heart Rate: {bpm:.0f} BPM")

class Temperature(HealthMetric):
    def __init__(self, celsius):
        self.celsius = celsius

    def calculate(self):
        fahrenheit = (self.celsius * 9/5) + 32
        print(f"Temperature: {self.celsius}°C / {fahrenheit:.1f}°F")

# Polymorphic usage — same loop, different calculations
metrics = [BMI(70, 1.75), HeartRate(720, 5), Temperature(37.2)]

for metric in metrics:
    metric.calculate()
```

**Output:**
```
BMI: 22.86
Heart Rate: 144 BPM
Temperature: 37.2°C / 98.9°F
```

**Real-world relevance:** In a hospital system, a dashboard might iterate over all patient metrics and call `.calculate()` on each. New metric types (blood pressure, SpO2) can be added without changing the loop.

---

## How Polymorphism Works Internally (Dynamic Dispatch)

```
Python code: appliance.start()

Step 1: Python evaluates `appliance`
        → Gets reference to object at memory address 0x1234

Step 2: Python looks up the object's type
        → Object is of type <class 'TV'>

Step 3: Python looks up `start` in TV's method resolution order (MRO)
        [TV → ElectronicContract → ABC → object]

Step 4: Found `start` in TV → calls TV.start(appliance)

Step 5: Prints "TV is starting..."
```

This entire process happens at **runtime**, not at parse time — which is why Python can call different implementations for different objects even when the code is identical.

---

## Common Mistakes & Debugging

### Mistake 1: Different method names (breaks polymorphism)

**Wrong:**
```python
class TV:
    def start_tv(self): pass

class Fan:
    def turn_on_fan(self): pass

# Can't use a loop — method names are different!
for item in [TV(), Fan()]:
    item.start()  # AttributeError: 'TV' has no attribute 'start'
```

**Correct:** Standardize the method name across all classes.

---

### Mistake 2: Not inheriting ABC when using `@abstractmethod`

**Wrong:**
```python
from abc import abstractmethod

class Contract:            # Missing (ABC) !
    @abstractmethod
    def start(self):
        pass

class Fan(Contract):
    pass

fan = Fan()   # No error — abstractmethod wasn't enforced
```

**Correct:**
```python
from abc import ABC, abstractmethod

class Contract(ABC):       # Must inherit ABC
    @abstractmethod
    def start(self):
        pass
```

---

### Mistake 3: Confusing Python polymorphism with Java method overloading

In **Java**, you can define the same method name with different parameter types (method overloading). **Python does NOT support method overloading** in the same way — the last definition wins. Use `*args` or default arguments instead.

```python
# Python: WRONG attempt at overloading
class Calculator:
    def add(self, a, b):
        return a + b

    def add(self, a, b, c):   # This REPLACES the first add()!
        return a + b + c

calc = Calculator()
calc.add(1, 2)    # TypeError! Only the 3-parameter version exists
```

```python
# Python: CORRECT way — use *args
class Calculator:
    def add(self, *args):
        return sum(args)
```

---

## Best Practices

- **Name methods by what they do, not who they belong to:** `start()` not `start_tv()`.
- **Use ABC to enforce contracts** in team environments where new developers may not know the design pattern.
- **Keep polymorphic methods focused** — a method named `calculate()` should only calculate, not also print results.
- **Document the expected behavior** of abstract methods clearly — subclass developers need to know what the method is supposed to do.

---

## When to Use / When NOT to Use

| Use When... | Avoid When... |
|------------|--------------|
| Multiple classes share a conceptually identical operation | Classes have truly unrelated responsibilities |
| You want to iterate over mixed-type objects with one loop | You have only 1-2 classes (YAGNI principle) |
| Building extensible systems (easy to add new subclasses) | The methods have fundamentally different signatures |
| Enforcing team coding standards via ABC | Runtime performance is critical (very minor overhead) |

---

## Interview & Exam Corner

### Frequently Asked Questions

1. **Q: What is polymorphism in Python?**
   **A:** Polymorphism is the ability for objects of different classes to be accessed through the same interface (method name), with each class providing its own specific implementation of that method.

2. **Q: What is the difference between method overloading and method overriding?**
   **A:** Python does **not** support true method overloading (same name, different parameter types). It supports **method overriding**: a subclass redefines a method from its parent class with a new implementation.

3. **Q: Can you achieve polymorphism without inheritance in Python?**
   **A:** Yes — through **duck typing**. As long as an object has the expected method, Python will call it regardless of class hierarchy. "If it walks like a duck and quacks like a duck, it's a duck."

4. **Q: What's the difference between polymorphism and abstraction?**
   **A:** Polymorphism is the *pattern* (same method name, different behavior). Abstraction (ABC) is the *enforcement tool*. You can have polymorphism without ABC; you use ABC when you want to guarantee all subclasses implement the method.

### Tricky Output Questions

```python
class A:
    def greet(self):
        return "Hello from A"

class B(A):
    def greet(self):
        return "Hello from B"

items = [A(), B(), A()]
for item in items:
    print(item.greet())
```

**Answer:**
```
Hello from A
Hello from B
Hello from A
```

**Why:** Each object carries its own class reference. `B().greet()` calls B's overridden version even though we iterate through a mixed list.

---

## Quick-Reference Cheat Sheet

```
POLYMORPHISM CHEAT SHEET
========================
Core idea: Same method name → different behavior per class

# Duck typing (no inheritance needed)
class A:
    def action(self): ...
class B:
    def action(self): ...
for obj in [A(), B()]:
    obj.action()   # Each calls its own action()

# Abstract enforcement
from abc import ABC, abstractmethod
class Base(ABC):
    @abstractmethod
    def action(self): pass

class Sub(Base):
    def action(self): ...  # Required — or TypeError on instantiation

# Operator overloading (built-in polymorphism)
1 + 1           → 2      (int.__add__)
"a" + "b"       → "ab"   (str.__add__)

Gotchas:
- Python has no method overloading (unlike Java)
- ABC only enforced if class inherits from ABC
- Method names MUST match for loop-based polymorphism
```

---

---

# PART 4: Modules

---

## Concept Overview

- A **module** is simply a `.py` file containing Python code (functions, classes, variables) that can be **imported** and reused in other files.
- It solves the problem of **code duplication and messy files**: instead of writing the same logic in every file, or cramming everything into one giant file, you organize code into logical, reusable units.
- **Real-world analogy:** A module is like a specialized toolbox. Your main workshop (`main.py`) doesn't contain all the tools — it just grabs the right toolbox (`calculator.py`, `utils.py`) when needed.
- In Python's bigger picture: `os`, `time`, `math` are all built-in modules. When you `import os`, you're importing Python's `os.py` file. You can create your own modules the exact same way.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **Module** | A `.py` file containing reusable Python code |
| **`import`** | Statement that creates a linkage to a module file |
| **`from ... import`** | Import specific components from a module |
| **`import as`** | Import a module under an alias (`import numpy as np`) |
| **Namespace** | The scope under which module members live (`module.function`) |
| **Module path** | Python searches for modules in the current directory first, then `sys.path` |

---

## Syntax & Definition

```python
# Import entire module
import module_name
module_name.function_or_class()

# Import specific item from module
from module_name import ClassName, function_name

# Import with alias
import module_name as alias
alias.function()

# Import everything (use sparingly)
from module_name import *
```

---

## Detailed Explanation with Examples

### Example 1: Creating and Importing a Custom Module

**File: `calculator.py`** (your module)
```python
import time

class Calculator:
    """A calculator class with history tracking."""

    def __init__(self):
        self._history = []   # Private — use name mangling

    def add(self, *args):
        """Add any number of values."""
        total = 0
        for value in args:
            total += value

        # Record operation in history with timestamp
        timestamp = time.time()
        self._history.append({
            f"add_{timestamp}": {
                "inputs": args,
                "result": total
            }
        })

        return total

    def get_history(self):
        """Print the operation history."""
        for record in self._history:
            print(record)


def hello_world():
    """A standalone function in the module."""
    print("Hello from the calculator module!")
```

**File: `main.py`** (your main script)
```python
import calculator

# Access class from module: module_name.ClassName()
calc_obj = calculator.Calculator()

# Use the calculator
print(calc_obj.add(1, 2, 3, 4, 5, 6, 7, 8, 9, 10))
print(calc_obj.add(8, 9))

# View history
calc_obj.get_history()

# Access standalone function
calculator.hello_world()
```

**Output:**
```
55
17
{'add_1738999200.45': {'inputs': (1, 2, 3, 4, 5, 6, 7, 8, 9, 10), 'result': 55}}
{'add_1738999205.12': {'inputs': (8, 9), 'result': 17}}
Hello from the calculator module!
```

**Line-by-line breakdown:**
- `import calculator` — Python looks for `calculator.py` in the current directory (and `sys.path`). Creates a module object called `calculator` in memory.
- `calculator.Calculator()` — `calculator` is the module (file name), `Calculator` is the class inside it. The `.` navigates from module → class.
- `calc_obj.add(1, 2, 3, ...)` — `*args` in `add()` accepts any number of arguments, making the method generic.
- `_history` — Single underscore signals "private by convention"; `get_history()` provides controlled access (encapsulation).

---

### Example 2: `from module import` — Selective Import

```python
from calculator import Calculator, hello_world

# Now you can use them without the module prefix
calc = Calculator()
print(calc.add(10, 20))
hello_world()
```

**Output:**
```
30
Hello from the calculator module!
```

**When to use this:** When you only need specific items from a large module. You avoid loading the entire module namespace and reduce verbosity.

---

## How Module Import Works Internally

```
Statement: import calculator

Step 1: Python searches for 'calculator' in this order:
        a) sys.modules (cache — already imported? use cached)
        b) Current directory (./calculator.py)
        c) PYTHONPATH directories
        d) Standard library directories
        e) Site-packages (third-party installs)

Step 2: Found ./calculator.py
        → Compiles to bytecode (creates __pycache__/calculator.cpython-3x.pyc)
        → Executes the file top-to-bottom
        → All definitions (classes, functions, variables) are stored in a module object

Step 3: Binds the module object to the name 'calculator' in current scope

Step 4: calculator.Calculator accesses the Calculator attribute of the module object
```

> **Note on `__pycache__`:** When you import a module, Python creates a compiled bytecode cache in `__pycache__/`. This is Python's own optimization — you don't need to interact with it. It speeds up subsequent imports.

---

## Common Mistakes & Debugging

### Mistake 1: Module not in the same directory

**Wrong:**
```
project/
  main.py           ← running from here
  utils/
    calculator.py   ← module is here
```

```python
import calculator   # ModuleNotFoundError
```

**Error:**
```
ModuleNotFoundError: No module named 'calculator'
```

**Correct:** Either move `calculator.py` to the same level as `main.py`, or use package import syntax (`from utils import calculator`).

---

### Mistake 2: Circular imports

**Wrong:**
```python
# a.py
import b

# b.py
import a   # Circular! a imports b, b imports a
```

**Why it fails:** Python can't complete either import — it gets stuck in a loop.

**Correct:** Refactor shared code into a third module that both `a.py` and `b.py` import.

---

## Best Practices

```python
# DO: Group imports at the top of the file
import time
import os
from calculator import Calculator

# DO: Use descriptive module names (snake_case)
# math_utils.py, data_processor.py, api_client.py

# DO: Keep one module per logical concern
# math_utils.py → only math functions
# file_utils.py → only file I/O functions

# DON'T: Put everything in one massive module
# god_module.py → 2000 lines with unrelated functions (anti-pattern)

# DON'T: Use 'import *' in production code
from calculator import *  # Pollutes namespace, hides where names come from
```

---

## When to Use / When NOT to Use

| Use When... | Avoid When... |
|------------|--------------|
| Code is reused across multiple files | Logic is used only once, in one place |
| Logical grouping of related functions/classes | The module would be trivially small (< 5 lines) |
| Keeping main script clean and focused | Circular dependency situations |
| Sharing utility code across a project | |

---

## Quick-Reference Cheat Sheet

```
MODULES CHEAT SHEET
========================
# A module = any .py file

import module_name                    → Full module import
from module_name import Item          → Specific import
from module_name import Item1, Item2  → Multiple specific imports
import module_name as alias           → With alias

module_name.ClassName()               → Access class
module_name.function()                → Access function
module_name.VARIABLE                  → Access variable

# Module must be in:
# - Same directory as the file that imports it
# - OR a directory in sys.path

Gotchas:
- import * pollutes the namespace
- Circular imports cause ImportError
- __pycache__ is Python's bytecode cache — ignore it
```

---

---

# PART 5: Packages

---

## Concept Overview

- A **package** is a **folder/directory that contains one or more related modules** (`.py` files), organized by logical grouping.
- It solves the problem of having too many module files scattered at the project root level — packages group related modules together, just like folders group related files on your computer.
- **Real-world analogy:** If a module is a book, a package is a bookshelf section. Your "Mathematics" shelf contains books on algebra, calculus, and geometry — all related, neatly grouped.
- In Python's bigger picture: The `os` module is actually part of a larger structure. `email`, `http`, `json` are packages. When you `pip install pandas`, you're installing a package.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **Package** | A directory containing Python modules and an optional `__init__.py` |
| **`__init__.py`** | Special file that marks a directory as a Python package; can expose shortcuts |
| **Sub-package** | A package nested inside another package |
| **`from package import module`** | Import a specific module from a package |
| **`from package.module import Class`** | Deep import: specify package → module → item |
| **Namespace package** | Package without `__init__.py` (Python 3.3+) |

---

## Syntax & Definition

```python
# Import a module from a package
from package_name import module_name

# Use the module
module_name.ClassName()

# Or import with full path
from package_name.module_name import ClassName

# Import everything exposed by __init__.py
from package_name import *
```

---

## Package Directory Structure

```
project/                    ← Project root
│
├── main.py                 ← Entry point (imports from packages)
│
├── utils/                  ← Package (folder of modules)
│   ├── __init__.py         ← Marks 'utils' as a package
│   ├── calculator.py       ← Module 1
│   ├── string_utils.py     ← Module 2
│   └── file_utils.py       ← Module 3
│
├── ml/                     ← Another package
│   ├── __init__.py
│   ├── models.py
│   └── preprocessing.py
│
└── .venv/                  ← Virtual environment (not a package)
```

---

## Detailed Explanation with Examples

### Example 1: Creating and Importing a Package

**Directory structure:**
```
project/
├── temp1.py
└── utils/
    └── calc.py      ← Same code as calculator.py
```

**File: `temp1.py`**
```python
# Without __init__.py — must specify full path
from utils import calc

calc_obj = calc.Calculator()
print(calc_obj.add(10, 20, 30))
calc.hello_world()
```

**Output:**
```
60
Hello from the calculator module!
```

**Line-by-line breakdown:**
- `from utils import calc` — Python goes into the `utils/` folder (the package) and imports the `calc.py` module from it.
- `calc.Calculator()` — `calc` is the module; `Calculator` is the class inside it.
- Without `__init__.py`, Python (3.3+) can still find the package, but the import path must include the module name explicitly.

---

### Example 2: Using `__init__.py` for Cleaner Imports

**File: `utils/__init__.py`**
```python
# Expose items directly from the package level
from .calc import Calculator, hello_world
```

**File: `temp2.py`**
```python
# Now import Calculator directly from utils — no need to specify 'calc'
from utils import Calculator, hello_world

calc_obj = Calculator()
print(calc_obj.add(5, 10, 15))
hello_world()
```

**Output:**
```
30
Hello from the calculator module!
```

**What `__init__.py` does here:**
- `from .calc import Calculator` — The `.` means "current directory" (relative import). This pulls `Calculator` from `calc.py` and exposes it at the `utils` package level.
- Now users of the package can write `from utils import Calculator` instead of `from utils.calc import Calculator` — simpler API.
- This is exactly how popular libraries work: `from sklearn.linear_model import LinearRegression` — you're using a `__init__.py` chain inside sklearn.

---

### Example 3: `from package import *`

**File: `utils/__init__.py`**
```python
from .calc import Calculator, hello_world
```

**File: `temp3.py`**
```python
from utils import *

# Both Calculator and hello_world are now available directly
calc_obj = Calculator()
print(calc_obj.add(1, 2, 3))
hello_world()
```

**Output:**
```
6
Hello from the calculator module!
```

> ⚠️ **Use `import *` sparingly.** It imports everything exposed by `__init__.py`, which can pollute your namespace and make it unclear where names come from. Prefer explicit imports when you know what you need.

**Performance implication:** `from utils import *` loads all 140 components if the module has 140. If you only need 2, use `from utils import Calculator, hello_world` — loads only what's needed.

---

### Example 4: `__init__.py` — Both Roles Explained

```python
# utils/__init__.py serves two roles:

# ROLE 1 (Historical — pre-Python 3.3):
# Simply existing as an empty file told Python "utils/ is a package, not just a folder"
# Required in Python < 3.3

# ROLE 2 (Modern — Python 3.3+):
# Create convenient import shortcuts by pre-importing items at the package level
from .calc import Calculator        # Shortcut
from .string_utils import StringHelper  # Shortcut
```

> **Best practice:** Even though `__init__.py` is not required in Python 3.3+, **always create it** when making packages. You'll see it in every professional open-source repository. It signals intent clearly and enables the shortcut-import pattern.

---

## Project Structure Best Practice

```
my_project/              ← Root directory (git repo)
│
├── main.py              ← Application entry point
├── .venv/               ← Virtual environment (never commit)
├── requirements.txt     ← Package dependencies
│
├── utils/               ← General utilities package
│   ├── __init__.py
│   ├── math_utils.py
│   └── file_utils.py
│
└── models/              ← Data model classes package
    ├── __init__.py
    ├── user.py
    └── product.py
```

**Rules:**
1. **Everything inside the project folder.** Never import from outside the project directory.
2. **One package per logical domain.** `utils/` for utilities, `models/` for data models.
3. **`__init__.py` in every package folder.**
4. **Virtual environment at project root** (`.venv/`), never committed to git.

---

## Common Mistakes & Debugging

### Mistake 1: Missing `__init__.py` in Python < 3.3

**Wrong:**
```
utils/
    calc.py     ← no __init__.py
```

```python
from utils import calc  # ImportError in Python < 3.3
```

**Correct:** Add `utils/__init__.py` (even empty).

---

### Mistake 2: Wrong `__init__.py` placement

**Wrong:**
```
__init__.py       ← In project root, not inside the package folder!
utils/
    calc.py
```

**Correct:**
```
utils/
    __init__.py   ← Inside the package folder
    calc.py
```

---

### Mistake 3: Importing package instead of module inside it

**Wrong:**
```python
import utils              # Imports the package object, not calc.py
calc_obj = utils.Calculator()  # AttributeError — Calculator not directly in utils
```

**Correct (without __init__.py shortcut):**
```python
from utils import calc
calc_obj = calc.Calculator()
```

**Correct (with __init__.py shortcut):**
```python
from utils import Calculator
calc_obj = Calculator()
```

---

## Quick-Reference Cheat Sheet

```
PACKAGES CHEAT SHEET
========================
Package = folder + __init__.py (or just folder in Python 3.3+)

# Import module from package
from package_name import module_name
module_name.Class()

# Import class directly (if __init__.py exposes it)
from package_name import ClassName
obj = ClassName()

# Import everything from package
from package_name import *

# __init__.py inside package — expose shortcuts
from .module_name import ClassName, function_name

# Relative import (inside __init__.py)
from .module    → current directory
from ..module   → parent directory

# Project structure rule
Keep ALL project files inside the project folder.
Never reference files outside the project directory.

Gotchas:
- __init__.py MUST be inside the package folder, not the parent
- import * loads everything — use explicit imports when possible
- Python 3.3+ supports packages without __init__.py (namespace packages)
  but always add it for clarity and the shortcut feature
```

---

---

# PART 6: Library vs Module vs Package

---

## Concept Overview

One of the most commonly confused distinctions in Python. Here's the definitive hierarchy:

```
Library
  └── Package(s)
        └── Module(s)  (.py files)
              └── Classes, Functions, Variables
```

---

## Comparison Table

| Concept | What it is | Example | Where it lives |
|---------|-----------|---------|----------------|
| **Module** | A single `.py` file | `calculator.py`, `os.py`, `time.py` | Any directory |
| **Package** | A folder of related modules + `__init__.py` | `utils/`, `sklearn/linear_model/` | Project directory |
| **Library** | A bundle of packages published for public/private reuse | `pandas`, `numpy`, `scikit-learn` | PyPI or private repo |

---

## Detailed Explanation

### Module
```
calculator.py        ← This is a module
```
A single Python file. Created by you for a specific purpose. Importable.

---

### Package
```
utils/               ← This is a package
├── __init__.py
├── calculator.py    ← Module inside a package
└── string_utils.py  ← Module inside a package
```
A folder containing related modules. Created by you to organize multiple related modules.

---

### Library

A library is a **published collection of packages** — made available for anyone (or your team) to install and use. Libraries live in **PyPI** (Python Package Index) — the central repository at [pypi.org](https://pypi.org).

```bash
pip install pandas       # Download pandas library from PyPI
pip install scikit-learn # Download scikit-learn library from PyPI
```

After installation, the library files live in your `.venv/lib/site-packages/` directory:
```
.venv/
└── lib/
    └── site-packages/
        ├── pandas/           ← pandas library (multiple packages)
        │   ├── __init__.py
        │   ├── core/         ← sub-package
        │   └── io/           ← sub-package
        └── sklearn/          ← scikit-learn library
            ├── __init__.py
            └── linear_model/ ← sub-package
```

---

### Real-World Analogy (Classroom Example)

```
100 students, 3 groups:
- Group 1 (1–33)  → Built 'AttendanceSys' package (att_sys/)
- Group 2 (34–66) → Built 'NotesSummarizer' package (nss/)
- Group 3 (67–100)→ Built 'ChatBot' package (cbot/)

All three packages combined → Published as library 'KNDS2' on PyPI

Anyone can now: pip install knds2
```

---

## Quick-Reference Cheat Sheet

```
LIBRARY vs MODULE vs PACKAGE
=============================
Module  = single .py file
Package = folder of modules + __init__.py
Library = bundle of packages, published to PyPI

Workflow:
1. Write functions/classes → put in .py file → Module
2. Group related modules in a folder → add __init__.py → Package
3. Bundle packages for distribution → publish to PyPI → Library

Install a library:    pip install library_name
                      uv add library_name

Use a library module: from sklearn.linear_model import LinearRegression
Use your package:     from utils import Calculator
Use your module:      import calculator
```

---

---

# PART 7: Assignment — Payment Processing System

---

## Assignment Requirements

Build a **payment processing system for a store** using Python, following OOP principles.

### System Behavior

When run (`python main.py`), the terminal should display:

```
Welcome to Payment System
Press 1 — Add cash to system (via Credit Card or UPI)
Press 2 — Refund money
Press 3 — Check balance
Press 4 — View all transactions
Press 5 — Exit
```

### Functional Requirements

| Option | Behavior |
|--------|---------|
| **1 — Add Cash** | Ask: credit card or UPI (1/2). Accept dummy card/UPI ID + amount. Add to system balance. |
| **2 — Refund** | Accept refund amount. Subtract from system balance. Log as a transaction. |
| **3 — Check Balance** | Print current system balance. |
| **4 — View Transactions** | Print all past transactions in clean format (not raw dict/list dump). |
| **5 — Exit** | Exit gracefully. |

### Technical Requirements

```
✅ OOP — classes for PaymentSystem, CreditCard, UPI
✅ Polymorphism — CreditCard and UPI both have .process_payment() method
✅ Abstraction (ABC) — enforce polymorphism via abstract base class
✅ Encapsulation — balance should be private
✅ While loop — system keeps running until user presses 5
✅ User input with typecasting — input() returns str; convert as needed
✅ Exception handling — handle invalid input, insufficient funds
✅ History/Transactions — similar to Calculator history pattern from class
✅ F-strings — for clean terminal output
✅ time module — timestamp each transaction
✅ Modules/Packages (Optional but recommended) — separate logic from main.py
```

### Suggested Class Structure

```python
from abc import ABC, abstractmethod
import time

class PaymentMethod(ABC):
    @abstractmethod
    def process_payment(self, amount):
        pass

class CreditCard(PaymentMethod):
    def __init__(self, card_number):
        self.card_number = card_number

    def process_payment(self, amount):
        # Process credit card payment logic
        pass

class UPI(PaymentMethod):
    def __init__(self, upi_id):
        self.upi_id = upi_id

    def process_payment(self, amount):
        # Process UPI payment logic
        pass

class PaymentSystem:
    def __init__(self):
        self.__balance      = 0.0    # Private
        self.__transactions = []     # Private

    def add_cash(self, payment_method, amount): ...
    def refund(self, amount): ...
    def check_balance(self): ...
    def get_history(self): ...
```

---

---

# Comprehensive Summary (TL;DR)

| Topic | Key Takeaway |
|-------|-------------|
| `time.time()` | Seconds since Jan 1, 1970 as float. Use `t2 - t1` (never `t1 - t2`) |
| `time.perf_counter()` | Higher precision than `time.time()`. Use for micro-benchmarking |
| `time.sleep(n)` | Pauses execution for n seconds. Blocks the thread |
| `divmod(sec, 3600)` | Returns `(hours, remainder)`. Chain with `divmod(remainder, 60)` for M:S |
| **F-strings** | `f"text {var:.2f}"` — fastest, most readable string formatting in Python 3.6+ |
| F-string formatting | `:.2f` = 2 decimal places; `:.2%` = percentage; `:>10` = right-align width 10 |
| **Polymorphism** | Same method name across classes → iterate objects with one loop |
| `ABC + @abstractmethod` | Enforces polymorphic contract — prevents incorrect implementations |
| **Module** | A `.py` file you `import`. Separates reusable code from main logic |
| **Package** | A folder of modules + `__init__.py`. Groups related modules |
| **Library** | Bundle of packages published to PyPI (`pip install`) |
| `__init__.py` | Marks folder as package; creates import shortcuts via `from .module import X` |

---

## Flashcard-Style Recall (Q&A pairs)

- **Q: What does `time.time()` return?**
  **A:** A float representing the total number of seconds elapsed since January 1, 1970 (Unix Epoch).

- **Q: Why use `time.perf_counter()` over `time.time()` for benchmarking?**
  **A:** `perf_counter()` has higher precision (more decimal places) and is guaranteed to be monotonic, making it better for measuring short durations.

- **Q: How do you correctly measure elapsed time?**
  **A:** `t1 = time.time()` before, `t2 = time.time()` after, then `elapsed = t2 - t1`. Always latest minus older.

- **Q: What is an F-string and how do you create one?**
  **A:** A formatted string literal. Prefix a string with `f` and embed expressions in `{}`: `f"Value is {x:.2f}"`.

- **Q: What does `f"{accuracy:.2%}"` do?**
  **A:** Multiplies `accuracy` by 100, formats to 2 decimal places, and appends `%`. E.g., `0.8745` → `"87.45%"`.

- **Q: What is polymorphism?**
  **A:** The ability of different objects to respond to the same method name, each with its own specific implementation.

- **Q: What is the difference between polymorphism and abstraction?**
  **A:** Polymorphism is the pattern (same method name, different behavior). Abstraction (ABC) is an optional enforcement tool that forces subclasses to implement the method.

- **Q: What is a Python module?**
  **A:** A `.py` file containing Python code (functions, classes, variables) that can be imported by other files.

- **Q: What is a Python package?**
  **A:** A directory/folder containing one or more related modules, typically with an `__init__.py` file.

- **Q: What is `__init__.py` and why is it important?**
  **A:** A special file inside a package directory. It marks the folder as a Python package and can expose shortcut imports (e.g., `from .module import Class`).

- **Q: What is the difference between a module, package, and library?**
  **A:** Module = single `.py` file. Package = folder of modules. Library = bundle of packages published to PyPI.

- **Q: What error occurs when you try to instantiate an abstract class that doesn't implement all abstract methods?**
  **A:** `TypeError: Can't instantiate abstract class ClassName with abstract method method_name`

- **Q: What does `from utils import *` do?**
  **A:** Imports everything exposed by `utils/__init__.py` into the current namespace.

- **Q: Can you have polymorphism without inheritance in Python?**
  **A:** Yes — through duck typing. Any object with the expected method name can participate in polymorphic behavior, regardless of its class hierarchy.
