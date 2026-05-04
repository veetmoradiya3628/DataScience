# Object-Oriented Programming (OOP) in Python — Introduction

---

## Concept Overview

- **Object-Oriented Programming (OOP)** is a programming paradigm that organizes code around **objects** — bundled units of data (attributes) and behavior (methods) — rather than a sequence of instructions.
- It exists to solve the problems of **procedural programming**: data scattered across global variables, functions unbound from their data, and poor scalability for real-world systems.
- **Real-world analogy:** A restaurant creating a **kitchen blueprint** — once the blueprint is ready, every new chef gets their own fully-equipped kitchen. Each chef's kitchen is independent, organized, and follows a standard.
- In Python, OOP is not optional — everything in Python is already an object (integers, lists, strings). Learning OOP means learning to create your own types.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **Procedural Programming** | Writing code as top-to-bottom sequential steps — variables, functions, control flow |
| **OOP** | Paradigm where data and behavior are bundled together into objects |
| **Class** | The **blueprint** — a definition of what an object looks like and can do |
| **Object / Instance** | An actual **physical allocation** in memory created from a class blueprint |
| **Constructor (`__init__`)** | Special method that runs automatically when an object is created; initializes instance state |
| **`self`** | The first argument of every method — a reference to the current object instance (its memory address) |
| **Method** | A function defined **inside** a class; bound to the object |
| **Attribute** | A variable defined inside a class; belongs to an object via `self` |
| **Instance Variable** | A variable prefixed with `self.` — shared across all methods of the same object |
| **Local Variable** | A variable without `self.` — only visible inside the function it's defined in |
| **Dot Operator (`.`)** | Used to access a method or attribute of an object: `object.attribute` |
| **PascalCase** | Naming convention for classes — each word starts with a capital letter (`BankAccount`) |
| **snake_case** | Naming convention for methods and variables — all lowercase with underscores (`customer_name`) |

---

## Syntax & Definition

```python
class ClassName:                        # Blueprint declaration
    def __init__(self, param1, param2): # Constructor — runs automatically on object creation
        self.param1 = param1            # Instance variable — accessible across all methods
        self.param2 = param2

    def method_name(self):              # Instance method — first arg is always 'self'
        print(self.param1)              # Access instance variable using self


# Creating an object (instance) from the class
my_object = ClassName(value1, value2)  # Allocates memory, calls __init__ automatically

# Accessing attributes and methods
my_object.method_name()                # Call a method using dot operator
my_object.param1                       # Access an attribute using dot operator
```

**Syntax breakdown:**
- `class` — Python keyword to define a blueprint
- `ClassName` — follows **PascalCase** (e.g., `BankAccount`, `Robot`, `DataScienceBatch`)
- `__init__` — double underscore (dunder) on both sides; special constructor method
- `self` — first argument of every method; not passed by the caller — Python passes it automatically; represents the current object instance
- `self.attribute = value` — binds `value` to the object in memory, making it available across all methods

---

## Why OOP? — The Problem with Procedural Programming

### Phase 1: Single-User Procedural Code

```python
# Simple bank account — single user
username = "Gaurav"
user_balance = 1000

def withdraw(balance, amount):
    return balance - amount

user_balance = withdraw(user_balance, 200)
print(username, ":", user_balance)
```

**Output:**
```
Gaurav : 800
```

**Works fine for one user — but what about 100 users?**

---

### Phase 2: Multiple Users — The Scaling Problem

```python
user1_name = "Feroz"
user1_balance = 1000

user2_name = "Abhinav"
user2_balance = 20000

user1_balance = withdraw(user1_balance, 2000)
user2_balance = withdraw(user2_balance, 20000)

print(user1_name, ":", user1_balance)
print(user2_name, ":", user2_balance)
```

**Output:**
```
Feroz : -1000
Abhinav : 0
```

> **Problem:** For 100 users → 100 name variables + 100 balance variables = **200 variables**. Not scalable.

---

### Phase 3: Quick Fix — Using a Dictionary

