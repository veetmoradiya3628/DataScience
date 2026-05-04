# Python Decorators & Static Variables / Static Methods

---

## Concept Overview

### Decorators
A **decorator** is a function that takes another function as an argument, wraps it inside a new inner function that adds extra behaviour, and returns that inner function. The original function's logic is preserved and *enhanced*, not replaced.

> **Why it exists:** You often want to add the same piece of behaviour (logging, timing, authentication, error-handling) to many different functions without copy-pasting that code into every one of them. A decorator lets you write that extra logic once and apply it anywhere.

**Real-world analogy:** Think of a decorator like a **phone case**. The phone (base function) works perfectly on its own. The case (decorator) wraps around it, adds protection and a kickstand, and the phone still makes calls — but now it does more. You can swap the same case onto any phone of the same size.

**Where it fits:** Decorators sit at the intersection of *first-class functions*, *closures*, and *higher-order functions*. They are used heavily in frameworks (Flask `@app.route`, Django `@login_required`), testing (`@pytest.fixture`), and production Python everywhere.

---

### Static Variables & Static Methods
A **static variable** (class variable) is a variable defined directly inside a class body — *without* `self` — so it belongs to the **class itself**, not to any specific instance. All objects of that class share the same copy.

A **static method** is a function inside a class decorated with `@staticmethod`. It receives no implicit `self` or `cls`, behaves like a plain function, but is logically grouped under the class namespace.

> **Why it exists:** Sometimes you need data or behaviour that is shared across all instances (e.g., a counter of how many objects have been created) or utility functions that logically belong to a class but do not operate on instance state.

**Real-world analogy (static variable):** Imagine a **school building** (the class). Every classroom (object) inside knows the school name — "Greenwood High". No matter which classroom you stand in, the school name is the same. If the school is renamed, every classroom reflects the new name automatically. That shared "school name" is a static variable.

**Real-world analogy (static method):** The school's **math formula sheet** posted on the wall. Any student (any object) can use it, or you can just walk into the school (use the class name directly) and read it. Nobody needs to be assigned to a specific classroom to use it.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **First-class function** | In Python, functions are objects; they can be stored in variables, passed as arguments, and returned from other functions |
| **Higher-order function** | A function that accepts a function as an argument and/or returns a function |
| **Decorator** | A higher-order function that takes a function, enhances it, and returns the enhanced version |
| **Wrapper / Inner function** | The new function created *inside* the decorator that contains the extra behaviour |
| **`@` syntax (syntactic sugar)** | Python shorthand that applies a decorator to a function at definition time |
| **Syntactic sugar** | Syntax designed to make code easier to read/write without changing the underlying behaviour |
| **Static variable (class variable)** | A variable defined inside a class body without `self`; shared across all instances |
| **Static method** | A method decorated with `@staticmethod`; no `self` or `cls` parameter; tied to the class for logical grouping |
| **Instance variable** | A variable bound to a specific object via `self.variable_name` |
| **Name mangling (shadowing)** | When you assign to `obj.class_variable`, Python creates a *new* object-level attribute instead of modifying the class-level one |
| **`*args`** | Captures any number of positional arguments as a tuple |
| **`**kwargs`** | Captures any number of keyword arguments as a dictionary |

---

## Part 1 — Decorators

### Foundation: Functions Are Objects

Before decorators make sense, you must internalise this: **everything in Python is an object**, including functions.

```python
def greet():
    print("Hello!")

# Python does TWO things when it reads `def greet()`:
# 1. Creates a function object in memory (stores all the logic there)
# 2. Binds the name `greet` to the memory address of that object

print(type(greet))   # <class 'function'>
print(id(greet))     # some memory address, e.g. 140234567890
```

**Output:**
```
<class 'function'>
140234567890
```

**Line-by-line breakdown:**
- `def greet():` → Python compiles the function body and stores it as a function object in memory.
- `greet` → This is a **variable** holding a *reference* (memory address) to that object — exactly like `age = 25` creates an `int` object and `age` holds its address.
- `type(greet)` → Confirms that `greet` is of type `function`.
- `id(greet)` → Returns the memory address of the function object.

**Why this matters:** Because `greet` is just a variable holding an address, you can do anything with it that you can do with any variable — including assign it to another variable or pass it into a function.

```python
# Assigning a function to another variable
alias = greet          # alias now points to the SAME function object
alias()                # This is a valid function call!
print(id(alias) == id(greet))  # True — both point to the same address
```

**Output:**
```
Hello!
True
```

---

### Foundation: Passing a Function as an Argument

```python
def greet():
    print("Hello from greet!")


def run(func):
    """Accepts a callable (function object reference) and calls it."""
    func()         # This works ONLY because func holds a function reference


run(greet)         # Pass the function object, NOT greet()
```

**Output:**
```
Hello from greet!
```

**Line-by-line breakdown:**
- `def run(func):` → `func` is a parameter. When we pass `greet`, `func` receives the memory address of the `greet` function object.
- `func()` → The `()` operator means "call whatever is at this address". Since `func` holds a function reference, this executes that function.
- `run(greet)` → Passing `greet` (the address) — **not** `greet()` (which would call it first and pass its return value).

> **Common mistake:** Writing `run(greet())` instead of `run(greet)`. The `()` calls the function immediately, and `None` (its return value) gets passed to `run`, which then tries to call `None` — causing `TypeError: 'NoneType' object is not callable`.

