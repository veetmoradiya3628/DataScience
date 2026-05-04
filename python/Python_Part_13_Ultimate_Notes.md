# Python Day 15 — Iterators, Generators & Decorators

> **Advanced Python Trilogy** — This session covers three tightly connected advanced concepts: Iterators (sequential element access with lazy loading), Generators (iterator factories using `yield`), and Decorators (function-wrapping with `@` syntax). Mastery of these three unlocks idiomatic, professional-grade Python.

---

# PART 1 — ITERATORS

---

## Concept Overview

An **iterator** is an object that allows sequential traversal of a collection (list, string, tuple, etc.) one element at a time, without exposing the collection's internal structure.

- **Why it exists:** Standard Python loops load all data upfront. Iterators use **lazy loading** — elements are fetched one at a time, only when requested. This is critical for huge datasets that won't fit in RAM.
- **Real-world analogy:** Think of a TV remote's channel-up button. You don't see all channels at once — you press the button once to see channel 1, once more for channel 2. The TV "iterator" gives you one channel at a time on demand.
- **Where it fits:** Iterators are the engine that powers Python's `for` loops. Every `for` loop internally uses an iterator under the hood.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **Iterable** | Any object you can loop over (list, string, tuple, dict, set) |
| **Iterator** | An object with a `__next__()` method that returns one element at a time |
| `iter()` | Built-in function that converts an iterable into an iterator |
| `next()` | Built-in function that fetches the next element from an iterator |
| **Lazy Loading** | Loading/computing data on-demand rather than all at once |
| **`StopIteration`** | Exception raised when an iterator has no more elements to yield |
| `list_iterator` | The iterator class Python creates internally for list iterators |

---

## Syntax & Definition

```python
# Step 1: Create an iterable (e.g., a list)
my_list = [1, 2, 3, 4, 5, 6]

# Step 2: Convert to an iterator using iter()
iterator = iter(my_list)

# Step 3: Fetch elements one at a time using next()
value = next(iterator)   # Returns: 1
value = next(iterator)   # Returns: 2
# ... continues until StopIteration is raised
```

---

## Detailed Explanation with Examples

### Example 1: Basic List Iteration vs Iterator

```python
my_list = [1, 2, 3, 4, 5, 6]

# --- Standard for loop ---
for i in my_list:
    print(i)

print(type(my_list))  # Check type of the collection
```

**Output:**
```
1
2
3
4
5
6
<class 'list'>
```

**Line-by-line breakdown:**
- Line 1: Creates a list with 6 integers stored fully in memory
- Lines 3-4: A `for` loop iterates over all elements — Python internally calls `iter()` and `next()` behind the scenes
- Line 6: `type(my_list)` confirms this is a regular `list` object

**Why this works:** A `list` is an *iterable* but not an *iterator*. The `for` loop transparently converts it to an iterator.

---

### Example 2: Creating and Using an Iterator Manually

```python
my_list = [1, 2, 3, 4, 5, 6]

# Convert the list to an iterator
iterator = iter(my_list)

print(type(iterator))   # Check the type of the iterator object
print(iterator)         # Print the iterator itself
```

**Output:**
```
<class 'list_iterator'>
<list_iterator object at 0x7f3c8a1b2e50>
```

```python
# Manually fetch elements one at a time
print(next(iterator))   # → 1
print(next(iterator))   # → 2
print(next(iterator))   # → 3
print(next(iterator))   # → 4
print(next(iterator))   # → 5
print(next(iterator))   # → 6
print(next(iterator))   # → StopIteration error!
```

**Output:**
```
1
2
3
4
5
6
StopIteration
```

**Line-by-line breakdown:**
- `iter(my_list)`: Converts the list into a `list_iterator` object. The object does NOT contain the elements visibly — it holds a *pointer* to the current position.
- `type(iterator)`: Reveals the class is `list_iterator`, a special Python internal class
- `print(iterator)`: Shows only the memory address — no elements are exposed (lazy loading principle)
- Each `next(iterator)` call: Advances the internal pointer by 1 and returns that element
- Final `next()` call after the last element: Raises `StopIteration` because there is nothing left

**Why this works:** Lazy loading means only one element lives in "active memory" at a time. The rest remain untouched in the original list.

---

### Example 3: Safe Iteration with Exception Handling

```python
my_list = [1, 2, 3, 4, 5, 6]
iterator = iter(my_list)

# Safely iterate using try/except
while True:
    try:
        value = next(iterator)
        print(value)
    except StopIteration:
        print("There are no elements in the iterator.")
        break
```

**Output:**
```
1
2
3
4
5
6
There are no elements in the iterator.
```

**Line-by-line breakdown:**
- `while True`: Infinite loop that runs until broken explicitly
- `try: next(iterator)`: Attempts to fetch the next element
- `except StopIteration`: Catches the end-of-iterator signal gracefully
- `print(...)`: Informs the user that all elements have been consumed
- `break`: Exits the infinite loop cleanly

