# Python Day 4 — Non-Primitive Data Types: List, Dictionary, Tuple & Set

---

## Concept Overview

This session covers Python's four **non-primitive (complex) data types**: **List**, **Dictionary**, **Tuple**, and **Set**. These are advanced containers that hold multiple values inside a single variable, unlike primitive types (`int`, `float`, `str`, `bool`) that hold only one value at a time.

**Why do these exist?** Imagine you have 1,000 student records. Without non-primitive data types, you'd need 1,000 separate variables. These data structures let you store, organize, access, and manipulate collections of data efficiently — the backbone of every real-world application.

**Real-world analogy:** Think of a **shopping bag** (list) where you toss items in order, a **register book** (dictionary) where each person's name maps to their details, a **laminated ID card** (tuple) whose content can never be changed, and a **guest list** (set) that automatically rejects duplicate names.

**Where it fits:** Non-primitive data types are the foundation for data manipulation in Python. Every area — web development, machine learning, APIs, databases — relies heavily on lists, dictionaries, tuples, and sets. In AI/ML specifically, data is communicated between systems using dictionaries (JSON format).

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **Non-Primitive Data Type** | Complex data types that can hold multiple values: `list`, `dict`, `tuple`, `set`. |
| **Mutable** | Can be changed after creation (elements can be added, removed, or modified). |
| **Immutable** | Cannot be changed after creation (fixed once defined). |
| **Index** | A number indicating the position of an element in a sequence. Starts from `0` in Python. |
| **Negative Index** | Accessing elements from the end. `-1` is the last element, `-2` is second-to-last, etc. |
| **Key-Value Pair** | A dictionary structure where each **key** (label) maps to a **value** (data). |
| **Hashable** | An object that has a fixed hash value during its lifetime. Required for dictionary keys and set elements. Only immutable types are hashable. |
| **Element** | A single item/value stored inside a data structure. |
| **Method** | A function attached to a data type, called via dot notation (e.g., `list.append()`). |
| **Function** | A standalone callable (e.g., `len()`, `sorted()`). Works with multiple data types. |
| **Iterable** | Any object that contains multiple values and can be looped over (lists, dicts, tuples, sets, strings). |
| **Traversal** | The process of accessing elements inside nested data structures step by step. |
| **Unordered** | A collection that does not maintain the sequence in which elements were added. |
| **Data Integrity** | Ensuring data remains accurate and unchanged when it shouldn't be modified. |

---

# PART 1: LIST

---

## Concept Overview

A **list** is an ordered, mutable collection that can hold any data type (including other lists). It maintains the **insertion order** — elements stay in the sequence you added them.

**Real-world analogy:** A list is like a **queue of people at a counter**. Each person gets a position number (index) starting from the person closest to the counter. You can add people to the queue (append), remove people (pop), or insert someone at a specific position (insert).

**Why use it?** When you need to store multiple related values in a single variable and may need to modify that collection later.

---

## Syntax & Definition

```python
# Creating a list
my_list = []                              # Empty list (Method 1)
my_list = list()                          # Empty list (Method 2)
my_list = [value1, value2, value3]        # List with initial values

# Lists accept ANY data type, including duplicates
mixed_list = ["hello", 42, 3.14, True, [1, 2, 3]]
```

**Key properties:**
1. **Ordered** — maintains the sequence in which elements are appended
2. **Mutable** — elements can be added, removed, or changed after creation
3. **Accepts any data type** — integers, strings, booleans, other lists, etc.
4. **Allows duplicates** — the same value can appear multiple times
5. **Uses square brackets** `[]`

---

## Detailed Explanation with Examples

### Example 1: Creating a List and Understanding Index

```python
L = ["hello", "hey", "Python", 10, False]
print(L)
```

**Output:**
```
['hello', 'hey', 'Python', 10, False]
```

**How indexing works:**

```
 Element:    "hello"   "hey"   "Python"    10     False
 Forward:       0        1        2         3       4
 Reverse:      -5       -4       -3        -2      -1
```

**Two directions of indexing:**
- **Forward (left to right):** starts at `0`, increments by `+1` → `0, 1, 2, 3, 4`
- **Reverse (right to left):** starts at `-1`, decrements by `-1` → `-1, -2, -3, -4, -5`

```python
# Accessing elements using forward index
print(L[0])     # "hello"  → index 0
print(L[3])     # 10       → index 3

# Accessing elements using negative index
print(L[-1])    # False    → last element
print(L[-2])    # 10       → second from last
```

**Output:**
```
hello
10
False
10
```

**Why negative indexing exists:** When you don't know the length of a list and need to access the last element, you can simply use `L[-1]` instead of calculating the index manually. This solves the "get the last value" problem elegantly.

---

### Example 2: List Methods — `append()`, `pop()`, `remove()`, `insert()`

#### `append()` — Add an element to the END

```python
L = []
L.append(0)
print(L)        # [0]

L.append(2)
print(L)        # [0, 2]

L.append(2)
print(L)        # [0, 2, 2]  → duplicates are allowed
```

**Output:**
```
[0]
[0, 2]
[0, 2, 2]
```

**Line-by-line breakdown:**
- Line 1: Creates an empty list
- Line 2: Appends `0` → list becomes `[0]`
- Line 4: Appends `2` → list becomes `[0, 2]` (added to the END, not sorted)
- Line 7: Appends `2` again → `[0, 2, 2]` (duplicates are perfectly valid)

**Why this works:** `append()` always adds to the **end** of the list. It does NOT sort or rearrange — it preserves insertion order.

---

#### `pop()` — Remove an element by INDEX

```python
L = ["hello", "hey", "Python", 10, False]
print(L)

# Pop without argument → removes LAST element (default behavior)
removed = L.pop()
print(f"Removed: {removed}")
print(L)

# Pop with index → removes element at specified index
removed = L.pop(0)
print(f"Removed: {removed}")
print(L)
```

