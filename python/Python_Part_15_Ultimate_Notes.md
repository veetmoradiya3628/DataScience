# Python Memory Management

---

## Concept Overview

- **Memory management in Python** is the process of allocating memory to objects when they are created and reclaiming that memory when objects are no longer needed.
- Python handles memory automatically using a combination of **reference counting**, a **cyclic garbage collector**, and various **internal optimizations** — so developers rarely need to manage memory manually.
- **Real-world analogy:** Think of memory like hotel rooms. When a guest (object) checks in, a room (memory) is assigned. When all guests leave (no more references), the room is freed for others. If two guests keep booking each other's rooms (circular reference), the hotel staff (garbage collector) must manually free those rooms.
- It sits at the core of Python's runtime — understanding it leads to writing more efficient, leak-free, production-quality code.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **Reference Counting** | Tracking how many variables/places point to a given object |
| **Garbage Collection (GC)** | Automated process to reclaim memory from unreachable objects |
| **Circular Reference** | Two or more objects referencing each other, preventing ref count from reaching zero |
| **Reference Cycle** | A chain of references that loops back to the original object |
| **Unreachable Object** | An object that can no longer be accessed by any variable in the program |
| **Deallocation** | Freeing the memory occupied by an object |
| **`sys` module** | Python's system configuration module; provides `getrefcount()` |
| **`gc` module** | Python's built-in garbage collector interface |
| **`tracemalloc`** | Standard library tool for tracing memory allocations |
| **Generator** | A function using `yield` to produce items one at a time, memory-efficiently |
| **`__del__`** | Destructor method called when an object is about to be garbage collected |

---

## Syntax & Definition

```python
import sys
import gc
import tracemalloc

# --- Reference Counting ---
sys.getrefcount(obj)          # Returns the reference count of obj

# --- Garbage Collection ---
gc.enable()                   # Enable automatic GC (default: already enabled)
gc.disable()                  # Disable automatic GC
gc.collect()                  # Manually trigger full garbage collection
gc.get_stats()                # Returns per-generation GC statistics
gc.garbage                    # List of unreachable objects found by GC

# --- Memory Profiling ---
tracemalloc.start()           # Start tracing memory allocations
snapshot = tracemalloc.take_snapshot()  # Capture current memory state
top_stats = snapshot.statistics('lineno')  # Get stats by line number

# --- Explicit Deletion ---
del variable_name             # Decrease reference count; may trigger deallocation
```

---

## Detailed Explanation with Examples

### Example 1: Reference Counting — Basic

```python
import sys

# Create an object (a list) and bind it to variable 'a'
a = [1, 2, 3]

# Check the reference count for the object 'a' points to
print(sys.getrefcount(a))
```

**Output:**
```
2
```

**Line-by-line breakdown:**
- `a = [1, 2, 3]` — Creates a list object in memory; variable `a` holds a reference to it. Reference count = 1.
- `sys.getrefcount(a)` — Calling this function passes `a` as an argument, which creates a **second temporary reference** inside the function. So the count reports **2**.

**Why this works:** `getrefcount` always reports one more than you expect for a single variable, because the function call itself holds a temporary reference. This is expected behavior — not a bug.

---

### Example 2: Reference Counting — Multiple References

```python
import sys

a = [1, 2, 3]       # ref count for the list object = 1
b = a               # b also points to the SAME object → ref count = 2

print(sys.getrefcount(a))   # prints 3 (a + b + getrefcount call)
print(sys.getrefcount(b))   # prints 3 (same object, same count)

del b               # remove b's reference → ref count drops to 2

print(sys.getrefcount(a))   # prints 2 (a + getrefcount call)
```

**Output:**
```
3
3
2
```

**Line-by-line breakdown:**
- `a = [1, 2, 3]` — List object created; `a` references it (count = 1).
- `b = a` — `b` points to the **same list object**, not a copy (count = 2).
- First two `getrefcount` calls — Both `a` and `b` point to the same object, so both return 3 (2 variables + 1 for the call).
- `del b` — Removes `b`'s reference; count drops to 2.
- Final `getrefcount` — Only `a` + the function call remain = 2.