```python
users = {
    "Gaurav": 1000,
    "Feroz": 1000,
    "Abhinav": 20000
}

def withdraw(users_dict, name, amount):
    users_dict[name] -= amount
    print(name, ":", users_dict[name])

withdraw(users, "Gaurav", 1000)
# Add new user dynamically:
users["Indrajit"] = 50000
```

**Output:**
```
Gaurav : 0
```

**Better — but two critical problems remain:**

| Problem | Description |
|---------|-------------|
| **Data and Function are Separated** | The `withdraw` function is not bound to `users`. You can pass *any* dictionary to it — even a wrong or new one. |
| **Data is "Naked"** | Anyone can directly modify `users["Gaurav"] = 99999999`. There's no protection. A developer can accidentally overwrite all customer data. |

---

## Detailed Explanation with Examples

### Example 1: Basic Class — Robot Blueprint

```python
class Robot:
    def __init__(self):          # Constructor — runs automatically when Robot() is called
        print("Hello! Welcome to Robot.")

    def walk(self):
        print("Walking 10 meters.")

    def sing(self):
        print("Singing a song.")


# Creating an object (instance)
a = Robot()     # Automatically prints: Hello! Welcome to Robot.
a.walk()        # Output: Walking 10 meters.
a.sing()        # Output: Singing a song.
```

**Output:**
```
Hello! Welcome to Robot.
Walking 10 meters.
Singing a song.
```

**Line-by-line breakdown:**
- `class Robot:` — Defines a blueprint named `Robot`
- `def __init__(self):` — Constructor; `self` is the connection name linking all methods
- `a = Robot()` — Allocates memory for a `Robot` instance; calls `__init__` automatically; stores the memory address in `a`
- `a.walk()` — Accesses the `walk` method via dot operator; `self` is passed automatically by Python

**Why this works:** `Robot()` tells the OS to allocate memory for all the class components (functions, variables). The address of this memory block is stored in `a`. The `self` parameter inside each method refers to this same memory address — that's how all methods "know" they belong to the same object.

---

### Example 2: Instance Variables — Connecting Data to the Object

```python
class Robot:
    def __init__(self):
        self.value1 = 100        # Instance variable — connected to this object via self

    def walk(self):
        print("Walking...")
        print("Value:", self.value1)   # Accessing instance variable inside another method


a = Robot()
a.walk()
print(a.value1)   # Accessing instance variable from outside the class
```

**Output:**
```
Walking...
Value: 100
100
```

**Line-by-line breakdown:**
- `self.value1 = 100` — The `self.` prefix makes `value1` an *instance variable* — it lives in the object's memory and is accessible across all methods
- `print(self.value1)` inside `walk()` — works because `self` is the same connection across all methods
- `a.value1` — accessing from outside via the dot operator

> **Key concept:** Without `self.`, a variable is *local* to its function only. With `self.`, it becomes a property of the entire object.

---

### Example 3: Constructor with Parameters — Custom Object State

```python
class Robot:
    def __init__(self, customer_name):
        self.name = "Chitty"                # Default instance variable
        self.ram_in_gb = 10                 # Default hardware spec
        self.version = "0.0.1"             # Default version string
        self.customer_name = customer_name  # Passed by user at creation time

    def config(self):
        print("RAM:", self.ram_in_gb)
        print("Version:", self.version)
        print("Robot Name:", self.name)

    def customer_details(self):
        print("Customer Name:", self.customer_name)


# Creating two separate instances from the same blueprint
r1 = Robot("Monal")
r2 = Robot("Nickel")

r1.config()
r1.customer_details()

r2.config()
r2.customer_details()
```

**Output:**
```
RAM: 10
Version: 0.0.1
Robot Name: Chitty
Customer Name: Monal
RAM: 10
Version: 0.0.1
Robot Name: Chitty
Customer Name: Nickel
```

**Line-by-line breakdown:**
- `def __init__(self, customer_name):` — Constructor takes one user-provided argument
- `self.customer_name = customer_name` — The *argument* `customer_name` (local to `__init__`) is assigned to `self.customer_name` (global to the whole class)
- `r1 = Robot("Monal")` — Creates a new memory block; `"Monal"` is passed to `__init__`
- `r2 = Robot("Nickel")` — Creates a **different** memory block; completely independent of `r1`

