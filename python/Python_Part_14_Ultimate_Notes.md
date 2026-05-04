# Multithreading & Multiprocessing in Python

---

## Concept Overview

- **Multithreading** is the ability of a process to manage multiple threads of execution concurrently — ideal for tasks that spend time waiting (I/O-bound tasks).
- **Multiprocessing** creates separate OS-level processes that run in true parallel on multiple CPU cores — ideal for computationally heavy tasks (CPU-bound tasks).
- These two techniques solve the problem of sequential, slow execution by allowing Python programs to perform multiple operations at the same time, dramatically improving throughput and efficiency.

**Real-world analogy:**
> Think of a restaurant kitchen. A **process** is an entire kitchen with its own equipment and staff. **Threads** are individual chefs inside that kitchen sharing the same equipment (memory) but working on different dishes simultaneously. **Multiprocessing** is opening multiple separate kitchens — completely independent, no sharing.

**Where it fits in Python's bigger picture:**
- Sits at the intersection of OS-level concurrency and Python application performance.
- Directly related to the **GIL (Global Interpreter Lock)**, **async/await**, and **concurrent programming** paradigms.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **Program** | A static sequence of instructions written in a programming language (e.g., an `.exe` file on disk) |
| **Process** | A running instance of a program with its own isolated memory space |
| **Thread** | A unit of execution *within* a process; threads share the process's memory |
| **Multithreading** | Running multiple threads concurrently within a single process |
| **Multiprocessing** | Running multiple processes in parallel, each with separate memory |
| **I/O-bound task** | A task that spends most of its time waiting for I/O (network, disk, file reads) |
| **CPU-bound task** | A task that is computationally intensive and heavily uses the CPU |
| **GIL** | Global Interpreter Lock — CPython's mechanism that allows only one thread to execute Python bytecode at a time |
| **Thread Pool** | A managed pool of reusable threads for executing multiple tasks |
| **Process Pool** | A managed pool of worker processes for parallel execution |
| **`join()`** | Waits for a thread/process to finish before continuing |
| **`start()`** | Begins execution of a thread or process |
| **Context Manager (`with`)** | Used with `ThreadPoolExecutor`/`ProcessPoolExecutor` to manage lifecycle automatically |
| **`map()`** | Distributes a function call across an iterable using the pool's workers |

---

## Syntax & Definition

### Program
```python
# A program is just a file on disk — a sequence of instructions
# Example: google_chrome.exe, my_script.py
```

### Process
```python
import multiprocessing

# Create a process targeting a function
process = multiprocessing.Process(target=my_function, args=(arg1,))
process.start()   # Launch the process
process.join()    # Wait for it to finish
```

### Thread
```python
import threading

# Create a thread targeting a function
thread = threading.Thread(target=my_function, args=(arg1,))
thread.start()   # Launch the thread
thread.join()    # Wait for it to finish
```

### ThreadPoolExecutor
```python
from concurrent.futures import ThreadPoolExecutor

with ThreadPoolExecutor(max_workers=3) as executor:
    results = executor.map(my_function, my_list)
```

### ProcessPoolExecutor
```python
from concurrent.futures import ProcessPoolExecutor

if __name__ == "__main__":  # Required guard for multiprocessing
    with ProcessPoolExecutor(max_workers=3) as executor:
        results = executor.map(my_function, my_list)
```

---

## Detailed Explanation with Examples

### Example 1: Sequential Execution (Baseline — No Concurrency)

```python
import time

def print_numbers():
    for i in range(5):
        time.sleep(2)          # Simulate I/O wait (e.g., file read)
        print(f"Number: {i}")

def print_letters():
    for letter in "ABCDE":
        time.sleep(2)          # Simulate I/O wait
        print(f"Letter: {letter}")

start = time.time()
print_numbers()                # Runs completely first
print_letters()                # Then this runs
finish = time.time() - start

print(f"Total time: {finish:.2f}s")
```

**Output:**
```
Number: 0
Number: 1
Number: 2
Number: 3
Number: 4
Letter: A
Letter: B
Letter: C
Letter: D
Letter: E
Total time: 20.00s
```

**Line-by-line breakdown:**
- `time.sleep(2)` — Simulates an I/O operation (file read, network call, database query) that takes 2 seconds.
- `print_numbers()` — Called first; blocks the program for 10 seconds (5 iterations × 2 seconds each).
- `print_letters()` — Only starts after `print_numbers()` fully completes — another 10 seconds.
- Total: **20 seconds** — purely sequential, completely wasted waiting time.