**Why this works:** Python does not copy objects on assignment. Both `a` and `b` point to the same memory address. `del` only removes the variable binding, not the object itself (unless count hits 0).

> **Pro tip:** Use `id(a) == id(b)` to confirm two variables reference the same object.

---

### Example 3: Circular References & Garbage Collection

```python
import gc

class MyObject:
    def __init__(self, name):
        self.name = name
        print(f"Object {self.name} created")

    def __del__(self):
        print(f"Object {self.name} deleted")

# --- Enable GC (it's enabled by default, but shown explicitly) ---
gc.enable()

# --- Create two objects with circular references ---
obj1 = MyObject("One")   # Object One created
obj2 = MyObject("Two")   # Object Two created

# Make them reference each other — CIRCULAR REFERENCE
obj1.reference = obj2
obj2.reference = obj1

# --- Delete the variables ---
del obj1   # ref count does NOT drop to 0 because obj2.reference still points to it
del obj2   # ref count does NOT drop to 0 because obj1.reference still points to it

# At this point, __del__ has NOT been called yet — objects are leaked!
print("After del — objects may still be in memory due to circular ref")

# --- Manually trigger garbage collection to break the cycle ---
collected = gc.collect()
print(f"GC collected {collected} objects")
# Now __del__ is called for both objects
```

**Output:**
```
Object One created
Object Two created
After del — objects may still be in memory due to circular ref
Object One deleted
Object Two deleted
GC collected 2 objects
```