---

### Foundation: Enhancing a Function's Behaviour

```python
def addition_function():
    print("Performing addition...")


def run(func):
    print("Welcome to Python class!")   # Extra behaviour BEFORE
    func()                               # Call the original function
    print("Class ending.")              # Extra behaviour AFTER


run(addition_function)
```

**Output:**
```
Welcome to Python class!
Performing addition...
Class ending.
```

**Why this works:** `run` wraps the call to `func` with extra print statements. The original function is unchanged — we have merely *enhanced* it by sandwiching extra code around it.

---

## Syntax & Definition

### Manual Decorator (Full Form — The "Why")

```python
def my_decorator(func):          # 1. Takes a function as argument
    def wrapper():               # 2. Defines a new inner function
        # --- Extra behaviour BEFORE ---
        print("Before the function runs")
        func()                   # 3. Calls the original function
        # --- Extra behaviour AFTER ---
        print("After the function runs")
    return wrapper               # 4. Returns the new function (NOT calling it)
```

**Breaking down each part:**

| Part | What it does |
|------|-------------|
| `def my_decorator(func):` | Outer function — takes the base function as argument |
| `def wrapper():` | Inner function — the "enhanced" version of `func` |
| `func()` | Calls the original base function inside the wrapper |
| `return wrapper` | Returns the wrapper *object* (its memory address), not the result of calling it |

### Syntactic Sugar Form (Industry Standard)

```python
@my_decorator
def base_function():
    print("I am the base function")
```

This single `@my_decorator` line above `base_function` is **exactly equivalent** to writing:

```python
base_function = my_decorator(base_function)
```

Python performs that assignment automatically. The `@` symbol is called **syntactic sugar** — it makes the code cleaner without changing what happens under the hood.

---

## Detailed Explanation with Examples

### Example 1: Manual Decorator — Step by Step

```python
def addition_function():
    print("Performing addition...")


def my_decorator(func):
    def wrapper():
        print("Start")
        func()        # func = addition_function (its address was passed in)
        print("End")
    return wrapper    # Return wrapper's address


# Manual way: pass the function, catch the returned wrapper
addition_function = my_decorator(addition_function)
# Now `addition_function` points to `wrapper`, NOT the original function

addition_function()   # Calling the NEW addition_function (which is wrapper)
```

**Output:**
```
Start
Performing addition...
End
```

**Line-by-line breakdown:**
- `my_decorator(addition_function)` → `func` receives the address of the original `addition_function`.
- Python reads `def wrapper():` → creates a new function object in memory (address e.g. `103`).
- `return wrapper` → Returns address `103` to the caller.
- `addition_function = my_decorator(...)` → The variable `addition_function` is **re-bound** to address `103` (the wrapper). The original function object at its old address still exists in memory (referenced by `func` inside the closure) but `addition_function` no longer points to it.
- `addition_function()` → Calls `wrapper()`. Inside `wrapper`, `func()` calls the *original* function.

**Memory model:**
```
Before decoration:
  addition_function  →  [address 101: "Performing addition..." logic]

After decoration:
  addition_function  →  [address 103: wrapper logic]
                              └── func  →  [address 101: original logic]
```

**Why this works (closures):** `wrapper` is a *closure* — it "closes over" `func` from the enclosing scope of `my_decorator`. Even after `my_decorator` returns, `wrapper` retains access to `func`.

---

### Example 2: Decorator with `@` Syntax — Timer

```python
import time


def my_decorator(func):
    def wrapper():
        t1 = time.perf_counter()   # Record start time
        func()                      # Execute the original function
        t2 = time.perf_counter()   # Record end time
        total = t2 - t1
        print(f"Function took {total:.2f} seconds")
    return wrapper


@my_decorator                       # Equivalent to: loop_and_multiply = my_decorator(loop_and_multiply)
def loop_and_multiply():
    total = 0
    for i in range(1, 100):
        time.sleep(0.01)            # Simulate work
        total += i * total if total else i
    print(f"Result: {total}")


loop_and_multiply()
```

**Output:**
```
Result: <large_number>
Function took 0.99 seconds
```

**Line-by-line breakdown:**
- `@my_decorator` → Before `loop_and_multiply` is even defined in the namespace, Python applies the decorator: `loop_and_multiply = my_decorator(loop_and_multiply)`.
- `t1 = time.perf_counter()` → High-resolution timer start; `perf_counter` is better than `time()` for measuring short durations.
- `func()` → Calls the original `loop_and_multiply` body.
- `t2 = time.perf_counter()` → Timer end.
- `print(f"... {total:.2f} ...")` → `:.2f` formats the float to 2 decimal places.

**Why this matters:** The original `loop_and_multiply` has **zero timing code**. The decorator adds timing to it without touching its source. You can now add `@my_decorator` to *any* function and get timing for free.

> **Pro tip:** `time.perf_counter()` is preferred over `time.time()` for benchmarking because it uses the system's highest-resolution clock and is not affected by system clock adjustments.

---

### Example 3: Exception-Handling Decorator (Real-World Production Pattern)

