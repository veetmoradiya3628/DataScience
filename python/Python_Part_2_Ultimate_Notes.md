# Python Day 2 — String Properties, Operators, Control Flow & Lists

---

## Concept Overview

This session covers four foundational Python pillars: **String Methods** (properties of the `str` class), **Operators** (relational, assignment, logical, and bitwise), **Control Flow** (`if`, `elif`, `else`), and an introduction to **Non-Primitive Data Types** (lists). Together, these building blocks enable you to store data, compare values, make decisions, and organize information efficiently.

**Why do these exist?** Without operators and control flow, a program can only execute line-by-line with no decision-making ability. Without lists, you'd need thousands of individual variables to store related data. These concepts transform a calculator into a programmable brain.

**Real-world analogy:** Think of writing a cooking recipe. String methods are like trimming vegetables (removing unwanted parts). Operators are like measuring and comparing ingredients. Control flow is the decision: "If the oven is hot enough, bake; otherwise, wait." Lists are your shopping cart — one container holding many items.

**Where it fits:** This is the core foundation of Python programming. Every application — from web apps to machine learning — uses these concepts daily.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **Method** | A function that belongs to a specific class/data type. Called using dot notation (e.g., `"hello".strip()`). |
| **Function** | A standalone block of code that performs a task. Called independently (e.g., `print()`, `type()`, `len()`). |
| **Class** | A blueprint that defines a data type and its properties. `str`, `int`, `list` are all classes. |
| **Property** | A characteristic or capability that belongs to a class (used interchangeably with "method" in this context). |
| **Indentation** | Whitespace (4 spaces or 1 tab) at the beginning of a line that defines code blocks in Python. |
| **Control Flow** | The order in which individual statements are executed, controlled by conditions. |
| **Primitive Data Type** | Basic built-in types: `int`, `float`, `str`, `bool`. |
| **Non-Primitive Data Type** | Complex types built from primitives: `list`, `dict`, `tuple`, `set`. |
| **Operator** | A symbol that performs an operation on values (e.g., `+`, `==`, `>=`, `and`). |
| **Operand** | The values on which operators act (e.g., in `3 + 5`, both `3` and `5` are operands). |
| **Truth Table** | A table showing all possible input combinations and their corresponding outputs for logical operations. |
| **Binary** | A number system using only 0 and 1, which is how computers process all data internally. |

---

# PART 1: STRING PROPERTIES (METHODS)

---

## Concept Overview

When you check the `type()` of any value in Python and see `<class 'str'>`, `<class 'int'>`, etc., the word **class** means that data type comes with its own built-in capabilities called **methods**.

**The key distinction:**
- **Function** = standalone code (e.g., `print()`, `type()`, `id()`, `len()`) — works independently
- **Method** = belongs to a class, accessed via dot notation (e.g., `"hello".strip()`) — only works with its own data type

**Real-world analogy:** A **house** (class) has properties like doors, windows, rooms. A **glass** (function) is standalone — you pour water and drink from it. Similarly, `strip()` is a property of the string class, while `print()` is a standalone function.

---

## Syntax & Definition

```python
# Method syntax: object.method_name()
string_variable.strip()      # Removes whitespace from BOTH sides
string_variable.lstrip()     # Removes whitespace from LEFT side only
string_variable.rstrip()     # Removes whitespace from RIGHT side only

# Function syntax: function_name(argument)
print(value)                  # Standalone function
type(value)                   # Standalone function
id(value)                     # Standalone function
len(value)                    # Standalone function
```

**Key rule:** Methods use dot notation (`.`). Functions do NOT use dot notation.

---

## Detailed Explanation with Examples

### Example 1: Basic `strip()` Usage

```python
name = "     Monal S     "
print(name)          # Before stripping

name = name.strip()  # Remove spaces from both sides, save back
print(name)          # After stripping
```

**Output:**
```
     Monal S     
Monal S
```

**Line-by-line breakdown:**
- Line 1: Creates a string with leading and trailing spaces
- Line 2: Prints the original string (spaces visible)
- Line 4: `name.strip()` creates a NEW string without leading/trailing spaces. We save it back to `name`
- Line 5: Prints the cleaned string — no extra spaces

**Why this works:** `strip()` is a method of the `str` class. It scans from both ends of the string, removing whitespace characters until it hits a non-whitespace character. It does NOT remove spaces in the middle of the string.

### Example 2: `lstrip()` and `rstrip()` Separately

```python
name = "     Monal S     "

# Remove only RIGHT side spaces
result_r = name.rstrip()
print(repr(result_r))   # repr() shows quotes to visualize spaces

# Remove only LEFT side spaces
result_l = name.lstrip()
print(repr(result_l))
```

**Output:**
```
'     Monal S'
'Monal S     '
```

**Line-by-line breakdown:**
- `rstrip()` removes spaces only from the right end → left spaces remain
- `lstrip()` removes spaces only from the left end → right spaces remain
- `repr()` is used to show the string with quotes, making spaces visible

### Example 3: Chaining Methods — Building `strip()` from `lstrip()` + `rstrip()`

```python
name = "     Monal S     "

# Method 1: Two separate steps
name = name.lstrip()     # Remove left spaces
name = name.rstrip()     # Remove right spaces
print(name)

# Method 2: Chaining in one line
name = "     Monal S     "
name = name.lstrip().rstrip()
print(name)
```

**Output:**
```
Monal S
Monal S
```

**How chaining works:**

```
Step 1: name = "     Monal S     "
Step 2: name.lstrip() returns "Monal S     "  (left spaces removed)
Step 3: "Monal S     ".rstrip() returns "Monal S"  (right spaces removed)
Step 4: Final result saved to name
```