**Proving they are different objects:**
```python
print(id(r1))   # e.g., 140234567890
print(id(r2))   # e.g., 140234567920  ← different address!
```

**Why this works:** Every call to `Robot(...)` instructs the OS to allocate a new block of memory. `r1` and `r2` hold the addresses of their respective blocks — they never share state.

---

### Example 4: OOP Bank Account — Solving the Original Problem

```python
class BankAccount:
    def __init__(self, name, balance):
        self.name = name
        self.balance = balance

    def withdraw(self, amount):
        self.balance -= amount
        print(f"{self.name} withdrew {amount}. New balance: {self.balance}")


# Creating users — each has their own independent account
user1 = BankAccount("Gaurav", 1000)
user2 = BankAccount("Feroz", 5000)

user1.withdraw(200)
user2.withdraw(1000)
```

**Output:**
```
Gaurav withdrew 200. New balance: 800
Feroz withdrew 1000. New balance: 4000
```

**Why OOP solves the problem:**
- Data (`name`, `balance`) and behavior (`withdraw`) are **tied together** in one object
- You **cannot** accidentally call `user1.withdraw` on `user2`'s data
- Adding a new user requires only one line: `user3 = BankAccount("Abhinav", 20000)`
- No global variable pollution; each account lives in its own memory block

---

## Comparison Table

### Procedural vs Object-Oriented Programming

| Feature | Procedural Programming | Object-Oriented Programming |
|---------|----------------------|--------------------------|
| Code structure | Top-to-bottom, sequential | Organized into classes and objects |
| Data storage | Global variables | Instance variables inside objects |
| Data-function binding | Separated — functions can use any data | Bound — methods operate on object's own data |
| Data protection | None — any code can modify variables | Controlled — only class methods should change state |
| Scalability | Poor — new users = more variables | Excellent — new users = new object instance |
| Code reuse | Manual copy-paste | One blueprint → unlimited instances |
| Real-world modeling | Weak | Strong — models real entities naturally |

### Method vs Function

| Aspect | Function | Method |
|--------|----------|--------|
| Location | Defined outside any class | Defined **inside** a class |
| Binding | Not tied to any data | Tied to its class's data via `self` |
| Access | Called directly: `function_name()` | Called via object: `object.method_name()` |
| First parameter | Any or none | Always `self` (by convention) |
| Example | `def withdraw(balance, amount):` | `def withdraw(self, amount):` |

### Instance Variable vs Local Variable

| Aspect | Instance Variable | Local Variable |
|--------|-----------------|----------------|
| Syntax | `self.variable_name` | `variable_name` |
| Scope | Accessible by **all methods** of the object | Accessible only within the **current function** |
| Lifetime | Lives as long as the object exists | Destroyed when the function returns |
| Example | `self.customer_name = "Monal"` | `customer_name = "Monal"` (inside a function) |

---

## How It Works Internally

### Memory Model: Class vs Object

```
PYTHON SOURCE CODE (in .ipynb / .py file)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  class Robot:          ← Just code/blueprint, no memory yet
      def __init__(self): ...
      def walk(self): ...


RAM (when object is created)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Address 0x1A2B:
  ┌────────────────────────────────┐
  │  Robot Instance                │
  │  ─────────────────────────     │
  │  customer_name → "Monal"       │
  │  ram_in_gb     → 10            │
  │  version       → "0.0.1"       │
  │  [ref to walk method]          │
  │  [ref to sing method]          │
  └────────────────────────────────┘
        ↑
  r1 = 0x1A2B   ← r1 stores the ADDRESS, not the object itself


  Address 0x3C4D:  ← DIFFERENT memory block
  ┌────────────────────────────────┐
  │  Robot Instance                │
  │  customer_name → "Nickel"      │
  │  ...                           │
  └────────────────────────────────┘
        ↑
  r2 = 0x3C4D
```

