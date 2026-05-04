# Python Day 8 — Variable Scope, *args/**kwargs, Libraries/PIP/PyPI, OS Library & File Operations

---

## Concept Overview

This session marks the **transition from Python Basics to Intermediate Python**. It covers five interconnected pillars:

1. **Variable Scope (Global vs Local)** — Understanding where variables live, how Python isolates function memory, and why some variables are accessible everywhere while others vanish after a function ends.
2. **Function Arguments (*args & **kwargs)** — Handling dynamic, flexible function inputs using tuple-packing (`*args`) and dictionary-packing (`**kwargs`).
3. **Libraries, PIP & PyPI** — The ecosystem that lets you leverage code written by millions of developers worldwide.
4. **OS Library** — Navigating your operating system's file structure programmatically.
5. **File Operations** — Reading, writing, and managing external files from Python code.

**Why this matters:** Every real-world Python application reads files, uses libraries, and organizes code into functions with flexible arguments. Without understanding scope, your functions will produce mysterious bugs. Without file operations, your programs can't persist or consume data.

**Real-world analogy:** Think of Python as a company. Variables are employees. **Global scope** is the open-plan office — everyone can see those employees. **Functions** are private meeting rooms — employees (local variables) created inside can't be seen from outside, and once the meeting ends, those employees leave. **Libraries** are consulting firms you hire (install) to do specialized work. **File operations** are the mail room — sending and receiving documents from the outside world.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **Scope** | The region of code where a variable is accessible |
| **Global Variable** | A variable defined at the top level of a script, accessible throughout the file |
| **Local Variable** | A variable defined inside a function, accessible only within that function |
| **`global` keyword** | Declares that a variable inside a function refers to the global version |
| **`*args`** | Collects extra positional arguments into a **tuple** |
| **`**kwargs`** | Collects extra keyword arguments into a **dictionary** |
| **Library/Package** | A collection of reusable Python code (functions, classes, modules) |
| **PIP** | The tool (command-line) used to install Python packages |
| **PyPI** | Python Package Index — the online warehouse hosting 700K+ libraries |
| **`os` module** | Built-in library for interacting with the operating system |
| **CWD** | Current Working Directory — the folder your script is running from |
| **File pointer/cursor** | Tracks the current read position within an opened file |
| **Context Manager** | The `with` statement that auto-manages resource cleanup (e.g., closing files) |
| **Absolute Path** | Full path from root directory (e.g., `C:\Users\project\data.txt`) |
| **Relative Path** | Path relative to current working directory (e.g., `data/file.txt`) |

---

# PART 1: Variable Scope (Global vs Local)

---

## Syntax & Definition

```python
# Global variable — accessible everywhere in this file
counter = 0

def increment():
    # Local variable — only exists inside this function
    local_counter = 0
    local_counter += 1
    print(f"Local: {local_counter}")

# Using the global keyword to modify a global variable inside a function
def increment_global():
    global counter
    counter += 1
    print(f"Global: {counter}")
```

- **Global variables** are declared at the top level (not inside any function)
- **Local variables** are declared inside a function and die when the function ends
- The **`global`** keyword tells Python: "I want to use the variable from outside, not create a new local one"

---

## Detailed Explanation with Examples

### Example 1: The UnboundLocalError Problem

```python
a = 0  # Global variable

def add():
    a += 1       # Trying to modify 'a' — but which 'a'?
    print(a)

print(a)  # Works fine — prints global 'a'
add()     # ERROR!
```

**Error:**
```
UnboundLocalError: cannot access local variable 'a' where it is not associated with a value
```

**Line-by-line breakdown:**
- Line 1: Creates global variable `a` with value `0`
- Line 3: Defines function `add`
- Line 4: `a += 1` is equivalent to `a = a + 1`. Python sees an assignment to `a`, so it treats `a` as a **local** variable. But `a` hasn't been initialized locally yet — hence the error
- Line 5: Would print the local `a` (if it existed)
- Line 7: Prints global `a` → outputs `0` (this works fine)
- Line 8: Calls `add()` → **crashes** because local `a` was never initialized

**Why this fails:** When Python compiles a function, it scans for assignments. If it finds `a = ...` anywhere in the function, it marks `a` as local to that function. Then at runtime, `a += 1` tries to read `a` before it's been assigned locally — triggering `UnboundLocalError`.

> **Pro Tip:** Python decides whether a variable is local or global at **compile time** (when it reads the function definition), not at runtime. This is a critical distinction!

---

### Example 2: Separate Memory Spaces — Global vs Local

```python
a = 0  # Global variable

def add():
    a = 2          # Local variable (different from global 'a')
    a += 1         # Modify local 'a': 2 + 1 = 3
    print(f"Inside function: {a}")  # Prints 3

print(f"Before function call: {a}")  # Prints 0 (global)
add()                                 # Prints 3 (local)
print(f"After function call: {a}")    # Prints 0 (global unchanged!)
```

**Output:**
```
Before function call: 0
Inside function: 3
After function call: 0
```

**Why this works:** Python maintains **separate memory spaces**:

```
RAM Layout:
┌─────────────────────────────┐
│  GLOBAL MEMORY              │
│  ┌───────────────────────┐  │
│  │ a = 0                 │  │
│  └───────────────────────┘  │
│                             │
│  FUNCTION: add()            │
│  ┌───────────────────────┐  │
│  │ a = 2  (LOCAL)        │  │
│  │ a = 3  (after +=1)    │  │
│  └───────────────────────┘  │
│  ↑ This block is DESTROYED  │
│    when add() finishes      │
└─────────────────────────────┘
```

