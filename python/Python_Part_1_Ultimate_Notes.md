# Python Day 1 — Development Environment Setup, Variables, Data Types, Type Casting & Arithmetic Operators

---

## Concept Overview

This foundational session covers the **entire toolchain setup** for Python development and dives deep into **variables, memory storage, data types, type casting, comments, print function, naming conventions, and arithmetic operators**. It establishes the critical mental model of how Python interacts with the operating system and RAM — knowledge that separates surface-level coders from engineers who truly understand what their code does.

**Why does this exist?** Before writing a single line of Python, you need a functioning development environment. And before building applications, you must understand how data is stored, named, typed, and manipulated. These are the atoms of programming — everything else is molecules built from them.

**Real-world analogy:** Think of building a house. The tools (UV, VS Code, Terminal) are your hammer, saw, and measuring tape. Variables are labeled storage boxes in your warehouse (RAM). Data types are like different container shapes — you can't pour liquid (float) into a box designed for solid blocks (int) without converting it first. Operators are the actions you perform — adding bricks, removing walls, measuring dimensions.

**Where it fits:** This is **Day 0 of programming**. Every Python program ever written — from a "Hello World" script to a TensorFlow neural network — uses variables, data types, and operators. Master these, and everything else builds naturally.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **UV** | A modern Python package/project/environment manager (replacement for pip + virtualenv). Handles Python installation, version management, virtual environments, and package installation. |
| **VS Code** | Visual Studio Code — a hybrid text editor + IDE used for writing, editing, and running code. Supports extensions for enhanced functionality. |
| **Terminal** | A text-based interface to communicate with your operating system using commands. On Windows: CMD (Command Prompt). On Mac/Linux: Bash/Zsh. |
| **CMD** | Command Prompt — the recommended terminal for Windows users in this course. Simpler and lighter than PowerShell. |
| **Virtual Environment (venv)** | An isolated set of Python files and folders that contains a specific Python version and packages. Keeps projects independent. |
| **Kernel** | The Python executable attached to a Jupyter Notebook. It runs your code cells. Think of it as the "engine" powering the notebook. |
| **IDE** | Integrated Development Environment — software that provides tools for writing, running, and debugging code. |
| **Extension** | A plugin installed in VS Code from its marketplace (like an app store) to add functionality (e.g., Python syntax highlighting, Jupyter support). |
| **Variable** | A named reference that holds the memory address of a stored value. The fundamental storage unit of any programming language. |
| **Data Type** | The classification of data that tells Python how much memory to allocate and what operations are valid. Core types: `int`, `float`, `str`, `bool`. |
| **Type Casting** | Converting a value from one data type to another (e.g., `int` → `float`, `float` → `str`). |
| **Operator** | A special symbol that performs an operation on values or variables (e.g., `+`, `-`, `*`, `/`, `%`). |
| **Operand** | The value(s) on which an operator acts. In `5 + 3`, both `5` and `3` are operands. |
| **RAM** | Random Access Memory — temporary, fast storage where running programs and their data live. Cleared when the program stops or system shuts down. |
| **HDD/SSD** | Hard Disk Drive / Solid State Drive — permanent storage where installed programs and saved files live. Persists after shutdown. |
| **Script (.py)** | A Python file that runs all code top-to-bottom in a single execution. |
| **Notebook (.ipynb)** | Interactive Python Notebook — allows cell-by-cell execution, mixing code with markdown notes. Used for experimentation and learning. |
| **Comment** | Non-executable text in code, ignored by Python. Used for notes and documentation. Single-line: `#`. Multi-line: `'''...'''` or `"""..."""`. |
| **Docstring** | A multi-line string (triple quotes) used for documentation. Technically a string literal, but used as a comment convention. |

---

# PART 1: DEVELOPMENT ENVIRONMENT SETUP

---

## Concept Overview

Before writing Python code, three tools must be in place: **UV** (manages Python versions and environments), **VS Code** (where you write and view code), and the **Terminal** (where you run system-level commands). Together, they form the complete development workflow.

**Real-world analogy:** UV is your **tool shed** (stores and manages all your tools/Python versions). VS Code is your **workshop** (where you build things). The Terminal is your **voice** — how you give instructions to the operating system.

---

## Tool 1: UV — Python & Environment Manager

### What UV Does

| Capability | Description |
|-----------|-------------|
| **Python Installation** | Install any version of Python (3.10, 3.12, 3.14, etc.) |
| **Version Management** | Manage multiple Python versions on the same machine |
| **Version Pinning** | Pin (select) one version as the active default |
| **Environment Creation** | Create isolated virtual environments with their own files/folders |
| **Package Management** | Install Python packages (replacement for pip) |

### Essential UV Commands

```bash
# 1. Install a specific Python version
uv python install 3.12

# 2. Pin (select) a Python version for current project
uv python pin 3.12

# 3. Verify which Python is active
uv run python

# 4. Create a virtual environment (with default name .venv)
uv venv

# 5. Create a virtual environment with a custom name
uv venv level_1_env

# 6. Run a Python script via UV
uv run python script_name.py
```

### Why Pin a Python Version?

You can install multiple Python versions (3.10, 3.12, 3.14) simultaneously. But for any given project, you need **one** active version. Pinning tells UV: "For this project, always use Python 3.12."

> **Pro Tip:** Always use a **stable, widely-tested** version like 3.12 or 3.10. Avoid bleeding-edge versions (3.14) in production — they may have untested edge cases. Use what the community has battle-tested.

### Why Virtual Environments?

Just like your operating system has its own files and folders that make it work, a **virtual environment** has its own Python files and folders. This isolation means:

- Project A can use Python 3.8 with Package X version 1.0
- Project B can use Python 3.12 with Package X version 5.0
- They never interfere with each other