**Why this works:** This is exactly how Python's internal `for` loop is implemented. The `for` loop handles the `StopIteration` exception automatically; this pattern lets you replicate that behavior manually.

---

### Example 4: String Iterator

```python
my_string = "Python"
string_iterator = iter(my_string)

print(next(string_iterator))   # → P
print(next(string_iterator))   # → y
print(next(string_iterator))   # → t
```

**Output:**
```
P
y
t
```

**Why this works:** Strings are iterables in Python. `iter()` works on any iterable, not just lists. Each `next()` call returns the next character.

---

## How It Works Internally

When you call `iter(my_list)`, Python calls `my_list.__iter__()` which returns a `list_iterator` object. This object maintains an **internal index pointer** starting at 0.

```
my_list = [1, 2, 3, 4, 5, 6]
            ↑
            index = 0  ← iterator pointer starts here

After next():
            [1, 2, 3, 4, 5, 6]
                ↑
                index = 1

After next():
            [1, 2, 3, 4, 5, 6]
                   ↑
                   index = 2
...and so on
```

When Python's `for` loop runs:
```
for i in my_list:
    ↓
temp_iter = my_list.__iter__()      # Step 1: Get iterator
while True:
    try:
        i = temp_iter.__next__()    # Step 2: Get next value
        <loop body>
    except StopIteration:
        break                       # Step 3: Exit on exhaustion
```

**Memory benefit:** A `list_iterator` consumes only **~48 bytes** regardless of list size. The list itself may consume megabytes, but iterating through it doesn't duplicate that data.

---

## Common Mistakes & Debugging

### Mistake 1: Iterating a consumed (exhausted) iterator

**Wrong:**
```python
my_list = [1, 2, 3]
iterator = iter(my_list)

for val in iterator:
    print(val)

# Trying to reuse the same iterator
for val in iterator:
    print(val)     # Nothing prints!
```

**Output:**
```
1
2
3
(nothing)
```

**Why it fails:** Once an iterator is exhausted (all elements consumed), its internal pointer stays at the end. Calling `next()` again simply raises `StopIteration` immediately.

**Correct:**
```python
my_list = [1, 2, 3]

for val in my_list:          # Iterate over the LIST (not an iterator)
    print(val)

for val in my_list:          # Works again — list creates a fresh iterator each time
    print(val)
```

---

### Mistake 2: Calling `next()` on an iterable instead of an iterator

**Wrong:**
```python
my_list = [1, 2, 3]
print(next(my_list))   # TypeError!
```

**Error:**
```
TypeError: 'list' object is not an iterator
```

**Why it fails:** A list is an *iterable*, not an *iterator*. You must convert it with `iter()` first.

**Correct:**
```python
my_list = [1, 2, 3]
iterator = iter(my_list)
print(next(iterator))   # → 1
```

---

## Best Practices & Pythonic Way

```python
# ✅ Pythonic: Just use a for loop — Python handles iter/next internally
for item in my_list:
    process(item)

# ✅ Use iter/next only when you need fine-grained control
iterator = iter(data)
first = next(iterator)            # Grab the first element separately
for remaining in iterator:        # Continue from where you left off
    process(remaining)

# ✅ Provide a default value to avoid StopIteration
value = next(iterator, None)      # Returns None instead of raising an error
```

---

## When to Use / When NOT to Use

| Use When... | Avoid When... |
|------------|--------------|
| You need to manually step through a collection | Simple iteration — just use a `for` loop |
| Processing a stream of data (files, network) | Data is small and fits easily in memory |
| Implementing custom data structures | You need random access (use list indexing instead) |
| Building generators or pipelines | You need to re-iterate multiple times over the same data |
| Reading large files line by line | You need the length of the data upfront |

---

---

# PART 2 — GENERATORS

---

## Concept Overview

A **generator** is a special kind of iterator created using a regular function with the `yield` keyword instead of `return`. Generators produce values **lazily** — one at a time, only when requested — without storing them all in memory.

- **Why it exists:** Creating a full iterator class requires implementing `__iter__()` and `__next__()` manually. Generators provide a simpler, more readable way to achieve the same result using a function.
- **Real-world analogy:** Think of a coffee machine. It doesn't brew 100 cups of coffee and store them. It brews one cup when you press the button. A generator is the coffee machine — it produces one value on demand, not all at once.
- **Where it fits:** Generators are a **subclass of iterators**. Every generator is an iterator, but not every iterator is a generator. They are essential for memory-efficient data pipelines, file processing, and infinite sequences.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **Generator function** | A function that contains one or more `yield` statements |
| **Generator object** | The iterator-like object returned when a generator function is called |
| **`yield`** | Keyword that pauses the function, saves its state, and returns a value to the caller |
| **`return`** | Terminates a function entirely; in a generator, equivalent to raising `StopIteration` |
| **Lazy evaluation** | Values are computed and delivered on demand, not all at once |
| **State preservation** | The generator remembers local variables and the execution point between `yield` calls |