**Line-by-line breakdown:**
- `__init__` — Constructor; prints confirmation when object is created.
- `__del__` — Destructor (Python's equivalent); called when the object is truly deallocated.
- `obj1.reference = obj2` / `obj2.reference = obj1` — Creates a reference cycle. Neither object's ref count can reach 0 by normal reference counting.
- `del obj1` / `del obj2` — Removes the variable bindings, but the objects still reference each other internally. Python's simple reference counter cannot free them.
- `gc.collect()` — Forces the cyclic garbage collector to detect and break the cycle, calling `__del__` on both objects and freeing memory.

**Why this works:** Python's default reference counting cannot handle cycles. The `gc` module's **cyclic garbage collector** specifically scans for reference cycles and reclaims them.

---

### Example 4: Using the `gc` Module — Statistics & Inspection

```python
import gc

# Enable / Disable
gc.enable()
gc.disable()
gc.enable()   # re-enable for normal use

# Manually collect and get count of unreachable objects reclaimed
count = gc.collect()
print(f"Objects reclaimed: {count}")

# Get per-generation statistics
stats = gc.get_stats()
for gen, stat in enumerate(stats):
    print(f"Generation {gen}: {stat}")

# Inspect uncollectable garbage (objects GC couldn't free)
print(f"Uncollectable garbage: {gc.garbage}")
```

**Output (example):**
```
Objects reclaimed: 4
Generation 0: {'collections': 182, 'collected': 1611, 'uncollectable': 0}
Generation 1: {'collections': 16, 'collected': 98, 'uncollectable': 0}
Generation 2: {'collections': 2, 'collected': 10, 'uncollectable': 0}
Uncollectable garbage: []
```

**Key points:**
- Python's GC uses a **3-generation model** (0, 1, 2). New objects start in generation 0. Objects that survive a collection are promoted to the next generation.
- `gc.garbage` lists objects with `__del__` methods that are part of a cycle and couldn't be safely collected before Python 3.4. In modern Python (3.4+), this is usually empty.

---

### Example 5: Generators for Memory Efficiency

```python
# Memory-INEFFICIENT approach: builds entire list in memory
def get_numbers_list(n):
    return [i for i in range(n)]   # All n items stored in RAM at once

# Memory-EFFICIENT approach: generator yields one item at a time
def generate_numbers(n):
    for i in range(n):
        yield i   # Pauses here, returns i, resumes on next iteration

# Using the generator
for num in generate_numbers(1_000_000):
    if num > 10:
        break
    print(num)
```

**Output:**
```
0
1
2
3
4
5
6
7
8
9
10
```

**Line-by-line breakdown:**
- `yield i` — Suspends the function state and returns `i` to the caller. Only ONE value is in memory at a time.
- `if num > 10: break` — Safely exits the generator; the remaining 999,989 numbers are **never computed or stored**.
- With the list approach (`get_numbers_list(1_000_000)`), all 1 million integers are allocated in RAM immediately.

**Why this works:** A generator object stores only the function frame (local state), not the entire sequence. This is $O(1)$ memory vs $O(n)$ for a list.

---

### Example 6: Memory Profiling with `tracemalloc`

```python
import tracemalloc

def create_large_list():
    return [i * 2 for i in range(10_000)]   # Allocates a list of 10,000 integers

def main():
    tracemalloc.start()           # Begin tracing memory allocations

    result = create_large_list()  # The function we want to profile

    snapshot = tracemalloc.take_snapshot()         # Capture memory state
    top_stats = snapshot.statistics('lineno')       # Group stats by source line

    print("Top 10 memory allocations:")
    for stat in top_stats[:10]:
        print(stat)

main()
```

**Output (example):**
```
Top 10 memory allocations:
<ipython-input>:5: size=390 KiB, count=10001, average=40 B
...
```

**Line-by-line breakdown:**
- `tracemalloc.start()` — Activates memory tracing; records every allocation from this point.
- `tracemalloc.take_snapshot()` — Freezes the current allocation state for analysis.
- `snapshot.statistics('lineno')` — Aggregates memory usage grouped by source file and line number.
- `top_stats[:10]` — Shows the top 10 largest memory consumers — invaluable for finding memory leaks.

---

## Comparison Table

| Technique | What It Does | Memory Cost | Use Case |
|-----------|-------------|-------------|----------|
| **Reference Counting** | Tracks references per object | Minimal overhead | All Python objects, always on |
| **Cyclic GC** | Detects and breaks reference cycles | Periodic scan cost | Classes with mutual references |
| **`del` statement** | Explicitly removes a reference | None | Large objects no longer needed |
| **Generators** | Yields items one at a time | $O(1)$ | Large datasets, infinite sequences |
| **List Comprehension** | Builds full list in memory | $O(n)$ | Small-to-medium collections |
| **`tracemalloc`** | Profiles memory allocation by line | Development only | Finding memory leaks in production code |

---

## How It Works Internally

### Reference Counting Engine

Every Python object has a hidden `ob_refcnt` field in its C struct. Python increments it on every new binding and decrements on every unbinding.

```
a = [1, 2, 3]
┌─────────────────────────────┐
│  List Object @ 0x7f3a...    │
│  ob_refcnt = 1              │  ← 'a' points here
│  ob_type   = list           │
│  items     = [1, 2, 3]      │
└─────────────────────────────┘

b = a
┌─────────────────────────────┐
│  List Object @ 0x7f3a...    │
│  ob_refcnt = 2              │  ← both 'a' and 'b' point here
└─────────────────────────────┘

del b
┌─────────────────────────────┐
│  List Object @ 0x7f3a...    │
│  ob_refcnt = 1              │  ← only 'a' remains
└─────────────────────────────┘

del a → ob_refcnt = 0 → DEALLOCATED IMMEDIATELY
```

### Cyclic Garbage Collector (3-Generation Model)

```
New objects → Generation 0
                │ (survive collection)
                ▼
           Generation 1
                │ (survive again)
                ▼
           Generation 2  ← Long-lived objects (modules, globals)
```

- Gen 0 is collected **most frequently** (every ~700 allocations).
- The GC traverses all tracked objects, looks for cycles, and breaks them.
- Objects that define `__del__` and are part of a cycle go into `gc.garbage` (Python < 3.4). In Python 3.4+, this was fixed and `__del__` is called safely.

---

## Common Mistakes & Debugging

### Mistake 1: Expecting immediate deallocation with circular references

**Wrong:**
```python
class Node:
    pass

a = Node()
b = Node()
a.next = b
b.prev = a   # circular reference

del a
del b
# Expecting __del__ to fire here — it WON'T without gc.collect()
```

**Why it fails:** Reference counting alone cannot detect cycles. Both objects' ref counts stay at 1 (each points to the other), so they are never automatically freed by the counter.

**Correct:**
```python
import gc

del a
del b
gc.collect()   # Now the cycle is detected and both objects are freed
```

---

### Mistake 2: Accessing a deleted variable

**Wrong:**
```python
a = [1, 2, 3]
b = a
del b
print(sys.getrefcount(b))   # NameError: name 'b' is not defined
```

**Error:**
```
NameError: name 'b' is not defined
```

**Why it fails:** `del b` removes the variable binding entirely. Attempting to use `b` afterward causes a `NameError`.

**Correct:**
```python
del b
print(sys.getrefcount(a))   # Use 'a' instead; the underlying object still exists
```

---

### Mistake 3: Using a list when a generator is sufficient

**Wrong:**
```python
def process_large_data():
    data = [row for row in range(10_000_000)]   # 10M items loaded into RAM
    for item in data:
        process(item)
```

**Why it fails:** This can cause `MemoryError` on systems with limited RAM, and is always slower to start.

**Correct:**
```python
def process_large_data():
    for item in (row for row in range(10_000_000)):   # Generator expression
        process(item)
```

---

### Mistake 4: Forgetting to call `tracemalloc.start()` before profiling

**Wrong:**
```python
snapshot = tracemalloc.take_snapshot()   # RuntimeError: the tracemalloc module must be tracing memory allocations
```

**Error:**
```
RuntimeError: the tracemalloc module must be tracing memory allocations to take a snapshot
```

**Correct:**
```python
tracemalloc.start()
snapshot = tracemalloc.take_snapshot()
```

---

## Best Practices & Pythonic Way

1. **Prefer local variables over global variables.** Local variables are on the stack frame and are freed as soon as the function returns.

   ```python
   # Good: local scope
   def compute():
       result = [i**2 for i in range(1000)]
       return sum(result)   # 'result' freed after function exits

   # Bad: global scope keeps object alive for program lifetime
   RESULT = [i**2 for i in range(1000)]
   ```

2. **Avoid circular references.** Design data structures to be hierarchical. If cycles are unavoidable, use `weakref`.

   ```python
   import weakref

   class Parent:
       def __init__(self):
           self.child = None

   class Child:
       def __init__(self, parent):
           self.parent = weakref.ref(parent)   # Weak reference — doesn't increment refcount
   ```

3. **Explicitly delete large objects when done.**

   ```python
   big_data = load_dataset()   # might be gigabytes
   process(big_data)
   del big_data                # Free it immediately; don't wait for scope exit
   ```

4. **Use generators for large or streaming data.**

   ```python
   # Instead of:
   lines = open('huge_file.txt').readlines()   # All lines in RAM

   # Use:
   for line in open('huge_file.txt'):   # One line at a time
       process(line)
   ```

5. **Profile before optimizing.** Use `tracemalloc` to find the actual bottleneck before rewriting code.

6. **Avoid disabling the GC (`gc.disable()`)** unless you have a very specific, measured performance reason. Always re-enable it afterward.

---

## When to Use / When NOT to Use

| Use When... | Avoid When... |
|------------|--------------|
| Processing large files or datasets → use generators | Your data is small and fits comfortably in RAM → lists are fine |
| Building complex graph-like structures → use `weakref` for back-references | Simple hierarchical parent-child structures → no need for weakref |
| Profiling a production app for leaks → use `tracemalloc` | Over-engineering small scripts — not worth the complexity |
| You have confirmed objects are not being freed → `gc.collect()` | Calling `gc.collect()` blindly in a loop — it has CPU cost |
| Large objects not needed after a block → `del` explicitly | Deleting variables that are still referenced elsewhere — no effect |

---

## Related Topics & Connections

- **Generators & `yield`** — Directly tied to memory-efficient iteration (covered in Advanced Python)
- **`weakref` module** — Alternative to breaking circular references manually
- **Context Managers (`with` statement)** — Ensures resources (file handles, DB connections) are freed deterministically
- **Object-Oriented Python (`__del__`, `__init__`)** — Destructor lifecycle ties directly to memory management
- **`sys` module** — Provides `getrefcount`, `getsizeof` for introspection
- **`memory_profiler` (third-party)** — Line-by-line memory profiling; complements `tracemalloc`

**Prerequisite knowledge:**
- Python classes and objects
- Functions and scope (local vs global)
- Generators and `yield`

**What to learn next:**
- `weakref` module for weak references
- `__slots__` to reduce per-object memory overhead
- `memory_profiler` for line-level profiling
- Python's `pymalloc` allocator internals

---

## Interview & Exam Corner

### Frequently Asked Questions

1. **Q: How does Python manage memory?**
   A: Python uses **reference counting** as the primary mechanism. Each object tracks how many references point to it. When the count drops to zero, the object is immediately deallocated. For reference cycles (which reference counting cannot handle), Python has a **cyclic garbage collector** (`gc` module) that runs periodically and uses a 3-generation model.

2. **Q: What is a reference cycle and why is it a problem?**
   A: A reference cycle occurs when two or more objects reference each other (e.g., `a.ref = b; b.ref = a`). Their reference counts never reach zero, so they're never automatically freed by reference counting, causing a **memory leak** until the GC runs.

3. **Q: What is the difference between `del` and garbage collection?**
   A: `del` removes a variable binding and decrements the reference count by 1. If the count reaches 0, the object is immediately freed (reference counting). Garbage collection is a separate, periodic process that handles **cycles** — objects whose ref counts can't reach zero due to mutual references.

4. **Q: What does `sys.getrefcount()` always return at minimum?**
   A: At least **2** — one for the variable holding the object, and one for the temporary reference created by passing the object to `getrefcount` itself.

5. **Q: What is the `gc.garbage` list?**
   A: A list of objects that the garbage collector found to be unreachable but **could not free** (in older Python, objects with `__del__` in a cycle). In Python 3.4+, `gc.garbage` is typically empty because `__del__` can now be called safely on cyclic objects.

6. **Q: What are the 3 generations in Python's GC?**
   A: Generation 0 (new objects, collected most often), Generation 1 (survived one collection), Generation 2 (long-lived objects, collected least often). This is the **generational hypothesis** — most objects die young.

---

### Tricky Output Questions

```python
import sys
a = []
b = a
c = b
print(sys.getrefcount(a))
```
**Answer:** `4`  
**Why:** `a`, `b`, and `c` all point to the same list (3 references), plus 1 for the `getrefcount` call = **4**.

---

```python
import sys
a = [1, 2, 3]
b = a
del a
print(sys.getrefcount(b))
```
**Answer:** `2`  
**Why:** After `del a`, only `b` holds a reference (count = 1) plus `getrefcount` itself = **2**. The list object was NOT deleted because `b` still references it.

---

```python
class Foo:
    def __del__(self):
        print("deleted")

a = Foo()
b = a
del a
del b
```
**Answer:**
```
deleted
```
**Why:** `__del__` only fires when the reference count hits 0. After `del a`, the count is 1 (b still holds it). After `del b`, count reaches 0 and `__del__` is called once.

---

### One-Liner Challenges

- **Challenge:** Get reference count of a variable `x`
  - Solution: `import sys; sys.getrefcount(x)`

- **Challenge:** Force garbage collection and print number of objects freed
  - Solution: `import gc; print(gc.collect())`

- **Challenge:** Create a generator for even numbers up to `n`
  - Solution: `evens = (i for i in range(n) if i % 2 == 0)`

- **Challenge:** Delete a variable and verify it's gone
  - Solution: `del x; print('x' in dir())  # False`

---

## Quick-Reference Cheat Sheet

```
PYTHON MEMORY MANAGEMENT CHEAT SHEET
======================================
sys.getrefcount(obj)        → Returns reference count (+1 for the call itself)
id(obj)                     → Memory address of the object
del obj                     → Removes variable binding; decrements ref count

gc.enable()                 → Enable cyclic GC (default: ON)
gc.disable()                → Disable cyclic GC (use with caution)
gc.collect()                → Manually run GC; returns # objects freed
gc.get_stats()              → List of per-generation dicts {collections, collected, uncollectable}
gc.garbage                  → List of uncollectable objects (usually empty in Python 3.4+)

tracemalloc.start()         → Begin memory tracing
tracemalloc.take_snapshot() → Capture current allocation state
snapshot.statistics('lineno')→ Top allocations by source line

GENERATORS                  → Use 'yield'; O(1) memory; best for large data
WEAKREF                     → weakref.ref(obj); doesn't increment ref count; prevents cycles
__del__                     → Destructor; called when object is about to be freed

GOTCHA: del doesn't free memory if other references exist
GOTCHA: getrefcount always returns count + 1
GOTCHA: circular refs need gc.collect() to be freed
GOTCHA: gc.disable() does NOT disable reference counting
```

---

## Summary (TL;DR)

- Python memory management is **automatic** — combining **reference counting** (primary) and a **cyclic garbage collector** (for cycles).
- Every object has a hidden **reference count**; when it hits 0, the object is immediately deallocated.
- **Circular references** (A → B → A) prevent ref counts from reaching 0; use `gc.collect()` or `weakref` to resolve.
- Use **generators** (`yield`) instead of lists for large datasets — they consume $O(1)$ memory vs $O(n)$.
- Use **`del`** to explicitly free large objects no longer needed; prefer **local variables** over globals.
- Use **`tracemalloc`** to profile and diagnose memory usage in your application.
- Python 3.4+ can safely call `__del__` even on objects in cycles — `gc.garbage` is typically empty in modern Python.

---

## Flashcard-Style Recall (Q&A pairs)

- **Q: What is reference counting?**  
  A: A mechanism where each Python object tracks how many variables/references point to it. When the count drops to 0, the object is immediately deallocated.

- **Q: Why does `sys.getrefcount()` return 2 for a freshly created variable?**  
  A: One reference from the variable itself, one from the `getrefcount()` function call (temporary argument reference).

- **Q: What is a circular reference?**  
  A: When two or more objects reference each other (e.g., `a.ref = b; b.ref = a`), creating a loop that prevents reference counts from reaching zero.

- **Q: How do you resolve a circular reference in Python?**  
  A: Call `gc.collect()` to manually trigger the cyclic garbage collector, or redesign using `weakref.ref()`.

- **Q: What does `gc.collect()` return?**  
  A: The number of unreachable objects it freed (reclaimed from memory).

- **Q: What is the memory benefit of a generator over a list?**  
  A: A generator holds only one item in memory at a time ($O(1)$), while a list holds all items ($O(n)$).

- **Q: What does `del x` do?**  
  A: Removes the variable binding `x` and decrements the reference count of the object it pointed to. Does NOT necessarily free the object if other references exist.

- **Q: What is `gc.garbage`?**  
  A: A list of objects the GC detected as unreachable but could not collect. In Python 3.4+, this is almost always empty.

- **Q: What are Python's 3 GC generations?**  
  A: Generation 0 (new objects), Generation 1 (survived 1 collection), Generation 2 (long-lived). Gen 0 is scanned most frequently.

- **Q: What tool do you use to find memory leaks by line number?**  
  A: `tracemalloc` — use `tracemalloc.start()`, `take_snapshot()`, and `snapshot.statistics('lineno')`.

- **Q: What is the difference between `gc.disable()` and not using `del`?**  
  A: `gc.disable()` turns off the **cyclic garbage collector** only (reference counting still works). Not using `del` simply means you're relying on Python to clean up when scope exits.

- **Q: What is `__del__` in Python?**  
  A: A destructor method called just before an object is garbage collected. Equivalent to destructors in C++/Java. Not guaranteed to run at a specific time.