> **Pro Tip:** Python evaluates chained methods left to right. First `lstrip()` runs on the original string, producing a new string. Then `rstrip()` runs on THAT new string. This is called **method chaining**.

### Example 4: Real-World Use Case — Cleaning Form Input

```python
# User entered "  27  " in a form field (with accidental spaces)
user_input = "  27  "

# This will FAIL:
# age = int(user_input)  # ValueError: invalid literal for int()

# Solution: Strip first, then convert
age = int(user_input.strip())
print(age)        # 27
print(type(age))  # <class 'int'>
```

**Output:**
```
27
<class 'int'>
```

**Why this matters:** When collecting data from forms, users often accidentally add spaces. `int("  27  ")` fails because Python can't convert spaces to numbers. Stripping first removes the spaces, making the conversion possible.

---

## Common Mistakes & Debugging

### Mistake 1: Using `strip()` as a Standalone Function

**Wrong:**
```python
strip("  hello  ")
```
**Error:**
```
NameError: name 'strip' is not defined
```
**Why it fails:** `strip()` is a METHOD of the `str` class, not a standalone function. It must be called on a string object using dot notation.

**Correct:**
```python
"  hello  ".strip()
```

### Mistake 2: Using String Methods on Non-String Types

**Wrong:**
```python
number = 42
number.strip()
```
**Error:**
```
AttributeError: 'int' object has no attribute 'strip'
```
**Why it fails:** `strip()` is a property of the `str` class only. Integers don't have this method. Each class has its own unique methods.

**Correct:**
```python
number = "42"       # Make it a string first
number.strip()      # Now it works
```

### Mistake 3: Expecting `strip()` to Remove Middle Spaces

**Wrong assumption:**
```python
text = "  hello   world  "
print(text.strip())
# Expected: "helloworld"
```
**Actual output:**
```
hello   world
```
**Why:** `strip()` ONLY removes leading and trailing whitespace, never spaces within the string. To remove internal spaces, use `.replace(" ", "")`.

---

## Comparison Table: String Whitespace Methods

| Method | Removes From | Example Input | Example Output |
|--------|-------------|---------------|----------------|
| `strip()` | Both sides | `"  hi  "` | `"hi"` |
| `lstrip()` | Left side only | `"  hi  "` | `"hi  "` |
| `rstrip()` | Right side only | `"  hi  "` | `"  hi"` |

---

# PART 2: RELATIONAL (COMPARISON) OPERATORS

---

## Concept Overview

**Relational operators** (also called **comparison operators**) compare two values and return a **Boolean** result: `True` or `False`. They answer the question: "What is the relationship between these two values?"

**Real-world analogy:** When you go shopping, you compare prices: "Is this cheaper than that?" "Is this the same brand?" These are real-world comparisons that return yes/no answers — exactly what relational operators do.

---

## Syntax & Definition

```python
# All relational operators
a > b       # Greater than
a < b       # Less than
a == b      # Equal to (NOT assignment — that's single =)
a >= b      # Greater than OR equal to
a <= b      # Less than OR equal to
a != b      # Not equal to
```

> **Critical Warning:** `=` is the assignment operator (assigns values). `==` is the comparison operator (checks equality). Mixing them up is one of the most common beginner mistakes!

---

## Detailed Explanation with Examples

### Example 1: Basic Comparisons with Numbers

```python
a = 100
b = 200

print(a > b)    # Is 100 greater than 200?
print(a < b)    # Is 100 less than 200?
print(a == b)   # Is 100 equal to 200?
print(a >= b)   # Is 100 greater than or equal to 200?
print(a <= b)   # Is 100 less than or equal to 200?
print(a != b)   # Is 100 not equal to 200?
```

**Output:**
```
False
True
False
False
True
True
```

**Line-by-line breakdown:**
- `100 > 200` → False (100 is not greater)
- `100 < 200` → True (100 is indeed less)
- `100 == 200` → False (they are not equal)
- `100 >= 200` → False (100 is neither greater than nor equal to 200)
- `100 <= 200` → True (100 IS less than 200, so the condition passes)
- `100 != 200` → True (they ARE different)

### Example 2: Understanding `>=` and `<=`

```python
# Greater than or equal to: EITHER condition being true makes it True
print(3 >= 3)    # 3 is not greater than 3, BUT 3 equals 3 → True
print(5 >= 3)    # 5 is greater than 3 → True
print(2 >= 3)    # 2 is not greater than 3 AND not equal → False
```

**Output:**
```
True
True
False
```

**Key insight:** `>=` and `<=` are like an OR condition — if EITHER part is true, the whole expression is true.

### Example 3: Solving `!=` (Not Equal) Using Opposite Logic

```python
# Trick: Solve != by first solving ==, then flipping the answer
# Step 1: Is 12 == 12? → True
# Step 2: Opposite of True → False
# Therefore: 12 != 12 → False

print(12 != 12)   # False (they ARE equal, so "not equal" is false)
print(3 != 12)    # True (they are NOT equal)
```

**Output:**
```
False
True
```

### Example 4: String Comparison (Practical Use Case)

```python
# Login system: checking credentials
stored_id = 123
stored_password = "DSAcademy"

# User enters wrong credentials
user_id = 100
user_password = "DSAcademy"

print(user_id == stored_id)              # False — ID doesn't match
print(user_password == stored_password)   # True — Password matches
```

**Output:**
```
False
True
```

**Real-world use:** This is the foundation of every login system — comparing user input against stored credentials.

### Example 5: Age Threshold System