---

## Syntax & Definition

```python
# Generator function — uses yield instead of return
def my_generator(n):
    for i in range(n):
        yield i * i          # Pauses here, returns value, resumes on next call

# Calling the generator function returns a generator OBJECT
gen = my_generator(5)

# Consume the generator
for value in gen:
    print(value)
```

- `yield` is the key difference — it transforms an ordinary function into a generator factory
- Each call to `next()` on the generator resumes from the last `yield` statement
- When the function body ends (or `return` is hit), `StopIteration` is raised automatically

---

## Detailed Explanation with Examples

### Example 1: `return` vs `yield` — The Core Difference

```python
# ❌ Using return — only gets ONE value from a loop
def square_return(n):
    for i in range(n):
        return i * i    # Exits on the FIRST iteration

result = square_return(3)
print(result)           # Only prints 0 (0²)
```

**Output:**
```
0
```

```python
# ✅ Using yield — gets ALL values lazily
def square_yield(n):
    for i in range(n):
        yield i * i     # Pauses, returns value, resumes next call

gen = square_yield(3)
print(gen)              # Shows generator object, not the values
```

**Output:**
```
<generator object square_yield at 0x7f3c8a1b2e50>
```

**Line-by-line breakdown:**
- `return` immediately exits the function and throws away all loop state
- `yield` *pauses* the function at that line, saves the entire local state (variables, loop counter), and hands the value back to the caller
- When `next()` is called again, execution resumes from the line *after* the `yield`

---

### Example 2: Consuming a Generator with a `for` Loop

```python
def square_yield(n):
    for i in range(n):
        yield i * i

# Method 1: for loop (most common)
for value in square_yield(3):
    print(value)
```

**Output:**
```
0
1
4
```

**Execution trace:**
1. `square_yield(3)` is called → generator object created, function body NOT executed yet
2. First `for` iteration → `next()` called internally → function runs until first `yield` → yields `0 * 0 = 0` → paused
3. Second iteration → resumes from after `yield` → `i` becomes 1 → yields `1 * 1 = 1` → paused
4. Third iteration → `i` becomes 2 → yields `2 * 2 = 4` → paused
5. Fourth iteration → `range(3)` exhausted → function returns → `StopIteration` raised → loop ends

---

### Example 3: Consuming a Generator with `next()`

```python
def square_yield(n):
    for i in range(n):
        yield i * i

gen = square_yield(3)

print(next(gen))   # → 0
print(next(gen))   # → 1
print(next(gen))   # → 4
print(next(gen))   # → StopIteration!
```

**Output:**
```
0
1
4
StopIteration
```

---

### Example 4: Multiple `yield` Statements in One Generator

```python
def my_generator():
    yield 1          # First pause point — returns 1
    yield 2          # Second pause point — returns 2
    yield 3          # Third pause point — returns 3
    # Function ends here — StopIteration raised

gen = my_generator()

print(next(gen))   # → 1
print(next(gen))   # → 2
print(next(gen))   # → 3
print(next(gen))   # → StopIteration
```

**Output:**
```
1
2
3
StopIteration
```

**Line-by-line breakdown:**
- Each `yield` is an independent pause point
- The function resumes execution from the *line after the last `yield`* each time
- A generator can have any number of `yield` statements scattered through its body

```python
# Same generator with a for loop
gen = my_generator()
for val in gen:
    print(val)
```

**Output:**
```
1
2
3
```

---

### Example 5: Real-World Use — Reading a Large File

```python
def read_large_file(file_path):
    """
    Generator that reads a large file one line at a time.
    Only one line is in memory at any given moment.
    """
    with open(file_path, 'r') as file:
        for line in file:
            yield line           # Returns one line, then pauses

# Usage — process a 10GB file with minimal memory usage
file_path = "large_file.txt"

for line in read_large_file(file_path):
    print(line.strip())          # Process one line at a time
```

**Line-by-line breakdown:**
- `with open(file_path, 'r') as file`: Opens the file using a context manager (auto-closes when done)
- `for line in file`: File objects themselves are iterables over their lines
- `yield line`: Returns one line to the caller and pauses — the next line is NOT read until requested
- `line.strip()`: Removes leading/trailing whitespace and newline characters

**Why this is powerful:** A 10GB log file would crash your program if loaded with `file.read()`. This generator never holds more than one line in memory regardless of file size.

---

## Comparison Table: `return` vs `yield`