```python
def decorator_exception(func):
    """A decorator that silently catches exceptions and prints them."""
    def wrapper(*args, **kwargs):
        try:
            result = func(*args, **kwargs)
            return result
        except Exception as e:
            print(f"[ERROR] {type(e).__name__}: {e}")
    return wrapper


@decorator_exception
def add_with_input():
    value = input("Enter a number: ")
    print(int(value) + 100)   # Will raise ValueError if input is non-numeric


@decorator_exception
def add_two_numbers(a, b):
    result = a + b
    print(result)
    return result


# Normal case
add_two_numbers(10, 20)

# Error case — user enters "abc"
add_with_input()   # Type "abc" → decorator catches ValueError gracefully
```

**Output (normal):**
```
30
```

**Output (error case, input="abc"):**
```
Enter a number: abc
[ERROR] ValueError: invalid literal for int() with base 10: 'abc'
```

**Line-by-line breakdown:**
- `def wrapper(*args, **kwargs):` → `*args` collects any positional arguments; `**kwargs` collects keyword arguments. This makes the wrapper **universal** — it works with functions of any signature.
- `result = func(*args, **kwargs)` → Passes the collected arguments through to the original function. The `*` and `**` unpack them.
- `return result` → Ensures any return value from the original function is passed back to the caller.
- `except Exception as e:` → Catches *any* exception. In production you'd be more specific, but for a general-purpose decorator this is fine.

**Why `*args, **kwargs` is essential:**

```python
# WITHOUT *args/**kwargs — decorator is rigid, only works for 0-argument functions
def bad_wrapper():
    func()               # Breaks if func needs arguments!

# WITH *args/**kwargs — decorator works for ANY function
def good_wrapper(*args, **kwargs):
    func(*args, **kwargs)  # Passes whatever was received right through
```

---

### Example 4: Advanced — Decorator with Return Value + `*args`/`**kwargs`

```python
def decorator_exception_v3(func):
    def wrapper(*args, **kwargs):
        try:
            result = func(*args, **kwargs)
            return result           # Crucial: return the wrapped function's result
        except Exception as e:
            print(f"[ERROR] {type(e).__name__}: {e}")
            return None
    return wrapper


@decorator_exception_v3
def add(*args):
    """Adds any number of positional arguments."""
    total = sum(args)
    print(f"Sum: {total}")
    return total


@decorator_exception_v3
def add_v2(*args, **kwargs):
    """Adds positional and keyword arguments."""
    total = sum(args)
    for key, value in kwargs.items():
        total += value
    print(f"Total: {total}")
    return total


result1 = add(1, 2, 3)
print(f"Captured return: {result1}")

result2 = add_v2(1, 2, c=100)
print(f"Captured return: {result2}")
```

**Output:**
```
Sum: 6
Captured return: 6
Total: 103
Captured return: 103
```

**Line-by-line breakdown:**
- `return result` inside `wrapper` → Without this, calling `add(1, 2, 3)` and assigning the result would give `None`. Any function that uses the decorator's return value *must* have this line.
- `sum(args)` → `args` is a tuple of all positional arguments; Python's built-in `sum` adds them.
- `for key, value in kwargs.items():` → Iterates over keyword arguments dictionary and adds each value to the total.

---

### Example 5: Stacking Multiple Decorators

```python
def decorator_upper(func):
    def wrapper(*args, **kwargs):
        print("=== UPPER DECORATOR ===")
        result = func(*args, **kwargs)
        print("=== UPPER DECORATOR END ===")
        return result
    return wrapper


def decorator_lower(func):
    def wrapper(*args, **kwargs):
        print("--- lower decorator ---")
        result = func(*args, **kwargs)
        print("--- lower decorator end ---")
        return result
    return wrapper


@decorator_upper
@decorator_lower
def greet(name):
    print(f"Hello, {name}!")


greet("Alice")
```

**Output:**
```
=== UPPER DECORATOR ===
--- lower decorator ---
Hello, Alice!
--- lower decorator end ---
=== UPPER DECORATOR END ===
```

**Why this works:** Decorators stack **bottom-up** at application time, but execute **top-down** at call time. `@decorator_upper` applied last wraps the already-decorated function. Reading from bottom to top: `greet = decorator_upper(decorator_lower(greet))`.

---

## How Decorators Work Internally

### Step-by-step memory walkthrough

```
1. Python reads `def addition_function():` →
   Creates function object at address [101]
   Binds name `addition_function` → [101]

2. Python reads `def my_decorator(func):` →
   Creates function object at address [102]
   Binds name `my_decorator` → [102]

3. Python reads `@my_decorator` above `addition_function` →
   Calls my_decorator(addition_function)
   Inside my_decorator: func → [101]
   Python reads `def wrapper():` → Creates function object at [103]
   return wrapper → Returns [103]
   Python re-binds: addition_function → [103]

4. addition_function() →
   Calls object at [103] (wrapper)
   wrapper calls func() → calls object at [101] (original)

[Stack at call time]
  addition_function() → wrapper() → func() (original)
```

> **Key insight from the instructor:** When you do `add = my_decorator(add)`, the old memory reference at `add`'s address is *dereferenced* (Python's garbage collector will eventually reclaim it if no other references exist). The variable `add` now holds the address of `wrapper`. This is called **rebinding**.

---

## Comparison Table