**Output:**
```
['hello', 'hey', 'Python', 10, False]
Removed: False
['hello', 'hey', 'Python', 10]
Removed: hello
['hey', 'Python', 10]
```

**Line-by-line breakdown:**
- `L.pop()` with no argument removes the **last** element (`False`), and returns what it removed
- `L.pop(0)` removes the element at **index 0** (`"hello"`)
- After removal, all elements shift left to fill the gap — `"hey"` becomes the new index 0

> **Pro tip:** `pop()` both removes AND returns the removed value. This is useful when you need to use the removed element.

**Index shifting after pop:**
```
Before pop(0):  ["hello", "hey", "Python", 10]
                   0        1       2       3

After pop(0):   ["hey", "Python", 10]
                   0       1       2
```

When an element is removed, everything to its right shifts one position LEFT.

---

#### `remove()` — Remove an element by VALUE

```python
L = ["hey", "Python", 10, True, "hey"]
print(L)

# Remove first occurrence of "hey"
L.remove("hey")
print(L)

# Remove remaining "hey"
L.remove("hey")
print(L)
```

**Output:**
```
['hey', 'Python', 10, True, 'hey']
['Python', 10, True, 'hey']
['Python', 10, True]
```

**Key differences between `pop()` and `remove()`:**

| Feature | `pop()` | `remove()` |
|---------|---------|------------|
| Identifies element by | **Index** (position number) | **Value** (actual content) |
| Default behavior | Removes **last** element | No default — value is required |
| Returns removed value | **Yes** | **No** |
| Multiple occurrences | Removes at specified index | Removes **first occurrence only** |
| Use when | You know the position | You know the value but not the position |

> **Important:** `remove()` only deletes the **first** occurrence. If you have 100 duplicates, you need to call `remove()` 100 times (or use a loop).

> **Case sensitivity:** `"hey"` and `"Hey"` are different values. Python is case-sensitive — all programming languages are.

---

#### `insert()` — Add an element at a SPECIFIC position

```python
L = ["Python", 10, True]
print(L)

# Insert "machine learning" at index 2
L.insert(2, "machine learning")
print(L)
```

**Output:**
```
['Python', 10, True]
['Python', 10, 'machine learning', True]
```

**Line-by-line breakdown:**
- `L.insert(2, "machine learning")` — inserts the value `"machine learning"` at **index 2**
- Everything at index 2 and beyond shifts **right** by one position
- `True` was at index 2, now moves to index 3

**Insert syntax:** `list.insert(index, value)`
- First argument: WHERE to insert (index position)
- Second argument: WHAT to insert (the value)

---

### Example 3: Replacing Values in a List

```python
L = ["Python", 10, "machine learning", True]
print(L[0])     # "Python"

# Replace value at index 0
L[0] = "Python v2.0"
print(L)
```

**Output:**
```
Python
['Python v2.0', 10, 'machine learning', True]
```

**Why this works:** Lists are **mutable**. You can directly assign a new value to any index using `L[index] = new_value`. This is a fundamental property that distinguishes lists from tuples.

---

## The `len()` Function

`len()` is a **built-in function** (not a method) that counts elements.

```python
# With strings: counts characters
print(len("data"))      # 4

# With lists: counts elements
L = ["hello", "hey", "Python", 10, False]
print(len(L))           # 5
```

**Output:**
```
4
5
```

> **Critical distinction: `len()` vs Index**
>
> | Concept | How it counts | Example (5 elements) |
> |---------|--------------|---------------------|
> | `len()` | Starts from **1** (total count) | `len(L)` = **5** |
> | Index | Starts from **0** (position) | Last index = **4** |
>
> **Formula:** `last_index = len(L) - 1`

### Common Mistake: Index Out of Range

```python
L = ["hello", "hey", "Python", 10, False]
print(len(L))       # 5
print(L[5])         # ERROR!
```

**Error:**
```
IndexError: list index out of range
```

**Why it fails:** The list has 5 elements, but indices go from 0 to 4. Index 5 does not exist.

**Correct approach:**
```python
# Access last element using len()
print(L[len(L) - 1])    # False   → 5 - 1 = 4 → L[4]

# Or simply use negative indexing (preferred)
print(L[-1])             # False   → much cleaner!
```

**Output:**
```
False
False
```

> **Best practice:** Use negative indexing (`L[-1]`) instead of `L[len(L) - 1]`. It's cleaner, more Pythonic, and requires fewer operations.

---

# PART 2: DICTIONARY

---

## Concept Overview

A **dictionary** (`dict`) stores data as **key-value pairs**. Instead of accessing values by position (index), you access them by a meaningful **label (key)**. This makes data retrieval fast and intuitive.

**Real-world analogy:** Think of a **visitor register** at an office. Each row has a person's **name** (key) and their **details** — phone number, address, ID (values). To find Preetam's details, you don't scan every column — you go directly to the "Name" column, find "Preetam", and read across. The name IS the key that unlocks the values.

**Why use it?** When you need to associate labels with data. In a list, you'd need to remember that index 3 holds the phone number. In a dictionary, you simply use `person["phone"]` — self-documenting and efficient.

**Critical importance:** Dictionaries are the foundation of **JSON** (JavaScript Object Notation), the universal data exchange format used in web APIs, machine learning model configurations, and communication between virtually all modern systems.

---

## Syntax & Definition

```python
# Creating a dictionary
my_dict = {}                                    # Empty dict (Method 1)
my_dict = dict()                                # Empty dict (Method 2)

# Dictionary with data — key: value pairs
my_dict = {
    "key1": "value1",
    "key2": "value2",
    "key3": "value3"
}

# Syntax rule: key: value, separated by commas
# Uses CURLY BRACES {}
```