| Feature | `return` | `yield` |
|--------|---------|--------|
| Exits function | Immediately and permanently | Temporarily (pauses) |
| Multiple values | Only one (the last one) | As many as needed |
| State preserved | No — local variables lost | Yes — entire frame saved |
| Creates generator | No | Yes |
| Memory usage | Returns full result | Returns one value at a time |
| Re-entrant | No | Yes (resumes from last `yield`) |

---

## Comparison Table: Iterator vs Generator

| Feature | Iterator (Manual) | Generator (Function with `yield`) |
|--------|------------------|----------------------------------|
| Creation | Implement `__iter__` and `__next__` class methods | Simple function with `yield` |
| Code complexity | High | Low |
| Memory efficiency | High | High |
| State management | Manual | Automatic |
| Readability | Verbose | Concise |
| Use case | Custom data structures | Data pipelines, large files, infinite sequences |
| Performance | Slightly faster | Slightly slower (has overhead) |

> **Pro tip:** Python's official docs note that generators write fast, compact code. Use generators for simple streaming use cases; write full iterator classes only for complex custom data structures.

---

## How It Works Internally

When Python encounters a `yield` inside a function, it transforms the function into a **generator function**. Calling this function returns a **generator object** — the function body is NOT executed at call time.

```
gen = square_yield(3)
↓
Python creates a generator frame:
  ┌──────────────────────────────┐
  │ Generator Frame              │
  │  locals: { n=3, i=0 }        │
  │  execution pointer: line 2   │
  │  state: SUSPENDED            │
  └──────────────────────────────┘

next(gen) is called →
  Frame resumes at line 2
  Loop runs → yield 0  →  SUSPENDED (i=0 captured)
  Returns 0 to caller

next(gen) is called again →
  Frame resumes AFTER yield
  i becomes 1 → yield 1 → SUSPENDED
  Returns 1 to caller

...until range(3) is exhausted →
  Frame reaches function end → CLOSED
  StopIteration raised
```

**Key insight:** Unlike a regular function call where the stack frame is destroyed on `return`, a generator's frame is *persisted in heap memory* between `next()` calls. This is what enables state preservation.

---

## Common Mistakes & Debugging

### Mistake 1: Using `return` where `yield` is needed

**Wrong:**
```python
def squares(n):
    for i in range(n):
        return i * i    # Only returns first value

for val in squares(5):
    print(val)
```

**Output:**
```
0
```

**Why it fails:** `return` exits the function completely on the first iteration. Only `0² = 0` is returned.

**Correct:**
```python
def squares(n):
    for i in range(n):
        yield i * i

for val in squares(5):
    print(val)
```

---

### Mistake 2: Reusing an exhausted generator

**Wrong:**
```python
gen = my_generator()
for val in gen:
    print(val)        # Prints 1, 2, 3

for val in gen:
    print(val)        # Prints nothing — generator is exhausted!
```

**Why it fails:** Generators can only be traversed once. After `StopIteration` is raised, the generator is permanently closed.

**Correct:**
```python
# Create a fresh generator for each traversal
for val in my_generator():    # Call the function again
    print(val)

for val in my_generator():    # New generator object
    print(val)
```

---

## Best Practices & Pythonic Way

```python
# ✅ Use generators for large data streams
def process_large_dataset(filename):
    with open(filename) as f:
        for line in f:
            yield line.strip()

# ✅ Generator expressions (one-liner generator)
squares = (x * x for x in range(10))     # Note: () not []

# ✅ Provide a default to avoid StopIteration crashes
gen = my_generator()
val = next(gen, "No more values")

# ✅ Use list() to materialize a generator when you need all values at once
all_squares = list(squares_gen(10))
```

---

## When to Use / When NOT to Use

| Use When... | Avoid When... |
|------------|--------------|
| Processing large files or datasets | Data is small — just use a list |
| Building data pipelines | You need to iterate multiple times (generators are one-shot) |
| Infinite sequences (e.g., counter) | You need random access by index |
| Memory-constrained environments | You need `len()` on the result |
| Streaming data (network, sensor feeds) | Code clarity is more important than memory savings |

---

---

# PART 3 — DECORATORS

---

## Concept Overview

A **decorator** is a design pattern in Python that allows you to modify or extend the behavior of a function (or class method) **without changing the function's actual source code**. Decorators are applied using the `@` symbol above a function definition.

- **Why it exists:** In real applications, you often need cross-cutting concerns — logging, authentication, timing, retrying — applied to many functions. Instead of copy-pasting that logic into every function, a decorator wraps the function with extra behavior once.
- **Real-world analogy:** Think of a coffee cup sleeve. The coffee (the function) stays the same. The sleeve (the decorator) adds heat protection (extra behavior) around it without changing the coffee itself.
- **Where it fits:** Decorators rely on two prerequisites: **function copies** (first-class functions) and **closures**. Understanding these two concepts is mandatory before decorators click.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **First-class function** | Functions are objects that can be assigned to variables, passed as arguments, and returned |
| **Function copy** | Assigning a function object to a variable, creating an alias |
| **Closure** | A function defined inside another function that can access the outer function's variables |
| **Decorator** | A function that takes another function as input and returns a new (wrapped) function |
| **`@` syntax** | Syntactic sugar that applies a decorator to a function at definition time |
| **Wrapper function** | The inner function inside a decorator that adds behavior around the original function |
| **`*args, **kwargs`** | Wildcards for positional and keyword arguments — makes decorators work on ANY function signature |