**Why this is bad:** The CPU sits idle for 20 seconds just because we are waiting for I/O. This is the exact problem multithreading solves.

---

### Example 2: Multithreading — Concurrent I/O Execution

```python
import threading
import time

def print_numbers():
    for i in range(5):
        time.sleep(2)
        print(f"Number: {i}")

def print_letters():
    for letter in "ABCDE":
        time.sleep(2)
        print(f"Letter: {letter}")

start = time.time()

# Create two threads
t1 = threading.Thread(target=print_numbers)
t2 = threading.Thread(target=print_letters)

# Start both threads
t1.start()
t2.start()

# Wait for both to complete before proceeding
t1.join()
t2.join()

finish = time.time() - start
print(f"Total time: {finish:.2f}s")
```

**Output:**
```
Letter: A
Number: 0
Number: 1
Letter: B
Number: 2
Letter: C
Letter: D
Number: 3
Number: 4
Letter: E
Total time: ~10.00s
```

**Line-by-line breakdown:**
- `threading.Thread(target=print_numbers)` — Creates a Thread object. The `target` is the function this thread will execute. The thread does **not** start yet.
- `t1.start()` — Launches Thread 1. `print_numbers` begins executing in a separate thread.
- `t2.start()` — Launches Thread 2 immediately after. `print_letters` now runs *concurrently* alongside Thread 1.
- `t1.join()` — Main thread waits here until `t1` finishes. Without this, the program could exit before threads complete.
- `t2.join()` — Same — wait for `t2` to finish.
- Total: **~10 seconds** — half the time because while one thread sleeps, the other runs.

**Why this works:** When `t1` calls `time.sleep(2)`, it releases control. The OS scheduler gives CPU time to `t2`, which does useful work. Both threads share the same process memory but have their own stack.

---

### Example 3: Multiprocessing — Parallel CPU Execution

```python
import multiprocessing
import time

def square_numbers():
    for i in range(5):
        time.sleep(1)
        print(f"Square: {i * i}")

def cube_numbers():
    for i in range(5):
        time.sleep(1.5)
        print(f"Cube: {i * i * i}")

if __name__ == "__main__":    # CRITICAL: always required for multiprocessing
    start = time.time()

    # Create two separate processes
    p1 = multiprocessing.Process(target=square_numbers)
    p2 = multiprocessing.Process(target=cube_numbers)

    p1.start()
    p2.start()

    p1.join()
    p2.join()

    finish = time.time() - start
    print(f"Total time: {finish:.2f}s")
```

**Output:**
```
Square: 0
Cube: 0
Square: 1
Square: 4
Cube: 1
Square: 9
Square: 16
Cube: 8
Cube: 27
Cube: 64
Total time: ~7.60s
```

**Line-by-line breakdown:**
- `if __name__ == "__main__":` — **Mandatory guard.** On Windows/macOS, Python spawns new processes by importing the module. Without this guard, each spawned process would try to create more processes → infinite loop / crash.
- `multiprocessing.Process(target=square_numbers)` — Creates an entirely new OS process with its own Python interpreter and memory.
- `p1.start()` — OS spawns a new process running `square_numbers`.
- `p2.start()` — OS spawns another process running `cube_numbers`. Both run on separate CPU cores simultaneously.
- `p1.join()`, `p2.join()` — Main process waits for both child processes to finish.

**Why this is different from threading:** Each process has completely separate memory. There is no GIL limitation — both processes run Python code in true parallel.

---

### Example 4: ThreadPoolExecutor — Advanced Thread Management

```python
from concurrent.futures import ThreadPoolExecutor
import time

def process_number(number):
    time.sleep(1)              # Simulate I/O wait
    return f"Number: {number}"

numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9]

start = time.time()

# max_workers=3 means at most 3 threads run concurrently
with ThreadPoolExecutor(max_workers=3) as executor:
    results = executor.map(process_number, numbers)

for result in results:
    print(result)

finish = time.time() - start
print(f"Total time: {finish:.2f}s")
```

**Output:**
```
Number: 1
Number: 2
Number: 3
Number: 4
Number: 5
Number: 6
Number: 7
Number: 8
Number: 9
Total time: ~3.00s
```