```
[Your Computer]
├── Operating System Files (NEVER touch these)
├── Project_Level_1/
│   ├── level_1_env/        ← Virtual Environment (NEVER touch these)
│   │   ├── Scripts/        ← Contains activate, python.exe (Windows)
│   │   ├── bin/            ← Contains activate, python (Mac/Linux)
│   │   ├── Lib/            ← Installed packages
│   │   └── pyvenv.cfg      ← Config showing Python version & path
│   └── lectures/           ← YOUR code goes here
│       ├── 17012026/
│       │   ├── notes.ipynb
│       │   └── code.py
│       └── 18012026/
│           └── ...
└── Project_Level_2/
    ├── level_2_env/        ← Different environment, different Python
    └── lectures/
```

> **Critical Rule:** NEVER create your code files inside the virtual environment folder. The environment folder is like your operating system's system files — modifying or adding files there can break it.

---

## Tool 2: VS Code — Editor & IDE

### Key Operations

| Action | How To |
|--------|--------|
| **Open a folder** | `File → Open Folder → Navigate to your project folder` OR right-click folder → "Open with Code" |
| **Open Terminal** | Menu: `Terminal → New Terminal` |
| **Switch to CMD (Windows)** | Click dropdown arrow next to "PowerShell" → Select "Command Prompt" |
| **Install Extensions** | Click the 4-square icon on the left sidebar → Search → Install |
| **Create a file** | Click the "new file" icon next to the folder name in Explorer |
| **Create a folder** | Click the "new folder" icon next to the folder name in Explorer |

### Required Extensions

| Extension | Purpose |
|-----------|---------|
| **Python** | Syntax highlighting, linting, IntelliSense for `.py` files |
| **Jupyter** | Support for `.ipynb` notebook files — cell execution, kernel management |

> **Important:** These extensions are for VS Code functionality only — they do NOT install Python on your system. UV handles that.

---

## Tool 3: Terminal — Communicating with the OS

### Essential Terminal Commands

| Command | Windows (CMD) | Mac/Linux | What It Does |
|---------|--------------|-----------|-------------|
| **Change directory** | `cd folder_name` | `cd folder_name` | Navigate into a folder |
| **Go back one level** | `cd ..` | `cd ..` | Navigate to parent folder |
| **Clear screen** | `cls` | `clear` | Remove all terminal output |
| **List files** | `dir` | `ls` | Show contents of current folder |

```bash
# Example: Navigate from PracticalLive to Level_1
cd Level_1

# Verify you're in the right place — the terminal prompt shows your current path
# Windows: C:\Users\you\PracticalLive\Level_1>
# Mac:     ~/PracticalLive/Level_1$
```

> **Critical Concept:** The terminal always operates from your **current location**. If you try to run `python code.py` but `code.py` is in a subfolder, it will fail. You must `cd` into that subfolder first — just like you must physically enter a room to see what's inside it.

---

## Running Python: Two File Types

### `.py` (Python Script)

```
[code.py]
├── Plain text file
├── No cells, no tools, no UI
├── Write code → Run entire file at once
├── ALL lines execute top-to-bottom
├── Used for: production code, final solutions, automation
└── Run with: python code.py (after activating env)
              OR: uv run python code.py
```

### `.ipynb` (Interactive Python Notebook)

```
[notes.ipynb]
├── Rich interactive interface
├── Code cells + Markdown cells
├── Run cells individually (Shift+Enter)
├── Only the current cell executes
├── Last expression auto-displays output (notebook feature)
├── Used for: experimentation, learning, data analysis, prototyping
└── Requires: Kernel selection (Python environment)
```

### How to Run Each

**Notebook (.ipynb):**
1. Open the `.ipynb` file in VS Code
2. Click "Select Kernel" (top-right) → Python Environments → Choose your environment
3. Click on a cell → Press `Shift + Enter` to run it

**Script (.py) — Method 1: Using UV (recommended)**
```bash
uv run python code.py
```

**Script (.py) — Method 2: Activate environment first**
```bash
# Windows:
level_1_env\Scripts\activate
python code.py

# Mac/Linux:
source level_1_env/bin/activate
python code.py

# To deactivate later:
deactivate
```

| Feature | `.py` Script | `.ipynb` Notebook |
|---------|-------------|-------------------|
| Execution | All at once | Cell by cell |
| Output | Only `print()` shows output | Last expression auto-displays + `print()` |
| Use case | Production, deployment | Experimentation, learning |
| Tools needed | Terminal + Python | VS Code + Jupyter extension + Kernel |
| Notes/Text | Only via `#` comments | Markdown cells for rich formatting |

---

# PART 2: VARIABLES — The Foundation of Storage

---

## Concept Overview

A **variable** is the fundamental building block of every programming language. It is a **named reference** that stores the **memory address** of a value in RAM. When you create a variable, you're telling Python: "Store this value in memory, and let me access it using this name."

**Why do variables exist?** Without variables, every value would be temporary — computed once and immediately lost. Variables let you store, retrieve, reuse, and manipulate data throughout your program.

**Real-world analogy:** A variable is like a **labeled mailbox**. The mailbox (variable name) has an address (memory location). The letter inside (value) is what you care about. When someone asks for your mail, they look at the label, go to the address, and retrieve the letter.

---

## Syntax & Definition

```python
# Basic variable assignment
variable_name = value

# The equals sign (=) is the ASSIGNMENT OPERATOR
# It does NOT mean "equals" in the mathematical sense
# It means: "store the value on the right into the name on the left"

# Examples:
age = 25
name = "Monal"
pi = 3.14
is_active = True
```

**Flow of execution — right to left:**

```
age = 25
 ↑    ↑
 │    └── Step 1: Python sees the value 25
 └─────── Step 2: Stores it in RAM, saves the address into 'age'
```

---

## How Variables Work Internally (Memory Model)

This is the depth that separates beginners from engineers. Here's what happens when you write `a = 12`:

### Step-by-Step Memory Flow

```
Code: a = 12

Step 1: Python tells the OS → "Store the integer 12 in RAM"
Step 2: OS allocates memory block(s) and stores 12
Step 3: OS returns the memory ADDRESS (e.g., 140726842816)
Step 4: Python stores this ADDRESS in variable 'a'
Step 5: Variable 'a' now POINTS TO the memory location of 12
```

```
RAM (Logical View):
┌──────────────────────────────────┐
│  Address: 140726842816           │
│  Value:   12                     │
│  Type:    int                    │
│  Referenced by: a                │
└──────────────────────────────────┘
         ↑
         │
    a ───┘  (a holds this address, NOT the value 12 directly)
```