| Feature | Manual Decorator | `@` Syntactic Sugar |
|---------|-----------------|-------------------|
| Readability | Lower | Higher |
| Explicit rebinding | Required (`func = dec(func)`) | Automatic |
| Flexibility | Can apply conditionally at runtime | Applied at definition time |
| Industry usage | Rare (only when dynamic) | Standard |
| Underlying mechanism | Identical | Identical |

| Wrapper has `*args, **kwargs`? | Effect |
|-------------------------------|--------|
| No | Decorator only works with functions of matching signature |
| Yes | Decorator works with **any** function — universal |

---

## Common Mistakes & Debugging — Decorators

### Mistake 1: Calling the function when passing to decorator

**Wrong:**
```python
add = my_decorator(add())   # add() calls the function FIRST, passes None
```
**Error:**
```
TypeError: 'NoneType' object is not callable
```
**Why it fails:** `add()` returns `None` (assuming no return statement). `my_decorator(None)` receives `None` as `func`. When `wrapper` calls `func()`, it tries to call `None`.

**Correct:**
```python
add = my_decorator(add)     # Pass the function object, not its result
```

---

### Mistake 2: Forgetting to return the wrapper

**Wrong:**
```python
def my_decorator(func):
    def wrapper():
        func()
    # Missing: return wrapper
```
**Error:**
```
TypeError: 'NoneType' object is not callable
```
**Why it fails:** `my_decorator` implicitly returns `None`. When you do `add = my_decorator(add)`, `add` becomes `None`.

**Correct:**
```python
def my_decorator(func):
    def wrapper():
        func()
    return wrapper          # Must return wrapper
```

---

### Mistake 3: Not using `*args`/`**kwargs` in wrapper

**Wrong:**
```python
def my_decorator(func):
    def wrapper():          # No parameters
        func()
    return wrapper

@my_decorator
def add(a, b):              # Needs two arguments
    return a + b

add(1, 2)   # Breaks!
```
**Error:**
```
TypeError: wrapper() takes 0 positional arguments but 2 were given
```
**Why it fails:** `add` is now `wrapper`. Calling `add(1, 2)` calls `wrapper(1, 2)`, but `wrapper` accepts no arguments.

**Correct:**
```python
def my_decorator(func):
    def wrapper(*args, **kwargs):
        result = func(*args, **kwargs)
        return result
    return wrapper
```

---

### Mistake 4: Forgetting to return result from wrapper

**Wrong:**
```python
def my_decorator(func):
    def wrapper(*args, **kwargs):
        func(*args, **kwargs)    # No return!
    return wrapper

@my_decorator
def add(a, b):
    return a + b

result = add(3, 4)
print(result)    # None!
```
**Why it fails:** `wrapper` calls `func` but discards its return value. `wrapper` returns `None` implicitly.

**Correct:**
```python
result = func(*args, **kwargs)
return result
```

---

## Best Practices & Pythonic Way — Decorators

```python
import functools

def my_decorator(func):
    @functools.wraps(func)      # Preserves __name__, __doc__ of original function
    def wrapper(*args, **kwargs):
        result = func(*args, **kwargs)
        return result
    return wrapper
```

- **Always use `@functools.wraps(func)`** inside your decorator. Without it, the decorated function loses its original `__name__` and `__doc__`, which breaks introspection, debugging, and tools like Sphinx.
- **Always use `*args, **kwargs`** unless you have a very specific reason to restrict the wrapper's signature.
- **Always `return result`** from the wrapper unless you are certain the wrapped function never returns a meaningful value.
- **Name your inner function meaningfully** — `wrapper` is the universal convention.
- Keep decorators focused on a **single cross-cutting concern** (timing, logging, auth, error-handling).

---

## When to Use / When NOT to Use — Decorators

| Use When... | Avoid When... |
|------------|--------------|
| Adding logging to multiple functions | The added behaviour is unique to one function — just write it inline |
| Timing/profiling functions | The decorator adds complexity that obscures simple code |
| Authentication / authorization checks | The decorator's side effects are unpredictable/hard to test |
| Retry logic on network calls | You need different behaviour per call (use a context manager instead) |
| Input validation shared across functions | The function signature changes frequently (wrapper needs updating) |
| Framework routes (`@app.route`) | You are a beginner still learning function basics |

---

## Related Topics & Connections — Decorators

- **Prerequisite knowledge:** Functions, closures, first-class functions, `*args`/`**kwargs`, `try/except`
- **How this connects to:**
  - **Closures** — `wrapper` is a closure; it captures `func` from the enclosing scope
  - **Higher-order functions** — `map`, `filter`, `sorted(key=...)` are all higher-order functions
  - **`@staticmethod` / `@classmethod` / `@property`** — Python built-in decorators (Python implements them in C, but conceptually identical)
  - **`@abstractmethod`** — Another built-in decorator (covered in OOP/ABC)
  - **Flask/Django routing** — `@app.route("/path")` is a decorator with arguments
- **What to learn next:** `functools.wraps`, decorator factories (decorators that accept arguments), class-based decorators

---

---

## Part 2 — Static Variables & Static Methods

### Syntax & Definition

```python
class ClassName:
    # Static (class) variable — defined WITHOUT self
    class_variable = "some value"

    def __init__(self, instance_var):
        # Instance variable — defined WITH self
        self.instance_var = instance_var

    @staticmethod
    def static_method(param1, param2):
        # No self or cls — acts like a plain function grouped under the class
        return param1 + param2
```