- Global `a` lives at one memory address
- Local `a` (inside `add()`) lives at a completely different memory address
- They just happen to share the same **name** — Python identifies them by their **address**, not their name
- When `add()` finishes, the local memory block is destroyed

---

### Example 3: Using the `global` Keyword

```python
a = 0  # Global variable

def add():
    global a       # "I want to use the GLOBAL 'a', not create a local one"
    a += 1         # Now modifies the global 'a'
    print(f"Inside function: {a}")

print(f"Before: {a}")   # 0
add()                     # Modifies global 'a' to 1
print(f"After: {a}")     # 1 (global was changed!)
```

**Output:**
```
Before: 0
Inside function: 1
After: 1
```

**Line-by-line breakdown:**
- Line 1: Global `a = 0`
- Line 4: `global a` tells the function: "Don't create a local `a`. Use the one from outside."
- Line 5: `a += 1` now increments the **global** `a` from 0 to 1
- Line 8: Prints `0` (before the function modifies it)
- Line 9: Calls `add()`, which changes global `a` to `1` and prints it
- Line 10: Prints `1` — the global was permanently modified

> **Warning:** Overusing `global` makes code hard to debug and maintain. Functions should ideally communicate through **parameters and return values**, not by mutating global state.

---

### Example 4: Mutable Objects (Lists) Behave Differently

```python
a = [0, 1, 2]  # Global list (mutable)

def add():
    a.append(123)  # Modifying in-place — NO error!
    print(f"Inside function: {a}")

print(f"Before: {a}")    # [0, 1, 2]
add()                      # [0, 1, 2, 123]
print(f"After: {a}")      # [0, 1, 2, 123] — Global was modified!
```

**Output:**
```
Before: [0, 1, 2]
Inside function: [0, 1, 2, 123]
After: [0, 1, 2, 123]
```

**Why lists work without `global`:**

This is one of the most confusing parts of Python scope. Here's the key insight:

- **Immutable types** (int, float, str, tuple): Any operation like `a = a + 1` creates a **new object** at a new address → Python treats `a` as local
- **Mutable types** (list, dict, set): Operations like `a.append(123)` **modify the existing object in-place** → the address doesn't change → Python doesn't see an assignment → it looks up the global `a`

```
Immutable (int):        Mutable (list):
a = 0                   a = [0, 1, 2]
a += 1  → NEW object    a.append(3)  → SAME object
        → NEW address              → SAME address
        → Python: "local!"         → Python: "global!"
```

> **Critical Rule:** If you **reassign** a mutable inside a function (e.g., `a = [99]`), it becomes local. Only **in-place modifications** (`.append()`, `.extend()`, `a[0] = 5`) access the global.

```python
a = [0, 1, 2]

def add():
    a = [99]  # This is REASSIGNMENT → creates a LOCAL 'a'
    print(f"Inside: {a}")  # [99] (local)

add()
print(f"Outside: {a}")    # [0, 1, 2] (global unchanged)
```

---

## How Variable Scope Works Internally (LEGB Rule)

Python resolves variable names using the **LEGB rule** — it searches in this order:

```
L → Local:      Variables inside the current function
E → Enclosing:  Variables in enclosing (outer) functions (for nested functions)
G → Global:     Variables at the module/file level
B → Built-in:   Python's built-in names (print, len, range, etc.)
```

```
┌───────────────────────────────────────┐
│ BUILT-IN (print, len, range...)       │
│ ┌───────────────────────────────────┐ │
│ │ GLOBAL (module-level variables)   │ │
│ │ ┌───────────────────────────────┐ │ │
│ │ │ ENCLOSING (outer function)    │ │ │
│ │ │ ┌───────────────────────────┐ │ │ │
│ │ │ │ LOCAL (current function)  │ │ │ │
│ │ │ └───────────────────────────┘ │ │ │
│ │ └───────────────────────────────┘ │ │
│ └───────────────────────────────────┘ │
└───────────────────────────────────────┘
         ↑ Python searches inward → outward
```

---

## Common Mistakes & Debugging

### Mistake 1: Forgetting `global` When Modifying Primitives

**Wrong:**
```python
count = 0

def increment():
    count += 1  # UnboundLocalError!

increment()
```

**Error:**
```
UnboundLocalError: cannot access local variable 'count' where it is not associated with a value
```

**Why it fails:** `count += 1` is syntactic sugar for `count = count + 1`. The assignment makes Python treat `count` as local, but local `count` doesn't exist yet.

**Correct:**
```python
count = 0

def increment():
    global count
    count += 1

increment()
print(count)  # 1
```

### Mistake 2: Assuming `print` Inside a Function Means It's Local

**Wrong assumption:**
```python
a = 10

def show():
    print(a)  # This WORKS — reading global 'a' is fine

show()  # 10
```

**Why this works:** Python only creates a local variable if there's an **assignment** to it inside the function. Simply **reading** a global variable is allowed.

---

## Best Practices & Pythonic Way

```python
# ❌ BAD: Overusing global variables
counter = 0
def increment():
    global counter
    counter += 1

# ✅ GOOD: Pass values in, return values out
def increment(counter):
    return counter + 1

counter = 0
counter = increment(counter)  # Explicit, traceable
```