### Proving It with Code

```python
a = 12
print(a)        # Output: 12       ← Python follows the address, retrieves the value
print(id(a))    # Output: 140726842816  ← The actual memory address
```

**Output:**
```
12
140726842816
```

**Line-by-line breakdown:**
- `a = 12` → Stores 12 in RAM. Variable `a` holds the memory address.
- `print(a)` → Python looks up `a`'s address → goes to that RAM location → retrieves `12` → prints it.
- `print(id(a))` → `id()` returns the memory address itself, not the value.

> **Key Insight:** When you "use" a variable anywhere in your code, Python automatically performs address lookup → value retrieval. You never deal with addresses directly (unlike C/C++).

### Memory Optimization: Shared References

```python
a = 12
b = 12
print(id(a))    # Output: 140726842816
print(id(b))    # Output: 140726842816  ← SAME address!
```

**Output:**
```
140726842816
140726842816
```

**Why same address?** Python is intelligent. When `b = 12` is executed, Python checks: "Does 12 already exist in memory?" Yes — `a` already stored it. So instead of wasting memory by storing another 12, Python makes `b` point to the **same** memory location.

```
RAM:
┌──────────────────────────────────┐
│  Address: 140726842816           │
│  Value:   12                     │
│  Referenced by: a, b             │
└──────────────────────────────────┘
         ↑       ↑
         │       │
    a ───┘       └─── b
```

> **Note:** This optimization (called **integer interning**) applies to small integers (-5 to 256) and some strings in CPython. Larger values may or may not share addresses.

### Different Types = Different Addresses

```python
a = 12       # int
b = 12       # int (same address as a)
c = 12.0     # float

print(id(a))  # 140726842816
print(id(b))  # 140726842816  ← Same (both int, same value)
print(id(c))  # 2264357891024  ← DIFFERENT
```

**Output:**
```
140726842816
140726842816
2264357891024
```

**Why different?** `12` (int) and `12.0` (float) are fundamentally different data types. An integer requires a certain amount of memory. A float requires **more** memory (to store the decimal point information). Since they have different memory footprints, they cannot share the same memory block.

```
RAM:
┌──────────────────┐    ┌──────────────────────┐
│  Addr: ...816     │    │  Addr: ...024          │
│  Value: 12        │    │  Value: 12.0           │
│  Type: int        │    │  Type: float           │
│  Size: ~28 bytes  │    │  Size: ~24 bytes       │
│  Refs: a, b       │    │  Refs: c               │
└──────────────────┘    └──────────────────────┘
```

### Variable Reassignment

```python
a = 12
print(id(a))    # Address X (for value 12)

a = 10
print(id(a))    # Address Y (for value 10) — DIFFERENT

# The old address (X) is now "orphaned" — no variable points to it
# Python's garbage collector will eventually clean it up
```

**What happens visually:**
```
Before reassignment:           After reassignment:
a → [Address X: 12]           a → [Address Y: 10]
                               [Address X: 12] ← orphaned, will be garbage collected
```

---

## The `print()` Function

### What It Does

`print()` is a **built-in function** that outputs the value of whatever is passed to it. It's the primary way to see output from your Python code.

### Syntax

```python
print(value)                  # Print a single value
print(var1, var2, var3)       # Print multiple values (separated by space)
print(expression)             # Print the result of an expression
```

### Examples

```python
a = 12
print(a)              # Output: 12
print(12)             # Output: 12 (raw value, NOT stored in RAM as variable)
print(type(a))        # Output: <class 'int'>
print(id(a))          # Output: 140726842816
print(a, "hello", 5)  # Output: 12 hello 5
```

### Notebook vs Script: The Print Behavior Difference

This is a common source of confusion:

**In `.py` scripts:** Nothing displays without `print()`. Period.
```python
# script.py
a = 12
a          # This line does NOTHING visible
id(a)      # This line does NOTHING visible
print(a)   # Output: 12 ← Only this produces output
```

**In `.ipynb` notebooks:** The **last expression** in a cell auto-displays (without `print()`).
```python
# In a notebook cell:
a = 12
b = 10
id(a)     # NOT displayed (not the last expression)
id(b)     # Displayed automatically (last expression in cell)
```

**Rules for notebook auto-display:**
1. Only the **last** expression in a cell is auto-displayed
2. If the last line is a `print()`, that takes priority — auto-display is suppressed
3. Assignment statements (`a = 12`) produce no output
4. This is a **notebook convenience feature**, NOT a Python language feature

> **Best Practice:** Always use `print()` explicitly. It works everywhere — scripts, notebooks, production. Don't rely on notebook auto-display.

---

# PART 3: DATA TYPES — Classifying Your Data

---

## Concept Overview

Every value in Python has a **type** — a classification that determines what it is, how much memory it needs, and what operations can be performed on it. The four **primitive** (basic) data types are:

| Type | Python Name | Example | What It Stores |
|------|------------|---------|---------------|
| **Integer** | `int` | `12`, `-5`, `0`, `1000` | Whole numbers (no decimal) |
| **Float** | `float` | `3.14`, `-0.5`, `12.0` | Numbers with decimal points |
| **String** | `str` | `"hello"`, `'Data Science'`, `"B"` | Text (characters, words, sentences) |
| **Boolean** | `bool` | `True`, `False` | Logical truth values (only two possible values) |

### Checking Data Type: `type()` Function

```python
a = 12
b = 3.14
c = "Data Science"
d = True

print(type(a))    # Output: <class 'int'>
print(type(b))    # Output: <class 'float'>
print(type(c))    # Output: <class 'str'>
print(type(d))    # Output: <class 'bool'>
```

**Output:**
```
<class 'int'>
<class 'float'>
<class 'str'>
<class 'bool'>
```

---

## Integer (`int`)

Whole numbers — positive, negative, or zero. No decimal point.

```python
age = 25
temperature = -10
count = 0
big_number = 1_000_000    # Underscores for readability (Python ignores them)

print(type(age))           # <class 'int'>
```