**Line-by-line breakdown:**
- `ThreadPoolExecutor(max_workers=3)` — Creates a pool of exactly 3 threads. No need to manually create/start/join individual threads.
- `with ... as executor:` — Context manager ensures all threads are properly cleaned up when the block exits, even on errors.
- `executor.map(process_number, numbers)` — Distributes each element of `numbers` to `process_number` across the 3 threads. Like Python's built-in `map()` but concurrent.
- `results` — A lazy iterator of return values in the **same order** as `numbers`, regardless of completion order.

**Why this is better than manual threading:**
- No manual `Thread()`, `start()`, `join()` for each item.
- Thread reuse — threads are recycled instead of created/destroyed for each task.
- Automatic error propagation — exceptions inside tasks are re-raised when you iterate `results`.

---

### Example 5: ProcessPoolExecutor — Advanced Process Management

```python
from concurrent.futures import ProcessPoolExecutor
import time

def compute_square(n):
    time.sleep(2)              # Simulate heavy computation
    return n * n

numbers = [1, 2, 3, 4, 5, 11, 12, 14]

if __name__ == "__main__":    # REQUIRED for multiprocessing
    start = time.time()

    with ProcessPoolExecutor(max_workers=3) as executor:
        results = executor.map(compute_square, numbers)

    for result in results:
        print(result)

    finish = time.time() - start
    print(f"Total time: {finish:.2f}s")
```

**Output:**
```
1
4
9
16
25
121
144
196
Total time: ~6.00s  # Much faster than sequential 16s
```

**Line-by-line breakdown:**
- `ProcessPoolExecutor(max_workers=3)` — Pool of 3 worker processes. Each uses a separate CPU core.
- `executor.map(compute_square, numbers)` — Distributes elements of `numbers` across the 3 processes in chunks.
- Each call to `compute_square` runs in an **isolated process** — safe for CPU-heavy tasks that bypass the GIL.

---

### Example 6: Real-World — Web Scraping with Multithreading

```python
import threading
import requests
from bs4 import BeautifulSoup
import time

# Multiple URLs to scrape concurrently
urls = [
    "https://www.tutorialspoint.com/python/index.htm",
    "https://www.w3schools.com/python/",
    "https://docs.python.org/3/tutorial/index.html"
]

def fetch_content(url):
    """Fetch a URL and print character count of its text content."""
    response = requests.get(url)
    soup = BeautifulSoup(response.content, "html.parser")
    print(f"Fetched {len(soup.text)} characters from {url}")

start = time.time()
threads = []

# Create one thread per URL
for url in urls:
    thread = threading.Thread(target=fetch_content, args=(url,))
    threads.append(thread)
    thread.start()             # Start immediately — don't wait for previous

# Wait for all threads to complete
for thread in threads:
    thread.join()

print("All web pages fetched!")
print(f"Total time: {time.time() - start:.2f}s")
```

**Output:**
```
Fetched 7477 characters from https://www.tutorialspoint.com/...
Fetched 8986 characters from https://www.w3schools.com/...
Fetched 66004 characters from https://docs.python.org/...
All web pages fetched!
Total time: ~2.50s
```

**Why multithreading excels here:**
- Network requests are pure I/O — the CPU does nothing while waiting for the server's response.
- With 3 threads, all 3 HTTP requests fly out simultaneously. Total time ≈ slowest single request (not the sum).
- Without threads: ~7–10 seconds (sequential). With threads: ~2–3 seconds. Near 3× speedup.

---

### Example 7: Real-World — Factorial Calculation with Multiprocessing

```python
import multiprocessing
import math
import sys
import time

# Allow Python to handle very large integers (needed for huge factorials)
sys.set_int_max_str_digits(100000)

def compute_factorial(number):
    """Compute factorial of a large number using math.factorial."""
    print(f"Computing factorial of {number}...")
    result = math.factorial(number)
    print(f"Factorial of {number} computed.")
    return result

if __name__ == "__main__":
    numbers = [6000, 7000, 8000]

    start_time = time.time()

    # Pool automatically uses all available CPU cores
    with multiprocessing.Pool() as pool:
        results = pool.map(compute_factorial, numbers)

    end_time = time.time()

    print(f"Time taken: {end_time - start_time:.4f}s")
```