- **Avoid `global`** in production code — it creates hidden dependencies
- **Pass data as parameters**, return results explicitly
- If you need shared state, use **classes** (covered later)
- Local variables are **faster** than global ones (Python optimizes local lookups)

---

# PART 2: Function Arguments — *args and **kwargs

---

## Concept Overview

What if you want a function that can accept **any number of arguments**? You can't define `def add(a, b, c, d, ...)` for every possibility. Python solves this with:

- **`*args`** — Catches unlimited positional arguments as a **tuple**
- **`**kwargs`** — Catches unlimited keyword arguments as a **dictionary**

**Real-world analogy:** A pizza ordering system. The **size** is mandatory (fixed argument). **Toppings** can be any number — pepperoni, mushrooms, olives, etc. (`*args`). **Extra details** are key-value pairs — crust type = "thin", extra cheese = True (`**kwargs`).

---

## Syntax & Definition

```python
def function_name(regular_arg, default_arg=value, *args, **kwargs):
    # regular_arg  → mandatory positional argument
    # default_arg  → optional with default value
    # *args        → tuple of extra positional arguments
    # **kwargs     → dictionary of extra keyword arguments
    pass
```

**Argument Order (MANDATORY):**

```
1. Without default  →  2. With default  →  3. *args  →  4. **kwargs
```

Violating this order causes a `SyntaxError`.

---

## Detailed Explanation with Examples

### Example 1: Basic *args — Dynamic Addition

```python
def add(*numbers):
    """Add any number of values together."""
    total = 0
    for num in numbers:
        total += num
    print(f"Sum: {total}")
    return total

add(1, 2, 3, 4, 5, 6)   # Sum: 21
add(1)                      # Sum: 1
add(10, 20.5, 30)           # Sum: 60.5
```

**Output:**
```
Sum: 21
Sum: 1
Sum: 60.5
```

**Line-by-line breakdown:**
- Line 1: `*numbers` means "catch all positional arguments and pack them into a tuple called `numbers`"
- Line 3: Initialize `total = 0`
- Line 4-5: Iterate through the tuple and accumulate the sum
- Line 9: When called with `1, 2, 3, 4, 5, 6`, internally `numbers = (1, 2, 3, 4, 5, 6)`

**What `*args` actually does:**

```
Call: add(1, 2, 3, 4, 5, 6)

Python sees *numbers in the definition
  → Creates: numbers = (1, 2, 3, 4, 5, 6)  [a tuple]

Call: add(1)
  → Creates: numbers = (1,)  [tuple with one element]

Call: add()
  → Creates: numbers = ()  [empty tuple]
```

> **Pro Tip:** The name `args` is just a convention. You can write `*values`, `*items`, `*data` — the asterisk `*` is what matters.

---

### Example 2: Mixing Regular Args with *args

```python
def add_v2(a, b, *more_values):
    """First two arguments go to a and b; rest go to more_values tuple."""
    print(f"a = {a} (type: {type(a).__name__})")
    print(f"b = {b} (type: {type(b).__name__})")
    print("=" * 40)
    print(f"more_values = {more_values} (type: {type(more_values).__name__})")

add_v2(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
```

**Output:**
```
a = 1 (type: int)
b = 2 (type: int)
========================================
more_values = (3, 4, 5, 6, 7, 8, 9, 10) (type: tuple)
```

**How Python distributes the arguments:**

```
Call: add_v2(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)

Step 1: a = 1          (first positional → first parameter)
Step 2: b = 2          (second positional → second parameter)
Step 3: more_values = (3, 4, 5, 6, 7, 8, 9, 10)  (remaining → *args tuple)
```

---

### Example 3: **kwargs — Keyword Arguments as Dictionary

```python
def add_v3(a, b=0, *args, **kwargs):
    """Demonstrates all argument types together."""
    print(f"a = {a}")
    print(f"b = {b}")
    print(f"args = {args}")
    print(f"kwargs = {kwargs}")

add_v3(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, value_1=100, value_2=200)
```

**Output:**
```
a = 1
b = 2
args = (3, 4, 5, 6, 7, 8, 9, 10)
kwargs = {'value_1': 100, 'value_2': 200}
```

**Line-by-line breakdown:**
- `a = 1` → First positional goes to `a`
- `b = 2` → Second positional goes to `b` (overrides default of 0)
- `args = (3, 4, 5, 6, 7, 8, 9, 10)` → Remaining positional values packed as tuple
- `kwargs = {'value_1': 100, 'value_2': 200}` → `key=value` pairs packed as dictionary

**How `**kwargs` works:**

```
When Python sees: value_1=100, value_2=200

It creates a dictionary:
kwargs = {
    'value_1': 100,
    'value_2': 200
}
```

> **Pro Tip:** `kwargs` is just convention. You can write `**details`, `**options`, `**config` — the double asterisk `**` is what matters.

---

### Example 4: Real-World — Pizza Ordering System

```python
def order_pizza(size=5, *toppings, **details):
    """
    Place a pizza order with flexible options.
    
    Args:
        size: Pizza size (5-11 inches), default 5
        *toppings: Any number of topping strings
        **details: Extra key-value options (crust, cheese, etc.)
    """
    print(f"Pizza Size: {size} inches")
    print(f"Toppings: {', '.join(toppings) if toppings else 'None'}")
    print(f"Extra Details: {details if details else 'None'}")
    print("-" * 40)

# Order 1: Full customization
order_pizza(11, "pepperoni", "olives", "mushrooms",
            crust="thin", extra_cheese=True)

# Order 2: Just size and toppings
order_pizza(8, "pepperoni", "onions")

# Order 3: Default size, no toppings, only details
order_pizza(crust="stuffed", extra_cheese=False)
```