---

## Prerequisite 1: Function Copy (First-Class Functions)

```python
def welcome():
    return "Welcome to the Advanced Python course"

# Assign the function OBJECT (not its result) to a new variable
well = welcome          # No parentheses — we're copying the function, not calling it

print(type(well))       # → <class 'function'>
print(well())           # → "Welcome to the Advanced Python course"
```

**Output:**
```
<class 'function'>
Welcome to the Advanced Python course
```

**Key insight:** In Python, functions are **objects**. `welcome` without parentheses refers to the function object itself. `well = welcome` creates a second reference pointing to the same function object.

```python
# Even after deleting the original, the copy survives
del welcome             # Delete the original reference

print(well())           # → Still works! "Welcome to the Advanced Python course"
```

**Why this matters for decorators:** A decorator receives a function as a parameter. This is only possible because Python treats functions as first-class objects.

---

## Prerequisite 2: Closures

A **closure** is a function defined inside another function. The inner function has access to variables in the outer function's scope, even after the outer function has finished executing.

```python
def main_welcome(message):
    message = "Welcome"           # Outer function's local variable

    def sub_welcome():            # Inner function (closure)
        print("Welcome to the Advanced Python course")
        print("Please learn these concepts properly")
        print(message)            # Accessing outer variable from inside inner function

    return sub_welcome            # Return the inner function object (not its result)

# Calling main_welcome() returns the sub_welcome function
result = main_welcome("Welcome")
result()                          # Now call the returned function
```

**Output:**
```
Welcome to the Advanced Python course
Please learn these concepts properly
Welcome
```

**Line-by-line breakdown:**
- `main_welcome` is the **outer function** — takes a `message` parameter
- `sub_welcome` is the **inner function** (the closure) — defined inside `main_welcome`
- `print(message)` — the inner function accesses `message`, which belongs to the outer function's scope
- `return sub_welcome` — the outer function returns the inner function *object* (not a call to it)
- `result = main_welcome("Welcome")` — calls `main_welcome`, which returns `sub_welcome`
- `result()` — now calls `sub_welcome()`

**Closure rule:** The inner function "closes over" all variables from the outer function's scope, keeping them alive even after the outer function returns.

### Closures with Function Parameters

```python
def main_welcome(func):           # Accepts a FUNCTION as a parameter
    def sub_welcome():
        print("Welcome to the Advanced Python course")
        print("Please learn these concepts properly")
        func("Welcome everyone to this tutorial")   # Calls the passed-in function

    return sub_welcome

# Pass the built-in print function as an argument
result = main_welcome(print)
result()
```

**Output:**
```
Welcome to the Advanced Python course
Please learn these concepts properly
Welcome everyone to this tutorial
```

**Why this matters:** This pattern — a function that takes another function as a parameter and returns a wrapped function — IS the decorator pattern. We just haven't added the `@` syntax yet.

---

## Syntax & Definition

```python
# A decorator is a closure that accepts a function
def my_decorator(func):            # Outer function receives the target function
    def wrapper():                 # Inner function wraps extra behavior around it
        print("Before the function runs")
        func()                     # Call the original function
        print("After the function runs")
    return wrapper                 # Return the wrapper (not a call to it)

# Apply the decorator using @ syntax
@my_decorator
def say_hello():
    print("Hello!")

# Calling say_hello() now calls the wrapper
say_hello()
```

**The `@my_decorator` line is equivalent to:**
```python
say_hello = my_decorator(say_hello)
```

---

## Detailed Explanation with Examples

### Example 1: Building and Using a Decorator

```python
# Step 1: Define the decorator
def my_decorator(func):
    def wrapper():
        print("Something is happening BEFORE the function is called.")
        func()
        print("Something is happening AFTER the function is called.")
    return wrapper

# Step 2: Apply the decorator using @ syntax
@my_decorator
def say_hello():
    print("Hello!")

# Step 3: Call the decorated function
say_hello()
```

**Output:**
```
Something is happening BEFORE the function is called.
Hello!
Something is happening AFTER the function is called.
```

**Line-by-line breakdown:**
- `my_decorator(func)`: Outer function — receives `say_hello` as `func`
- `def wrapper()`: Inner function — will replace `say_hello`
- `func()`: Calls the original `say_hello()` inside the wrapper
- `return wrapper`: Returns the wrapper function object
- `@my_decorator` above `say_hello`: Python executes `say_hello = my_decorator(say_hello)` at definition time
- `say_hello()`: Actually calls `wrapper()`, which calls the original `say_hello` in the middle