**Output:**
```
Computing factorial of 6000...
Computing factorial of 7000...
Computing factorial of 8000...
Factorial of 6000 computed.
Factorial of 7000 computed.
Factorial of 8000 computed.
Time taken: 4.4375s
```

**Line-by-line breakdown:**
- `sys.set_int_max_str_digits(100000)` — Python 3.11+ limits integer-to-string conversion size as a security measure. We raise the limit since factorials of large numbers produce enormous integers.
- `math.factorial(number)` — Highly optimized C-level factorial computation.
- `multiprocessing.Pool()` — Automatically creates a pool equal to the number of CPU cores on the machine (e.g., 8 on an 8-core CPU, 32 on a workstation).
- `pool.map(compute_factorial, numbers)` — Distributes `[6000, 7000, 8000]` across available cores. Each number runs in a separate process on a separate core.

**Why NOT threads here:** Factorial computation is pure CPU work. The GIL prevents true parallel thread execution in CPython. Multiprocessing bypasses the GIL entirely by using separate processes.

---

## Process Memory Architecture

Every process gets these dedicated resources from the OS:

```
PROCESS MEMORY LAYOUT
======================
┌──────────────────────────┐
│      Code Segment        │  ← The program's compiled instructions
├──────────────────────────┤
│      Data Segment        │  ← Global and static variables
├──────────────────────────┤
│       Heap Memory        │  ← Dynamic allocations (objects, lists, dicts)
│    (grows upward ↑)      │
├──────────────────────────┤
│          Stack           │  ← Local variables, function call frames
│    (grows downward ↓)    │
├──────────────────────────┤
│       Registers          │  ← Small temporary CPU-level storage
└──────────────────────────┘
```

**What threads share vs. own:**

```
MULTI-THREADED PROCESS
========================
Process (shared memory):
  ├── Code Segment   ← Shared by all threads
  ├── Data Segment   ← Shared by all threads
  ├── Heap Memory    ← Shared by all threads
  │
  ├── Thread 1:
  │     ├── Stack    ← Thread's own
  │     └── Registers ← Thread's own
  │
  └── Thread 2:
        ├── Stack    ← Thread's own
        └── Registers ← Thread's own
```

> **Key insight:** Threads are lightweight because they share memory. Processes are heavier but isolated — one crashing process cannot corrupt another.

---

## How It Works Internally

### The GIL (Global Interpreter Lock) — Critical Understanding

CPython (the standard Python interpreter) has a mutex called the GIL that allows **only one thread to execute Python bytecode at a time.**

```
WITHOUT GIL (ideal world):
  Thread 1: [■■■■■■■■■■]
  Thread 2:   [■■■■■■■■■■]
  Time:     ─────────────────>
              Truly parallel

WITH GIL (CPython reality for CPU-bound):
  Thread 1: [■■■][   ][■■■][   ]
  Thread 2: [   ][■■■][   ][■■■]
  Time:     ─────────────────>
              Interleaved, NOT parallel
```

**But for I/O-bound tasks**, the GIL is released while waiting for I/O:
```
Thread 1 (I/O wait): [■][WAITING...][■][WAITING...]
Thread 2:            [   ■■■■■■■■■■■   ■■■■■■■■■■]
                     ← Thread 2 runs DURING Thread 1's I/O wait
```

This is why:
- **Multithreading works** for I/O-bound tasks despite the GIL.
- **Multiprocessing is needed** for CPU-bound tasks — each process has its own GIL.

### Process Isolation
```
Process 1 (Chrome Tab 1)    Process 2 (Chrome Tab 2)
┌────────────────────┐      ┌────────────────────┐
│   Memory Space A   │      │   Memory Space B   │
│   (isolated)       │      │   (isolated)       │
└────────────────────┘      └────────────────────┘
         ↑                           ↑
    Crashes here               Still running!
```
Because processes have separate memory, one crashing process cannot affect another — this is why one frozen browser tab doesn't kill all tabs.

---

## Comparison Table

### Multithreading vs Multiprocessing

