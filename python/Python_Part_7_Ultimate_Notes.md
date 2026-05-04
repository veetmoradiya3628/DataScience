# Python Day 9 — File Operations Part 2 (Modes), Exception Handling & Exit Commands

---

## Concept Overview

This session completes **File Operations** and introduces two critical intermediate topics:

1. **File Modes Matrix (r, w, a, r+, w+, a+)** — Understanding every mode's pointer behavior, whether it wipes data, and when to use each one.
2. **File Pointer Methods (`f.tell()`, `f.seek()`)** — Taking manual control of the read/write cursor.
3. **Exception Handling (try / except / else / finally)** — Preventing your program from crashing by catching and handling errors gracefully.
4. **Raising Custom Exceptions (`raise`)** — Throwing your own errors when business logic demands it.
5. **Exit Commands (`exit()`, `sys.exit()`, `os._exit()`)** — Gracefully terminating Python programs.

**Why this matters:** Production software must never crash unexpectedly. Every professional codebase wraps risky operations in exception handlers. File modes determine whether you accidentally destroy data or safely append to it. Exit commands give users control over when a program stops.

**Real-world analogy:** File modes are like doors with different access rules — some doors are read-only (museum), some let you write on a blank canvas (w), some let you add sticky notes at the end (a), and some give you full renovation access (r+). Exception handling is like a safety net under a tightrope — even if you fall (error), you don't crash to the ground (system exit). Exit commands are the "hang up" button on a phone call — a graceful way to end the conversation.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **File Mode** | A string passed to `open()` that determines what operations (read/write) are allowed |
| **File Pointer** | An internal cursor tracking the current byte position in an open file |
| **`f.tell()`** | Returns the current position (index) of the file pointer |
| **`f.seek(n)`** | Moves the file pointer to byte position `n` |
| **Wipe / Truncate** | Deleting all existing content when a file is opened (happens with `w` and `w+`) |
| **Append** | Adding content at the end of a file without deleting existing data |
| **Exception** | A runtime error that disrupts normal program flow |
| **Exception Type** | The category of error (e.g., `FileNotFoundError`, `TypeError`, `ZeroDivisionError`) |
| **Exception Description** | The message explaining what went wrong |
| **`try` block** | Code you want to attempt — where errors might occur |
| **`except` block** | Code that runs only when an error occurs in the `try` block |
| **`else` block** | Code that runs only when the `try` block succeeds with zero errors |
| **`finally` block** | Code that runs no matter what — error or not |
| **`raise`** | Keyword to intentionally throw an exception from your code |
| **`sys.exit()`** | Gracefully exits a Python program |
| **`os._exit(n)`** | Immediately terminates the process (lower-level than `sys.exit()`) |

---

# PART 1: File Modes — The Complete Matrix

---

## The Modes Table

This is the single most important reference for file operations. Every mode controls four things: what operations are allowed, where the pointer starts, whether existing data is destroyed, and the typical use case.

| Mode | Meaning | Pointer Starts | Wipes File? | Creates File if Missing? | Usage |
|------|---------|---------------|-------------|--------------------------|-------|
| **`r`** | Read only | Beginning | No | No (raises `FileNotFoundError`) | Standard reading |
| **`w`** | Write only | Beginning | **Yes** — deletes all content | **Yes** | Fresh start, deletes old data |
| **`a`** | Append only | End | No | **Yes** | Adding more content at the end |
| **`r+`** | Read + Write | Beginning | No | No (raises `FileNotFoundError`) | Modifying existing content |
| **`w+`** | Write + Read | Beginning | **Yes** — deletes all content | **Yes** | Overwrite file, then optionally read back |
| **`a+`** | Append + Read | End | No | **Yes** | Add content at end + read existing content |

### Key Patterns to Remember