**Execution flow:**
```
say_hello()
  ↓ (which is now wrapper())
  "Something is happening BEFORE..."
  func()  ← original say_hello()
    "Hello!"
  "Something is happening AFTER..."
```

---

### Example 2: Manual Decorator (Without `@` Syntax)

```python
def main_welcome(func):
    def sub_welcome():
        func("Welcome everyone to this tutorial")
        print("Welcome to the Advanced Python course")
        print("Please learn these concepts properly")
    return sub_welcome

def course_introduction():
    print("This is an Advanced Python course")

# Manual decorator application (what @ does behind the scenes)
course_introduction = main_welcome(course_introduction)
course_introduction()
```

**Output:**
```
This is an Advanced Python course
Welcome to the Advanced Python course
Please learn these concepts properly
```

This is identical to writing `@main_welcome` above `course_introduction`. The `@` is purely syntactic sugar.

---

### Example 3: Decorator with Arguments (`*args, **kwargs`)

```python
# A decorator that repeats any function n times
def repeat(n):                          # Outer function receives the repeat count
    def decorator(func):                # Middle function receives the target function
        def wrapper(*args, **kwargs):   # Inner function — accepts ANY arguments
            for _ in range(n):
                func(*args, **kwargs)   # Calls the function n times with original args
        return wrapper
    return decorator

# Apply the decorator with an argument
@repeat(3)                              # repeat(3) returns decorator, which wraps greet
def greet(name):
    print(f"Hello, {name}!")

greet("Alice")
```

**Output:**
```
Hello, Alice!
Hello, Alice!
Hello, Alice!
```

**Line-by-line breakdown:**
- `repeat(n)`: The outermost function takes the decorator *argument* (3 in this case) and returns a decorator
- `decorator(func)`: Receives the function to be decorated (`greet`)
- `wrapper(*args, **kwargs)`: Uses `*args` and `**kwargs` to accept any number of positional and keyword arguments — making the decorator generic
- `for _ in range(n)`: Calls the original function `n` times
- `@repeat(3)`: Python evaluates `repeat(3)` first (returns `decorator`), then applies `decorator` to `greet`

**Why `*args, **kwargs` matters:** Without them, a decorator only works on functions with no arguments. With them, the decorator works universally on ANY function.

---

### Example 4: Real-World Decorator — Timing a Function

```python
import time

def timer(func):
    def wrapper(*args, **kwargs):
        start = time.time()               # Record start time
        result = func(*args, **kwargs)    # Call the original function
        end = time.time()                 # Record end time
        print(f"{func.__name__} ran in {end - start:.4f} seconds")
        return result                     # Always return the original result
    return wrapper

@timer
def compute_squares(n):
    return [i * i for i in range(n)]

output = compute_squares(1_000_000)
```

**Output:**
```
compute_squares ran in 0.0843 seconds
```

**Why this is powerful:** You add timing to ANY function by just adding `@timer` above it — zero modification to the function's own code. This is the "open/closed principle" — open for extension, closed for modification.

---

## How It Works Internally

```
STEP 1: Python reads @my_decorator above say_hello
         ↓
STEP 2: Python executes: say_hello = my_decorator(say_hello)
         ↓
STEP 3: my_decorator receives say_hello as its 'func' parameter
         ↓
STEP 4: my_decorator creates 'wrapper' closure (captures 'func' in its scope)
         ↓
STEP 5: my_decorator returns the 'wrapper' function object
         ↓
STEP 6: Python binds the name 'say_hello' to the 'wrapper' object
         ↓
STEP 7: When say_hello() is called, wrapper() runs — which calls original func() inside

Memory layout:
  say_hello (name) ──→ wrapper (function object)
                            └─ captures: func ──→ original say_hello code
```

---

## Common Mistakes & Debugging

### Mistake 1: Calling the function when returning from decorator

**Wrong:**
```python
def my_decorator(func):
    def wrapper():
        func()
    return wrapper()    # ❌ Calling wrapper with () — runs immediately!
```

**Error:** The decorator executes immediately at definition time instead of when called.

**Correct:**
```python
def my_decorator(func):
    def wrapper():
        func()
    return wrapper      # ✅ Return the function OBJECT, not a call to it
```

---

### Mistake 2: Forgetting `*args, **kwargs` in the wrapper

**Wrong:**
```python
def my_decorator(func):
    def wrapper():      # No *args, **kwargs
        func()
    return wrapper

@my_decorator
def greet(name):        # This function takes an argument
    print(f"Hello, {name}!")

greet("Alice")          # TypeError: wrapper() takes 0 positional arguments
```

**Error:**
```
TypeError: wrapper() takes 0 positional arguments but 1 was given
```