**Key properties:**
1. **Key-value pairs** — every entry has a key (label) and associated value
2. **Keys must be unique** — no two keys can have the same name
3. **Keys must be immutable** — only `str`, `int`, `float`, `tuple` can be keys (NOT `list`, `dict`, or `set`)
4. **Values can be anything** — integers, strings, lists, other dictionaries, tuples, booleans, etc.
5. **Duplicate values are allowed** — different keys can map to identical values
6. **Uses curly braces** `{}`

---

## Detailed Explanation with Examples

### Example 1: Basic Dictionary — The Register Concept

```python
register = {
    "Rohit": ["12-12-2000", "1234567890", "ABCDEFGH"],
    "Rohith": ["05-03-1995", "9876543210", "XYZWVUTS"],
    "Monal": False
}

print(register)
```

**Output:**
```
{'Rohit': ['12-12-2000', '1234567890', 'ABCDEFGH'], 'Rohith': ['05-03-1995', '9876543210', 'XYZWVUTS'], 'Monal': False}
```

**Line-by-line breakdown:**
- `"Rohit"` is a key (string), its value is a list of details
- `"Rohith"` is a DIFFERENT key (different spelling) — keys must be unique
- `"Monal"` has `False` as its value — values can be any data type
- If you used `"Rohit"` twice, the second would **overwrite** the first (last one wins)

---

### Example 2: Accessing Values — Two Methods

```python
government_sys = {
    101: ["Monal", "01-01-1990", "+91-111"],
    102: ["Amit", "15-06-1985", "+91-222"],
    103: ["Dahwal", "20-11-1998", "+91-333"],
    104: ["Surya", "08-03-2001", "+91-444"]
}

# Method 1: Square bracket notation
print(government_sys[101])

# Method 2: .get() method
print(government_sys.get(101))
```

**Output:**
```
['Monal', '01-01-1990', '+91-111']
['Monal', '01-01-1990', '+91-111']
```

**Critical difference between `[]` and `.get()`:**

```python
# When key does NOT exist:

# Square bracket → RAISES KeyError (breaks your code)
print(government_sys[105])    # KeyError: 105

# .get() → Returns None silently (code continues)
print(government_sys.get(105))    # None (no output, no error)

# .get() with default value → Returns your custom message
print(government_sys.get(105, "Value not present"))    # "Value not present"
```

| Feature | `dict[key]` | `dict.get(key)` |
|---------|-------------|-----------------|
| Key exists | Returns value | Returns value |
| Key missing | **Raises `KeyError`** | Returns `None` |
| Default value | Not supported | `dict.get(key, default)` |
| Code stability | Can crash your program | Safe — never raises error |
| Use when | You're SURE the key exists | You're not sure, or want safety |

> **Pro tip (Interview question):** `.get()` is more stable for production code because it prevents crashes. Think of it like an `if-else`: if the key exists, return the value; else, return the default.

---

### Example 3: `.keys()` and `.values()`

```python
government_sys = {
    101: ["Monal", "01-01-1990", "+91-111"],
    102: ["Amit", "15-06-1985", "+91-222"],
    103: ["Dahwal", "20-11-1998", "+91-333"],
    104: ["Surya", "08-03-2001", "+91-444"]
}

# Get all keys
print(government_sys.keys())

# Get all values
print(government_sys.values())

# Convert to list for easier manipulation
keys_list = list(government_sys.keys())
values_list = list(government_sys.values())
print(keys_list)
print(values_list)
```

**Output:**
```
dict_keys([101, 102, 103, 104])
dict_values([['Monal', '01-01-1990', '+91-111'], ['Amit', '15-06-1985', '+91-222'], ['Dahwal', '20-11-1998', '+91-333'], ['Surya', '08-03-2001', '+91-444']])
[101, 102, 103, 104]
[['Monal', '01-01-1990', '+91-111'], ['Amit', '15-06-1985', '+91-222'], ['Dahwal', '20-11-1998', '+91-333'], ['Surya', '08-03-2001', '+91-444']]
```

**Why the outer brackets?** When `.keys()` returns multiple keys (101, 102, 103, 104), Python can't store four separate values in a single variable. So it wraps them in a list-like structure (`dict_keys`). Similarly, `.values()` returns all values wrapped in `dict_values`. You can convert these to regular lists using `list()`.

**Why two sets of brackets in values?** Each value is itself a list (e.g., `["Monal", "01-01-1990", "+91-111"]`). When Python returns all four lists, it wraps them in an outer list — creating a "list of lists".

---

### Example 4: Designing Keys Like a Developer

```python
# BAD: Using names as keys (names can duplicate)
register_bad = {
    "Rohit": ["details..."],
    "Rohit": ["other details..."]    # Overwrites the first Rohit!
}

# GOOD: Using unique IDs as keys
register_good = {
    "ID_001": ["Rohit", "Mumbai", "9876543210"],
    "ID_002": ["Rohit", "Delhi", "1234567890"]    # Same name, different person — no conflict!
}
```

**Why this matters:** In real-world systems (government databases, user registrations), names are NOT unique. That's why every system uses unique IDs (Aadhaar, SSN, Employee ID). As a developer, always choose a **unique identifier** as your dictionary key.

---

# PART 3: TUPLE

---

## Concept Overview

A **tuple** is an **ordered, immutable** collection. Once created, its values **cannot be changed** — no appending, no removing, no reassigning individual elements.

**Real-world analogy:** A tuple is like your **date of birth** or **GPS coordinates** of a city. Your birthday is `(10, "Feb", 1920)` — this fact should never change. Bangalore's coordinates are `(12.9716, 77.5946)` — the city doesn't move. Any data that must remain constant belongs in a tuple.

**Why use it?** To protect data integrity. If you store something in a list, any part of your code can accidentally modify it. A tuple guarantees the data stays exactly as it was created.

---

## Syntax & Definition

```python
# Creating a tuple
my_tuple = ()                           # Empty tuple (Method 1)
my_tuple = tuple()                      # Empty tuple (Method 2)
my_tuple = (value1, value2, value3)     # Tuple with values

# Uses PARENTHESES ()
```