**Step-by-step execution of `r1 = Robot("Monal")`:**
1. Python sees `Robot("Monal")` — instruction to create an object
2. Python asks the OS: *"Allocate memory for a Robot instance"*
3. OS reserves a block (enough for all `__init__` variables + methods)
4. `__init__` runs automatically — populates `self.name`, `self.customer_name`, etc.
5. OS returns the memory address (e.g., `0x1A2B`) to Python
6. Python stores this address in `r1`
7. `r1` now *points to* the Robot object — accessing `r1.walk()` follows the address

**What is `self` really?**

`self` is the **memory address** of the current object. When you write `r1.walk()`, Python internally translates this to `Robot.walk(r1)` — it passes `r1`'s address as the first argument (`self`). All methods receive this same address, which is why they can all access the same `self.name`, `self.balance`, etc.

```
r1.walk()
  ↓
Python internally calls: Robot.walk(r1)
  ↓
Inside walk: self == r1 == 0x1A2B
  ↓
self.name  →  reads from address 0x1A2B → "Monal"
```

**Why `self` is not a keyword:**
`self` is just a **naming convention**. You could write `a`, `r1`, `this` — any name works as the first argument. But **every developer in the world uses `self`**, so deviating from this convention will confuse anyone who reads your code.

```python
class Robot:
    def __init__(a, customer_name):    # "a" works, but terrible practice
        a.customer_name = customer_name

    def walk(a):
        print(a.customer_name)          # still works
```

> In **Java**, the equivalent keyword is `this` — same concept, different name.

---

## Common Mistakes & Debugging

### Mistake 1: Forgetting `self` when declaring an instance variable

**Wrong:**
```python
class Robot:
    def __init__(self):
        value1 = 100   # Local variable — dies when __init__ ends

    def walk(self):
        print(self.value1)  # AttributeError!
```

**Error:**
```
AttributeError: 'Robot' object has no attribute 'value1'
```

**Why it fails:** `value1 = 100` without `self.` is *local* to `__init__`. It is destroyed when `__init__` returns. The `walk` method cannot find `value1` anywhere in the object's memory.

**Correct:**
```python
class Robot:
    def __init__(self):
        self.value1 = 100  # Now it's an instance variable — survives across methods

    def walk(self):
        print(self.value1)   # Works: 100
```

---

### Mistake 2: Passing too many arguments to a method

**Wrong:**
```python
r1 = Robot("Monal")
r1.walk("10 meters")   # walk(self) takes 0 arguments besides self
```

**Error:**
```
TypeError: walk() takes 1 positional argument but 2 were given
```

**Why it fails:** `self` is the first argument, automatically passed by Python. The user should not pass it. If `walk` is defined as `def walk(self):`, only `self` is expected. Passing an extra argument causes a mismatch.

**Correct:**
```python
def walk(self):
    print("Walking...")

r1.walk()   # No extra arguments
```

---

### Mistake 3: Passing too many arguments to constructor

**Wrong:**
```python
class Robot:
    def __init__(self):     # Only self — no room for a name
        pass

r1 = Robot("Monal")        # Passing a name here
```

**Error:**
```
TypeError: __init__() takes 1 positional argument but 2 were given
```

**Why it fails:** `Robot("Monal")` passes `"Monal"` to `__init__`, but `__init__` only declares `self`. There's no parameter to receive `"Monal"`.

**Correct:**
```python
class Robot:
    def __init__(self, customer_name):   # Add the parameter
        self.customer_name = customer_name

r1 = Robot("Monal")   # Now works
```

---

### Mistake 4: Using same class name twice (overwriting the blueprint)

**Wrong:**
```python
class BankAccount:
    def __init__(self, name):
        self.name = name

class BankAccount:      # Redefines/overwrites the first class
    def __init__(self):
        pass
```

**Why it fails:** Python only keeps the **last** definition of `BankAccount` in memory. The first blueprint is lost — just like reassigning a variable.

**Correct:** Use unique class names. If you need variations, use inheritance (covered in the next session).