**Correct:**
```python
def my_decorator(func):
    def wrapper(*args, **kwargs):   # Accept and pass through all arguments
        func(*args, **kwargs)
    return wrapper
```

---

### Mistake 3: Not returning the function's result from the wrapper

**Wrong:**
```python
def my_decorator(func):
    def wrapper(*args, **kwargs):
        func(*args, **kwargs)       # Calls the function but discards its return value
    return wrapper

@my_decorator
def add(a, b):
    return a + b

result = add(3, 4)
print(result)           # → None (return value was lost!)
```

**Correct:**
```python
def my_decorator(func):
    def wrapper(*args, **kwargs):
        result = func(*args, **kwargs)
        return result               # Always return the result!
    return wrapper
```

---

## Best Practices & Pythonic Way

```python
import functools

# ✅ Use functools.wraps to preserve the original function's metadata
def my_decorator(func):
    @functools.wraps(func)          # Preserves __name__, __doc__, __module__
    def wrapper(*args, **kwargs):
        print("Before")
        result = func(*args, **kwargs)
        print("After")
        return result
    return wrapper

# Without @functools.wraps, the decorated function's __name__ becomes 'wrapper'
# With it, the __name__ remains the original function name
```

```python
# ✅ Stack multiple decorators (applied bottom-up)
@decorator_one
@decorator_two
def my_function():
    pass

# Equivalent to: my_function = decorator_one(decorator_two(my_function))
```

---

## When to Use / When NOT to Use

| Use When... | Avoid When... |
|------------|--------------|
| Adding logging to multiple functions | Only wrapping one function once — just put the logic inline |
| Authentication/authorization checks | The extra indirection makes code harder to debug |
| Timing or profiling functions | You need to inspect the decorated function's signature |
| Input validation | Decorator logic is complex — prefer explicit function composition |
| Caching results (`@functools.lru_cache`) | Team is unfamiliar with decorators |
| Flask/Django route registration (`@app.route`) | Simple single-use cases |

---

## Related Topics & Connections

- **First-class functions** — The foundation of decorators; functions as objects
- **Closures** — The mechanism that makes the wrapper "remember" the original function
- **Generators** — Also use a function-based pattern for creating iterators
- **`functools.wraps`** — Standard library tool for well-behaved decorators
- **`@property`** — A built-in decorator for getters/setters in classes
- **`@staticmethod`, `@classmethod`** — Built-in decorators for OOP methods
- **Flask/Django routes** — `@app.route('/home')` is a parametrized decorator
- **`@functools.lru_cache`** — Decorator for memoization (caching return values)

---

## Interview & Exam Corner

### Frequently Asked Questions

1. **Q: What is an iterator in Python?**
   A: An iterator is an object with `__iter__()` and `__next__()` methods. It delivers elements of a collection one at a time using lazy loading. Created from an iterable using `iter()`, consumed using `next()`.

2. **Q: What is the difference between an iterable and an iterator?**
   A: An *iterable* is any object that can be looped over (list, string, tuple). An *iterator* is the object that does the looping — it has state and returns one element per `next()` call. All iterators are iterables, but not all iterables are iterators.

3. **Q: What does `yield` do?**
   A: `yield` pauses a generator function, saves its local state (all variables and the execution pointer), and returns a value to the caller. When `next()` is called again, execution resumes from the line after the `yield`.

4. **Q: How is a generator different from a regular function?**
   A: A regular function runs to completion and returns one value. A generator pauses at each `yield`, remembers its state, and can return many values lazily over multiple `next()` calls.

5. **Q: What is a decorator?**
   A: A decorator is a function that takes another function as input, adds extra behavior by wrapping it, and returns the modified function. Applied using the `@decorator_name` syntax.

6. **Q: What are closures and how do they relate to decorators?**
   A: A closure is a function that captures variables from its enclosing scope. Decorators use closures to "capture" the original function (`func`) inside the wrapper function, so the wrapper can call it later.

7. **Q: What is `StopIteration`?**
   A: An exception raised by an iterator when there are no more elements to return. Python's `for` loop catches this exception automatically to cleanly end iteration.

---

### Tricky Output Questions

```python
# Question 1: What does this print?
def gen():
    yield 1
    yield 2
    yield 3

g = gen()
print(next(g))
print(next(g))
for val in g:
    print(val)
```
**Answer:** `1` then `2` then `3`. The `for` loop picks up from where `next()` left off.

---

```python
# Question 2: What does this print?
def my_decorator(func):
    def wrapper():
        print("A")
        func()
        print("C")
    return wrapper

@my_decorator
def greet():
    print("B")

greet()
```
**Answer:** `A` then `B` then `C`. The decorator sandwiches the original function.

---