---

## Detailed Explanation with Examples — Static Variables

### Example 1: Basic Static Variable

```python
class User:
    course_name = "Ultimate Data Science Batch 2"   # Static variable

    def __init__(self, batch_number):
        self.batch_number = batch_number             # Instance variable


# Create two objects
obj_user1 = User(1)
obj_user2 = User(2)

# Instance variables — each object has its own copy
print(obj_user1.batch_number)    # 1
print(obj_user2.batch_number)    # 2

# Static variable — shared across ALL objects
print(obj_user1.course_name)     # Ultimate Data Science Batch 2
print(obj_user2.course_name)     # Ultimate Data Science Batch 2

# Standard way to access static variable: via class name
print(User.course_name)          # Ultimate Data Science Batch 2
```

**Output:**
```
1
2
Ultimate Data Science Batch 2
Ultimate Data Science Batch 2
Ultimate Data Science Batch 2
```

**Line-by-line breakdown:**
- `course_name = "Ultimate Data Science Batch 2"` → Defined at class level, no `self`. Python binds this to the `User` class object itself in memory.
- `self.batch_number = batch_number` → Bound to each individual instance. `obj_user1.batch_number` and `obj_user2.batch_number` live in separate memory spaces.
- `obj_user1.course_name` → Python first looks in `obj_user1`'s own namespace, doesn't find `course_name`, then looks up to the class and finds it there.
- `User.course_name` → Accesses the class namespace directly — the standard, explicit way.

---

### Example 2: Changing a Static Variable via Class Name

```python
class User:
    course_name = "Ultimate Data Science Batch 2"

    def __init__(self, batch_number):
        self.batch_number = batch_number


obj_user1 = User(1)
obj_user2 = User(2)

print(obj_user1.course_name)    # Ultimate Data Science Batch 2
print(obj_user2.course_name)    # Ultimate Data Science Batch 2

# Change via class name — affects ALL instances
User.course_name = "Generative AI"

print(obj_user1.course_name)    # Generative AI
print(obj_user2.course_name)    # Generative AI
```

**Output:**
```
Ultimate Data Science Batch 2
Ultimate Data Science Batch 2
Generative AI
Generative AI
```

**Why this works:** `User.course_name = "Generative AI"` modifies the value at the class level. Both `obj_user1` and `obj_user2` still look up to the same class, so they see the new value.

**Memory model:**
```
[Class: User]
  course_name  →  "Generative AI"     ← shared reference
       ↑               ↑
[obj_user1]        [obj_user2]
  batch_number=1     batch_number=2
```

---

### Example 3: Critical — Changing via Object (Name Shadowing/Mangling)

```python
class User:
    course_name = "Ultimate Data Science Batch 2"

    def __init__(self, batch_number):
        self.batch_number = batch_number


obj_user1 = User(1)
obj_user2 = User(2)

# Assigning via OBJECT — creates a NEW instance variable, does NOT modify class variable
obj_user1.course_name = "hello"

print(obj_user1.course_name)    # hello      ← obj_user1's own NEW copy
print(obj_user2.course_name)    # Ultimate Data Science Batch 2  ← class variable unchanged
print(User.course_name)         # Ultimate Data Science Batch 2  ← class variable unchanged
```

**Output:**
```
hello
Ultimate Data Science Batch 2
Ultimate Data Science Batch 2
```

**Why this happens (name shadowing):** When you write `obj_user1.course_name = "hello"`, Python does NOT modify `User.course_name`. Instead, it creates a *brand new instance variable* called `course_name` directly on `obj_user1`. Now `obj_user1` has its own `course_name` in its namespace, which **shadows** (hides) the class-level one when you access `obj_user1.course_name`.

**Memory model after shadowing:**
```
[Class: User]
  course_name  →  "Ultimate Data Science Batch 2"

[obj_user1]                     [obj_user2]
  batch_number = 1                batch_number = 2
  course_name  = "hello"          (no course_name — looks up to class)
```

> **Rule of thumb:** Always change class variables via `ClassName.variable = new_value`. Never via `obj.variable = new_value` if your intent is to change the shared class variable.

---

## Detailed Explanation with Examples — Static Methods

### Example 1: Basic Static Method

```python
class MathUtils:
    """A utility class grouping math operations — no instance state needed."""

    @staticmethod
    def add(a, b):
        return a + b

    @staticmethod
    def subtract(a, b):
        """Returns a - b."""
        return a - b


# Call without creating any object — use class name directly
result = MathUtils.add(10, 5)
print(result)                   # 15

result = MathUtils.subtract(100, 98)
print(result)                   # 2
```

**Output:**
```
15
2
```

**Line-by-line breakdown:**
- `@staticmethod` → A built-in Python decorator. Python internally processes this and marks `add` as a static method — no `self` or `cls` is injected when it is called.
- `MathUtils.add(10, 5)` → Called directly on the class. No object creation required.
- The functions behave exactly like plain functions, but they are *namespaced* under `MathUtils`, making it clear they are related to math operations.

---

### Example 2: Static Method + Instance Method in Same Class