```python
age_threshold = 18
person_age = 16

# Check if person can log in
print(person_age >= age_threshold)  # False — too young

person_age = 18
print(person_age >= age_threshold)  # True — exactly 18, allowed
```

**Output:**
```
False
True
```

---

## Comparison Table: All Relational Operators

| Operator | Name | Example | Result | Reads As |
|----------|------|---------|--------|----------|
| `>` | Greater than | `5 > 3` | `True` | "Is 5 greater than 3?" |
| `<` | Less than | `5 < 3` | `False` | "Is 5 less than 3?" |
| `==` | Equal to | `5 == 5` | `True` | "Is 5 equal to 5?" |
| `>=` | Greater than or equal | `5 >= 5` | `True` | "Is 5 greater than or equal to 5?" |
| `<=` | Less than or equal | `3 <= 5` | `True` | "Is 3 less than or equal to 5?" |
| `!=` | Not equal to | `5 != 3` | `True` | "Is 5 not equal to 3?" |

> **Important:** Relational operators work with numbers AND strings. For numbers, they compare magnitude. For strings, `==` and `!=` check if the text is identical. Greater/less than on strings compares alphabetical (lexicographic) order.

---

# PART 3: ASSIGNMENT OPERATORS

---

## Concept Overview

**Assignment operators** are shortcuts for performing an operation AND saving the result back to the same variable in a single step. The basic `=` assigns a value. Compound assignment operators like `+=`, `-=`, `*=`, `/=` combine an arithmetic operation with assignment.

**Real-world analogy:** Instead of saying "Take my bank balance, add 500 to it, and save the new balance" (long way), you say "Deposit 500" (shortcut). The compound assignment operator IS the shortcut.

---

## Syntax & Definition

```python
# Basic assignment
a = 2              # Assign 2 to variable a

# Compound assignment operators
a += 1             # Same as: a = a + 1
a -= 1             # Same as: a = a - 1
a *= 2             # Same as: a = a * 2
a /= 2             # Same as: a = a / 2
a //= 2            # Same as: a = a // 2
a %= 2             # Same as: a = a % 2
a **= 2            # Same as: a = a ** 2
```

**How to read it:** The operator always comes BEFORE the `=`. `+=` means "add, then assign." `*=` means "multiply, then assign."

> **Important:** Python does NOT support `a++` or `a--` (increment/decrement operators from C/C++/Java). Use `a += 1` and `a -= 1` instead.

---

## Detailed Explanation with Examples

### Example 1: Step-by-Step Value Tracking

```python
a = 2
print(f"Initial: a = {a}")

a += 1      # a = a + 1 = 2 + 1 = 3
print(f"After a += 1: a = {a}")

a -= 1      # a = a - 1 = 3 - 1 = 2
print(f"After a -= 1: a = {a}")

a *= 2      # a = a * 2 = 2 * 2 = 4
print(f"After a *= 2: a = {a}")

a /= 2      # a = a / 2 = 4 / 2 = 2.0
print(f"After a /= 2: a = {a}")

a //= 2     # a = a // 2 = 2.0 // 2 = 1.0
print(f"After a //= 2: a = {a}")
```

**Output:**
```
Initial: a = 2
After a += 1: a = 3
After a -= 1: a = 2
After a *= 2: a = 4
After a /= 2: a = 2.0
After a //= 2: a = 1.0
```

**Key insight:** Each operation uses the CURRENT value of `a` (not the original). The value changes with each operation.

> **Notice:** `/=` converts the result to a float (2.0, not 2). This is because `/` in Python always returns a float. Use `//=` for integer (floor) division.

### Example 2: Why `a` Must Be Initialized First

**Wrong:**
```python
a += 1   # What is a + 1 if a doesn't exist?
```
**Error:**
```
NameError: name 'a' is not defined
```
**Why it fails:** `a += 1` expands to `a = a + 1`. Python tries to evaluate the right side first, where it encounters `a`, which hasn't been assigned yet.

**Correct:**
```python
a = 0    # Initialize first
a += 1   # Now a = 0 + 1 = 1
```

---

## Comparison Table: Assignment Operators

| Operator | Shorthand | Equivalent Long Form | Example (a=10) | Result |
|----------|-----------|---------------------|-----------------|--------|
| `=` | `a = 5` | `a = 5` | `a = 5` | `5` |
| `+=` | `a += 3` | `a = a + 3` | `a = 10 + 3` | `13` |
| `-=` | `a -= 3` | `a = a - 3` | `a = 10 - 3` | `7` |
| `*=` | `a *= 3` | `a = a * 3` | `a = 10 * 3` | `30` |
| `/=` | `a /= 3` | `a = a / 3` | `a = 10 / 3` | `3.33` |
| `//=` | `a //= 3` | `a = a // 3` | `a = 10 // 3` | `3` |
| `%=` | `a %= 3` | `a = a % 3` | `a = 10 % 3` | `1` |
| `**=` | `a **= 3` | `a = a ** 3` | `a = 10 ** 3` | `1000` |

---

# PART 4: LOGICAL OPERATORS

---

## Concept Overview

**Logical operators** combine multiple Boolean expressions and return a single Boolean result. Python has three logical operators: `and`, `or`, and `not`. These are the programming equivalents of logic gates in electronics.

**Real-world analogy:** A club has two entry requirements: age ≥ 18 AND balance ≥ 500. BOTH must be true (AND). A library lets you in if you have a student ID OR a library card — either one works (OR). NOT is simply the opposite — if the door is locked (True), NOT locked means unlocked (False).

---

## Syntax & Definition

```python
# Logical AND — True only when BOTH are True
result = condition1 and condition2

# Logical OR — True when AT LEAST ONE is True
result = condition1 or condition2

# Logical NOT — Reverses the Boolean value
result = not condition
```