**Output:**
```
Pizza Size: 11 inches
Toppings: pepperoni, olives, mushrooms
Extra Details: {'crust': 'thin', 'extra_cheese': True}
----------------------------------------
Pizza Size: 8 inches
Toppings: pepperoni, onions
Extra Details: None
----------------------------------------
Pizza Size: 5 inches
Toppings: None
Extra Details: {'crust': 'stuffed', 'extra_cheese': False}
----------------------------------------
```

---

### Example 5: Named Arguments Override Position

```python
def show(a=0, b=0, c=0, d=0):
    print(a, b, c, d)

show(1, c=3)  # a=1 (positional), b=0 (default), c=3 (named), d=0 (default)
```

**Output:**
```
1 0 3 0
```

**Why this works:** You can skip arguments by **naming** the ones you want to set. Python matches by name, not position, for keyword arguments.

---

## Comparison Table

| Feature | `*args` | `**kwargs` |
|---------|---------|------------|
| **Collects** | Extra positional arguments | Extra keyword (key=value) arguments |
| **Data type** | `tuple` | `dict` |
| **Syntax in definition** | `*args` (single asterisk) | `**kwargs` (double asterisk) |
| **How to pass values** | `func(1, 2, 3)` | `func(x=1, y=2)` |
| **Iteration** | `for item in args` | `for key, val in kwargs.items()` |
| **Position in signature** | After regular & default args | Always last |
| **Name matters?** | No (`*anything` works) | No (`**anything` works) |

---

## Argument Order — Complete Hierarchy

```
def func(positional, default=val, *args, **kwargs):
         ─────────  ───────────  ─────  ────────
            1st         2nd       3rd     4th
         REQUIRED    OPTIONAL   DYNAMIC  KEYWORD
```

**Violating the order causes errors:**

```python
# ❌ SyntaxError: non-default argument follows default argument
def bad_func(a=0, b):
    pass

# ❌ SyntaxError: arguments cannot follow **kwargs
def bad_func(**kwargs, a):
    pass
```

---

## Common Mistakes & Debugging

### Mistake 1: Placing Regular Args After *args

**Wrong:**
```python
def func(*args, b):
    print(args, b)

func(1, 2, 3)  # TypeError!
```

**Error:**
```
TypeError: func() missing 1 required keyword-only argument: 'b'
```

**Why it fails:** After `*args`, all remaining parameters become **keyword-only** — they must be passed by name.

**Correct:**
```python
func(1, 2, b=3)  # args=(1, 2), b=3
```

### Mistake 2: Passing Positional After Keyword

**Wrong:**
```python
def func(a, b, c):
    pass

func(a=1, 2, 3)  # SyntaxError!
```

**Error:**
```
SyntaxError: positional argument follows keyword argument
```

**Correct:**
```python
func(1, 2, 3)         # All positional
func(1, b=2, c=3)     # Mixed (positional first, then keyword)
func(a=1, b=2, c=3)   # All keyword
```

---

# PART 3: Libraries, PIP & PyPI

---

## Concept Overview

A **library** is a collection of pre-written Python code (functions, classes, modules) that you can reuse. Instead of writing everything from scratch, you install libraries that solve common problems.

**Real-world analogy (Play Store model):**

| Real World | Python Equivalent |
|-----------|-------------------|
| **App Store / Play Store** | **PyPI** (pypi.org) — the warehouse of all libraries |
| **Individual Apps** | **Libraries** — packages of reusable code |
| **Download Button** | **PIP** — the tool that downloads and installs libraries |
| **Your Phone** | **Your Python environment** — where installed libraries live |

---

## Key Concepts

### What Is a Library?

A library is one or more Python files containing reusable code. Example:

```
todo_library/
├── __init__.py
├── add.py        # Function to add tasks
├── remove.py     # Function to remove tasks
├── edit.py       # Function to edit tasks
└── remind.py     # Function to set reminders
```

You name it, upload it to **PyPI**, and anyone in the world can install it.

### What Is PyPI?