- **Any mode with `w`** (w, w+) → **wipes the file** on open
- **Any mode with `a`** (a, a+) → **always appends at the end** (even `f.seek()` won't change write position)
- **`r` and `r+`** → file **must already exist** (no auto-creation)
- **`w`, `w+`, `a`, `a+`** → **create the file** if it doesn't exist
- **`f.seek()` works for writing** in `r+` and `w+`, but **NOT in `a` or `a+`** (append modes always write at the end)

---

## f.tell() — Know Your Pointer Position

`f.tell()` returns the current byte position of the file pointer as an integer.

```python
with open("temp/temp1.txt", "r") as f:
    print(f.tell())        # 0 — pointer starts at beginning

    content = f.read()     # reads everything, pointer moves to end
    print(f.tell())        # 586 — pointer is now at byte 586 (end of file)

    print(content)         # prints all file contents
```

**How the count works:** Every character (including spaces, `\n` newlines) counts as at least one byte. If you manually count every character from index 0, you arrive at the number `f.tell()` returns.

---

## f.seek(n) — Move Your Pointer Manually

`f.seek(n)` repositions the file pointer to byte position `n`.

```python
with open("temp/temp2.txt", "r+") as f:
    f.seek(0)    # go to the beginning
    f.seek(7)    # go to the 7th byte
    f.seek(100)  # go to the 100th byte
```

### Critical Rule: f.seek() Does NOT Work for Writes in Append Mode

```python
# This will NOT write at position 7 — it writes at the END
with open("file.txt", "a+") as f:
    f.seek(7)
    f.write("hello")  # still appends at the end!

# This WILL write at position 7
with open("file.txt", "r+") as f:
    f.seek(7)
    f.write("hello")  # writes starting at byte 7, overwriting existing chars
```

`f.seek()` works for **reading** in `a+` mode, but for **writing**, append modes always write at the end of the file.

---

## Detailed Mode Examples

### Mode `r` — Read Only (Default)

```python
with open("temp/temp1.txt", "r") as f:
    print(f.tell())          # 0
    content = f.read()
    print(f.tell())          # 586 (end of file)
    print(content)
```

- Default mode when you don't specify anything in `open()`
- `f.read()` moves pointer from beginning to end
- Cannot write — `f.write()` will raise an error
- File must exist — raises `FileNotFoundError` if missing

### Mode `w` — Write Only (Wipes Everything)

```python
with open("temp/temp1_copy.txt", "w") as f:
    print(f.tell())          # 0 (file is wiped, pointer at start)
    f.write("Welcome to Data Science Bootcamp")
    print(f.tell())          # 32 (pointer moved to end of written text)
    # f.read() would raise an error — write mode only!
```

- **First thing that happens on open:** ALL existing content is deleted
- Cannot read — only write
- Creates the file if it doesn't exist

### Mode `a` — Append Only

```python
with open("temp/temp1_copy.txt", "a") as f:
    print(f.tell())          # 32 (starts at end of existing content)
    f.write(" Learn AI and ML")
    print(f.tell())          # 68 (pointer moved further)
```

- Pointer starts at the **end** of existing content
- Existing data is **preserved** — new data is added after it
- Cannot read — only append
- Creates the file if it doesn't exist

### Mode `r+` — Read + Write (Modify Existing)

This is the most nuanced mode. It allows both reading and writing, but the pointer behavior depends on what you do first.

**Scenario 1: Read first, then write (acts like append)**
```python
with open("temp/temp1_copy.txt", "r+") as f:
    content = f.read()       # pointer moves to end
    f.write("\nNew line")    # writes at the end (after all content)
```

**Scenario 2: Write first (overwrites from beginning)**
```python
with open("temp/temp1_copy.txt", "r+") as f:
    f.write("*****")         # overwrites first 5 characters
    remaining = f.read()     # reads everything AFTER byte 5
    print(remaining)         # original content from position 5 onward
```

**Visual example of Scenario 2:**
```
Before: H e l l o ,   h o w   a r e   y o u ?
         0 1 2 3 4 5 6 7 8 9 ...

After f.write("***"):
         * * * l o ,   h o w   a r e   y o u ?
         0 1 2 3 4 5 6 7 8 9 ...
         ^pointer is now at 3
```

- Does NOT wipe the file
- Does NOT create the file if missing
- `f.seek()` works for both reading and writing

### Mode `w+` — Write + Read (Wipes, then allows both)

```python
with open("temp/temp2.txt", "w+") as f:
    f.write("Hello World")
    print(f.tell())          # 11

    f.seek(6)                # move pointer to position 6
    f.write("****")          # overwrites "World" → "Hello ****d"
    print(f.tell())          # 10
```

- **Wipes the file first** (like `w`)
- Then allows both reading and writing
- `f.seek()` works normally
- Creates file if not present

### Mode `a+` — Append + Read

```python
with open("temp/temp2.txt", "a+") as f:
    print(f.tell())          # starts at end of file

    f.seek(0)                # move to beginning for reading
    content = f.read()       # reads all content
    print(content)

    f.write("\nAppended!")   # ALWAYS writes at end, regardless of seek
```

- Pointer starts at the **end**
- `f.seek()` works for **reading only**
- All writes go to the **end** of the file — `f.seek()` has no effect on write position
- Creates file if not present

---

## Assignment: Add New Data in the Middle Without Overwriting

**The Problem:** Using `r+`, when you `f.write()` at a middle position, it **overwrites** existing characters. How do you **insert** new data without losing anything?

**Algorithm:**

1. Open file with `r+` (if file exists) or `w+` (if starting fresh)
2. If using `w+`: write initial content first
3. Get the last index:
   - For `w+`: use `f.tell()` after writing
   - For `r+`: use `f.read()` to move pointer to end, then `f.tell()`
4. Calculate middle index: `middle_index = last_index // 2 + 1`
5. Move pointer to middle: `f.seek(middle_index)`
6. **Save everything from middle to end:** `from_center_content = f.read()`
7. **Move pointer back to middle:** `f.seek(middle_index)`
8. Write your new data: `f.write("***")`
9. Get new pointer position: `new_pointer = f.tell()`
10. Write back the saved content: `f.write(from_center_content)`

```python
with open("temp/temp2.txt", "w+") as f:
    f.write("Hello World")
    last_index = f.tell()                       # 11
    middle_index = last_index // 2 + 1          # 6
    
    f.seek(middle_index)
    from_center_content = f.read()              # "orld"
    
    f.seek(middle_index)
    f.write("***")                              # insert "***"
    f.write(from_center_content)                # write back "orld"
    
    # Result: "Hello ***orld" (no data lost!)
```

---

# PART 2: Exception Handling

---

## Why Exception Handling Exists

Without exception handling, any error **crashes the entire program**:

```python
# temp.py — NO exception handling
val1 = input("Enter a number to add: ")
def add(value1, value2):
    return value1 + value2

calc = add(100, val1)       # ERROR: can't add int + string
print("Hello World")        # NEVER EXECUTES — program already crashed
```

**The phone analogy:** If WhatsApp crashes on your phone, you don't want the entire phone to shut down. You want WhatsApp to show an error while everything else keeps working. Exception handling gives your code this resilience.

---

## The try / except Structure

```python
try:
    # Your normal code goes here
    # If any error occurs, jump to except
    risky_operation()
except:
    # Runs ONLY if an error occurred in try
    print("Something went wrong")

# Code here runs regardless (after try/except completes)
print("Program continues")
```

**Comparison to if/else:**
- `if` block: runs code if condition is True
- `try` block: runs code and watches for errors
- `else` block: runs if condition is False
- `except` block: runs if an error occurred

---

## Basic try/except Example

```python
# WITHOUT exception handling — system crashes
with open("temp/temp3.txt", "r+") as f:   # FileNotFoundError!
    content = f.read()
print("Hello World")                       # never executes

# WITH exception handling — system continues
try:
    with open("temp/temp3.txt", "r+") as f:
        content = f.read()
        print(content)
except:
    print("File not found")

print("Hello World")  # executes normally!
```

---

## Catching Specific Exception Types

Python has many built-in exception types. You can catch specific ones for better control:

| Exception Type | When It Occurs |
|---------------|----------------|
| `FileNotFoundError` | Trying to open a file that doesn't exist |
| `TypeError` | Incompatible operation between types (e.g., `int + str`) |
| `ZeroDivisionError` | Dividing by zero |
| `ValueError` | Function receives correct type but inappropriate value |
| `NameError` | Using a variable that hasn't been defined |
| `Exception` | **Parent of all exceptions** — catches everything |

### Multiple except Blocks

```python
import os

file_path = os.path.join("temp", "temp3.txt")
number = 0

try:
    with open(file_path, "r+") as f:
        content = f.read()
        print(content)
    calc = 100 / number
    print(calc)

except FileNotFoundError:
    print("File not found!")
    print(f"Current location: {os.getcwd()}")
    print(f"File selected: {file_path}")
    print(f"Files available: {os.listdir(os.path.join(os.getcwd(), 'temp'))}")

except TypeError:
    print("Issue with multiple type operation")

except Exception as e:
    print(f"Caught in parent exception: {e}")

print("Try-except block completed")
```

### How Multiple except Blocks Work

- Python checks each `except` block **top to bottom**
- The **first matching** exception type catches the error
- Only **one** except block executes (just like if/elif/else)
- `Exception` is the **parent of all exceptions** — it catches anything the specific blocks miss

### Critical Rule: Put `Exception` LAST

```python
# WRONG — Exception catches everything, specific blocks never run
except Exception as e:          # catches ALL errors
    print(e)
except FileNotFoundError:       # NEVER reached
    print("File issue")

# CORRECT — specific exceptions first, generic last
except FileNotFoundError:       # catches file errors
    print("File issue")
except TypeError:               # catches type errors
    print("Type issue")
except Exception as e:          # catches everything else
    print(f"Unknown error: {e}")
```

**Logic, not memorization:** `Exception` is the parent that catches everything. If you put it first, nothing else ever runs. Put specific exceptions first so they get a chance to match.

---

## Capturing the Error Message with `as`

```python
except Exception as e:
    print(e)    # prints the original Python error message

except ZeroDivisionError as z:
    print(z)    # prints "division by zero"
```

The `as e` syntax stores Python's original error description in a variable. This is essential when using the generic `Exception` — it tells you what actually went wrong.

---

## The `else` Block — Runs Only on Success

```python
try:
    result = 100 / 1            # no error
except ZeroDivisionError:
    print("Cannot divide by zero")
else:
    print("Try block executed successfully!")   # this runs
```

- `else` runs **only when `try` completes with zero errors**
- It does NOT run if any except block was triggered
- **Optional** — useful for separating "success actions" from the main try logic
- Keeps the `try` block focused on risky operations only

---

## The `finally` Block — Runs No Matter What

```python
try:
    f = open("temp/temp2.txt", "r+")
    content = f.read()
    print(content)
    calc = 100 / 1
    print(calc)

except FileNotFoundError:
    print("File not found")

except Exception as e:
    print(f"Error: {e}")

except NameError:
    print("File was never initialized")

else:
    print("Try block executed successfully")

finally:
    f.close()
    print("File is closed")
```

- `finally` **always runs** — whether `try` succeeded, an exception was caught, or even if there's a return statement
- **Use case:** Cleanup operations — closing files, closing database connections, releasing resources
- **Real-world analogy:** When your OS shuts down, it tries to close apps gracefully (`try`), handles any apps that won't close (`except`), and then force-closes everything remaining (`finally`)

---

## Complete Structure

```python
try:
    # Main logic — code that might fail
    pass
except SpecificError:
    # Handle known error type
    pass
except AnotherError as e:
    # Handle another known error with message
    pass
except Exception as e:
    # Catch-all for unknown errors (ALWAYS LAST)
    pass
else:
    # Runs ONLY if try succeeded (optional)
    pass
finally:
    # Runs NO MATTER WHAT (optional)
    pass
```

**Mandatory:** `try` + at least one `except`
**Optional:** `else`, `finally`, multiple `except` blocks

---

## Raising Custom Exceptions with `raise`

Until now, Python throws exceptions. With `raise`, **you** throw exceptions.

```python
number = int(input("Enter a number: "))

try:
    if number == 0:
        raise ZeroDivisionError("You passed 0 in denominator, which will result in infinity")
    calc = 100 / number
    print(calc)

except ZeroDivisionError as z:
    print(f"Issue with arithmetic operation. Message: {z}")

except Exception as e:
    print(f"Caught in parent exception: {e}")
```

**When the user enters 0:**
```
Issue with arithmetic operation. Message: You passed 0 in denominator, which will result in infinity
```

### raise Syntax

```python
raise ExceptionType("Your custom description message")
```

- `raise` tells Python to throw an error **intentionally**
- You choose the exception type (`ZeroDivisionError`, `TypeError`, `ValueError`, `Exception`, etc.)
- You write a custom description message
- The raised exception is caught by matching `except` blocks just like any other error

### When to Use raise

- **Input validation:** User provides invalid data that your code can detect
- **Business logic violations:** A value is technically valid but logically wrong
- **Precondition checks:** A function receives arguments outside expected range

```python
def withdraw(balance, amount):
    if amount > balance:
        raise ValueError(f"Cannot withdraw {amount}. Balance is only {balance}")
    return balance - amount
```

---

## Exception Handling — Three Use Cases

1. **Handle errors gracefully** — Prevent system crashes, show user-friendly messages
2. **Write better debug comments** — Print detailed info (current directory, available files, variable states) to diagnose issues
3. **Execute recovery code** — If the primary operation fails, run an alternative (e.g., try reading a backup file)

---

# PART 3: Exit Commands

---

## Why Exit Commands?

When you run a Python script, it executes top-to-bottom and then automatically exits. But sometimes you need to exit **mid-execution** based on user input or a condition.

---

## Three Ways to Exit

### 1. `exit()` — Terminal / Interactive Only

```python
# In Python terminal/REPL:
>>> a = 0
>>> b = 5
>>> print(a + b)
5
>>> exit()
# Returns to system terminal
```

- Works in interactive Python sessions (terminal/REPL)
- **Not recommended for scripts** — use `sys.exit()` instead

### 2. `sys.exit()` — Graceful Script Exit (Recommended)

```python
import sys

while True:
    choice = int(input("Press 1 for addition, 2 for subtraction, 3 to exit: "))
    
    if choice == 1:
        print("Doing addition")
        break
    elif choice == 2:
        print("Doing subtraction")
        break
    elif choice == 3:
        print("Exiting system...")
        sys.exit()
    else:
        print("None of the above. Please choose 1, 2, or 3")

print("Out of loop")  # only prints if break was used, NOT if sys.exit() was used
```

- **Gracefully exits** the entire Python program
- Can optionally pass a status code: `sys.exit(0)` (success) or `sys.exit(1)` (error)
- Raises `SystemExit` exception (can be caught by try/except if needed)

### 3. `os._exit(n)` — Immediate Process Termination

```python
import os

while True:
    choice = int(input("Press 1 for addition, 2 for subtraction, 3 to exit: "))
    
    if choice == 1:
        print("Doing addition")
        break
    elif choice == 2:
        print("Doing subtraction")
        break
    elif choice == 3:
        print("Exiting system...")
        os._exit(0)        # Note: underscore before exit!
    else:
        print("None of the above. Please choose 1, 2, or 3")
```

- Lower-level exit — terminates the process **immediately**
- Requires a status code argument: `0` for normal exit, `1` for abnormal
- Does **not** run `finally` blocks or cleanup handlers
- Note the **underscore**: `os._exit()` (not `os.exit()`)

### break vs sys.exit() — They Are Different!

```python
while True:
    choice = int(input("Choice: "))
    if choice == 1:
        print("Addition")
        break              # exits the LOOP only
    elif choice == 3:
        sys.exit()         # exits the ENTIRE PROGRAM

print("Out of loop")      # runs after break, NOT after sys.exit()
```

| | `break` | `sys.exit()` |
|---|---------|--------------|
| **Scope** | Exits current loop only | Exits entire program |
| **Code after loop** | Executes | Does NOT execute |
| **Use case** | Loop control | Program termination |

---

# Summary (TL;DR)

- **File Modes:** `r` (read), `w` (write, wipes), `a` (append at end), `r+` (read+write), `w+` (write+read, wipes), `a+` (append+read). Modes with `w` wipe files. Modes with `a` always append at end.
- **`f.tell()`** returns current pointer position. **`f.seek(n)`** moves pointer to position n. Seek does NOT affect write position in append modes.
- **Exception handling:** `try` for risky code, `except` for error handling, `else` for success-only code, `finally` for always-run cleanup. Put specific exceptions before generic `Exception`.
- **`raise`** lets you throw custom exceptions: `raise ValueError("message")`.
- **`sys.exit()`** gracefully exits a program. `os._exit(0)` immediately terminates. `break` only exits a loop, not the program.
- **File auto-creation:** `w`, `w+`, `a`, `a+` create files if missing. `r` and `r+` require existing files.

---

## Quick-Reference Cheat Sheet

```
FILE MODES CHEAT SHEET
========================
r    → Read only, pointer at start, no wipe, file must exist
w    → Write only, pointer at start, WIPES file, creates if missing
a    → Append only, pointer at end, no wipe, creates if missing
r+   → Read+Write, pointer at start, no wipe, file must exist
w+   → Write+Read, pointer at start, WIPES file, creates if missing
a+   → Append+Read, pointer at end, no wipe, creates if missing

POINTER METHODS
=================
f.tell()         → Current pointer position (int)
f.seek(n)        → Move pointer to position n
f.seek() for writing does NOT work in a / a+ modes

EXCEPTION HANDLING CHEAT SHEET
================================
try:             → Code that might fail
except Type:     → Handle specific error
except Type as e:→ Handle + capture message
except Exception:→ Catch-all (put LAST)
else:            → Runs only if try succeeds
finally:         → Runs NO MATTER WHAT
raise Type("msg")→ Throw your own exception

COMMON EXCEPTION TYPES
========================
FileNotFoundError  → File doesn't exist
TypeError          → Wrong type operation (int + str)
ZeroDivisionError  → Division by zero
ValueError         → Wrong value for correct type
NameError          → Variable not defined
Exception          → Parent of ALL exceptions

EXIT COMMANDS CHEAT SHEET
===========================
exit()             → Interactive terminal only
sys.exit()         → Graceful program exit (recommended)
os._exit(0)        → Immediate process termination
break              → Exits loop only (NOT the program)
```

---

## Flashcard-Style Recall (Q&A Pairs)

- **Q:** What is the default file mode when you call `open("file.txt")`?
  **A:** `"r"` (read only).

- **Q:** Which file modes wipe/delete all existing content?
  **A:** `"w"` and `"w+"` — any mode starting with `w`.

- **Q:** Which file modes create the file if it doesn't exist?
  **A:** `"w"`, `"w+"`, `"a"`, `"a+"`. Only `"r"` and `"r+"` require the file to exist.

- **Q:** What does `f.tell()` return?
  **A:** The current byte position of the file pointer as an integer.

- **Q:** What does `f.seek(10)` do?
  **A:** Moves the file pointer to byte position 10.

- **Q:** Does `f.seek()` work for writing in append mode (`a` or `a+`)?
  **A:** No. Append modes always write at the end of the file regardless of seek position. Seek only works for reading in `a+`.

- **Q:** In `r+` mode, what happens if you `f.write("***")` without reading first?
  **A:** It overwrites the first 3 characters of the file because the pointer starts at position 0.

- **Q:** In `r+` mode, what happens if you `f.read()` first, then `f.write("***")`?
  **A:** The write appends at the end because `f.read()` moved the pointer to the end of the file.

- **Q:** What's the purpose of `try/except`?
  **A:** To catch errors so the program continues running instead of crashing.

- **Q:** What's the difference between `except:` and `except Exception as e:`?
  **A:** Both catch all exceptions, but `as e` stores the error message in variable `e` so you can print/use it.

- **Q:** When does the `else` block execute?
  **A:** Only when the `try` block completes with zero errors.

- **Q:** When does the `finally` block execute?
  **A:** Always — regardless of whether an error occurred or not.

- **Q:** Why should `except Exception` be the LAST except block?
  **A:** Because `Exception` is the parent of all exceptions — it catches everything. If placed first, specific except blocks below it would never execute.

- **Q:** What does `raise ValueError("bad input")` do?
  **A:** Intentionally throws a `ValueError` exception with the message "bad input", which must be caught by a matching except block.

- **Q:** What's the difference between `break` and `sys.exit()`?
  **A:** `break` only exits the current loop (code after loop still runs). `sys.exit()` exits the entire program (nothing after it runs).

- **Q:** What's the difference between `sys.exit()` and `os._exit(0)`?
  **A:** `sys.exit()` is graceful (runs finally blocks, cleanup). `os._exit(0)` is immediate termination (skips cleanup). Note the underscore in `os._exit()`.