| Feature | Multithreading | Multiprocessing |
|---------|---------------|-----------------|
| **Best for** | I/O-bound tasks | CPU-bound tasks |
| **Memory** | Shared within process | Separate per process |
| **GIL impact** | Limited by GIL for CPU work | Bypasses GIL completely |
| **Overhead** | Low (threads are cheap) | High (process spawn is expensive) |
| **Communication** | Easy (shared memory) | Complex (needs IPC: pipes, queues) |
| **Crash isolation** | One bad thread can crash process | Processes are fully isolated |
| **Python module** | `threading`, `concurrent.futures` | `multiprocessing`, `concurrent.futures` |
| **`if __name__ == "__main__"`** | Not required | **Required** on Windows/macOS |
| **Real-world use** | Web scraping, file I/O, API calls | Data science, ML training, image processing |

### `threading.Thread` vs `ThreadPoolExecutor`

| Feature | `threading.Thread` | `ThreadPoolExecutor` |
|---------|-------------------|---------------------|
| **Control** | Manual (create/start/join each) | Automatic pool management |
| **Thread reuse** | No — create new each time | Yes — threads are recycled |
| **Return values** | Need manual synchronization | Returns `Future` objects directly |
| **Error handling** | Manual try/except per thread | Exceptions re-raised on result access |
| **Best for** | Few, long-running tasks | Many short tasks (task pool pattern) |
| **Code complexity** | More boilerplate | Clean and concise |

### `multiprocessing.Process` vs `multiprocessing.Pool`

| Feature | `multiprocessing.Process` | `multiprocessing.Pool` |
|---------|--------------------------|----------------------|
| **Control** | Manual spawn/join | Pool manages workers automatically |
| **Use case** | Few specific processes | Many tasks distributed across cores |
| **API** | `start()`, `join()` | `map()`, `apply()`, `starmap()` |
| **Memory** | Separate per process | Separate per worker |

---

## Common Mistakes & Debugging

### Mistake 1: Missing `if __name__ == "__main__"` guard

**Wrong:**
```python
import multiprocessing

def worker():
    print("Working...")

p = multiprocessing.Process(target=worker)
p.start()
p.join()
```
**Error:**
```
RuntimeError: An attempt has been made to start a new process before the
current process has finished its bootstrapping phase.
```
**Why it fails:** On Windows and macOS, Python uses `spawn` to create new processes. The child process imports the parent's module. Without the guard, the import triggers more `Process()` calls → recursive process spawning.

**Correct:**
```python
import multiprocessing

def worker():
    print("Working...")

if __name__ == "__main__":
    p = multiprocessing.Process(target=worker)
    p.start()
    p.join()
```

---

### Mistake 2: Using `time.time()` incorrectly

**Wrong:**
```python
import time
t = time.now()           # AttributeError
# OR
t = time.time.now()      # AttributeError
```
**Error:**
```
AttributeError: module 'time' has no attribute 'now'
```
**Why it fails:** `time.now()` does not exist. `datetime.now()` exists in the `datetime` module. The `time` module uses `time.time()`.

**Correct:**
```python
import time
start = time.time()
# ... code ...
elapsed = time.time() - start
print(f"Time taken: {elapsed:.2f}s")
```

---

### Mistake 3: Forgetting `.join()` — threads run but main exits

**Wrong:**
```python
import threading, time

def slow_task():
    time.sleep(3)
    print("Task done!")

t = threading.Thread(target=slow_task)
t.start()
# No join() — main thread exits immediately
print("Main done")
```
**Output:**
```
Main done
# "Task done!" may or may not print — race condition
```
**Why it fails:** The main thread exits, which may terminate the entire program before the child thread completes.

**Correct:**
```python
t = threading.Thread(target=slow_task)
t.start()
t.join()    # Wait for thread to complete
print("Main done")
```

---

### Mistake 4: Using multithreading for CPU-bound tasks expecting speedup

**Wrong assumption:**
```python
import threading

def heavy_computation():
    result = sum(i * i for i in range(10_000_000))  # CPU-bound
    return result

# This will NOT be faster than sequential — GIL prevents true parallelism
t1 = threading.Thread(target=heavy_computation)
t2 = threading.Thread(target=heavy_computation)
t1.start(); t2.start()
t1.join(); t2.join()
```
**Why it fails:** The GIL means only one thread runs Python bytecode at a time. Two CPU-bound threads actually run slower than sequential due to GIL contention overhead.

**Correct:** Use `multiprocessing` for CPU-bound tasks:
```python
import multiprocessing

def heavy_computation(_):
    return sum(i * i for i in range(10_000_000))

if __name__ == "__main__":
    with multiprocessing.Pool(processes=2) as pool:
        results = pool.map(heavy_computation, [1, 2])
```