**Key properties:**
1. **Ordered** — maintains insertion order
2. **Immutable** — CANNOT be changed after creation (no append, no pop, no reassignment)
3. **Accepts any data type** — integers, strings, lists, etc.
4. **Allows duplicates**
5. **Supports indexing** — access elements with `tuple[index]`
6. **Uses parentheses** `()`
7. **Hashable** — can be used as dictionary keys (unlike lists)

---

## Mutable vs Immutable — The Core Concept

Before diving into tuple examples, understand this fundamental classification:

| Property | Mutable | Immutable |
|----------|---------|-----------|
| **Meaning** | Can be changed after creation | Cannot be changed after creation |
| **Operations** | Append, pop, insert, reassign | Read-only after creation |
| **Data types** | `list`, `dict`, `set` | `tuple`, `str`, `int`, `float`, `bool` |
| **Use case** | Data that needs modification | Data that must remain constant |
| **Hashable** | No | Yes |
| **As dict key** | Not allowed | Allowed |

```python
# MUTABLE: List allows changes
my_list = [1, 2, 3]
my_list[0] = 99           # Works fine
my_list.append(4)          # Works fine
print(my_list)             # [99, 2, 3, 4]

# IMMUTABLE: Tuple rejects changes
my_tuple = (1, 2, 3)
my_tuple[0] = 99           # TypeError!
```

**Error:**
```
TypeError: 'tuple' object does not support item assignment
```

---

## Detailed Explanation with Examples

### Example 1: Basic Tuple — Date of Birth

```python
date_of_birth = (10, "Feb", 1920)
print(type(date_of_birth))    # <class 'tuple'>
print(date_of_birth)          # (10, 'Feb', 1920)

# Accessing elements (indexing works just like lists)
print(date_of_birth[0])       # 10
print(date_of_birth[1])       # Feb
print(date_of_birth[-1])      # 1920

# Trying to modify → ERROR
date_of_birth[0] = 25         # TypeError!
```

**Output:**
```
<class 'tuple'>
(10, 'Feb', 1920)
10
Feb
1920
TypeError: 'tuple' object does not support item assignment
```

---

### Example 2: Tuple Inside a Dictionary — Real-World Structure

```python
register = {
    101: {
        "name": {"first_name": "Monal", "last_name": "Singh"},
        "dob": (10, "Feb", 1920),           # Tuple — cannot change birthday
        "phone": "+91-1111111111"
    },
    102: {
        "name": {"first_name": "Ashish", "last_name": "Kumar"},
        "dob": (5, "Mar", 1990),
        "phone": "+91-2222222222"
    }
}

print(register[101])
```

**Output:**
```
{'name': {'first_name': 'Monal', 'last_name': 'Singh'}, 'dob': (10, 'Feb', 1920), 'phone': '+91-1111111111'}
```

**Why tuple for DOB?** Date of birth should never be modified accidentally. Using a tuple ensures data integrity — even if someone tries to change it, Python will throw an error.

---

### Example 3: Traversing Nested Structures

```python
register = {
    101: {
        "name": {"first_name": "Monal", "last_name": "Singh"},
        "dob": (10, "Feb", 1920),
        "phone": "+91-1111111111"
    }
}

# Step 1: Access the outer dictionary → get person 101's data
person_data = register[101]
print(person_data)
# {'name': {'first_name': 'Monal', 'last_name': 'Singh'}, 'dob': (10, 'Feb', 1920), 'phone': '+91-1111111111'}

# Step 2: Access DOB from person's data
dob = register[101]["dob"]
print(dob)                # (10, 'Feb', 1920)
print(type(dob))          # <class 'tuple'>

# Step 3: Access specific element of DOB tuple
year = register[101]["dob"][-1]
print(year)               # 1920

# Alternative: Using .get() chain
dob_via_get = register.get(101).get("dob")
print(dob_via_get)        # (10, 'Feb', 1920)
```

**Output:**
```
{'name': {'first_name': 'Monal', ...}, 'dob': (10, 'Feb', 1920), 'phone': '+91-1111111111'}
(10, 'Feb', 1920)
<class 'tuple'>
1920
(10, 'Feb', 1920)
```

**How traversal works — step by step:**

```
register[101]               → returns inner dictionary
register[101]["dob"]        → returns tuple (10, "Feb", 1920)
register[101]["dob"][-1]    → returns 1920

Python replaces each step:
  register[101]       → {name: ..., dob: ..., phone: ...}
  {...}["dob"]         → (10, "Feb", 1920)
  (10, "Feb", 1920)[-1] → 1920
```

> **Key insight:** You can chain `[]` or `.get()` as many levels deep as needed. Each step returns a value, and the next `[]` operates on THAT returned value.

---

### Understanding Variable Reassignment vs Tuple Immutability

```python
# This is NOT changing the tuple — it's reassigning the variable
a = (1, 2, 3)      # a points to tuple (1, 2, 3) in memory
a = 10              # a now points to integer 10 — old tuple is abandoned

# This IS trying to change the tuple — FAILS
a = (1, 2, 3)
a[0] = 99           # TypeError! Can't modify tuple elements
```

**Memory model:**
```
Step 1: a = (1, 2, 3)
   a ──→ [Memory: (1, 2, 3)]

Step 2: a = 10
   a ──→ [Memory: 10]          ← a now points to new location
         [Memory: (1, 2, 3)]   ← old tuple still exists until garbage collected

Step 3: a[0] = 99
   a ──→ [Memory: (1, 2, 3)]
   Trying to MODIFY the tuple itself → BLOCKED by Python
```

**The distinction:** Reassigning the **variable** (changing what `a` points to) is always allowed. Modifying the **content** of a tuple is what's forbidden.

---

# PART 4: SET

---

## Concept Overview

A **set** is an **unordered** collection that stores only **unique values**. If you add duplicate elements, the set automatically removes them. Sets are primarily used for membership testing and eliminating duplicates.