```python
class MathUtilsNew:
    class_label = "Math Utilities v2"   # Static variable

    @staticmethod
    def add(a, b):
        return a + b

    @staticmethod
    def subtract(a, b):
        return a - b

    def describe(self):
        """Instance method that uses the static method internally."""
        result = MathUtilsNew.add(1, 2)   # Call static via class name inside instance method
        print(f"Label: {self.class_label}, add(1,2) = {result}")


# Using static method directly via class
print(MathUtils.add(3, 4))       # 7

# Using static method via an object (also valid, but not idiomatic)
obj = MathUtilsNew()
obj.describe()                   # Works fine
print(obj.add(10, 20))           # 30 — accessible but not recommended
```

**Output:**
```
7
Label: Math Utilities v2, add(1,2) = 3
30
```

**Why both work:** Python's attribute lookup finds `add` in the class namespace regardless of whether you access it via `MathUtilsNew.add(...)` or `obj.add(...)`. However, `MathUtilsNew.add(...)` is the **idiomatic, industry-preferred** way because it makes the intent clear: "I'm using a class utility, not object state."

---

## How Static Variables Work Internally

```
When Python reads `class User:` ...

[Memory]
Class Object for User
├── __name__: "User"
├── course_name: "Ultimate Data Science Batch 2"   ← stored HERE, in class namespace
├── __init__: <function>
└── ... other class attributes

When `obj_user1 = User(1)` is called:

[Memory]
Instance Object for obj_user1
├── batch_number: 1               ← stored HERE, in instance namespace
└── __class__: → [Class Object for User]   ← link back to class
```

Python's attribute lookup order (MRO): **instance namespace → class namespace → parent class namespaces**.

When you access `obj_user1.course_name`:
1. Python looks in `obj_user1`'s own `__dict__` — not found.
2. Python follows `__class__` link to `User`'s `__dict__` — found! Returns `"Ultimate Data Science Batch 2"`.

When you do `obj_user1.course_name = "hello"`:
1. Python writes `course_name: "hello"` into `obj_user1`'s own `__dict__`.
2. The class's `course_name` is **untouched**.

---

## Comparison Table — Static Variable vs Instance Variable vs Static Method vs Instance Method

| Feature | Static Variable | Instance Variable | Static Method | Instance Method |
|---------|----------------|-------------------|---------------|-----------------|
| Defined with | Class body, no `self` | `self.name = value` in `__init__` | `@staticmethod` + no `self` | `def method(self, ...)` |
| Belongs to | Class | Each instance separately | Class (logically) | Each instance |
| Shared across objects? | Yes | No | N/A | No |
| Access via class name | Yes (standard) | No | Yes (standard) | No |
| Access via object | Yes (but creates shadow if assigned) | Yes | Yes (not recommended) | Yes |
| Requires object creation | No | Yes | No | Yes |
| Use case | Shared config, counters | Per-object state | Utility functions | Object behaviour |

---

## Common Mistakes & Debugging — Static

### Mistake 1: Modifying class variable via object (unintentional shadowing)

**Wrong intent:**
```python
class Config:
    debug_mode = False

config1 = Config()
config1.debug_mode = True   # Intent: enable debug for everyone

config2 = Config()
print(config2.debug_mode)   # False — NOT changed! config1 got its own copy
```

**Why it fails:** As explained above, `config1.debug_mode = True` creates a new *instance* variable on `config1`, leaving the class variable untouched.

**Correct:**
```python
Config.debug_mode = True    # Modifies the class variable — affects ALL instances
print(config2.debug_mode)   # True
```

---

### Mistake 2: Adding `self` to a static method

**Wrong:**
```python
class MathUtils:
    @staticmethod
    def add(self, a, b):    # self is wrong here
        return a + b

MathUtils.add(1, 2)         # TypeError: add() missing 1 required positional argument
```
**Error:**
```
TypeError: add() missing 1 required positional argument: 'b'
```
**Why it fails:** Python does not inject `self` for `@staticmethod`, so when you call `MathUtils.add(1, 2)`, Python maps `1 → self`, `2 → a`, and `b` is missing.

**Correct:**
```python
@staticmethod
def add(a, b):
    return a + b
```

---

### Mistake 3: Forgetting `@staticmethod` decorator

**Wrong:**
```python
class MathUtils:
    def add(a, b):          # No @staticmethod, no self
        return a + b

MathUtils.add(1, 2)         # Works in Python 3 but is technically unbound
obj = MathUtils()
obj.add(1, 2)               # TypeError: add() takes 2 positional arguments but 3 were given
```
**Why it fails:** Without `@staticmethod`, calling `obj.add(1, 2)` injects `obj` as the first argument, making it `add(obj, 1, 2)` — 3 args for a 2-parameter function.

**Correct:**
```python
@staticmethod
def add(a, b):
    return a + b
```

---

## Best Practices & Pythonic Way — Static

- **Access class variables via the class name**, not via an object: `User.course_name` not `self.course_name` or `obj.course_name`.
- **Use `@staticmethod`** when the method does not need access to `self` (instance) or `cls` (class). If it needs class-level data, use `@classmethod` instead.
- **Use static variables for shared configuration** (e.g., API base URLs, version numbers, counters) that must be consistent across all instances.
- **Use static methods for utility/helper functions** that logically belong to a class concept but don't need instance state (e.g., `MathUtils.add`, `StringUtils.to_camel_case`).
- **Do not use mutable objects** (lists, dicts) as static variables unless you intentionally want mutation to be shared — this is a classic source of bugs.