---

### Mistake 5: Large integer conversion error for factorial

**Wrong:**
```python
import math
result = math.factorial(50000)
print(result)   # Crashes in Python 3.11+
```
**Error:**
```
ValueError: Exceeds the limit (4300) for integer string conversion
```
**Why it fails:** Python 3.11 introduced a security limit on converting huge integers to strings to prevent DoS attacks.

**Correct:**
```python
import sys, math
sys.set_int_max_str_digits(100000)   # Raise the limit
result = math.factorial(50000)
print(result)
```

---

## Best Practices & Pythonic Way

```python
# ✅ DO: Use ThreadPoolExecutor for managing multiple threads
from concurrent.futures import ThreadPoolExecutor

with ThreadPoolExecutor(max_workers=5) as executor:
    results = list(executor.map(my_func, my_list))

# ❌ DON'T: Manually create threads for bulk tasks
threads = [threading.Thread(target=my_func, args=(x,)) for x in my_list]
for t in threads: t.start()
for t in threads: t.join()
```

```python
# ✅ DO: Always use if __name__ == "__main__" guard with multiprocessing
if __name__ == "__main__":
    with ProcessPoolExecutor() as executor:
        results = executor.map(cpu_heavy_func, numbers)

# ❌ DON'T: Put multiprocessing code at module level
with ProcessPoolExecutor() as executor:      # Will crash on Windows/macOS
    results = executor.map(cpu_heavy_func, numbers)
```

```python
# ✅ DO: Choose the right tool based on task type
# I/O-bound (network, files) → threading / ThreadPoolExecutor
# CPU-bound (math, data processing) → multiprocessing / ProcessPoolExecutor

# ✅ DO: Use context managers (with) for executor pools
# They automatically call shutdown() on exit, even on exceptions

# ✅ DO: Set a reasonable max_workers
# For I/O-bound: can be 10–100 (threads are cheap, mostly waiting)
# For CPU-bound: typically = number of CPU cores (os.cpu_count())
import os
optimal_workers = os.cpu_count()
```

---

## When to Use / When NOT to Use

### Multithreading

| Use When... | Avoid When... |
|------------|--------------|
| Making multiple network requests concurrently | Performing heavy mathematical computation |
| Reading/writing multiple files simultaneously | Tasks that need true parallelism (GIL blocks this) |
| Handling multiple database queries | Sharing mutable state without synchronization |
| GUI apps — keep UI responsive during background tasks | Simple sequential scripts with no waiting |
| Web scraping multiple URLs | When processes need complete memory isolation |

### Multiprocessing

| Use When... | Avoid When... |
|------------|--------------|
| CPU-intensive computations (ML, image processing) | Tasks are I/O-bound (overhead outweighs benefit) |
| Need to utilize all CPU cores | Tasks are very short (process spawn overhead dominates) |
| Tasks that must be completely isolated | When you need shared mutable state (hard with processes) |
| Data science batch processing | Limited memory (each process duplicates memory) |
| Bypassing Python's GIL limitation | Spawning inside loops without pool management |

---

## Related Topics & Connections

- **GIL (Global Interpreter Lock)** — The core reason why `threading` doesn't help CPU-bound tasks in CPython
- **`asyncio`** — Python's async/await model for single-threaded concurrency (better for very high I/O concurrency like thousands of connections)
- **`concurrent.futures`** — High-level unified API for both threading and multiprocessing
- **`queue.Queue`** — Thread-safe queue for inter-thread communication
- **`multiprocessing.Queue` / `multiprocessing.Pipe`** — Inter-process communication
- **Race conditions & Locks** — `threading.Lock()`, `threading.RLock()` — critical for thread safety
- **`functools.partial`** — Useful when passing fixed arguments to pool functions
- **Generators & Iterators** — `executor.map()` returns a lazy iterator

**Prerequisites:** Functions, closures, Python's memory model, OS basics
**What to learn next:** `asyncio` and `aiohttp`, thread safety and Locks, `multiprocessing.Queue` for IPC, `concurrent.futures.as_completed()`

---

## Interview & Exam Corner

### Frequently Asked Questions