```python
# Question 3: Is this a generator function?
def test():
    return 1
    yield 2

print(type(test()))
```
**Answer:** `<class 'generator'>`. The presence of ANY `yield` statement in a function — even unreachable code — makes it a generator function. The `return` terminates it immediately, but it still returns a generator object.

---

### One-Liner Challenges

- **Challenge:** Create a generator that yields all even numbers from 0 to n
  - **Solution:** `def evens(n): yield from range(0, n+1, 2)`

- **Challenge:** Create a decorator that prints the function name before calling it
  - **Solution:** `def log(f): return lambda *a, **k: (print(f.__name__), f(*a, **k))[1]`

- **Challenge:** Create a generator expression for squares of 1 to 10
  - **Solution:** `squares = (x**2 for x in range(1, 11))`

---

## Quick-Reference Cheat Sheet

```
ITERATORS, GENERATORS & DECORATORS — CHEAT SHEET
=================================================

ITERATORS
---------
iter(obj)           → Convert iterable to iterator
next(iterator)      → Get next element
next(it, default)   → Get next element or return default
StopIteration       → Raised when iterator is exhausted
type(iter([]))      → list_iterator

GENERATORS
----------
def gen(): yield x  → Generator function (creates generator object)
yield               → Pause, save state, return value
return in generator → End generator (raises StopIteration)
next(gen)           → Get next yielded value
for x in gen()      → Most common consumption pattern
(x for x in range) → Generator expression (use () not [])

DECORATORS
----------
@decorator          → Apply decorator: func = decorator(func)
def dec(func):      → Outer: receives function
  def wrapper():    → Inner: wraps extra behavior
    func()          → Call original function
  return wrapper    → Return wrapper object (not wrapper())

*args, **kwargs     → Make wrapper accept any function signature
@functools.wraps(f) → Preserve __name__, __doc__ of original function
@decorator(arg)     → Parametrized decorator — needs 3 levels of nesting

COMMON GOTCHAS
--------------
Generators are one-shot — once exhausted, cannot be restarted
next() on a list    → TypeError: 'list' is not an iterator (use iter() first)
return wrapper()    → Bug: runs immediately; should be return wrapper
Missing *args       → Wrapper won't accept arguments from decorated function
```

---

## Summary (TL;DR)

- **Iterators** convert iterables (lists, strings) into sequential, lazy-loading objects using `iter()` and `next()`. They deliver one element at a time and raise `StopIteration` when exhausted. Every `for` loop uses iterators internally.
- **Generators** are the easiest way to create iterators — write a function with `yield` instead of `return`. Python saves the function's entire state between calls, enabling lazy, memory-efficient value production.
- **`yield` vs `return`:** `return` exits and forgets; `yield` pauses and remembers.
- **Generators are ideal for large files** — read and process one line at a time without loading the entire file into memory.
- **Decorators** wrap functions with extra behavior using the `@` syntax. They are closures that receive a function as input and return a modified version of it — without touching the original code.
- **The `@` symbol is syntactic sugar** for `func = decorator(func)` — Python applies the decorator at definition time.
- **All three concepts share a theme:** lazy evaluation, memory efficiency, and separation of concerns.

---

## Flashcard-Style Recall (Q&A pairs)

- **Q: What is an iterator?** A: An object that delivers elements of a collection one at a time using `__next__()`. Created with `iter()`, consumed with `next()`.

- **Q: How do you create an iterator from a list?** A: `iterator = iter(my_list)`

- **Q: What exception does an exhausted iterator raise?** A: `StopIteration`

- **Q: What keyword turns a function into a generator?** A: `yield`

- **Q: How is `yield` different from `return`?** A: `return` exits the function permanently; `yield` pauses it and saves its state so it can resume on the next `next()` call.

- **Q: What is a generator object?** A: The iterator-like object returned when you call a generator function. The function body is not executed until `next()` is called.

- **Q: What is the type of `iter([1,2,3])`?** A: `list_iterator`

- **Q: What is a closure?** A: A function defined inside another function that can access and "close over" the outer function's variables.

- **Q: What does a decorator do?** A: It wraps a function to add extra behavior before and/or after the function runs, without modifying the original function's code.

- **Q: What does `@my_decorator` above a function definition mean?** A: It's shorthand for `func = my_decorator(func)`. Python applies the decorator at definition time.

- **Q: Why use `*args, **kwargs` in a wrapper function?** A: To make the decorator work with any function, regardless of how many or what type of arguments that function takes.

- **Q: What is the real-world use case for generators with files?** A: Reading large files one line at a time without loading the entire file into memory — critical for files that are GBs in size.

- **Q: What is the difference between a generator and an iterator?** A: Every generator is an iterator, but not vice versa. Generators are created with functions + `yield`; iterators can also be created by implementing `__iter__` and `__next__` in a class.

- **Q: What error occurs if you call `next()` on a plain list?** A: `TypeError: 'list' object is not an iterator`