> **Python Unique:** Unlike C/Java, Python integers have **unlimited precision**. You can store numbers as large as your memory allows — no overflow.

---

## Float (`float`)

Numbers with a decimal point. Used for precision, measurements, and calculations.

```python
pi = 3.14
price = 99.99
temperature = -0.5
whole_as_float = 12.0     # Still a float, even though the decimal part is 0

print(type(pi))            # <class 'float'>
print(type(whole_as_float))  # <class 'float'> — NOT int!
```

> **Key Distinction:** `12` and `12.0` look similar but are fundamentally different types with different memory addresses. `12` is `int`, `12.0` is `float`.

---

## String (`str`)

Text data — any sequence of characters enclosed in quotes (single `'...'` or double `"..."`).

```python
name = "Monal"         # Double quotes
greeting = 'Hello'     # Single quotes — identical behavior
sentence = "Data Science is amazing"

print(type(name))      # <class 'str'>
```

### Single vs Double Quotes

```python
# Both are identical in Python:
name1 = "Monal"    # ✅ Valid
name2 = 'Monal'    # ✅ Valid — same result

# Useful when the string itself contains quotes:
message = "It's a beautiful day"     # ✅ Double quotes wrap single quote
message = 'She said "hello"'         # ✅ Single quotes wrap double quotes
```

### Strings MUST Be Quoted

```python
mentor_name = "Monal"     # ✅ "Monal" is a string value
mentor_name = Monal       # ❌ NameError: name 'Monal' is not defined
# Without quotes, Python thinks 'Monal' is a VARIABLE name — and it doesn't exist
```

---

## Boolean (`bool`)

Represents logical truth values. Only two possible values: `True` and `False`.

```python
is_visible = True
has_permission = False

print(type(is_visible))    # <class 'bool'>
```

### Case Sensitivity is Critical

```python
is_active = True      # ✅ Capital T
is_active = true      # ❌ NameError: name 'true' is not defined
is_active = TRUE      # ❌ NameError: name 'TRUE' is not defined
is_active = False     # ✅ Capital F
is_active = false     # ❌ NameError: name 'false' is not defined
```

> **Rule:** Boolean values are `True` (capital T) and `False` (capital F). Anything else is treated as a variable name.

### Boolean ↔ Integer Relationship

Booleans are fundamentally linked to the binary system (0 and 1):

| Boolean | Integer Equivalent | Binary | Meaning |
|---------|-------------------|--------|---------|
| `True` | `1` | `1` | ON / Yes / Active |
| `False` | `0` | `0` | OFF / No / Inactive |

```python
print(int(True))     # Output: 1
print(int(False))    # Output: 0
```