---

## Truth Tables

### AND Truth Table

| A | B | A `and` B |
|---|---|-----------|
| True | True | **True** |
| True | False | False |
| False | True | False |
| False | False | False |

**Rule:** AND produces True ONLY when **both** inputs are True.

### OR Truth Table

| A | B | A `or` B |
|---|---|----------|
| True | True | **True** |
| True | False | **True** |
| False | True | **True** |
| False | False | False |

**Rule:** OR produces True when **any** input is True.

### NOT Truth Table

| A | `not` A |
|---|---------|
| True | False |
| False | True |

**Rule:** NOT simply **flips** the value.

### XOR Truth Table (Bonus — Not Native in Python)

| A | B | A `^` B (XOR) |
|---|---|---------------|
| True | True | False |
| True | False | **True** |
| False | True | **True** |
| False | False | False |

**Rule:** XOR produces True ONLY when **exactly one** input is True (not both).

> **Note:** Python doesn't have a keyword `xor`. For bitwise XOR, use `^`. For logical XOR, you can use `bool(a) != bool(b)`.

---

## Detailed Explanation with Examples

### Example 1: Basic Boolean Operations

```python
bool_a = True
bool_b = False

print(bool_a and bool_b)    # True AND False
print(bool_a or bool_b)     # True OR False
print(not bool_a)            # NOT True
print(not bool_b)            # NOT False
```

**Output:**
```
False
True
False
True
```

### Example 2: Real-World — Club Entry System

```python
# Club entry requirements:
# 1. Person must be 18 or older
# 2. Person must have at least 500 INR

person_age = 20
age_threshold = 18
person_balance = 100
minimum_balance = 500

# Check BOTH conditions using AND
can_enter = (person_age >= age_threshold) and (person_balance >= minimum_balance)
print(f"Can enter club: {can_enter}")
```

**Output:**
```
Can enter club: False
```

**Why False?** Age check passes (20 ≥ 18 → True), but balance check fails (100 ≥ 500 → False). AND requires BOTH to be True, so the result is False.

```python
# Now with sufficient balance
person_balance = 600
can_enter = (person_age >= age_threshold) and (person_balance >= minimum_balance)
print(f"Can enter club: {can_enter}")
```

**Output:**
```
Can enter club: True
```

Both conditions are now True, so AND returns True.

### Example 3: Understanding Evaluation Flow

```python
age = 25
has_id = True
is_banned = False

# Complex condition
can_access = (age >= 18) and (has_id or not is_banned)
print(can_access)
```

**Output:**
```
True
```

**Evaluation steps:**
```
Step 1: age >= 18        → 25 >= 18    → True
Step 2: not is_banned    → not False   → True
Step 3: has_id or True   → True or True → True
Step 4: True and True    → True
```

---

# PART 5: BITWISE OPERATORS

---

## Concept Overview

**Bitwise operators** work on the **binary (bit-level) representation** of numbers. Unlike logical operators that work with `True`/`False`, bitwise operators manipulate individual bits (0s and 1s).

**Real-world analogy:** If logical operators are like answering yes/no questions, bitwise operators are like flipping individual switches on a circuit board — each switch (bit) is handled independently.

> **Note for Data Scientists:** Bitwise operators are rarely used in data science. They're primarily used in competitive programming, systems programming, and hardware simulation. This section is included for completeness.

---

## Binary Number System Quick Reference

```
Decimal →  Binary
   1    →  001
   2    →  010
   3    →  011
   4    →  100
   5    →  101
   6    →  110
   7    →  111
```

**How to convert:** Each position represents a power of 2 (from right to left): 2⁰=1, 2¹=2, 2²=4, 2³=8...

```python
# Python has a built-in function to see binary
print(bin(3))    # 0b11
print(bin(7))    # 0b111
print(bin(10))   # 0b1010
```

---

## Syntax & Definition

```python
a & b     # Bitwise AND
a | b     # Bitwise OR
~a        # Bitwise NOT (complement)
a ^ b     # Bitwise XOR
a << n    # Left shift by n positions
a >> n    # Right shift by n positions
```

---

## Detailed Explanation with Examples

### Example 1: Bitwise AND, OR, NOT

```python
a = 3    # Binary: 011
b = 1    # Binary: 001

print(a & b)   # Bitwise AND
print(a | b)   # Bitwise OR
print(~a)      # Bitwise NOT of a
```

**Output:**
```
1
3
-4
```

**How AND works (bit by bit):**
```
  011  (3)
& 001  (1)
------
  001  (1)   → Both bits must be 1
```

**How OR works (bit by bit):**
```
  011  (3)
| 001  (1)
------
  011  (3)   → At least one bit must be 1
```