1. **Q: What is the difference between a process and a thread?**
   A: A process is an independent program instance with its own memory space. A thread is a unit of execution *within* a process that shares the process's memory. Threads are lighter but share memory; processes are heavier but fully isolated.

2. **Q: What is the GIL and why does it matter?**
   A: The Global Interpreter Lock is a mutex in CPython that ensures only one thread executes Python bytecode at a time. It makes single-threaded programs faster but limits true CPU parallelism with threads. For CPU-bound tasks, use multiprocessing to bypass it.

3. **Q: When would you use multithreading over multiprocessing?**
   A: Use multithreading for I/O-bound tasks (network requests, file I/O) because threads can overlap waiting time. Use multiprocessing for CPU-bound tasks (computations, data processing) because it bypasses the GIL and runs on multiple cores.

4. **Q: What is `ThreadPoolExecutor` and why is it preferred over raw threading?**
   A: `ThreadPoolExecutor` manages a pool of reusable threads automatically. It's preferred because it eliminates boilerplate (no manual `start()`/`join()`), reuses threads reducing overhead, and returns `Future` objects making error handling easier.

5. **Q: Why must you use `if __name__ == "__main__"` with multiprocessing?**
   A: On Windows and macOS, new processes are created using `spawn` (importing the parent module). Without this guard, the import itself triggers more process creation, causing infinite recursive spawning and a crash.

6. **Q: What resources does a thread own vs. share with its process?**
   A: A thread **owns**: its own stack and registers. A thread **shares** with the process: code segment, data segment, and heap memory.

7. **Q: Can you achieve true parallelism with Python threads?**
   A: For CPU-bound tasks, no — the GIL prevents true parallelism. For I/O-bound tasks, yes — the GIL is released during I/O waits, allowing effective concurrency. For true CPU parallelism, use `multiprocessing`.

---

### Tricky Output Questions

```python
import threading

counter = 0

def increment():
    global counter
    for _ in range(100000):
        counter += 1

t1 = threading.Thread(target=increment)
t2 = threading.Thread(target=increment)
t1.start(); t2.start()
t1.join(); t2.join()

print(counter)
```
**Answer:** Could be anything less than `200000` (e.g., `153847`)
**Why:** `counter += 1` is NOT atomic — it's a read-modify-write operation. Both threads can read the same value, both increment, and both write back the same value, losing one increment. This is a **race condition**.

**Fix:** Use `threading.Lock()`:
```python
lock = threading.Lock()
def increment():
    global counter
    for _ in range(100000):
        with lock:
            counter += 1
```

---

```python
from concurrent.futures import ThreadPoolExecutor

def square(n):
    return n * n

with ThreadPoolExecutor(max_workers=2) as executor:
    results = executor.map(square, [1, 2, 3, 4, 5])

print(list(results))
```
**Answer:** `[1, 4, 9, 16, 25]`
**Why:** `executor.map()` always returns results **in the same order as the input**, regardless of which thread finishes first.

---

### One-Liner Challenges

- **Challenge:** Run a function on a list of 10 numbers using 4 threads
  ```python
  list(ThreadPoolExecutor(max_workers=4).map(my_func, range(10)))
  ```

- **Challenge:** Run CPU-heavy function on a list using all CPU cores
  ```python
  with ProcessPoolExecutor() as ex: results = list(ex.map(cpu_func, numbers))
  ```

- **Challenge:** Get number of CPU cores available
  ```python
  import os; cores = os.cpu_count()
  ```

---

## Quick-Reference Cheat Sheet

```
MULTITHREADING & MULTIPROCESSING CHEAT SHEET
=============================================

MULTITHREADING (I/O-bound)
─────────────────────────
import threading
t = threading.Thread(target=fn, args=(arg,))
t.start()                          → launch thread
t.join()                           → wait for completion

from concurrent.futures import ThreadPoolExecutor
with ThreadPoolExecutor(max_workers=N) as ex:
    results = ex.map(fn, iterable) → distribute work across N threads

MULTIPROCESSING (CPU-bound)
───────────────────────────
import multiprocessing
p = multiprocessing.Process(target=fn, args=(arg,))
p.start(); p.join()

if __name__ == "__main__":         → ALWAYS REQUIRED
    with multiprocessing.Pool() as pool:
        results = pool.map(fn, list)

from concurrent.futures import ProcessPoolExecutor
with ProcessPoolExecutor(max_workers=N) as ex:
    results = ex.map(fn, iterable) → distribute work across N processes

KEY RULES
─────────
I/O-bound  → threading (GIL released during I/O wait)
CPU-bound  → multiprocessing (bypasses GIL entirely)
Use Pool / Executor → preferred over manual thread/process management
Always join() → prevent orphaned threads/processes
Always if __name__ == "__main__" → required for multiprocessing on Win/Mac

COMMON GOTCHAS
──────────────
time.time()       → correct (not time.now())
sys.set_int_max_str_digits(N) → needed for huge integers (Python 3.11+)
Race condition    → use threading.Lock() for shared mutable state
GIL              → threads don't help CPU-bound; use processes instead
```