---

### Mistake 5: Confusing local variable with instance variable

**Wrong:**
```python
class Robot:
    def __init__(self, customer_name):
        customer_name = customer_name    # Assigning local to local — no effect on object

    def customer_details(self):
        print(self.customer_name)        # AttributeError
```

**Why it fails:** `customer_name = customer_name` creates/updates a *local* variable. `self.customer_name` is never set.

**Correct:**
```python
class Robot:
    def __init__(self, customer_name):
        self.customer_name = customer_name   # Instance variable

    def customer_details(self):
        print(self.customer_name)            # Works
```

---

## Best Practices & Pythonic Way

```python
# DO: Use PascalCase for class names
class BankAccount:        # ✓
class bank_account:       # ✗
class bankaccount:        # ✗

# DO: Use snake_case for methods and variables
def get_balance(self):    # ✓
def getBalance(self):     # ✗ (camelCase — common in Java, not Python)

# DO: Use self.attribute for any variable that should live beyond __init__
class User:
    def __init__(self, name):
        self.name = name      # ✓ accessible everywhere

# DO: Keep __init__ focused — only set up initial state
class Robot:
    def __init__(self, customer_name):
        self.customer_name = customer_name
        self.battery = 100
        # Don't do complex logic here

# DO: Give methods clear action-oriented names (verbs)
def withdraw(self, amount):   # ✓
def balance_info(self):       # ✓

# DON'T: Write 200 variables when you should write 1 class
# BAD — procedural
user1_name = "Alice"
user1_balance = 1000
user2_name = "Bob"
user2_balance = 2000

# GOOD — OOP
user1 = BankAccount("Alice", 1000)
user2 = BankAccount("Bob", 2000)

# DO: Use default arguments in __init__ for optional parameters
class Robot:
    def __init__(self, customer_name=None):
        self.customer_name = customer_name   # None if not provided
```

> **Pro tip:** In Python, `self` is not enforced by the language — it's enforced by the community. Every Python developer uses `self`. Deviating from this is a red flag in code reviews.

---

## When to Use / When NOT to Use OOP

| Use OOP When... | Avoid OOP When... |
|----------------|------------------|
| You're modeling real-world entities (User, BankAccount, Robot) | You're writing a quick one-off script or utility |
| Multiple instances of the same "thing" are needed | The problem is purely sequential with no repeated entities |
| You need to bundle data + behavior together | You have only one function with no shared state |
| You want data protection and controlled access | Over-engineering would make the code harder to read |
| You're building scalable, maintainable applications | Simple data transformations (use functions instead) |
| You want to use inheritance and polymorphism later | Team is unfamiliar and there's no time to learn |

---

## Related Topics & Connections