```python
# DANGEROUS — mutable static variable
class BadClass:
    shared_list = []          # All instances share the SAME list object

# SAFE — each instance gets its own list
class GoodClass:
    def __init__(self):
        self.own_list = []
```

---

## When to Use / When NOT to Use — Static

| Use Static Variable When... | Avoid When... |
|-----------------------------|---------------|
| Data is truly shared (course name, DB config, version) | Each instance needs its own independent value |
| Counting total instances created | The "shared" value is only incidentally the same |
| Defining class-level constants | You need the variable to be immutable — use `@dataclass(frozen=True)` instead |

| Use Static Method When... | Avoid When... |
|---------------------------|---------------|
| Function is a pure utility that logically belongs to the class | The function needs `self` (instance state) — use instance method |
| You want clean namespacing without creating objects | The function needs class-level data at runtime — use `@classmethod` with `cls` |
| Implementing factory-like helpers | The method needs to be overridden in subclasses — use `@classmethod` |

---

## Related Topics & Connections — Static

- **Prerequisite knowledge:** Classes, objects, `__init__`, `self`, instance variables, methods
- **How this connects to:**
  - **`@classmethod`** — Like `@staticmethod` but receives `cls` (the class itself) as first arg; useful for alternative constructors
  - **`@property`** — Another built-in decorator; creates managed attributes
  - **`@abstractmethod`** — Forces subclasses to implement a method
  - **Inheritance** — Subclasses inherit static variables; overriding them requires care
  - **Name mangling (`__var`)** — Related to how Python manages attribute namespaces
- **What to learn next:** `@classmethod`, `@property`, dataclasses, `__slots__` for memory optimization

---

## Interview & Exam Corner

### Frequently Asked Questions

**Decorators:**

1. **Q: What is a decorator in Python?**
   A: A decorator is a higher-order function that takes a function as input, defines a new inner function (wrapper) that adds behaviour before/after the original, and returns the wrapper. The `@` syntax is shorthand for `func = decorator(func)`.

2. **Q: What is the difference between `@decorator` and `func = decorator(func)`?**
   A: They are functionally identical. `@decorator` is syntactic sugar applied at function definition time. Both rebind the function name to the wrapper returned by the decorator.

3. **Q: Why do we use `*args` and `**kwargs` in the wrapper?**
   A: To make the decorator universal — it can wrap functions with any signature. Without them, the wrapper's parameter list would need to match the wrapped function's exactly, limiting reusability.

4. **Q: What happens to the original function after decoration?**
   A: The variable name that held the original function is rebound to the wrapper. The original function object still exists in memory (referenced by `func` inside the closure) but is no longer accessible by its original name. Without `@functools.wraps`, its `__name__` and `__doc__` are lost.

5. **Q: Name three real-world use cases for decorators.**
   A: (1) Timing/profiling functions, (2) Authentication/authorization in web frameworks, (3) Automatic error handling / retry logic, (4) Logging, (5) Caching (`@functools.lru_cache`).

**Static:**

6. **Q: What is the difference between a class variable and an instance variable?**
   A: A class variable is defined at class level (no `self`) and is shared across all instances. An instance variable is defined via `self.name` in `__init__` and is unique to each object.

7. **Q: If I do `obj.class_var = "new"`, does it change the class variable?**
   A: No. Python creates a new *instance* variable `class_var` on `obj`, shadowing the class variable. The class variable and other instances are unaffected. To change the class variable, use `ClassName.class_var = "new"`.

8. **Q: What is the difference between `@staticmethod` and `@classmethod`?**
   A: `@staticmethod` receives no implicit argument — it's a plain function namespaced under the class. `@classmethod` receives `cls` (the class itself) as the first argument, allowing it to access/modify class-level state and work correctly with inheritance.

9. **Q: Can you call a static method via an instance?**
   A: Yes, `obj.static_method()` works, but the convention is `ClassName.static_method()` because it makes the intent clear and avoids confusion with instance methods.

---

### Tricky Output Questions

**Question 1:**
```python
def decorator(func):
    def wrapper():
        print("Before")
        func()
        print("After")
    return wrapper

@decorator
def say_hello():
    print("Hello")

say_hello()
```
**Answer:** `Before` → `Hello` → `After` **Why:** `say_hello` is now `wrapper`. Calling it runs `print("Before")`, then `func()` (original `say_hello`), then `print("After")`.

---

**Question 2:**
```python
class Counter:
    count = 0

c1 = Counter()
c2 = Counter()
c1.count = 10
print(Counter.count)
print(c2.count)
```
**Answer:** Both print `0`. **Why:** `c1.count = 10` creates an *instance* variable on `c1`, shadowing the class variable. `Counter.count` and `c2.count` (which still looks up to the class) remain `0`.

---

**Question 3:**
```python
def d1(func):
    def w():
        print("D1 start")
        func()
        print("D1 end")
    return w

def d2(func):
    def w():
        print("D2 start")
        func()
        print("D2 end")
    return w

@d1
@d2
def hello():
    print("Hello")

hello()
```
**Answer:**
```
D1 start
D2 start
Hello
D2 end
D1 end
```
**Why:** `hello = d1(d2(hello))`. Calling `hello()` calls `d1`'s wrapper, which calls `d2`'s wrapper, which calls original `hello`.