**Real-world analogy:** A set is like a **guest list** at an exclusive party. If someone's name is already on the list, writing it again doesn't add a second entry — the list only keeps unique names. Also, the list isn't sorted alphabetically; names appear in no particular order.

**Why use it?** When you need to ensure uniqueness or quickly remove duplicates from a collection.

---

## Syntax & Definition

```python
# Creating a set
my_set = set()                              # Empty set (ONLY way)
my_set = {value1, value2, value3}           # Set with values

# WARNING: {} creates an EMPTY DICTIONARY, not an empty set!
empty_dict = {}          # This is a DICTIONARY
empty_set = set()        # This is a SET
```

**Key properties:**
1. **Unordered** — does NOT maintain insertion order (elements may appear in any sequence)
2. **Mutable** — you can add/remove elements (but cannot change existing elements)
3. **Unique values only** — duplicates are automatically removed
4. **Elements must be immutable (hashable)** — strings, numbers, tuples allowed; lists, dicts NOT allowed
5. **No indexing** — you cannot access elements by position (`my_set[0]` is an error)
6. **Uses curly braces** `{}` (but empty set must use `set()`)

---

## Detailed Explanation with Examples

### Example 1: Automatic Duplicate Removal

```python
guests = {"Alice", "Tanmay", "Sudarshan", 123, 101, "Alice", 123}
print(guests)
```

**Output:**
```
{101, 'Tanmay', 'Sudarshan', 123, 'Alice'}
```

**What happened:**
- `"Alice"` appeared twice → only one kept
- `123` appeared twice → only one kept
- Order changed from what we typed → sets are **unordered**

> **Important:** Don't rely on the order of elements in a set. The order can change between runs. Sets are designed for **uniqueness**, not **ordering**.

---

### Example 2: Adding Elements with `.add()`

```python
guests = set()             # Create empty set
print(guests)              # set()

guests.add("Alice")
guests.add("Bob")
guests.add(10)
print(guests)              # {10, 'Alice', 'Bob'} — order may vary

guests.add("Alice")        # Duplicate — ignored
print(guests)              # {10, 'Alice', 'Bob'} — no change
```

**Output:**
```
set()
{'Bob', 10, 'Alice'}
{'Bob', 10, 'Alice'}
```

| List Method | Set Method | Purpose |
|-------------|-----------|---------|
| `list.append(value)` | `set.add(value)` | Add a single element |
| `list.pop()` | `set.pop()` | Remove an element |

---

### Example 3: Why Lists Cannot Be Set Elements

```python
# This WORKS — tuples are immutable (hashable)
valid_set = {(1, 2), (3, 4), "hello"}
print(valid_set)

# This FAILS — lists are mutable (not hashable)
invalid_set = {[1, 2], [3, 4]}    # TypeError!
```

**Error:**
```
TypeError: unhashable type: 'list'
```

**Why it fails:** Sets use **hash values** internally to check for duplicates. Lists are mutable, so their hash value could change at any time, making duplicate detection unreliable. Only immutable (hashable) objects can be stored in a set.

> **Interview question:** "Why can't a list be inside a set?" Answer: Because sets rely on hash values for uniqueness checks, and mutable objects like lists don't have a stable hash value. This is the same reason lists can't be dictionary keys.

---

## The `sorted()` Function

`sorted()` is a **built-in function** that sorts any iterable and **always returns a list**.

```python
# Sorting a set
alpha = {"B", "Z", "A", "G", "D"}
sorted_alpha = sorted(alpha)
print(sorted_alpha)               # ['A', 'B', 'D', 'G', 'Z']
print(type(sorted_alpha))         # <class 'list'>

# Sorting in reverse
reverse_alpha = sorted(alpha, reverse=True)
print(reverse_alpha)              # ['Z', 'G', 'D', 'B', 'A']

# Sorting numbers
numbers = {50, 10, 30, 20, 40}
print(sorted(numbers))            # [10, 20, 30, 40, 50]
```

**Output:**
```
['A', 'B', 'D', 'G', 'Z']
<class 'list'>
['Z', 'G', 'D', 'B', 'A']
[10, 20, 30, 40, 50]
```