**Real-world analogy:** A light switch. It's either ON (True/1) or OFF (False/0). There's no in-between (we're not doing quantum computing).

---

# PART 4: COMMENTS — Notes for Humans, Invisible to Python

---

## Concept Overview

**Comments** are lines in your code that Python completely ignores during execution. They exist solely for human readers — to explain logic, leave notes, or temporarily disable code.

**Why do comments exist?** Code tells the computer *what* to do. Comments tell humans *why*. When you revisit code after 6 months, or when a teammate reads your code, comments provide context that the code alone cannot convey.

---

## Single-Line Comments (`#`)

```python
# This is a comment — Python ignores this entire line
a = 12  # This is an inline comment — Python executes 'a = 12' but ignores everything after #

# The hash symbol (#) tells Python: "Skip everything after this on this line"
```

### What Happens Without `#`

```python
a = 12
This is a note about the variable    # ❌ SyntaxError!
# Python tries to execute "This is a note..." as code — and fails
```

```python
a = 12
# This is a note about the variable  # ✅ Works — Python skips this line
```

---

## Multi-Line Comments (Triple Quotes)

When you need to write several lines of notes:

```python
"""
Ultimate Data Science Batch V2
In this class, we are learning Python variables.
This entire block is ignored by Python when used correctly.
"""

'''
This also works with single triple-quotes.
Both are equivalent for multi-line comments.
'''
```

> **Technical Note:** Triple-quoted strings are technically **docstrings** (document strings), not true comments. Python does read them — but if they're not assigned to a variable or used as a function's first statement, they have no effect on execution. In practice, developers use them interchangeably as multi-line comments.

### Verification That Multi-Line Comments Don't Execute

```python
a = 12
"""
print("This will NOT appear in output")
"""
print(a)
# Only output: 12
# The print inside triple quotes is treated as text, not code
```

**Output:**
```
12
```

---

## Markdown (Notebook Only)

In `.ipynb` notebooks, you can create rich formatted notes using **Markdown cells** (not code cells):

| Markdown Syntax | Result |
|----------------|--------|
| `# Heading` | Large heading |
| `## Subheading` | Medium heading |
| `**bold**` | **bold text** |
| `- item` | Bullet point |
| `1. item` | Numbered list |

> **Key Distinction:**
> - `#` in a **code cell** = Python comment
> - `#` in a **Markdown cell** = Heading (Markdown language, not Python)
> - Markdown is a feature of the **notebook**, not Python. You cannot use Markdown in `.py` files.

---

# PART 5: TYPE CASTING — Converting Between Types

---

## Concept Overview

**Type casting** (or type conversion) is the process of converting a value from one data type to another. Python provides built-in functions for this: `int()`, `float()`, `str()`, `bool()`.

**Why does it exist?** Different operations require different types. You can't add a number to a string, but you might need to display a number inside a sentence. Type casting bridges these gaps.

**Real-world analogy:** Currency conversion. You have US dollars but need Euros to shop in Europe. The value is conceptually the same, but the "type" changes to be compatible with the system you're working in.

---

## Syntax

```python
int(value)      # Convert to integer
float(value)    # Convert to float
str(value)      # Convert to string
bool(value)     # Convert to boolean
```

---

## Detailed Examples

### Integer → Float

```python
a = 1
print(type(a))           # <class 'int'>

a = float(a)             # Convert int 1 to float 1.0
print(a)                 # Output: 1.0
print(type(a))           # <class 'float'>
```

**What happens:**
```
float(1) → 1.0
The decimal point is added. No data loss.
Memory address CHANGES (different type = different memory footprint).
```

### Float → Integer (DATA LOSS!)

```python
pi = 3.14
pi_int = int(pi)

print(pi_int)            # Output: 3
print(type(pi_int))      # <class 'int'>
```

**Output:**
```
3
<class 'int'>
```

> **Critical Warning:** Converting float to int **truncates** the decimal — it does NOT round. `int(3.14)` → `3`, `int(3.99)` → `3`, `int(-2.7)` → `-2`. The decimal part is simply **removed**.

### Float → String

```python
a = 1.0
a = str(a)

print(a)                 # Output: 1.0
print(type(a))           # <class 'str'>
# It LOOKS like 1.0, but it's now a string "1.0" — you can't do math with it
```

### Boolean → Integer

```python
print(int(True))         # Output: 1
print(int(False))        # Output: 0
```

### Boolean → Reassignment Check

```python
is_visible = False
print(is_visible)         # Output: False

is_visible = True
print(is_visible)         # Output: True
# The old value (False) is replaced. Variable now holds True.
```

---

## Type Casting Compatibility Matrix

| From → To | `int()` | `float()` | `str()` | `bool()` |
|-----------|---------|-----------|---------|----------|
| **int** | — | ✅ `1 → 1.0` | ✅ `1 → "1"` | ✅ `0→False, else→True` |
| **float** | ✅ `3.14 → 3` (truncates) | — | ✅ `3.14 → "3.14"` | ✅ `0.0→False, else→True` |
| **str** | ✅ only if numeric: `"12" → 12` | ✅ only if numeric: `"3.14" → 3.14` | — | ✅ `""→False, else→True` |
| **bool** | ✅ `True→1, False→0` | ✅ `True→1.0, False→0.0` | ✅ `True→"True"` | — |

### Important: Type Casting Does NOT Modify the Original

```python
a = 1
float(a)        # Returns 1.0, but does NOT change 'a'
print(a)        # Output: 1 (still an int!)
print(type(a))  # <class 'int'>

# To actually change 'a', you must REASSIGN:
a = float(a)    # Now 'a' is updated
print(a)        # Output: 1.0
print(type(a))  # <class 'float'>
```

**Why this works the way it does:**
```
float(a) → This is a FUNCTION that takes a value, converts it, and RETURNS the result
        → It does NOT reach back and modify 'a'
        → You must capture the returned result: a = float(a)
```

---

## Common Mistakes & Debugging

### Mistake 1: Casting an incompatible string

**Wrong:**
```python
result = int("hello")
```
**Error:**
```
ValueError: invalid literal for int() with base 10: 'hello'
```
**Why it fails:** `"hello"` contains no numeric characters. Python cannot interpret letters as a number.

**Correct:**
```python
result = int("42")     # ✅ "42" is a numeric string
print(result)          # Output: 42
```

### Mistake 2: Expecting rounding instead of truncation

**Wrong assumption:**
```python
result = int(3.99)
print(result)          # Output: 3 (NOT 4!)
```
**Why:** `int()` truncates (chops off the decimal), it does NOT round. If you want rounding, use `round()`.

**Correct:**
```python
print(int(3.99))       # Output: 3 (truncation)
print(round(3.99))     # Output: 4 (rounding)
```

---

# PART 6: NAMING CONVENTIONS — Writing Clean Variable Names

---

## Concept Overview

Variable names aren't just labels — they're **communication**. A well-named variable tells you what it stores without needing a comment. A poorly-named variable creates confusion that compounds as code grows.

---

## Rules (Python Enforced)

| Rule | Valid Examples | Invalid Examples |
|------|--------------|-----------------|
| Can contain letters, digits, underscores | `bmi`, `person1`, `_name` | — |
| Must NOT start with a digit | `value1`, `_test` | `1value` ❌, `2nd_name` ❌ |
| No spaces allowed | `mentor_name` | `mentor name` ❌ |
| No special characters (except `_`) | `user_id`, `_private` | `user#id` ❌, `my-name` ❌ |
| Case sensitive | `Hello`, `hello`, `HELLO` are **three different** variables | — |
| Don't use Python keywords | `user_type`, `id_number` | `type` ❌, `print` ❌, `id` ❌ |

---

## Best Practices

```python
# ❌ BAD — meaningless names
a = 25
b = "Monal"
c = 3.14

# ✅ GOOD — self-documenting names
age = 25
mentor_name = "Monal"
pi_value = 3.14
```

> **The 1000-Line Test:** After writing 1000 lines of code, can you remember what `a`, `b`, `c` store? Probably not. But `age`, `mentor_name`, `pi_value` are instantly clear.

### Python Naming Conventions (PEP 8)

| Convention | Use For | Example |
|-----------|---------|---------|
| `snake_case` | Variables, functions | `mentor_name`, `calculate_bmi()` |
| `PascalCase` | Classes | `DataProcessor`, `UserProfile` |
| `UPPER_SNAKE_CASE` | Constants | `MAX_RETRIES`, `PI_VALUE` |
| `_single_leading_underscore` | Private/internal use | `_internal_helper` |

---

# PART 7: EXECUTION FLOW — How Python Reads Your Code

---

## Concept Overview

Python executes code in a predictable order: **top to bottom** (line by line) and **right to left** within expressions (evaluate the right side first, then assign to the left).

---

## Detailed Example

```python
mentor_name = 'Monal'       # Line 1: Store "Monal" → mentor_name
# Both single and double quotes work for strings

print(mentor_name)            # Line 2: Lookup mentor_name → "Monal" → print it
value_1 = 12                  # Line 3: Store 12 → value_1
print(500)                    # Line 4: Print raw value 500
value_2 = "Android"           # Line 5: Store "Android" → value_2
print(value_2)                # Line 6: Lookup value_2 → "Android" → print it
```

**Output:**
```
Monal
500
Android
```

### Execution Order Breakdown

```
Step 1: mentor_name = 'Monal'  → Store, no output
Step 2: # comment              → Ignored by Python
Step 3: print(mentor_name)     → Lookup mentor_name → get address → retrieve "Monal" → output: Monal
Step 4: value_1 = 12           → Store, no output
Step 5: print(500)             → Output: 500 (raw value, not stored)
Step 6: value_2 = "Android"    → Store, no output
Step 7: print(value_2)         → Lookup value_2 → retrieve "Android" → output: Android
```

### How `print(mentor_name)` Actually Works (Inner to Outer)

```
print(mentor_name)
  │       │
  │       └── Step 1: Python sees 'mentor_name' (a variable)
  │           Step 2: Looks up its memory address
  │           Step 3: Goes to that address in RAM
  │           Step 4: Retrieves the value "Monal"
  │           Step 5: mentor_name is REPLACED with "Monal"
  │
  └────────── Step 6: print("Monal") executes → displays: Monal
```

> **Execution Rule for Nested Functions:** Python evaluates from the **innermost parentheses outward**. In `print(type(a))`: first `type(a)` executes (returns `<class 'int'>`), then `print()` displays that result.

---

# PART 8: ARITHMETIC OPERATORS

---

## Concept Overview

**Arithmetic operators** perform mathematical operations on numeric values (and some special operations on strings). They are the most intuitive operators because they mirror basic math.

**Where they fit:** Part of a larger family of operators (relational, assignment, logical, bitwise) — arithmetic is the first and most fundamental category.

---

## Complete Operator Table

| Operator | Name | Example | Result | Description |
|----------|------|---------|--------|-------------|
| `+` | Addition | `2 + 2` | `4` | Adds two numbers |
| `-` | Subtraction | `100 - 2` | `98` | Subtracts right from left |
| `*` | Multiplication | `3 * 4` | `12` | Multiplies two numbers |
| `/` | Division | `11 / 2` | `5.5` | Divides (always returns float) |
| `//` | Floor Division | `11 // 2` | `5` | Divides and truncates to integer |
| `%` | Modulus | `11 % 2` | `1` | Returns the remainder of division |
| `**` | Exponentiation | `2 ** 3` | `8` | Raises to a power |

---

## Detailed Examples

### Basic Arithmetic

```python
# Using raw values
print(2 + 2)          # Output: 4

# Using variables
a = 2
b = 2
print(a + b)          # Output: 4

# Mixing variables and values
c = a + 2             # Right side evaluates first: a(2) + 2 = 4, then stored in c
print(c)              # Output: 4
```

### Division vs Floor Division

```python
print(11 / 2)         # Output: 5.5  (true division — always returns float)
print(11 // 2)        # Output: 5    (floor division — truncates decimal)
```

> **Key Insight:** Single `/` ALWAYS returns a float, even for whole number results: `10 / 2` returns `5.0`, not `5`.

### Modulus (Remainder)

```python
print(11 % 2)         # Output: 1    (11 ÷ 2 = 5 remainder 1)
print(10 % 3)         # Output: 1    (10 ÷ 3 = 3 remainder 1)
print(12 % 4)         # Output: 0    (12 ÷ 4 = 3 remainder 0 — evenly divisible)
```

**Common use:** Check if a number is even or odd:
```python
number = 7
if number % 2 == 0:
    print("Even")
else:
    print("Odd")       # Output: Odd
```

### Computing Pi

```python
print(22 / 7)         # Output: 3.142857142857143
# This is an approximation of π
# For precise π, use: import math; math.pi
```

---

## String Arithmetic (Special Behavior)

### String + Integer: NOT ALLOWED

```python
print(2 + "Data Science")
# TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

```python
print(2 - "Data Science")
# TypeError: unsupported operand type(s) for -: 'int' and 'str'
```

### String * Integer: REPETITION

```python
print("Data Science" * 2)
# Output: Data ScienceData Science

# Note: No space between! Because the original string has no trailing space.

print("Data Science " * 2)     # Added space at end
# Output: Data Science Data Science

# Practical use — creating visual separators:
print("=" * 40)
# Output: ========================================
```

> **Rule:** `*` with a string repeats it. `+` and `-` with incompatible types raise `TypeError`. Think of `*` as "copy-paste N times."

---

## Operators Work with Variables Too

```python
a = 2
b = 2
c = a + b             # Variable + Variable
d = a + 3             # Variable + Value
e = 10 + 5            # Value + Value

# All three forms work because Python converts everything to values first:
# c = a + b → c = 2 + 2 → c = 4
# d = a + 3 → d = 2 + 3 → d = 5
```

---

## Operator Preview: Other Categories (Covered in Next Sessions)

| Category | Examples | Purpose |
|----------|---------|---------|
| **Arithmetic** ✅ (today) | `+`, `-`, `*`, `/`, `//`, `%`, `**` | Math operations |
| **Assignment** | `=`, `+=`, `-=`, `*=` | Store and update values |
| **Relational/Comparison** | `==`, `!=`, `>`, `<`, `>=`, `<=` | Compare values (returns bool) |
| **Logical** | `and`, `or`, `not` | Combine boolean expressions |
| **Bitwise** | `&`, `\|`, `^`, `~`, `<<`, `>>` | Operate on binary representations |

---

# How It Works Internally

## Variable Storage: The Complete Picture

```
Python Code:  a = 12
                │
                ▼
    ┌─── Python Interpreter ───┐
    │                           │
    │  1. Parse: a = 12         │
    │  2. Create int object     │
    │  3. Request RAM from OS   │
    │                           │
    └───────────┬───────────────┘
                │
                ▼
    ┌─── Operating System ─────┐
    │                           │
    │  1. Find free RAM block   │
    │  2. Store value 12        │
    │  3. Return address:       │
    │     140726842816          │
    │                           │
    └───────────┬───────────────┘
                │
                ▼
    ┌─── Python Interpreter ───┐
    │                           │
    │  4. Bind name 'a' to     │
    │     address 140726842816  │
    │  5. Store in namespace    │
    │                           │
    └───────────────────────────┘

When we later write: print(a)
    1. Python looks up 'a' in namespace
    2. Gets address: 140726842816
    3. Goes to that RAM location
    4. Reads value: 12
    5. Passes 12 to print()
    6. print() outputs: 12
```

## Garbage Collection

When a variable is reassigned, the old value may become **orphaned** (no variable points to it). Python's **garbage collector** automatically detects orphaned objects and frees their memory for future use.

```python
a = 12      # Address X stores 12, 'a' points to X
a = 10      # Address Y stores 10, 'a' now points to Y
            # Address X (holding 12) is orphaned
            # Garbage collector will eventually reclaim that memory
```

---

## Common Mistakes & Debugging

### Mistake 1: Using a variable before defining it

**Wrong:**
```python
print(x)
```
**Error:**
```
NameError: name 'x' is not defined
```
**Why it fails:** Python executes top-to-bottom. It encounters `x` but has no record of it in memory.

**Correct:**
```python
x = 10
print(x)       # Output: 10
```

### Mistake 2: Forgetting quotes around strings

**Wrong:**
```python
name = Monal
```
**Error:**
```
NameError: name 'Monal' is not defined
```
**Why it fails:** Without quotes, Python interprets `Monal` as a variable name and tries to look it up.

**Correct:**
```python
name = "Monal"    # ✅ String value
```

### Mistake 3: Using lowercase boolean

**Wrong:**
```python
is_active = true
```
**Error:**
```
NameError: name 'true' is not defined
```
**Why it fails:** Python booleans require capital first letter.

**Correct:**
```python
is_active = True   # ✅ Capital T
```

### Mistake 4: Creating files inside the virtual environment

**Wrong:**
```
level_1_env/
├── Scripts/
├── Lib/
├── my_code.py       ← ❌ WRONG LOCATION
└── pyvenv.cfg
```
**Why it fails:** While it won't cause an immediate error, it pollutes the environment, makes project structure confusing, and risks accidental deletion when recreating environments.

**Correct:**
```
Project/
├── level_1_env/      ← Environment (don't touch)
└── lectures/         ← Your code goes here
    └── my_code.py    ← ✅ CORRECT LOCATION
```

### Mistake 5: Running commands in Python interpreter instead of terminal

**Wrong (you're inside Python — notice the `>>>` prompt):**
```python
>>> cd folder_name
  File "<stdin>", line 1
    cd folder_name
       ^
SyntaxError: invalid syntax
```
**Why it fails:** `cd` is an OS terminal command, not Python code. The `>>>` prompt means you're inside the Python interpreter.

**Correct:**
```python
>>> exit()       # First, exit Python
$ cd folder_name # Now you're back in the terminal
```

---

## Best Practices & Pythonic Way

### Variables
```python
# ❌ Avoid
a = 25
b = "John"
c = True

# ✅ Prefer
age = 25
user_name = "John"
is_authenticated = True
```

### Type Checking
```python
# ❌ Avoid — comparing type strings
if str(type(x)) == "<class 'int'>":

# ✅ Prefer — use isinstance()
if isinstance(x, int):
    print("x is an integer")
```

### Comments
```python
# ❌ Avoid — obvious comments that add no value
x = 10  # assign 10 to x

# ✅ Prefer — explain WHY, not WHAT
x = 10  # retry limit based on API rate-limiting policy
```

---

## When to Use / When NOT to Use

| Use When... | Avoid When... |
|------------|--------------|
| Use `int` for counting, indexing, IDs | Don't use `int` for money (use `Decimal`) |
| Use `float` for measurements, science | Don't use `float` for exact decimal math (floating-point errors) |
| Use `str` for text, names, messages | Don't use `str` for numeric calculations |
| Use `bool` for flags, toggles, conditions | Don't use `bool` when you need more than 2 states |
| Use notebooks for experimentation | Don't use notebooks for production deployment |
| Use `.py` scripts for automation, production | Don't use scripts when you need interactive exploration |

---

## Related Topics & Connections

- **Prerequisite knowledge:** Basic computer literacy, understanding of files/folders, RAM vs storage
- **How this connects to:**
  - **Control Flow (if/else):** Uses boolean values and comparison operators
  - **Loops:** Iterate using variables and arithmetic
  - **Functions:** Accept parameters (variables) and return values (typed)
  - **Data Structures (lists, dicts):** Store collections of typed values
  - **Object-Oriented Programming:** Everything in Python is an object with a type
- **What to learn next:** Relational operators, logical operators, assignment operators, string methods, control flow (`if`/`elif`/`else`)

---

## Interview & Exam Corner

### Frequently Asked Questions

1. **Q: What is a variable in Python?**
   A: A variable is a named reference that holds the memory address of a value stored in RAM. When you use the variable, Python automatically follows the address to retrieve the stored value. Variables are created via assignment (`=`) and don't need explicit type declarations.

2. **Q: What is the difference between `=` and `==`?**
   A: `=` is the **assignment operator** — it stores a value into a variable (`a = 5`). `==` is the **comparison operator** — it checks if two values are equal and returns a boolean (`a == 5` returns `True`).

3. **Q: What happens when you write `a = 12` in Python?**
   A: Python requests RAM from the OS to store integer 12, receives a memory address, and binds the name `a` to that address. The variable `a` doesn't "contain" 12 — it contains the address where 12 lives.

4. **Q: Why do `a = 12` and `b = 12` have the same `id()`?**
   A: Python optimizes memory by reusing existing objects. Since 12 is already in memory (from `a`), Python makes `b` point to the same location instead of creating a duplicate. This is called **integer interning** for small integers (-5 to 256).

5. **Q: What is the difference between `12` and `12.0`?**
   A: `12` is an `int` (integer) and `12.0` is a `float`. They have different types, require different amounts of memory, and have different memory addresses — even though they represent the same mathematical value.

6. **Q: What does `int(3.99)` return?**
   A: `3`. The `int()` function **truncates** (removes the decimal), it does NOT round. To round, use `round(3.99)` which returns `4`.

### Tricky Output Questions

```python
# What will this print?
a = 12
b = 12
print(id(a) == id(b))
```
**Answer:** `True` **Why:** Both variables point to the same memory address due to Python's integer interning optimization for small integers.

```python
# What will this print?
a = 12
a = 10
print(a)
```
**Answer:** `10` **Why:** Variable reassignment. The second line overwrites the first. `a` now points to the address of `10`, and the connection to `12` is lost.

```python
# What will this print?
print(type(True))
```
**Answer:** `<class 'bool'>` **Why:** `True` is a boolean literal in Python.

```python
# What will this print?
a = 1
b = float(a)
print(a, type(a))
print(b, type(b))
```
**Answer:**
```
1 <class 'int'>
1.0 <class 'float'>
```
**Why:** `float(a)` creates a new float value and assigns it to `b`. It does NOT modify `a`. The original variable remains unchanged.

```python
# What will this print?
print("Ha" * 3)
```
**Answer:** `HaHaHa` **Why:** String multiplication repeats the string N times.

```python
# What will this print?
print(11 // 2)
print(11 / 2)
print(11 % 2)
```
**Answer:**
```
5
5.5
1
```
**Why:** `//` is floor division (truncates), `/` is true division (returns float), `%` is modulus (returns remainder).

### One-Liner Challenges

- **Challenge:** Create a visual separator of 50 dashes
  **Solution:** `print("-" * 50)`

- **Challenge:** Check if a number is even without using `if`
  **Solution:** `print(number % 2 == 0)`

- **Challenge:** Swap two variables without a temp variable
  **Solution:** `a, b = b, a`

---

## Quick-Reference Cheat Sheet

```
PYTHON FUNDAMENTALS CHEAT SHEET
================================

ENVIRONMENT SETUP:
uv python install 3.12           → Install Python version
uv python pin 3.12               → Pin version for project
uv venv my_env                   → Create virtual environment
my_env/Scripts/activate           → Activate (Windows)
source my_env/bin/activate        → Activate (Mac/Linux)
deactivate                        → Deactivate environment
uv run python script.py           → Run script via UV

VARIABLES:
variable_name = value             → Store value (right → left)
print(variable)                   → Display value
type(variable)                    → Get data type
id(variable)                      → Get memory address

DATA TYPES:
int     → 12, -5, 0              → Whole numbers
float   → 3.14, -0.5, 12.0      → Decimal numbers
str     → "hello", 'world'       → Text (must be quoted!)
bool    → True, False            → Capital T and F only!

TYPE CASTING:
int(x)   → Convert to integer (truncates floats!)
float(x) → Convert to float
str(x)   → Convert to string
bool(x)  → Convert to boolean

COMMENTS:
# single line                     → Python ignores this
"""multi-line"""                  → Triple-quote block

ARITHMETIC OPERATORS:
+   → Addition           5 + 3   = 8
-   → Subtraction        5 - 3   = 2
*   → Multiplication     5 * 3   = 15
/   → Division           5 / 3   = 1.666... (always float)
//  → Floor Division     5 // 3  = 1 (truncates)
%   → Modulus            5 % 3   = 2 (remainder)
**  → Exponentiation     5 ** 3  = 125

GOTCHAS:
int(3.99) = 3 (truncates, NOT rounds!)
12 ≠ 12.0 (different types, different memory)
"hello" + 5 → TypeError (can't add str + int)
"ha" * 3 → "hahaha" (string repetition)
true → NameError (must be True with capital T)
```

---

## Summary (TL;DR)

- **Three tools needed:** UV (manages Python), VS Code (write code), Terminal (run commands)
- **Virtual environments** isolate project dependencies — create with `uv venv name`, never put code files inside them
- **Variables** store memory addresses of values in RAM, not the values themselves. `id()` reveals the address.
- **Four primitive data types:** `int` (whole numbers), `float` (decimals), `str` (text in quotes), `bool` (`True`/`False`)
- **Type casting** converts between types: `int()`, `float()`, `str()`, `bool()` — but `int()` truncates floats (doesn't round!)
- **Python executes top-to-bottom, right-to-left** within expressions, and inner-to-outer for nested functions
- **Arithmetic operators:** `+`, `-`, `*`, `/` (float result), `//` (floor), `%` (remainder) — string `*` int does repetition
- **Comments** (`#` for single-line, `"""..."""` for multi-line) are ignored by Python — use them for documentation
- **Always use `print()`** to see output — notebook auto-display is a convenience, not a language feature

---

## Flashcard-Style Recall (Q&A Pairs)

- **Q:** What is UV used for?
  **A:** Managing Python versions, creating virtual environments, and installing packages. It replaces pip + virtualenv.

- **Q:** What command creates a virtual environment named `my_env`?
  **A:** `uv venv my_env`

- **Q:** What does `id(variable)` return?
  **A:** The memory address where the variable's value is stored in RAM.

- **Q:** Why do two variables with the same integer value share the same `id()`?
  **A:** Python's integer interning optimization — for small integers (-5 to 256), Python reuses existing memory instead of creating duplicates.

- **Q:** What's the difference between `/` and `//`?
  **A:** `/` is true division (always returns float: `11/2 = 5.5`). `//` is floor division (truncates decimal: `11//2 = 5`).

- **Q:** What does `int(3.99)` return?
  **A:** `3` — it truncates (removes decimal), does NOT round.

- **Q:** What error occurs when you write `name = Monal` without quotes?
  **A:** `NameError: name 'Monal' is not defined` — Python thinks `Monal` is a variable name.

- **Q:** What's the difference between `.py` and `.ipynb`?
  **A:** `.py` is a script (runs all at once, only `print()` shows output). `.ipynb` is a notebook (runs cell-by-cell, last expression auto-displays).

- **Q:** What does `"abc" * 3` produce?
  **A:** `"abcabcabc"` — string repetition.

- **Q:** Can you do `"hello" + 5`?
  **A:** No — `TypeError`. You must cast: `"hello" + str(5)` → `"hello5"`.

- **Q:** How do you write a multi-line comment in Python?
  **A:** Use triple quotes: `"""comment"""` or `'''comment'''`. For single-line, use `#`.

- **Q:** What are the only two valid boolean values in Python?
  **A:** `True` (capital T) and `False` (capital F). Lowercase versions cause `NameError`.

- **Q:** Where should you NEVER create code files?
  **A:** Inside the virtual environment folder. Keep code in separate project folders.