**How NOT works:** `~a` inverts ALL bits and also flips the sign (due to two's complement representation). `~3` = `-4` because Python uses signed integers.

### Example 2: Bitwise XOR

```python
a = 3    # Binary: 011
b = 1    # Binary: 001

print(a ^ b)   # Bitwise XOR
```

**Output:**
```
2
```

**How XOR works:**
```
  011  (3)
^ 001  (1)
------
  010  (2)   → Exactly one bit must be 1
```

---

# PART 6: CONTROL FLOW — `if`, `elif`, `else`

---

## Concept Overview

**Control flow** statements allow you to control WHICH lines of code execute based on conditions. Without control flow, Python executes every line top-to-bottom with no ability to skip or choose between paths.

**What problem does it solve?** Programs need to make decisions. A login system needs to check if credentials are correct. A shopping app needs to verify if items are in stock. Control flow enables this decision-making.

**Real-world analogy:** Buying apples at a store:
- **IF** the store has 4+ apples → buy them
- **ELSE** → don't buy, go home

This is exactly how `if-else` works in Python.

---

## Syntax & Definition

```python
# Pattern 1: if only (no else)
if condition:
    # code runs only when condition is True
    statement1
    statement2

# Pattern 2: if-else
if condition:
    # runs when condition is True
    statement1
else:
    # runs when condition is False
    statement2

# Pattern 3: if-elif-else (multiple conditions)
if condition1:
    # runs when condition1 is True
    statement1
elif condition2:
    # runs when condition1 is False AND condition2 is True
    statement2
elif condition3:
    # runs when condition1 and condition2 are False AND condition3 is True
    statement3
else:
    # runs when ALL above conditions are False
    statement4
```

**Syntax rules:**
1. `if` keyword + condition + colon `:`
2. Indented block (4 spaces or 1 tab) = code belonging to that condition
3. `elif` and `else` must be at the SAME indentation level as their `if`
4. `else` has NO condition — it's the "catch everything else" block
5. Only ONE block runs in a single if-elif-else chain

---

## Detailed Explanation with Examples

### Example 1: Simple `if-else`

```python
current_batch = 2

if current_batch == 2:
    print("Access to UDS Batch 1.0 also provided")
else:
    print("You only have access to Batch 1.0")
```

**Output:**
```
Access to UDS Batch 1.0 also provided
```

**Execution flow:**
1. Python evaluates `current_batch == 2` → `2 == 2` → `True`
2. Since condition is True, Python enters the `if` block
3. Prints the first message
4. Skips `else` entirely (because `if` was True)

### Example 2: When Condition is False

```python
current_batch = 3

if current_batch == 2:
    print("Access to UDS Batch 1.0 also provided")
else:
    print("You only have access to Batch 1.0")
```

**Output:**
```
You only have access to Batch 1.0
```

**Execution flow:**
1. Python evaluates `current_batch == 2` → `3 == 2` → `False`
2. Since condition is False, Python skips the `if` block
3. Goes to `else` block and executes it

### Example 3: `if` Without `else`

```python
current_batch = 3

if current_batch == 2:
    print("Access to UDS Batch 1.0 also provided")

a = 2
print(a + 2)
```

**Output:**
```
4
```

**Why:** The condition is False, so the `if` block is skipped. Python continues to the next line at the same indentation level (normal code flow). We see only `4` printed.

> **Key Rule:** `if` can exist without `else`. But `else` CANNOT exist without `if`. An `else` alone makes no logical sense — it has no condition to be the "other" of.

### Example 4: `if-elif-else` — Calculator Example

```python
operation_name = "divide"

if operation_name == "add":
    print("Perform addition")
elif operation_name == "subtract":
    print("Perform subtraction")
elif operation_name == "divide":
    print("Perform division")
else:
    print("No selected operation name found")

print("Program ended")
```

**Output:**
```
Perform division
Program ended
```

**Execution flow:**
1. Check `operation_name == "add"` → `"divide" == "add"` → False → skip
2. Check `operation_name == "subtract"` → `"divide" == "subtract"` → False → skip
3. Check `operation_name == "divide"` → `"divide" == "divide"` → **True** → enter this block
4. Print "Perform division"
5. **Exit the entire if-elif-else chain** (don't check `else`)
6. Continue to next line outside the chain: print "Program ended"

### Example 5: Multiple `if` vs `if-elif` — THE Critical Difference

```python
operation_name = "add"

# VERSION 1: Multiple separate if blocks (2 control flow blocks)
if operation_name == "add":
    print("Perform addition — Block 1")

if operation_name == "add":
    print("Perform addition — Block 2")
```

**Output:**
```
Perform addition — Block 1
Perform addition — Block 2
```

```python
# VERSION 2: Single if-elif chain (1 control flow block)
if operation_name == "add":
    print("Perform addition — Block 1")
elif operation_name == "add":
    print("Perform addition — Block 2")
```

**Output:**
```
Perform addition — Block 1
```

**Why the difference?**
- **Multiple `if` statements** = multiple INDEPENDENT control flow blocks. Each `if` starts a NEW block that checks independently. Both can run.
- **`if-elif` chain** = ONE control flow block. Once ANY condition is True, Python executes that block and **exits the entire chain**. The second condition is never even checked.

```
Multiple if:                    if-elif chain:
┌─ Block 1 ─┐                 ┌─ Single Block ──────┐
│ if → check │                 │ if → check          │
└────────────┘                 │ elif → check (skip)  │
┌─ Block 2 ─┐                 │ else → (skip)        │
│ if → check │                 └─────────────────────┘
└────────────┘
(Both checked independently)    (Only first True runs)
```

### Example 6: Multiple Independent Checks

```python
age = 19
batch = 2
session_number = 4

# Three SEPARATE if blocks — all are checked independently
if age > 18:
    print("Age verified")

if batch > 1:
    print("Give access to V1.0")

if session_number > 1:
    print("Introduction done")
```

**Output:**
```
Age verified
Give access to V1.0
Introduction done
```

All three conditions are True, and since they're separate `if` blocks, all three execute.

> **When to use multiple `if`:** When conditions are INDEPENDENT and you want to check ALL of them regardless of other results.
>
> **When to use `if-elif-else`:** When conditions are MUTUALLY EXCLUSIVE and you want only ONE path to execute.

---

## How Indentation Works Internally

```python
if True:
    print("This is inside the if block")    # 4 spaces = part of if
    print("This too")                         # 4 spaces = part of if
print("This is OUTSIDE the if block")        # 0 spaces = normal code
```

**Output:**
```
This is inside the if block
This too
This is OUTSIDE the if block
```

Python uses indentation (not curly braces like Java/C++) to define code blocks:
- Lines with 4 spaces (or 1 tab) after `if:` belong to the if block
- Lines at the original indentation level are outside the block
- Indentation MUST be consistent (don't mix tabs and spaces)

---

## Acceptable Patterns Summary

| Pattern | Valid? | Description |
|---------|--------|-------------|
| `if` alone | ✅ | Check one condition, do something |
| `if` + `else` | ✅ | Two-way decision |
| `if` + `elif` | ✅ | Multiple conditions, no fallback |
| `if` + `elif` + `else` | ✅ | Multiple conditions with fallback |
| `if` + many `elif` + `else` | ✅ | Many conditions with fallback |
| `else` alone | ❌ | Makes no sense without a condition |
| `elif` alone | ❌ | Must follow an `if` |
| `elif` before `if` | ❌ | `if` must come first |

---

## Common Mistakes & Debugging

### Mistake 1: Missing Colon

**Wrong:**
```python
if age > 18
    print("Adult")
```
**Error:**
```
SyntaxError: expected ':'
```
**Why:** Python requires a colon `:` after every `if`, `elif`, and `else` statement.

**Correct:**
```python
if age > 18:
    print("Adult")
```

### Mistake 2: Missing Indentation

**Wrong:**
```python
if age > 18:
print("Adult")
```
**Error:**
```
IndentationError: expected an indented block
```
**Why:** After the colon, Python expects at least one indented line to define the block.

**Correct:**
```python
if age > 18:
    print("Adult")
```

### Mistake 3: Using `=` Instead of `==`

**Wrong:**
```python
if age = 18:    # This ASSIGNS, not compares!
    print("Exactly 18")
```
**Error:**
```
SyntaxError: invalid syntax
```
**Correct:**
```python
if age == 18:   # Double equals for comparison
    print("Exactly 18")
```

### Mistake 4: `else` With a Condition

**Wrong:**
```python
if age > 18:
    print("Adult")
else age <= 18:       # else doesn't take conditions!
    print("Minor")
```
**Error:**
```
SyntaxError: invalid syntax
```
**Correct:**
```python
if age > 18:
    print("Adult")
elif age <= 18:       # Use elif for additional conditions
    print("Minor")
```

---

## Best Practices & Pythonic Way

```python
# ❌ Non-Pythonic: Comparing to True/False explicitly
if is_active == True:
    print("Active")

# ✅ Pythonic: Direct boolean check
if is_active:
    print("Active")

# ❌ Non-Pythonic: Redundant else
if condition:
    return True
else:
    return False

# ✅ Pythonic: Return the condition directly
return condition

# ❌ Non-Pythonic: Deeply nested if-else
if a:
    if b:
        if c:
            do_something()

# ✅ Pythonic: Combine with and
if a and b and c:
    do_something()
```

---

# PART 7: NON-PRIMITIVE DATA TYPES — LISTS

---

## Concept Overview

**Lists** are Python's most versatile non-primitive data type. A list is an **ordered, mutable collection** that can hold items of **any data type** — integers, floats, strings, booleans, even other lists.

**What problem does it solve?** Without lists, storing 100 student IDs requires 100 separate variables. With a list, you need just ONE variable.

**Real-world analogy:** A shopping cart. It holds multiple items of different types (vegetables, drinks, snacks). You can add items, remove items, and check how many you have — all in one container.

**Python lists vs Java arrays:**

| Feature | Python List | Java Array |
|---------|------------|------------|
| Mixed types | ✅ Can hold `int`, `str`, `float`, etc. together | ❌ Single type only |
| Dynamic size | ✅ Grows/shrinks automatically | ❌ Fixed size at creation |
| Syntax | `[1, "hi", 3.14]` | `int[] arr = {1, 2, 3}` |

---

## Syntax & Definition

```python
# Creating lists
empty_list = []                           # Empty list
numbers = [100, 101, 102, 103]            # List of integers
mixed = [1, "hello", 3.14, True]          # Mixed types allowed!
nested = [[1, 2], [3, 4], [5, 6]]        # List of lists

# Common operations
my_list.append(value)    # Add item to END of list (method)
len(my_list)             # Get number of items (function)
print(my_list)           # Display the list
type(my_list)            # Returns <class 'list'>
```

> **Key distinction:** `append()` is a **method** (uses dot notation, belongs to list class). `len()` is a **function** (standalone, works with many types).

---

## Detailed Explanation with Examples

### Example 1: Creating and Printing Lists

```python
id_people = [100, 101, 102, 103, 104, 105]
print(id_people)
print(type(id_people))
```

**Output:**
```
[100, 101, 102, 103, 104, 105]
<class 'list'>
```

### Example 2: Mixed Data Types in a Single List

```python
people = [101, 100, 102, "A", "B", "C", 9.5, True]
print(people)
```

**Output:**
```
[101, 100, 102, 'A', 'B', 'C', 9.5, True]
```

**Why this works:** Python lists are dynamically typed. They don't enforce a single data type — each element is stored as a reference to a Python object, regardless of its type.

### Example 3: `append()` — Adding Items

```python
dummy = []            # Start with empty list
print(f"Empty list: {dummy}, Length: {len(dummy)}")

dummy.append(0)       # Add integer
print(f"After append(0): {dummy}")

dummy.append("hello") # Add string
print(f"After append('hello'): {dummy}")

dummy.append([1, 2, 3])  # Add another list as a single element!
print(f"After append([1,2,3]): {dummy}")

print(f"Total elements: {len(dummy)}")
```

**Output:**
```
Empty list: [], Length: 0
After append(0): [0]
After append('hello'): [0, 'hello']
After append([1,2,3]): [0, 'hello', [1, 2, 3]]
Total elements: 3
```

**Key insight:** When you append a list to a list, the ENTIRE inner list counts as ONE element. That's why `len()` returns 3, not 5.

### Example 4: Real-World — Building Register System

```python
# Building entry register — from 700 variables to 1 variable!
register = []    # One variable to hold all entries

# Person 1 entry: [name, flat_number, phone, reason]
register.append(["Monal", 101, "+911234567890", "visit"])
print(f"After person 1: {register}")

# Person 2 entry
register.append(["Pradeep", 202, "+919876543210", "delivery"])
print(f"After person 2: {register}")

print(f"Total entries: {len(register)}")
```

**Output:**
```
After person 1: [['Monal', 101, '+911234567890', 'visit']]
After person 2: [['Monal', 101, '+911234567890', 'visit'], ['Pradeep', 202, '+919876543210', 'delivery']]
Total entries: 2
```

**The optimization:**
- Without lists: 7 variables × 100 people = **700 variables**
- With lists per person: 1 variable × 100 people = **100 variables**
- With nested lists: **1 variable** holds everything

### Example 5: Understanding Double Brackets `[[ ]]`

```python
register = []                     # Outer list: []
register.append([1, 2, 3])       # Now: [[1, 2, 3]]
register.append([4, 5, 6])       # Now: [[1, 2, 3], [4, 5, 6]]
```

**Visual breakdown:**
```
register = [                      ← Outer list bracket
    [1, 2, 3],                    ← First element (a list itself)
    [4, 5, 6]                     ← Second element (a list itself)
]                                 ← Outer list bracket

register has 2 elements, NOT 6
```

### Example 6: `len()` — Counting Elements

```python
dummy = [0, 0, "hello", [1, 2, 3], ["hello", "my name", 101]]
print(len(dummy))
```

**Output:**
```
5
```

**Counting:** Element 1: `0`, Element 2: `0`, Element 3: `"hello"`, Element 4: `[1,2,3]` (one element), Element 5: `["hello", "my name", 101]` (one element). Total = 5.

---

## How It Works Internally

```
Memory Layout:

register = []           →  register points to empty list object

register.append([...])  →  register → [ ref_to_list1 ]
                                        ↓
                                      [1, 2, 3]

register.append([...])  →  register → [ ref_to_list1, ref_to_list2 ]
                                        ↓              ↓
                                      [1, 2, 3]     [4, 5, 6]
```

Lists store **references** to objects, not the objects themselves. This is why a list can hold any type — it just stores pointers.

---

## Common Mistakes & Debugging

### Mistake 1: Running `append()` Cell Multiple Times in Jupyter

```python
dummy = []
dummy.append(0)
print(dummy)
```

If you run this cell 3 times, the output will be:
```
[0, 0, 0]
```

**Why:** Jupyter notebooks maintain state across cells. Each run appends another `0`. To reset, re-run the cell that creates the empty list.

### Mistake 2: Confusing `append()` with Direct Assignment

**Wrong intention:**
```python
my_list = []
my_list = 5      # This replaces the list with integer 5!
```

**Correct:**
```python
my_list = []
my_list.append(5)  # Adds 5 to the list
```

---

## When to Use / When NOT to Use

| Use Lists When... | Avoid Lists When... |
|-------------------|---------------------|
| Storing multiple related items | You need key-value pairs (use `dict`) |
| Order matters | You need unique items only (use `set`) |
| Items need to be added/removed | Data should not change (use `tuple`) |
| You need mixed data types | Large numerical computations (use `numpy` arrays) |

---

# Related Topics & Connections

- **Prerequisites:** Variables, data types, type casting, basic arithmetic operators
- **Topics covered that connect:** Functions vs methods, classes, Boolean logic
- **What to learn next:**
  - More list methods: `insert()`, `remove()`, `pop()`, `sort()`, `reverse()`
  - List indexing and slicing
  - Dictionary, tuple, and set data types
  - Loops (`for`, `while`) — iterate through lists
  - Nested if-else (advanced control flow)
  - List comprehensions

---

## Interview & Exam Corner

### Frequently Asked Questions

1. **Q: What's the difference between a function and a method?**
   A: A function is standalone code called by name (e.g., `print()`). A method belongs to a class and is called using dot notation on an object (e.g., `"hello".upper()`). All methods are functions, but not all functions are methods.

2. **Q: Can `else` exist without `if`?**
   A: No. `else` requires a preceding `if` (or `for`/`while`/`try` in other contexts). Standalone `else` raises a `SyntaxError`.

3. **Q: What's the difference between `=` and `==`?**
   A: `=` is assignment (stores a value: `x = 5`). `==` is comparison (checks equality: `x == 5` returns `True` or `False`).

4. **Q: Can a Python list hold different data types?**
   A: Yes! Unlike arrays in many languages, Python lists can hold `int`, `str`, `float`, `bool`, and even other lists simultaneously.

5. **Q: How many `elif` blocks can you have?**
   A: Unlimited. You can have as many `elif` blocks as needed between `if` and `else`.

6. **Q: Does `strip()` remove spaces from the middle of a string?**
   A: No. `strip()` only removes leading and trailing whitespace. Use `.replace(" ", "")` for internal spaces.

### Tricky Output Questions

```python
# Question 1: What will this print?
x = 5
if x > 3:
    print("A")
if x > 4:
    print("B")
if x > 5:
    print("C")
```
**Answer:** `A` and `B` (on separate lines). **Why:** These are three SEPARATE `if` blocks, not if-elif. Each is checked independently. `x > 5` is False, so "C" is not printed.

```python
# Question 2: What will this print?
x = 5
if x > 3:
    print("A")
elif x > 4:
    print("B")
elif x > 5:
    print("C")
```
**Answer:** Only `A`. **Why:** This is ONE if-elif chain. Once `x > 3` is True, Python prints "A" and exits the chain entirely.

```python
# Question 3: What will this print?
a = 10
a += 5
a *= 2
print(a)
```
**Answer:** `30`. **Why:** `a = 10` → `a += 5` makes `a = 15` → `a *= 2` makes `a = 30`.

```python
# Question 4: What will this print?
print(not not True)
```
**Answer:** `True`. **Why:** `not True` = `False`, then `not False` = `True`.

```python
# Question 5: What will this print?
my_list = [1, [2, 3], "hello"]
print(len(my_list))
```
**Answer:** `3`. **Why:** The list has three elements: `1`, `[2, 3]` (counts as one element), and `"hello"`.

---

## Quick-Reference Cheat Sheet

```
PYTHON DAY 2 CHEAT SHEET
===============================================

STRING METHODS
  "  hi  ".strip()      → "hi"        (both sides)
  "  hi  ".lstrip()     → "hi  "      (left only)
  "  hi  ".rstrip()     → "  hi"      (right only)
  "hi".strip().upper()  → chaining OK

RELATIONAL OPERATORS
  >   Greater than         5 > 3   → True
  <   Less than            5 < 3   → False
  ==  Equal to             5 == 5  → True
  >=  Greater or equal     5 >= 5  → True
  <=  Less or equal        3 <= 5  → True
  !=  Not equal            5 != 3  → True

ASSIGNMENT OPERATORS
  a = 5                   Assign
  a += 3   →  a = a + 3  Add & assign
  a -= 3   →  a = a - 3  Subtract & assign
  a *= 3   →  a = a * 3  Multiply & assign
  a /= 3   →  a = a / 3  Divide & assign

LOGICAL OPERATORS
  and   → True only if BOTH True
  or    → True if ANY True
  not   → Flips True↔False

CONTROL FLOW
  if condition:      → check condition
  elif condition:    → check if above failed
  else:              → runs if ALL above failed
  NOTE: Only ONE block runs per chain!

LISTS
  []                 → empty list
  [1, "hi", 3.14]   → mixed types OK
  .append(value)     → add to end (method)
  len(my_list)       → count elements (function)

KEY DISTINCTIONS
  Function: standalone    → print(), len(), type()
  Method: uses dot        → "hi".strip(), [].append()
  = : assignment          → x = 5
  == : comparison         → x == 5
  Multiple if: independent checks
  if-elif: only first True runs
```

---

## Summary (TL;DR)

- **Methods vs Functions:** Methods use dot notation and belong to a class (`str.strip()`). Functions are standalone (`print()`, `len()`).
- **String methods** `strip()`, `lstrip()`, `rstrip()` remove whitespace from both, left, or right sides of a string — essential for cleaning user input.
- **Relational operators** (`>`, `<`, `==`, `>=`, `<=`, `!=`) compare two values and return `True` or `False`.
- **Assignment operators** (`+=`, `-=`, `*=`, `/=`) are shortcuts for modifying a variable in place. `a += 1` is equivalent to `a = a + 1`.
- **Logical operators** (`and`, `or`, `not`) combine Boolean conditions. AND requires both True; OR requires any True; NOT flips the value.
- **Control flow** (`if`/`elif`/`else`) lets you execute code conditionally. Only ONE block runs per chain. `if` can exist alone; `else`/`elif` cannot exist without `if`.
- **Lists** are ordered, mutable collections that hold ANY data type. Use `append()` to add items, `len()` to count them. They solve the "too many variables" problem.

---

## Flashcard-Style Recall (Q&A Pairs)

- **Q:** What is a method in Python?
  **A:** A function that belongs to a specific class, called using dot notation (e.g., `"text".strip()`).

- **Q:** How do you remove spaces from both sides of a string?
  **A:** Use `string.strip()`. For left only: `lstrip()`. For right only: `rstrip()`.

- **Q:** What's the difference between `=` and `==`?
  **A:** `=` assigns a value to a variable. `==` compares two values for equality and returns True/False.

- **Q:** What does `a += 5` mean?
  **A:** It's a shortcut for `a = a + 5`. It adds 5 to the current value of `a` and saves the result back.

- **Q:** When does `and` return True?
  **A:** Only when BOTH operands are True.

- **Q:** When does `or` return True?
  **A:** When AT LEAST ONE operand is True.

- **Q:** Can `else` exist without `if`?
  **A:** No. `else` always requires a preceding `if`. Standalone `else` causes a SyntaxError.

- **Q:** What's the difference between multiple `if` statements and `if-elif`?
  **A:** Multiple `if` = independent checks (all can run). `if-elif` = one chain, only the first True block runs.

- **Q:** Can a Python list hold different data types?
  **A:** Yes. `[1, "hello", 3.14, True, [1,2]]` is perfectly valid.

- **Q:** What error occurs when you use `strip()` on an integer?
  **A:** `AttributeError: 'int' object has no attribute 'strip'` — because `strip()` is a method of the `str` class only.

- **Q:** What does `len([1, [2,3], "hi"])` return?
  **A:** `3` — the inner list `[2,3]` counts as a single element.

- **Q:** Does Python support `a++` increment syntax?
  **A:** No. Python does not have `++` or `--` operators. Use `a += 1` instead.