**Key behaviors of `sorted()`:**
1. **Always returns a list** — even if you pass a set or tuple
2. **Does NOT modify the original** — creates a new sorted collection
3. **Default order:** ascending (A→Z, 0→9)
4. **Reverse:** `sorted(iterable, reverse=True)` for descending
5. **Cannot mix types:** `sorted({"A", 1, "B"})` raises `TypeError` (can't compare `str` and `int`)

```python
# sorted() does NOT modify the original
alpha = {"B", "Z", "A"}
result = sorted(alpha)
print(result)          # ['A', 'B', 'Z']  → sorted list
print(alpha)           # {'B', 'Z', 'A'}  → original set unchanged

# To save sorted result, assign it back
alpha = sorted(alpha)
print(alpha)           # ['A', 'B', 'Z']  → now alpha is a LIST, not a set!
```

> **Pro tip:** `sorted()` is a function (standalone). Don't confuse it with `list.sort()` which is a method that sorts a list **in-place** and returns `None`.

---

## Accessing Nested Data Structures — Multi-Level Traversal

### Nested Lists

```python
L2 = [[1, 2, 3, 4], [5, 6, 7, 8]]

# Level 1: L2 has 2 elements (each is a list)
# Index 0 → [1, 2, 3, 4]
# Index 1 → [5, 6, 7, 8]

# Goal: Access the number 8

# Step 1: Which inner list contains 8? → Index 1
print(L2[1])           # [5, 6, 7, 8]

# Step 2: Within [5, 6, 7, 8], where is 8? → Index 3 (or -1)
print(L2[1][3])        # 8
print(L2[1][-1])       # 8
```

**Output:**
```
[5, 6, 7, 8]
8
8
```

**Traversal visualization:**
```
L2 = [[1, 2, 3, 4], [5, 6, 7, 8]]
       ↑ index 0      ↑ index 1

L2[1] = [5, 6, 7, 8]
         ↑0  ↑1  ↑2  ↑3

L2[1][3] = 8
```

> **Important:** `L2[1, 3]` is NOT valid. Python does not support combined indexing like this for lists. You must chain separate brackets: `L2[1][3]`.

---

# PART 5: COMPARISON TABLE — All Four Data Types

---

| Feature | List | Dictionary | Tuple | Set |
|---------|------|-----------|-------|-----|
| **Symbol** | `[]` | `{}` | `()` | `{}` / `set()` |
| **Ordered** | Yes | Yes (Python 3.7+) | Yes | **No** |
| **Mutable** | **Yes** | **Yes** | **No** | **Yes** (but elements must be immutable) |
| **Duplicates** | Allowed | Keys: No, Values: Yes | Allowed | **Not allowed** |
| **Indexing** | Yes (`L[0]`) | By key (`d["key"]`) | Yes (`t[0]`) | **No** |
| **Empty creation** | `[]` or `list()` | `{}` or `dict()` | `()` or `tuple()` | `set()` ONLY |
| **Add element** | `.append()` | `d[key] = value` | N/A | `.add()` |
| **Remove element** | `.pop()`, `.remove()` | `del d[key]`, `.pop(key)` | N/A | `.remove()`, `.discard()` |
| **Key/Value** | No | Yes | No | No |
| **Hashable** | No | Keys: Yes | Yes | Elements: Yes |
| **Use case** | Ordered collection | Labeled data (JSON) | Constant data | Unique values |

---

## How It Works Internally

### List Memory Model

```
L = ["hello", "hey", "Python"]

L ──→ [list object at 0xA100]
        ├── index 0 → "hello"  (at 0xB200)
        ├── index 1 → "hey"    (at 0xB300)
        └── index 2 → "Python" (at 0xB400)

After L.append(10):
L ──→ [list object at 0xA100]    ← same object, modified
        ├── index 0 → "hello"
        ├── index 1 → "hey"
        ├── index 2 → "Python"
        └── index 3 → 10        ← new element added
```

### Dictionary Hash Table (Simplified)

```
d = {101: "Monal", 102: "Amit"}

hash(101) → bucket_A → {"key": 101, "value": "Monal"}
hash(102) → bucket_B → {"key": 102, "value": "Amit"}

Lookup d[101]:
  1. Compute hash(101) → bucket_A
  2. Return "Monal"
  → O(1) average time — extremely fast!
```

### Why Keys Must Be Hashable

```
hash("name")    → 1234567 (always same for "name")   ✓ Immutable → stable hash
hash((1, 2))    → 7654321 (always same for (1, 2))   ✓ Immutable → stable hash
hash([1, 2])    → ERROR!  List can change → hash would change → unreliable
```

---

## Common Mistakes & Debugging

### Mistake 1: Empty Set Created as Dictionary

**Wrong:**
```python
my_set = {}
print(type(my_set))
```

**Output:**
```
<class 'dict'>    # NOT a set!
```

**Why it fails:** `{}` defaults to an empty **dictionary** in Python. The curly brace syntax is shared between dict and set, but dict gets priority for empty `{}`.

**Correct:**
```python
my_set = set()
print(type(my_set))    # <class 'set'>
```

---

### Mistake 2: Using `len()` as Index Directly

**Wrong:**
```python
L = [10, 20, 30, 40, 50]
print(L[len(L)])        # IndexError!
```

**Error:**
```
IndexError: list index out of range
```

**Why it fails:** `len(L)` returns `5`, but the last valid index is `4` (indices go from 0 to 4).

**Correct:**
```python
print(L[len(L) - 1])    # 50
print(L[-1])             # 50  ← preferred
```

---

### Mistake 3: Duplicate Dictionary Keys — Silent Overwrite

**Wrong:**
```python
data = {
    "Rohit": "Engineer",
    "Rohit": "Doctor"        # Silently overwrites first entry!
}
print(data)
```

**Output:**
```
{'Rohit': 'Doctor'}    # First "Rohit" is GONE
```

**Why it fails:** Dictionary keys must be unique. When Python encounters a duplicate key, it doesn't raise an error — it **silently replaces** the old value with the new one.

**Correct:** Use unique identifiers as keys:
```python
data = {
    "EMP001": {"name": "Rohit", "role": "Engineer"},
    "EMP002": {"name": "Rohit", "role": "Doctor"}
}
```

---

### Mistake 4: Trying to Modify a Tuple

**Wrong:**
```python
coordinates = (12.97, 77.59)
coordinates[0] = 13.00        # TypeError!
```

**Error:**
```
TypeError: 'tuple' object does not support item assignment
```

**Why it fails:** Tuples are immutable by design. This is a feature, not a bug — it protects data integrity.

**Correct:** If you need to "change" a tuple, create a new one:
```python
coordinates = (13.00, 77.59)    # Reassign variable to new tuple
```

---

### Mistake 5: Adding a List to a Set

**Wrong:**
```python
my_set = {[1, 2, 3]}    # TypeError!
```

**Error:**
```
TypeError: unhashable type: 'list'
```

**Why it fails:** Sets use hash values internally for uniqueness checks. Lists are mutable and cannot produce a stable hash.

**Correct:** Use a tuple instead:
```python
my_set = {(1, 2, 3)}    # Works — tuples are hashable
```

---

### Mistake 6: Trying to Index a Set

**Wrong:**
```python
my_set = {"apple", "banana", "cherry"}
print(my_set[0])    # TypeError!
```

**Error:**
```
TypeError: 'set' object is not subscriptable
```

**Why it fails:** Sets are unordered — there's no "first" or "second" element. Indexing requires order.

**Correct:** Convert to list first, or iterate:
```python
my_list = list(my_set)
print(my_list[0])    # Works (but order is arbitrary)

# Or iterate
for item in my_set:
    print(item)
```

---

### Mistake 7: Saving `list.sort()` to a Variable

**Wrong:**
```python
numbers = [3, 1, 4, 1, 5]
result = numbers.sort()
print(result)        # None!
```

**Output:**
```
None
```

**Why it fails:** `.sort()` modifies the list **in-place** and returns `None`. It does NOT return the sorted list.

**Correct:**
```python
# Option 1: Use .sort() for in-place sorting
numbers = [3, 1, 4, 1, 5]
numbers.sort()
print(numbers)    # [1, 1, 3, 4, 5]

# Option 2: Use sorted() to get a new sorted list
numbers = [3, 1, 4, 1, 5]
result = sorted(numbers)
print(result)     # [1, 1, 3, 4, 5]
```

---

## Best Practices & Pythonic Way

1. **Use negative indexing** instead of `len(L) - 1`:
   ```python
   # Bad
   last = my_list[len(my_list) - 1]
   # Good
   last = my_list[-1]
   ```

2. **Use `.get()` for dictionaries** when the key might not exist:
   ```python
   # Bad — may crash
   value = my_dict["missing_key"]
   # Good — safe with default
   value = my_dict.get("missing_key", "Not found")
   ```

3. **Use tuples for data that shouldn't change:**
   ```python
   # Bad — using list for constants
   rgb_red = [255, 0, 0]
   # Good — tuple signals "don't modify"
   rgb_red = (255, 0, 0)
   ```

4. **Use sets to remove duplicates from a list:**
   ```python
   names = ["Alice", "Bob", "Alice", "Charlie", "Bob"]
   unique_names = list(set(names))
   print(unique_names)    # ['Charlie', 'Alice', 'Bob']
   ```

5. **Use meaningful keys** in dictionaries:
   ```python
   # Bad
   d = {"a": 25, "b": "NYC", "c": "Engineer"}
   # Good
   d = {"age": 25, "city": "NYC", "role": "Engineer"}
   ```

6. **Use `sorted()` (function) when you need a new list; use `.sort()` (method) for in-place sorting.**

---

## When to Use / When NOT to Use

| Data Type | Use When... | Avoid When... |
|-----------|-------------|---------------|
| **List** | You need ordered, modifiable collection; duplicates OK | You need uniqueness or immutability |
| **Dictionary** | You need labeled data (key → value); fast lookups | You only need a simple sequence of values |
| **Tuple** | Data must not change (coordinates, DOB, config); need hashable container | You need to add/remove elements frequently |
| **Set** | You need unique values; duplicate removal; membership testing | You need ordering or indexing |

---

## Related Topics & Connections

- **How this connects to:**
  - **For loops** — iterating over lists, dicts, tuples, sets
  - **While loops** — processing collections with conditions
  - **Functions** — passing collections as arguments, returning collections
  - **List comprehensions** — creating lists efficiently (Pythonic way)
  - **JSON** — dictionaries are the Python equivalent of JSON objects
  - **Classes & OOP** — understanding methods vs functions at a deeper level
  - **File handling** — reading data into lists/dicts
  - **Pandas DataFrames** — built on top of dictionaries and lists

- **Prerequisite knowledge:** Variables, data types (int, float, str, bool), type casting, basic operators, if/elif/else, for loops

- **What to learn next:** While loops, functions, function arguments, string slicing, list slicing, list comprehensions, dictionary comprehensions

---

## Interview & Exam Corner

### Frequently Asked Questions

1. **Q: What is the difference between a list and a tuple?**
   A: Lists are **mutable** (can be modified after creation), while tuples are **immutable** (cannot be modified). Lists use `[]`, tuples use `()`. Use lists when data needs to change; use tuples when data must remain constant.

2. **Q: What is the difference between a set and a dictionary?**
   A: Both use curly braces `{}`, but dictionaries store **key-value pairs** (`{key: value}`), while sets store **individual unique values** (`{value1, value2}`). Dictionaries support key-based access; sets do not support indexing.

3. **Q: Can a list be a dictionary key?**
   A: No. Dictionary keys must be **hashable** (immutable). Lists are mutable, so they cannot produce a stable hash. Use tuples instead.

4. **Q: How do you remove all duplicates from a list?**
   A: Convert to set and back: `unique_list = list(set(original_list))`. Note: this does not preserve order.

5. **Q: What happens if you use duplicate keys in a dictionary?**
   A: Python does NOT raise an error. The **last value** silently overwrites the previous one.

6. **Q: What is the difference between `sorted()` and `.sort()`?**
   A: `sorted()` is a function that returns a **new sorted list** (original unchanged). `.sort()` is a method that sorts the list **in-place** and returns `None`.

7. **Q: Why does `{}` create a dict instead of a set?**
   A: Historical reasons — dictionaries existed before sets in Python. For empty sets, you must explicitly use `set()`.

### Tricky Output Questions

```python
# Question 1: What will this print?
a = [1, 2, 3]
b = a
b.append(4)
print(a)
```
**Answer:** `[1, 2, 3, 4]` **Why:** `b = a` doesn't create a copy — both `a` and `b` point to the same list object. Modifying through `b` also affects `a`.

```python
# Question 2: What will this print?
d = {"a": 1, "b": 2, "a": 3}
print(d)
```
**Answer:** `{'a': 3, 'b': 2}` **Why:** Duplicate key `"a"` — the last value (`3`) overwrites the first (`1`).

```python
# Question 3: What will this print?
t = (1, 2, 3)
t = (4, 5, 6)
print(t)
```
**Answer:** `(4, 5, 6)` **Why:** This is **variable reassignment**, not tuple modification. The variable `t` now points to a completely new tuple object.

```python
# Question 4: What will this print?
s = {3, 1, 4, 1, 5, 9, 2, 6, 5}
print(len(s))
```
**Answer:** `7` **Why:** Duplicates (1, 5) are removed. Unique values: {1, 2, 3, 4, 5, 6, 9}.

```python
# Question 5: What will this print?
L = [10, 20, 30]
result = L.sort()
print(result)
```
**Answer:** `None` **Why:** `.sort()` sorts in-place and returns `None`. The sorted data is in `L`, not in `result`.

### One-Liner Challenges

- **Challenge:** Remove duplicates from a list while preserving order
  **Solution:** `list(dict.fromkeys(original_list))`

- **Challenge:** Get all unique values from a list
  **Solution:** `unique = list(set(my_list))`

- **Challenge:** Check if a key exists in a dictionary
  **Solution:** `"key" in my_dict`

- **Challenge:** Merge two dictionaries
  **Solution:** `merged = {**dict1, **dict2}` (Python 3.5+)

---

## Quick-Reference Cheat Sheet

```
NON-PRIMITIVE DATA TYPES CHEAT SHEET
======================================

LIST (ordered, mutable, duplicates OK)
  create:     L = [] or L = list()
  add:        L.append(val)           → add to end
  insert:     L.insert(idx, val)      → add at position
  remove:     L.pop()                 → remove last (returns it)
              L.pop(idx)              → remove at index (returns it)
              L.remove(val)           → remove first occurrence by value
  access:     L[0], L[-1]            → by index
  replace:    L[0] = new_val         → direct assignment
  length:     len(L)                 → total elements

DICTIONARY (key-value, keys unique, mutable)
  create:     d = {} or d = dict()
  add/update: d["key"] = value
  access:     d["key"] or d.get("key", default)
  all keys:   d.keys()
  all values: d.values()
  remove:     del d["key"] or d.pop("key")

TUPLE (ordered, IMMUTABLE, duplicates OK)
  create:     t = () or t = tuple()
  access:     t[0], t[-1]            → by index (read-only)
  NO append, NO pop, NO reassignment of elements

SET (unordered, unique values only, mutable)
  create:     s = set()              → MUST use set() for empty
  add:        s.add(val)
  remove:     s.remove(val) or s.discard(val)
  NO indexing — cannot use s[0]
  duplicates auto-removed

FUNCTIONS
  len(x)      → count elements/characters
  sorted(x)   → returns NEW sorted list
  type(x)     → check data type
  list(x)     → convert to list
  set(x)      → convert to set (removes duplicates)
  tuple(x)    → convert to tuple

GOTCHAS
  {} creates dict, NOT set → use set()
  len(L) ≠ last index → last index = len(L) - 1
  L.sort() returns None → use sorted(L) for new list
  Duplicate dict keys → last value wins silently
```

---

## Summary (TL;DR)

- **List** `[]`: ordered, mutable, allows duplicates. Methods: `append()`, `pop()`, `remove()`, `insert()`. Access by index (starts at 0). Negative indexing: `-1` for last element.
- **Dictionary** `{}`: key-value pairs. Keys must be unique and immutable. Values can be anything. Access via `dict[key]` or `dict.get(key, default)`. Use `.keys()` and `.values()` to extract all keys/values.
- **Tuple** `()`: ordered, **immutable**. Once created, cannot be modified. Use for data that must not change (DOB, coordinates). Can be used as dictionary keys.
- **Set** `{}`/`set()`: unordered, unique values only. Automatically removes duplicates. Elements must be hashable (immutable). Cannot be indexed.
- **`len()`** counts elements (starts from 1). **Index** gives positions (starts from 0). `last_index = len(L) - 1`.
- **`sorted()`** returns a new sorted **list** from any iterable. Does NOT modify the original.
- **Mutable** (list, dict, set) = changeable. **Immutable** (tuple, str, int, float, bool) = unchangeable. Only immutable types can be dict keys or set elements.

---

## Flashcard-Style Recall (Q&A Pairs)

- **Q:** What symbol does a list use? **A:** Square brackets `[]`
- **Q:** What symbol does a dictionary use? **A:** Curly braces `{}` with key: value pairs
- **Q:** What symbol does a tuple use? **A:** Parentheses `()`
- **Q:** How do you create an empty set? **A:** `set()` — NOT `{}` (that creates a dict)
- **Q:** What does `append()` do? **A:** Adds an element to the END of a list
- **Q:** What does `pop()` do without arguments? **A:** Removes and returns the LAST element
- **Q:** What does `pop(0)` do? **A:** Removes and returns the element at index 0
- **Q:** What's the difference between `pop()` and `remove()`? **A:** `pop()` works by index; `remove()` works by value
- **Q:** What happens with duplicate dictionary keys? **A:** Last value silently overwrites previous ones
- **Q:** Can a list be a dictionary key? **A:** No — lists are mutable and not hashable
- **Q:** Can a tuple be a dictionary key? **A:** Yes — tuples are immutable and hashable
- **Q:** What does `sorted()` return? **A:** Always a new **list**, regardless of input type
- **Q:** What's the difference between `sorted()` and `.sort()`? **A:** `sorted()` returns new list; `.sort()` modifies in-place, returns `None`
- **Q:** What error occurs when accessing a non-existent list index? **A:** `IndexError: list index out of range`
- **Q:** What error occurs when accessing a non-existent dict key with `[]`? **A:** `KeyError`
- **Q:** What error occurs when modifying a tuple element? **A:** `TypeError: 'tuple' object does not support item assignment`
- **Q:** How do you remove duplicates from a list? **A:** `unique = list(set(original_list))`
- **Q:** What is negative indexing? **A:** Accessing elements from the end: `-1` is last, `-2` is second-to-last
- **Q:** What does `len()` return for a list vs the last index? **A:** `len()` returns count (starts from 1); last index = `len() - 1` (starts from 0)
- **Q:** Why can't you put a list inside a set? **A:** Lists are mutable/unhashable; sets require hashable elements