- **This topic connects to:**
  - `__init__` and constructors → Encapsulation (protecting data via private attributes `__`)
  - `self` and instances → Inheritance (child class sharing parent's `self`)
  - Methods → Polymorphism (same method name, different behavior in subclasses)
  - Global/local scope → Instance scope (`self.`) vs method scope (local variable)

- **Prerequisite knowledge needed:**
  - Functions (defining with `def`, parameters, return values)
  - Dictionaries (key-value storage)
  - Global vs local variables
  - Variable assignment and Python memory model (variables store addresses)

- **What to learn next:**
  - Encapsulation — hiding data using private attributes (`__balance`)
  - Inheritance — creating child classes that extend a parent class
  - Polymorphism — same interface, different behaviors
  - Magic/dunder methods (`__str__`, `__repr__`, `__len__`, etc.)
  - Class variables vs instance variables
  - Static methods and class methods

---

## Interview & Exam Corner

### Frequently Asked Questions

1. **Q: What is the difference between a class and an object?**  
   A: A class is a **blueprint** — it exists only as code, takes no memory. An object (instance) is the **actual allocation in RAM** created from that blueprint. You can create unlimited objects from one class, each with their own independent data.

2. **Q: What is `__init__` and why is it special?**  
   A: `__init__` is the **constructor** — a special method that runs **automatically** when a new object is created. It is used to initialize instance variables (the starting state of the object). It is "special" because Python calls it implicitly; you never call it directly.

3. **Q: What is `self` and why must it be the first argument?**  
   A: `self` is a reference to the **current object instance** — it holds the memory address of the object. It must be the first argument so Python knows which object's data to operate on. You can name it anything, but by community convention it's always `self`.

4. **Q: What is the difference between a method and a function?**  
   A: A **function** is defined outside any class, standalone. A **method** is a function defined **inside a class**, bound to its object via `self`. Methods are accessed using the dot operator (`object.method()`).

5. **Q: Is `__init__` mandatory?**  
   A: No. If you don't write `__init__`, Python creates a default one. But without it, you cannot initialize instance variables at the time of object creation.

6. **Q: Why can't two classes have the same name?**  
   A: Like any variable, the last definition wins. If you define `class Robot` twice, the second definition overwrites the first in memory — the original blueprint is lost.

7. **Q: What is the difference between `self.x` and `x` inside a method?**  
   A: `self.x` is an **instance variable** — persists for the lifetime of the object and is accessible from any method. `x` (without `self`) is a **local variable** — only lives during the current function call.

---

### Tricky Output Questions

**Question 1:**
```python
class Counter:
    def __init__(self):
        count = 0   # ← no self

    def increment(self):
        print(self.count)


c = Counter()
c.increment()
```
**Answer:** `AttributeError: 'Counter' object has no attribute 'count'`  
**Why:** `count = 0` without `self.` is a local variable inside `__init__` — it's destroyed after `__init__` finishes. The `increment` method can't find it.

---

**Question 2:**
```python
class Robot:
    def __init__(self, name):
        self.name = name

r1 = Robot("Alpha")
r2 = Robot("Beta")
r1.name = "Gamma"
print(r1.name)
print(r2.name)
```
**Answer:**
```
Gamma
Beta
```
**Why:** `r1` and `r2` are separate memory blocks. Changing `r1.name` does not affect `r2.name`.

---

**Question 3:**
```python
class Robot:
    def __init__(self):
        print("Robot created!")

r1 = Robot()
r2 = Robot()
```
**Answer:**
```
Robot created!
Robot created!
```
**Why:** `__init__` runs automatically every time a new object is created. Two objects = two automatic calls.

---

**Question 4:**
```python
class Robot:
    def walk(self):
        print("Walking")

Robot.walk()   # ← No object, calling on class directly
```
**Answer:** `TypeError: walk() missing 1 required positional argument: 'self'`  
**Why:** `walk` is an instance method — it requires `self` (the object's address). Calling it on the class directly provides no object, so `self` is missing.

---

### One-Liner Challenges

- **Challenge:** Check the type of an object `r1` created from class `Robot`
- **Solution:** `print(type(r1))` → `<class '__main__.Robot'>`

- **Challenge:** Get the memory address of object `r1`
- **Solution:** `print(id(r1))`

- **Challenge:** Check if `r1` is an instance of `Robot`
- **Solution:** `print(isinstance(r1, Robot))` → `True`

---

## Quick-Reference Cheat Sheet

```
OOP CHEAT SHEET
=====================================
class ClassName:           → Define a blueprint (PascalCase name)
  def __init__(self):      → Constructor — runs on object creation
  def method(self):        → Instance method (first arg always self)
  self.attr = value        → Instance variable — shared across all methods
  local_var = value        → Local variable — only lives in current method

obj = ClassName()          → Create an object (allocates RAM)
obj.method()               → Call a method via dot operator
obj.attr                   → Access an attribute via dot operator
id(obj)                    → Get the memory address of the object
type(obj)                  → Returns <class '__main__.ClassName'>

PascalCase  → class names (BankAccount, DataScienceBatch)
snake_case  → methods and variables (customer_name, get_balance)
self        → NOT a keyword; convention for the connection variable
__init__    → constructor; also called as 'dunder init'
             equivalent to 'this' in Java

GOTCHA: self.x ≠ x (local vs instance scope)
GOTCHA: __init__ is optional, but needed for instance variables
GOTCHA: Every method call obj.method() → Python auto-passes obj as self
GOTCHA: Each obj = ClassName() creates a NEW memory block — not a copy
```

---

## Summary (TL;DR)

- **Procedural programming** executes code top-to-bottom; data and functions are **separate entities** — not scalable for real-world systems with many users/objects.
- **OOP** solves this by bundling data (attributes) and behavior (methods) into a **class** (blueprint), and creating **objects** (instances) from it.
- A **class** takes no memory — it's just code. An **object** is actual RAM allocated when you write `ClassName()`.
- **`__init__`** is the constructor — it runs automatically on object creation and is used to set up the object's initial state with instance variables.
- **`self`** is the connection name (by convention) — it refers to the object's own memory address and links all methods and variables of an object together.
- Variables with `self.` prefix are **instance variables** (accessible everywhere in the class). Without `self.`, they are **local** to the function.
- Each `ClassName()` call creates a **new, independent memory block** — `r1` and `r2` never share data.
- **Naming conventions:** Classes → `PascalCase`; Methods/variables → `snake_case`

---

## Flashcard-Style Recall (Q&A Pairs)

- **Q: What is a class?**  
  A: A blueprint (just code) that defines what data and behavior an object should have. Takes no memory by itself.

- **Q: What is an object/instance?**  
  A: The actual data stored in RAM, created from a class blueprint using `ClassName()`.

- **Q: What is `__init__`?**  
  A: The constructor — a special method that runs automatically when an object is created. Used to initialize instance variables.

- **Q: What is `self`?**  
  A: A reference (memory address) to the current object instance. The first parameter of every instance method. Connects all methods and variables in a class.

- **Q: What's the difference between `self.x` and `x`?**  
  A: `self.x` is an instance variable — accessible across all methods of the object. `x` is local — only lives inside the current function.

- **Q: What error occurs when you forget `self.` on an instance variable?**  
  A: `AttributeError: 'ClassName' object has no attribute 'x'`

- **Q: What is a method vs a function?**  
  A: A method is a function defined inside a class, bound to an object via `self`. A function stands alone, not tied to any data.

- **Q: How do you access a method or attribute of an object?**  
  A: Using the dot operator: `object.method()` or `object.attribute`

- **Q: What naming convention is used for class names?**  
  A: PascalCase — every word starts with a capital letter, no spaces or underscores. E.g., `BankAccount`, `DataScienceBatch`

- **Q: What naming convention is used for methods and variables?**  
  A: snake_case — all lowercase, words separated by underscores. E.g., `customer_name`, `get_balance`

- **Q: Does every new object from the same class share memory?**  
  A: No. Every `ClassName()` call creates a new, independent memory block in RAM.

- **Q: Is `__init__` mandatory in a class?**  
  A: No. Python provides a default. But without it, you can't initialize instance variables at object creation time.

- **Q: What is the special name for `__init__` in interview context?**  
  A: Constructor.

---

## Assignment

Create a `UltimateDataScienceBatch` class with the following requirements:

1. **Each batch will have only one instance** (one object per batch)
2. **Be able to add multiple students** to a single batch, along with their details
3. **Be able to print all student names** of that batch

```python
# Starter template — complete the logic

class UltimateDataScienceBatch:
    def __init__(self, batch_name):
        self.batch_name = batch_name
        self.students = []           # Hint: use a list to store multiple students

    def add_student(self, student_name, student_details):
        # TODO: Add student to self.students
        pass

    def print_all_students(self):
        # TODO: Print all student names in this batch
        pass


# Usage:
batch1 = UltimateDataScienceBatch("Batch 2")
batch1.add_student("Gaurav", {"age": 25, "city": "Mumbai"})
batch1.add_student("Feroz",  {"age": 28, "city": "Delhi"})
batch1.print_all_students()
```

> **Hint:** The `students` list is an instance variable. Use `self.students.append(...)` inside `add_student` to grow the list. Use a loop inside `print_all_students` to iterate and print.