---

### One-Liner Challenges

- **Challenge:** Apply a timing decorator to a function using `@` syntax.
  **Solution:** `@timing_decorator` placed immediately above the `def` line.

- **Challenge:** Make a decorator that prints "Starting" before any function call.
  **Solution:** `def dec(f): return lambda *a, **k: (print("Starting"), f(*a, **k))[1]`

- **Challenge:** Access a class variable without creating an object.
  **Solution:** `ClassName.variable_name`

---

## Quick-Reference Cheat Sheet

```
DECORATORS CHEAT SHEET
========================
def decorator(func):
    def wrapper(*args, **kwargs):
        # before logic
        result = func(*args, **kwargs)
        # after logic
        return result
    return wrapper

@decorator                  → shorthand for func = decorator(func)
@functools.wraps(func)      → preserves __name__, __doc__ of original
*args, **kwargs in wrapper  → makes decorator universal (any function)
return result               → must return if wrapped function has a return value
stack decorators            → applied bottom-up, run top-down
@staticmethod/@property     → built-in Python decorators

gotcha 1: passing func()  instead of func  → passes None, crashes
gotcha 2: missing return wrapper          → decorator returns None
gotcha 3: no *args/**kwargs               → rigid, only works for exact signature
gotcha 4: no return result                → swallows return value

STATIC VARIABLES & METHODS CHEAT SHEET
========================================
class_var = "value"         → static variable (no self), shared across all instances
@staticmethod               → static method decorator
ClassName.var               → standard access/modify for static variable
obj.var = "new"             → creates INSTANCE variable (shadows class var — DANGER)
ClassName.var = "new"       → modifies class variable — affects ALL instances
@staticmethod def fn(a, b)  → no self/cls, call via ClassName.fn(a, b)
@classmethod def fn(cls, a) → receives class as first arg, use for factory methods

gotcha 1: obj.class_var = x → does NOT change class variable (shadowing)
gotcha 2: @staticmethod with self parameter → TypeError (no self injected)
gotcha 3: mutable static var (list/dict)  → shared mutation across all instances
```

---

## Summary (TL;DR)

**Decorators:**
- A decorator is a function that takes a function, wraps it in a new function with extra behaviour, and returns the new function.
- The `@decorator_name` syntax is shorthand for `func = decorator_name(func)`.
- Always use `*args, **kwargs` in the wrapper to support any function signature.
- Always `return result` from the wrapper if the original function returns something.
- Real-world uses: timing, logging, authentication, error handling, caching.
- `@staticmethod`, `@classmethod`, `@property` are all built-in Python decorators.

**Static Variables & Methods:**
- Static variables are class-level variables (no `self`); shared across all instances.
- Access and modify them via `ClassName.variable` to avoid shadowing bugs.
- Assigning via an object (`obj.class_var = x`) creates a new instance variable — it does NOT modify the class variable.
- `@staticmethod` creates a method that behaves like a plain function but is namespaced under the class — no object needed to call it.
- Use static methods for utility functions that logically belong to a class but don't need instance or class state.

---

## Flashcard-Style Recall

- **Q: What is a decorator?** A: A function that takes another function as an argument, wraps it to add behaviour, and returns the enhanced function.

- **Q: What does `@my_decorator` above a function do?** A: It is equivalent to `func = my_decorator(func)` — it rebinds the function name to the wrapper returned by the decorator.

- **Q: Why use `*args, **kwargs` in the wrapper?** A: To make the decorator work with any function regardless of its parameter signature.

- **Q: What error occurs when you forget `return wrapper`?** A: `TypeError: 'NoneType' object is not callable` — the decorator returns `None`, which gets called later.

- **Q: What is syntactic sugar?** A: Syntax that makes code easier to read/write without changing the underlying behaviour (e.g., `@decorator` instead of `func = decorator(func)`).

- **Q: What is a static variable?** A: A class-level variable defined without `self`, shared across all instances of the class.

- **Q: How do you properly change a static variable?** A: Via the class name: `ClassName.variable = new_value`. Never via an object.

- **Q: What happens when you do `obj.class_var = "hello"`?** A: Python creates a new *instance* variable on `obj`, shadowing the class variable. The class variable is unchanged.

- **Q: What is a static method?** A: A method decorated with `@staticmethod` that takes no `self` or `cls`. It behaves like a plain function grouped under the class namespace.

- **Q: What is the difference between `@staticmethod` and `@classmethod`?** A: `@staticmethod` receives no implicit argument. `@classmethod` receives `cls` (the class) as its first argument.

- **Q: Can a static method be called without creating an object?** A: Yes — `ClassName.static_method(args)` works without any instance.

- **Q: What is the purpose of `@functools.wraps(func)` in a decorator?** A: It copies the `__name__`, `__doc__`, and other metadata from the original function onto the wrapper, preventing loss of identity after decoration.

- **Q: What are three built-in Python decorators?** A: `@staticmethod`, `@classmethod`, `@property` (and `@abstractmethod` from `abc`).

- **Q: Name a closure relationship inside a decorator.** A: The `wrapper` function is a closure — it retains access to `func` from `my_decorator`'s scope even after `my_decorator` has returned.