---

## Summary (TL;DR)

- A **program** is a static file of instructions; a **process** is a running instance with its own memory; a **thread** is a lightweight unit of execution within a process sharing the process's memory.
- Use **multithreading** for I/O-bound tasks (file I/O, network requests, web scraping) — threads overlap waiting time, reducing total runtime significantly.
- Use **multiprocessing** for CPU-bound tasks (math, data processing, ML) — separate processes bypass the GIL and run on multiple CPU cores in true parallel.
- `ThreadPoolExecutor` and `ProcessPoolExecutor` from `concurrent.futures` are the **modern, Pythonic way** — they manage pools automatically, eliminating manual thread/process lifecycle code.
- **Always use `if __name__ == "__main__"`** as the entry point guard for multiprocessing code on Windows/macOS — without it, new process spawning causes a recursive crash.
- The **GIL** is why threading doesn't help CPU-bound Python code — it ensures only one thread executes bytecode at a time; multiprocessing creates separate interpreters with separate GILs.
- `executor.map()` returns results in the **same order as the input**, making it safe to collect results without extra synchronization.

---

## Flashcard-Style Recall (Q&A Pairs)

- **Q: What is a program?**  
  A: A static sequence of instructions written in a programming language, stored on disk (e.g., `.py` file, `.exe`).

- **Q: What is a process?**  
  A: A running instance of a program with its own isolated memory space (code segment, data segment, heap, stack, registers).

- **Q: What is a thread?**  
  A: A unit of execution within a process. Threads share the process's code, data, and heap, but each has its own stack and registers.

- **Q: What does `thread.join()` do?**  
  A: It blocks the calling thread (usually the main thread) until the target thread completes execution.

- **Q: What is the GIL?**  
  A: The Global Interpreter Lock — a mutex in CPython that allows only one thread to execute Python bytecode at a time, limiting CPU parallelism with threads.

- **Q: Why use multiprocessing over multithreading for CPU-bound tasks?**  
  A: Multiprocessing creates separate processes, each with its own GIL and Python interpreter, enabling true parallel execution on multiple CPU cores.

- **Q: What does `ThreadPoolExecutor(max_workers=3)` mean?**  
  A: It creates a pool of 3 reusable threads. Tasks submitted to this pool are distributed across these 3 threads.

- **Q: Why must multiprocessing code be inside `if __name__ == "__main__"`?**  
  A: On Windows/macOS, new processes are created by spawning (re-importing the module). Without this guard, each child process would try to spawn more processes, causing infinite recursion.

- **Q: What error occurs when `time.now()` is used?**  
  A: `AttributeError: module 'time' has no attribute 'now'`. The correct call is `time.time()`.

- **Q: What is the difference between `ThreadPoolExecutor` and `ProcessPoolExecutor`?**  
  A: Both are high-level pool managers from `concurrent.futures`. `ThreadPoolExecutor` uses threads (good for I/O-bound), while `ProcessPoolExecutor` uses processes (good for CPU-bound, bypasses GIL).

- **Q: What does `executor.map(fn, iterable)` return?**  
  A: A lazy iterator of return values from `fn`, in the **same order** as the input iterable.

- **Q: How do you measure execution time in Python?**  
  A: `start = time.time()` before the code, then `elapsed = time.time() - start` after.

- **Q: What is `sys.set_int_max_str_digits(100000)` used for?**  
  A: It raises Python 3.11+'s security limit on converting large integers to strings, required when computing factorials of large numbers.

- **Q: What is a race condition?**  
  A: When two or more threads access shared mutable state simultaneously without synchronization, causing unpredictable results. Fixed using `threading.Lock()`.