- **Python Package Index** — [pypi.org](https://pypi.org)
- Hosts **730,000+** projects as of 2026
- Free, open platform where developers upload libraries
- Anyone can search, browse, and download

### What Is PIP?

- **P**ip **I**nstalls **P**ackages
- Command-line tool that downloads libraries from PyPI to your environment
- Comes bundled with Python

### Essential PIP Commands

```bash
# Activate your virtual environment first!
# Windows:
path\to\venv\Scripts\activate
# Mac/Linux:
source path/to/venv/bin/activate

# See all installed libraries in current environment
pip freeze

# If using UV as package manager
uv pip freeze

# Install a library
pip install library_name

# Install a specific version
pip install library_name==2.1.0

# Uninstall a library
pip uninstall library_name
```

### Standard Library vs Third-Party

| Type | Needs Installation? | Examples |
|------|-------------------|----------|
| **Standard Library** | No — comes with Python | `os`, `sys`, `math`, `json`, `datetime` |
| **Third-Party Library** | Yes — install via pip | `numpy`, `pandas`, `requests`, `flask` |

---

# PART 4: OS Library — Navigating the File System

---

## Concept Overview

The **`os`** module lets you interact with your operating system from Python code. It replaces manual actions like clicking through folders, checking file locations, and navigating directories.

**Real-world analogy:** The `os` library is like having a remote control for your file explorer. Instead of clicking with a mouse, you type commands.

---

## Syntax & Key Methods

```python
import os  # Load the OS library into memory

# 1. Get Current Working Directory
os.getcwd()

# 2. List Files/Folders in a Directory
os.listdir()                    # Current directory
os.listdir("/path/to/folder")   # Specific directory

# 3. Join Paths (cross-platform)
os.path.join("folder", "subfolder", "file.txt")
```

---

## Detailed Explanation with Examples

### Example 1: Where Am I? — `os.getcwd()`

```python
import os

current_location = os.getcwd()
print(current_location)
```

**Output (Windows):**
```
C:\Users\monal\work\lectures\31012026
```

**Output (Mac/Linux):**
```
/Users/monal/work/lectures/31012026
```

**What it does:** Returns the **absolute path** of the directory where your Python script is running.

---

### Example 2: What's Here? — `os.listdir()`

```python
import os

# List files in current directory
files = os.listdir()
print(files)

# List files in a specific directory
lecture_files = os.listdir("C:/Users/monal/work/lectures")
print(lecture_files)
```

**Output:**
```
['dummy.txt', 'temp', 'class_notebook.ipynb']
['17012026', '18012026', '21012026', '24012026', '25012026', '31012026']
```

> **Pro Tip:** Always use **forward slashes** (`/`) in paths — they work on Windows, Mac, and Linux. Backslashes (`\`) only work on Windows and cause issues as escape characters.

---

### Example 3: Cross-Platform Paths — `os.path.join()`

```python
import os

# ❌ FRAGILE: Hardcoding slashes (breaks across OS)
path_windows = os.getcwd() + "\\temp\\dummy_2.txt"
path_linux = os.getcwd() + "/temp/dummy_2.txt"

# ✅ ROBUST: os.path.join handles slashes automatically
path = os.path.join(os.getcwd(), "temp", "dummy_2.txt")
print(path)
```

**Output (Windows):**
```
C:\Users\monal\work\lectures\31012026\temp\dummy_2.txt
```

**Output (Mac/Linux):**
```
/Users/monal/work/lectures/31012026/temp/dummy_2.txt
```

**Why `os.path.join` is essential:**
- Automatically uses `\` on Windows, `/` on Mac/Linux
- Same code works everywhere — no OS-specific logic needed
- Handles edge cases (trailing slashes, empty strings)

---

### Example 4: Flexible Path Construction (Best Practice)

```python
import os

folder_name = "temp"
file_name = "dummy_2.txt"

full_path = os.path.join(os.getcwd(), folder_name, file_name)
print(full_path)

# Changing the file? Just update the variable:
file_name = "dummy_3.txt"
full_path = os.path.join(os.getcwd(), folder_name, file_name)
print(full_path)  # Automatically uses new filename
```

**Why this matters:** Using variables instead of hardcoded strings makes code **flexible and maintainable**. Change one variable → the entire path updates.

---

## Absolute vs Relative Paths

| Type | Description | Example |
|------|-------------|---------|
| **Absolute** | Full path from the root of the filesystem | `C:/Users/monal/project/data.txt` |
| **Relative** | Path from the current working directory | `data/data.txt` |

```python
import os

# Relative path (from current directory)
relative = "temp/dummy_2.txt"

# Absolute path (from root)
absolute = os.path.join(os.getcwd(), "temp", "dummy_2.txt")

# Both work with open():
f1 = open(relative)    # Relative
f2 = open(absolute)    # Absolute
```

> **Best Practice:** Use `os.path.join()` to build absolute paths programmatically. Hardcoded absolute paths break when code moves to a different machine.

---

# PART 5: File Operations

---

## Concept Overview

**File operations** let you read, write, and manage files on disk from Python. This is how programs persist data, consume input, and produce output beyond the console.

**Real-world analogy:** Think of file operations like using Notepad:
- **Open** → Double-click a file to open it
- **Read** → View the contents
- **Write** → Type new content
- **Append** → Add text at the end
- **Close** → Close the file to free resources

---

## Syntax & Definition

```python
# Method 1: Manual open/close
f = open("filename.txt", mode)
content = f.read()
f.close()  # DON'T FORGET!

# Method 2: Context Manager (recommended)
with open("filename.txt", mode) as f:
    content = f.read()
# File is automatically closed here
```

**Common Modes:**

| Mode | Description |
|------|-------------|
| `"r"` | Read (default) — file must exist |
| `"w"` | Write — creates new or **overwrites** existing |
| `"a"` | Append — adds to end of file |
| `"r+"` | Read and write |
| `"x"` | Create — fails if file already exists |

---

## Detailed Explanation with Examples

### Example 1: Reading an Entire File — `f.read()`

Suppose `dummy.txt` contains:
```
how to learn
python basics
for machine learning
store information
```

```python
# Open the file
f = open("dummy.txt")

# Read ALL contents at once as a single string
content = f.read()
print(content)
print(type(content))  # <class 'str'>

# MUST close when done
f.close()
```

**Output:**
```
how to learn
python basics
for machine learning
store information
<class 'str'>
```

**What `f.read()` returns:** One big string with `\n` (newline characters) between lines:
```python
'how to learn\npython basics\nfor machine learning\nstore information'
```

---

### Example 2: The File Pointer Problem

```python
f = open("dummy.txt")

first_read = f.read()   # Reads everything, pointer moves to END
second_read = f.read()   # Pointer is at END — nothing left to read!

print(f"First: {first_read}")
print(f"Second: '{second_read}'")  # Empty string!

f.close()
```

**Output:**
```
First: how to learn
python basics
for machine learning
store information
Second: ''
```

**Why the second read is empty:**

```
Before f.read():
┌────────────────────────────────────────┐
│ how to learn\npython basics\nfor...    │
│↑                                       │
│ POINTER starts here                    │
└────────────────────────────────────────┘

After first f.read():
┌────────────────────────────────────────┐
│ how to learn\npython basics\nfor...    │
│                                       ↑│
│                          POINTER here  │
└────────────────────────────────────────┘

Second f.read():
  → Pointer is at the end
  → Nothing left to read
  → Returns empty string ""
```

**Solution: Save the result to a variable:**
```python
f = open("dummy.txt")
content = f.read()  # Read once, save to variable
f.close()

# Now use 'content' as many times as you want
lines = content.split("\n")  # Split by newlines
print(lines)  # ['how to learn', 'python basics', 'for machine learning', 'store information']
```

---

### Example 3: Reading Line by Line — `f.readlines()`

```python
f = open("dummy.txt")
lines = f.readlines()  # Returns a LIST of lines
f.close()

print(lines)
```

**Output:**
```
['how to learn\n', 'python basics\n', 'for machine learning\n', 'store information']
```

**Key observations:**
- Returns a **list** where each element is one line
- Each line includes the `\n` newline character (except possibly the last line)
- Use `.strip()` to remove `\n`:

```python
clean_lines = [line.strip() for line in lines]
print(clean_lines)
# ['how to learn', 'python basics', 'for machine learning', 'store information']
```

---

### Example 4: One Line at a Time — `f.readline()`

```python
f = open("dummy.txt")

line1 = f.readline()  # Reads line 1, pointer → line 2
line2 = f.readline()  # Reads line 2, pointer → line 3
line3 = f.readline()  # Reads line 3, pointer → line 4

print(line1.strip())  # how to learn
print(line2.strip())  # python basics
print(line3.strip())  # for machine learning

f.close()
```

**When to use `readline()`:**
- When the file is **too large** to fit in memory (e.g., 12GB text file)
- `f.read()` and `f.readlines()` load the **entire file** into RAM
- `f.readline()` loads **one line** at a time — memory efficient

---

### Example 5: Loop Through File with `readline()` — Memory Efficient

```python
f = open("dummy.txt")

while True:
    line = f.readline()
    if not line:   # Empty string = end of file
        break
    print(line.strip())

f.close()
```

**Output:**
```
how to learn
python basics
for machine learning
store information
```

**How the loop works:**

```
Iteration 1: readline() → "how to learn\n"    → line exists → print
Iteration 2: readline() → "python basics\n"    → line exists → print
Iteration 3: readline() → "for machine...\n"   → line exists → print
Iteration 4: readline() → "store information"   → line exists → print
Iteration 5: readline() → ""                    → empty = falsy → break!
```

**Why `not line` works:**
- When `readline()` reaches the end of file, it returns `""` (empty string)
- Empty string is **falsy** in Python
- `not ""` → `not False` → `True` → enters the `if` block → `break`

**Alternative (equivalent) approach:**
```python
f = open("dummy.txt")

while True:
    line = f.readline()
    if line:
        print(line.strip())
    else:
        break

f.close()
```

---

### Example 6: Context Manager — The `with` Statement

```python
# ❌ Manual approach — easy to forget f.close()
f = open("dummy.txt")
content = f.read()
f.close()  # What if an error occurs before this line?

# ✅ Context Manager — auto-closes even if errors occur
with open("dummy.txt") as f:
    content = f.read()
    print(content)
# File is automatically closed here — guaranteed!
```

**Why context managers are better:**

| Manual `open()/close()` | Context Manager `with` |
|--------------------------|----------------------|
| Must remember to close | Auto-closes |
| If error occurs before `close()`, file stays open | Closes even if error occurs |
| More lines of code | Cleaner, more Pythonic |
| Resource leak risk | No resource leak |

**Syntax breakdown:**
```python
with open("dummy.txt") as f:
#    ──────────────────    ─
#    │                     └── alias (variable name for the file object)
#    └── function that returns a file object
```

This is equivalent to:
```python
f = open("dummy.txt")
# ... do work ...
f.close()
```

But safer and cleaner.

---

### Example 7: Opening Files in Subdirectories

```python
import os

# File in a subdirectory
# ❌ Relative (works but fragile)
with open("temp/dummy_2.txt") as f:
    print(f.read())

# ✅ Absolute with os.path.join (robust)
folder = "temp"
filename = "dummy_2.txt"
full_path = os.path.join(os.getcwd(), folder, filename)

with open(full_path) as f:
    print(f.read())

# To switch files, just change the variable:
filename = "dummy_3.txt"
full_path = os.path.join(os.getcwd(), folder, filename)
with open(full_path) as f:
    print(f.read())
```

---

## Comparison Table — Read Methods

| Method | Returns | Loads All at Once? | Memory Efficient? | Use Case |
|--------|---------|-------------------|-------------------|----------|
| `f.read()` | Single string | Yes | No | Small files |
| `f.readlines()` | List of strings | Yes | No | When you need all lines as a list |
| `f.readline()` | Single string (one line) | No | Yes | Very large files (GB+) |

---

## Common Mistakes & Debugging

### Mistake 1: Reading Twice Without Reopening

**Wrong:**
```python
f = open("data.txt")
content1 = f.read()   # Reads everything
content2 = f.read()   # Returns "" — pointer at end!
f.close()
```

**Fix:** Save the result to a variable, or reopen the file:
```python
f = open("data.txt")
content = f.read()
f.close()
# Use 'content' multiple times — it's just a string
```

### Mistake 2: Forgetting to Close Files

**Wrong:**
```python
f = open("data.txt")
content = f.read()
# Forgot f.close() — file stays open in RAM!
```

**Fix:** Always use context managers:
```python
with open("data.txt") as f:
    content = f.read()
# Auto-closed!
```

### Mistake 3: FileNotFoundError

**Wrong:**
```python
f = open("nonexistent.txt")
```

**Error:**
```
FileNotFoundError: [Errno 2] No such file or directory: 'nonexistent.txt'
```

**Fix:** Verify the file exists and the path is correct:
```python
import os

filepath = "data.txt"
if os.path.exists(filepath):
    with open(filepath) as f:
        content = f.read()
else:
    print(f"File not found: {filepath}")
```

---

## Best Practices & Pythonic Way

```python
# ❌ BAD: Manual open/close
f = open("file.txt")
data = f.read()
f.close()

# ✅ GOOD: Context manager
with open("file.txt") as f:
    data = f.read()

# ❌ BAD: Hardcoded paths
path = "C:\\Users\\monal\\data\\file.txt"

# ✅ GOOD: Dynamic, cross-platform paths
import os
path = os.path.join(os.getcwd(), "data", "file.txt")

# ❌ BAD: Reading huge files entirely into memory
with open("huge_file.txt") as f:
    data = f.read()  # 12GB into RAM!

# ✅ GOOD: Process line by line for large files
with open("huge_file.txt") as f:
    for line in f:  # Python's built-in iteration — memory efficient!
        process(line.strip())
```

> **Pro Tip:** The most Pythonic way to iterate over file lines is `for line in f:` — it uses `readline()` internally but with cleaner syntax.

---

## When to Use / When NOT to Use

| Use When... | Avoid When... |
|------------|--------------|
| `f.read()` — Small files that fit in memory | Large files (GB+) — will crash |
| `f.readlines()` — Need all lines as a list | Don't need random access to lines |
| `f.readline()` / `for line in f:` — Huge files | Small files (unnecessary complexity) |
| Context manager `with` — Always | Manual `open/close` — error-prone |
| `os.path.join()` — Always for paths | Hardcoded slashes — breaks cross-platform |
| `global` keyword — Quick scripts/debugging | Production code — use parameters/returns |
| `*args` — Unknown number of positional inputs | When you know exact parameter count |
| `**kwargs` — Flexible configuration/options | When parameters are well-defined |

---

## Related Topics & Connections

- **Variable Scope** → connects to **Closures**, **Decorators**, **Classes** (self)
- **`*args`/`**kwargs`** → connects to **Decorators** (wrapping functions), **Inheritance** (super().__init__)
- **Libraries/PIP** → connects to **Virtual Environments**, **requirements.txt**, **Docker**
- **OS Library** → connects to **pathlib** (modern alternative), **shutil** (file copying)
- **File Operations** → connects to **CSV handling**, **JSON parsing**, **Pandas DataFrames**

**Prerequisites:** Functions, Data Types (tuple, dict, list), Loops, String methods

**What to learn next:** File Writing/Appending, Exception Handling (try/except), Classes & OOP

---

## Interview & Exam Corner

### Frequently Asked Questions

1. **Q: What is the difference between local and global variables?**
   A: Global variables are defined at module level and accessible throughout the file. Local variables are defined inside a function and only exist during that function's execution. Functions create separate memory spaces for their local variables.

2. **Q: Why can you modify a global list inside a function without `global`, but not an integer?**
   A: Lists are **mutable** — operations like `.append()` modify the object in-place without changing its memory address. Integers are **immutable** — any modification (like `a += 1`) creates a new object, which Python interprets as a local assignment, requiring the `global` keyword.

3. **Q: What is the difference between `*args` and `**kwargs`?**
   A: `*args` collects extra positional arguments as a **tuple**. `**kwargs` collects extra keyword arguments (key=value pairs) as a **dictionary**. The asterisk count determines the behavior: single `*` = tuple, double `**` = dictionary.

4. **Q: What does a context manager (`with` statement) do?**
   A: It automatically handles resource cleanup (like closing files) when the block exits — even if an error occurs. It's safer and more Pythonic than manual `open()/close()`.

5. **Q: What is the difference between `f.read()`, `f.readline()`, and `f.readlines()`?**
   A: `read()` returns the entire file as one string. `readline()` returns one line and moves the pointer to the next line. `readlines()` returns all lines as a list of strings.

### Tricky Output Questions

```python
# Question 1: What will this print?
x = 10
def func():
    x = 20
    print(x)
func()
print(x)
```
**Answer:** `20` then `10` **Why:** Inside `func()`, `x = 20` creates a **local** variable. The global `x` remains `10`.

```python
# Question 2: What will this print?
def greet(*args, **kwargs):
    print(type(args), type(kwargs))

greet(1, 2, 3, name="Alice")
```
**Answer:** `<class 'tuple'> <class 'dict'>` **Why:** `*args` always creates a tuple, `**kwargs` always creates a dict.

```python
# Question 3: What will this print?
a = [1, 2, 3]
def modify():
    a.append(4)
modify()
print(a)
```
**Answer:** `[1, 2, 3, 4]` **Why:** Lists are mutable. `.append()` modifies in-place without reassignment, so Python uses the global list.

```python
# Question 4: What happens here?
f = open("test.txt")
content1 = f.read()
content2 = f.read()
print(len(content2))
f.close()
```
**Answer:** `0` **Why:** After the first `f.read()`, the file pointer is at the end. The second `f.read()` reads nothing, returning an empty string of length 0.

### One-Liner Challenges

- **Challenge:** Sum any number of arguments
- **Solution:** `def total(*args): return sum(args)`

- **Challenge:** Read a file into a list of clean lines
- **Solution:** `lines = open("f.txt").read().strip().split("\n")`

- **Challenge:** Get the absolute path of a file in a subdirectory
- **Solution:** `import os; path = os.path.join(os.getcwd(), "sub", "file.txt")`

---

## Quick-Reference Cheat Sheet

```
VARIABLE SCOPE CHEAT SHEET
============================
x = 10 (outside func)  → Global variable (accessible everywhere)
def f(): x = 5         → Local variable (only inside f())
def f(): global x      → Use global 'x' inside function
Mutable (list/dict)     → Can modify global without 'global' keyword
Immutable (int/str)     → Need 'global' keyword to modify

FUNCTION ARGUMENTS CHEAT SHEET
================================
def f(a, b)             → Required positional arguments
def f(a, b=0)           → Default argument (b is optional)
def f(*args)            → Dynamic positional → tuple
def f(**kwargs)         → Dynamic keyword → dictionary
Order: required → default → *args → **kwargs

LIBRARY / PIP / PyPI CHEAT SHEET
==================================
pip install <name>      → Download library from PyPI
pip freeze              → List installed libraries
import <library>        → Load library into your script
PyPI = warehouse        → pypi.org (700K+ packages)
PIP = downloader        → CLI tool to install from PyPI

OS LIBRARY CHEAT SHEET
========================
import os
os.getcwd()             → Current working directory (string)
os.listdir()            → Files in current directory (list)
os.listdir(path)        → Files in specified directory
os.path.join(a, b, c)   → Cross-platform path builder

FILE OPERATIONS CHEAT SHEET
==============================
open("file.txt")        → Open file (read mode default)
f.read()                → Entire file as one string
f.readline()            → One line at a time
f.readlines()           → All lines as a list
f.close()               → Free the file from memory
with open(...) as f:    → Context manager (auto-close)
```

---

## Summary (TL;DR)

- **Variable Scope:** Global variables live everywhere in the file; local variables live only inside their function and die when it ends. Use `global` keyword to modify a global variable inside a function. Mutable objects (lists, dicts) can be modified in-place without `global`.
- **Argument Order:** Required → Default → `*args` → `**kwargs`. Never violate this order.
- **`*args`** packs extra positional arguments into a **tuple**; `**kwargs` packs key=value pairs into a **dictionary**. The names are convention — the asterisks are what matter.
- **PyPI** is the warehouse (like Play Store), **PIP** is the download tool, **Library** is the app/package of code.
- **`os` module:** `os.getcwd()` for current location, `os.listdir()` for folder contents, `os.path.join()` for cross-platform paths.
- **File reading:** `f.read()` gets everything at once, `f.readlines()` gets a list of lines, `f.readline()` gets one line (memory efficient for huge files). The file **pointer** moves forward — you can only read forward, not backward.
- **Always use `with open(...) as f:`** — it automatically closes files, even if errors occur.

---

## Flashcard-Style Recall (Q&A Pairs)

- **Q:** What is variable scope?
  **A:** The region of code where a variable is accessible — either global (entire file) or local (inside a function only).

- **Q:** What error do you get when modifying a global int inside a function without `global`?
  **A:** `UnboundLocalError: cannot access local variable where it is not associated with a value`.

- **Q:** Why can you append to a global list inside a function without `global`?
  **A:** Because `.append()` modifies the object in-place (same memory address). Python only creates a local variable when it sees an assignment (`=`).

- **Q:** What data type does `*args` create?
  **A:** A **tuple** containing all extra positional arguments.

- **Q:** What data type does `**kwargs` create?
  **A:** A **dictionary** containing all extra keyword arguments.

- **Q:** What is the correct order of function parameters?
  **A:** Required → Default → `*args` → `**kwargs`.

- **Q:** What is PyPI?
  **A:** Python Package Index (pypi.org) — an online warehouse hosting 730K+ Python libraries.

- **Q:** What command installs a Python library?
  **A:** `pip install library_name`.

- **Q:** What does `os.path.join()` do?
  **A:** Combines path components using the correct separator for the current OS (`\` on Windows, `/` on Mac/Linux).

- **Q:** What's the difference between `f.read()` and `f.readlines()`?
  **A:** `f.read()` returns one big string. `f.readlines()` returns a list where each element is one line.

- **Q:** Why does calling `f.read()` twice return empty string the second time?
  **A:** The file pointer moves to the end after the first read. The second read starts from the end, finding nothing.

- **Q:** What is a context manager and why use it?
  **A:** The `with` statement that auto-closes files when the block exits. It prevents resource leaks and is cleaner than manual `close()`.

- **Q:** What's the difference between absolute and relative paths?
  **A:** Absolute = full path from root (`C:/Users/data/file.txt`). Relative = from current directory (`data/file.txt`).
