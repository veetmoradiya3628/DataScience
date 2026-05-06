# NUMPY ARRAY CREATION, ATTRIBUTES & RESHAPING

---

## Prerequisites
- Basic Python knowledge (lists, loops, list comprehensions, `zip()`)
- Understanding of Python data types (`int`, `float`, `str`)
- Familiarity with Python's `range()` function
- Basic understanding of matrices and vectors (helpful but not required)
- Python 3.8+ installed

---

## Environment Setup

```python
pip install numpy
```

```python
import numpy as np
import time
```

> **NumPy 2.x Note:** If upgrading from NumPy 1.x, run `pip install numpy --upgrade`. NumPy 2.0 introduced breaking changes — see the [Deprecation & Migration](#deprecation--numpy-2x-migration-notes) section below.

---

## Concept Overview

**What is NumPy?**
NumPy (Numerical Python) is the foundational package for numerical computing in Python. It provides the `ndarray` — a powerful N-dimensional array object — along with broadcasting, vectorized operations, and a rich library of linear algebra, Fourier transforms, and random number generation.

**Why does it exist? What problem does it solve?**
Python lists are flexible but fundamentally slow for numerical operations. They store pointers to scattered Python objects in memory, require type-checking for every operation, and cannot leverage CPU-level vectorized instructions. NumPy solves this by storing data in contiguous, typed memory blocks and executing operations in optimized C/Fortran code — making numerical computation **10x–100x faster**.

**Real-world analogy:**
Think of a Python list as a filing cabinet where each drawer contains a differently-wrapped package (each element is a full Python object with overhead). A NumPy array is like a sheet of graph paper — every cell is the same size, laid out in a contiguous grid, so you can process the entire sheet in one sweep instead of unwrapping each package individually.

**Where it fits in the data science ecosystem:**
NumPy is the bedrock layer. Pandas DataFrames are built on NumPy arrays. Scikit-learn operates on NumPy arrays internally. TensorFlow and PyTorch can share memory with NumPy. Every data science, ML, and scientific computing workflow in Python ultimately touches NumPy.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| `ndarray` | NumPy's core N-dimensional array object — a fixed-size, homogeneous container of elements |
| `dtype` | Data type of array elements (e.g., `int64`, `float64`, `str_`) — all elements share the same dtype |
| `shape` | Tuple describing array dimensions, e.g., `(2, 3)` means 2 rows × 3 columns |
| `ndim` | Number of dimensions (axes) — a 1D array has `ndim=1`, a matrix has `ndim=2` |
| `size` | Total number of elements in the array (`shape[0] × shape[1] × ...`) |
| `vectorization` | Applying operations to entire arrays at once (no Python loops), executed in C |
| `broadcasting` | NumPy's mechanism for operating on arrays of different shapes |
| `view` | A new array object sharing the same underlying data — changes propagate both ways |
| `copy` | An independent duplicate — changes do NOT propagate |
| `homogeneous` | All elements must be of the same data type |
| `axis` | A specific dimension of an array: `axis=0` = rows, `axis=1` = columns |
| `strides` | Number of bytes to step in memory to reach the next element along each dimension |
| `itemsize` | Size in bytes of a single element |

---

## The Performance Proof [BEGINNER]

This is the **most important motivational example** from the notebook. It demonstrates why NumPy exists.

**Task:** Add two lists/arrays of 1,000,000 elements.

### Pure Python Approach

```python
import time

a = list(range(1000000))
b = list(range(1000000))

start_p = time.perf_counter()
c = [x + y for x, y in zip(a, b)]
end_p = time.perf_counter()
total_p = end_p - start_p
print("Python:", total_p)
```

**Output:**

```
Python: 0.04614700000092853
```

**Line-by-line breakdown:**
- `list(range(1000000))` — creates a Python list of 1 million integers [0, 1, 2, ..., 999999]
- `zip(a, b)` — pairs up elements from both lists lazily
- `[x + y for x, y in zip(a, b)]` — list comprehension iterating through all 1M pairs, adding each
- `time.perf_counter()` — high-resolution timer for benchmarking

**Why it's slow:** Each iteration requires: (1) fetching a Python object from `a`, (2) fetching from `b`, (3) unboxing both to C integers, (4) performing the add, (5) boxing the result back into a Python object, (6) appending to the new list. That's ~6 operations × 1M times.

### NumPy Approach

```python
import numpy as np

a_np = np.array(a)
b_np = np.array(b)

start_n = time.perf_counter()
c_np = a_np + b_np
end_n = time.perf_counter()
total_n = end_n - start_n
print("NumPy:", total_n)
```

**Output:**

```
NumPy: 0.0018817000000126427
```

### The Speedup

```python
print(f"Numpy is faster than python by : {total_p/total_n:.2f}")
```

**Output:**

```
Numpy is faster than python by : 24.52
```

**NumPy is ~24.5x faster** for this operation.

**Why NumPy wins:**
- `a_np + b_np` is a single vectorized operation — no Python loop, no element-by-element overhead
- Internally, NumPy calls a compiled C function that operates on contiguous memory using CPU SIMD instructions
- No Python object boxing/unboxing — data stays as raw C integers in a contiguous buffer

### Side-by-Side Comparison

| Aspect | Python List | NumPy Array |
|--------|-------------|-------------|
| **Code** | `[x+y for x,y in zip(a,b)]` | `a_np + b_np` |
| **Time** | ~0.046 seconds | ~0.0019 seconds |
| **Speedup** | 1x (baseline) | **~24.5x faster** |
| **Mechanism** | Interpreted Python loop | Compiled C vectorized op |
| **Memory** | Scattered Python objects | Contiguous typed buffer |
| **Readability** | Verbose | Clean, mathematical |

**Complexity:** Both are O(n), but NumPy's constant factor is dramatically smaller due to C-level execution and memory locality.

---

## Why Use NumPy? (from notebook notes)

**Python lists are:**
- Flexible but **slow** for numerical operations
- Not designed for matrix math
- Do not support vectorized operations
- Memory inefficient for large numerical data

**Why use NumPy?**
- Faster than pure Python for numerical computation
- Vectorized operations (no explicit loops needed)
- Broadcasting (operate on arrays of different shapes)
- Memory efficient (contiguous, typed storage)
- Comes with powerful linear algebra, random number tools, etc.

**Primary use:** Scientific Python, data science, machine learning, engineering simulations

---

## Array Creation Functions — Detailed Explanation with Examples

---

### 1. `np.array()` — Create Array from Existing Data [BEGINNER]

**What:** Converts Python lists, tuples, or other sequences into a NumPy `ndarray`. This is the most fundamental and frequently used array creation function.

**Real-world analogy:** `np.array()` is like pouring mixed items from various bags (Python lists) into a standardized, compartmentalized tray (ndarray) where every slot is the same size and type.

**Syntax:**

```python
np.array(object, dtype=None, copy=True, order='K', subok=False, ndmin=0, like=None)
```

- `object` — list, tuple, or array-like to convert
- `dtype` — desired data type (auto-detected if `None`)
- `copy` — if `True` (default), always copy data. **NumPy 2.x change:** `copy=False` now raises an error if a copy is needed (previously it silently copied). Use `copy=None` for "copy only if necessary" behavior.
- `ndmin` — minimum number of dimensions for the result

---

#### Example 1: 1D Array from a List [BEGINNER]

```python
arr_1 = np.array([1, 2, 3])

print(arr_1)
print(type(arr_1))
```

**Output:**

```
[1 2 3]
<class 'numpy.ndarray'>
```

**Line-by-line breakdown:**
- `np.array([1, 2, 3])` — converts a Python list of 3 integers into a 1D ndarray
- `print(arr_1)` — shows the array content; note the **lack of commas** (NumPy's display format, unlike Python lists)
- `type(arr_1)` — confirms the type is `numpy.ndarray`, not `list`

**Shape:** `(3,)` — a 1D array with 3 elements
**dtype:** `int64` (auto-detected from integer inputs on most systems)

---

#### Example 2: Jupyter repr vs print [BEGINNER]

```python
arr_1
```

**Output:**

```
array([1, 2, 3])
```

**Why this matters:** In Jupyter, simply writing a variable name shows the `repr()` output, which includes `array(...)` wrapper and commas. Using `print()` shows a cleaner format. This is a key distinction when debugging — `repr` gives you more info about the object type.

---

#### Example 3: Element Type Inspection [BEGINNER]

```python
type(arr_1[0])
```

**Output:**

```
numpy.int64
```

**Why this matters:** Even though you passed Python `int` values, NumPy stores them as `numpy.int64` — a fixed-size 64-bit integer. This is critical: NumPy elements are NOT Python `int` objects. They are C-level typed values, which is why operations on them are fast.

---

#### Example 4: 2D Array (Matrix) [BEGINNER]

```python
arr_2 = np.array([[1, 2, 3], [4, 5, 6]])

print(arr_2)
```

**Output:**

```
[[1 2 3]
 [4 5 6]]
```

**Shape:** `(2, 3)` — 2 rows, 3 columns
**ndim:** 2

**ASCII diagram:**

```
         col0  col1  col2
row0  [   1     2     3  ]
row1  [   4     5     6  ]

Shape: (2, 3)  →  2 rows × 3 columns
```

---

#### Example 5: 2D Indexing — Two Syntax Forms [BEGINNER]

```python
arr_2[0]
```

**Output:**

```
array([1, 2, 3])
```

Accessing `arr_2[0]` returns the entire first row as a 1D array.

```python
arr_2[0][2]
```

**Output:**

```
np.int64(3)
```

```python
arr_2[1][2]
```

**Output:**

```
np.int64(6)
```

**Teaching point:** `arr_2[0][2]` uses **chained indexing** — first `arr_2[0]` creates a temporary array `[1, 2, 3]`, then `[2]` indexes into it. This works but is **less efficient** than the comma syntax `arr_2[0, 2]` which is a single operation. (See [Indexing & Slicing] in the 18-02-2026 notes.)

---

#### Example 6: Type Coercion — Mixed Types [INTERMEDIATE]

```python
arr_3 = np.array([1, 2, 3, "hello"])

print(arr_3)
print(type(arr_3))
```

**Output:**

```
['1' '2' '3' 'hello']
<class 'numpy.ndarray'>
```

```python
print(type(arr_3[1]))
```

**Output:**

```
<class 'numpy.str_'>
```

**Why this works (and why it's dangerous):**
NumPy arrays are **homogeneous** — all elements must be the same type. When you mix integers and strings, NumPy **upcasts everything** to the most general type that can hold all values. The hierarchy is: `bool → int → float → complex → str`. Since `"hello"` can't become a number, ALL elements become strings. The integers `1, 2, 3` silently become `'1', '2', '3'`.

**Mnemonic:** "One string poisons the whole array" — if any element is a string, everything becomes a string.

**Warning:** This silent upcasting is a common source of bugs. You might think you're doing math on numbers, but they're secretly strings, and `arr_3 + arr_3` would give `['11', '22', '33', 'hellohello']` (string concatenation) instead of `[2, 4, 6, ...]`.

---

#### Example 7: Explicit dtype Specification [BEGINNER]

```python
arr_4 = np.array([1, 2, 3], dtype=np.int8)
print(type(arr_4))
```

**Output:**

```
<class 'numpy.ndarray'>
```

```python
type(arr_4[0])
```

**Output:**

```
numpy.int8
```

**Why specify dtype?**
- **Memory control:** `int8` uses 1 byte per element vs `int64`'s 8 bytes — that's **8x memory savings**
- **Interoperability:** Some libraries (e.g., image processing) expect specific dtypes
- **Performance:** Smaller dtypes can be faster for large arrays due to cache efficiency

**int8 range:** -128 to 127. Values outside this range will **overflow silently** in NumPy (wrapping around).

---

#### Example 8: dtype Conversion Error [INTERMEDIATE]

```python
arr_5 = np.array([1, 2, 3, "hello"], dtype=np.float16)
```

**Output:**

```
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
Cell In[18], line 1
----> 1 arr_5 = np.array([1,2,3, "hello"], dtype=np.float16)

ValueError: could not convert string to float: 'hello'
```

**Why it fails:** When you explicitly set `dtype=np.float16`, NumPy tries to convert EVERY element to `float16`. While `1, 2, 3` convert fine, `"hello"` has no numeric interpretation, so NumPy raises a `ValueError`.

**Key insight:** Without `dtype`, NumPy silently upcasts to string (Example 6). WITH `dtype`, NumPy raises an error if conversion is impossible. **Explicit dtype is safer.**

---

#### Example 9: String-to-Number Conversion [INTERMEDIATE]

```python
arr_6 = np.array([1, 2, 3, "10"], dtype=np.float16)
```

```python
arr_6
```

**Output:**

```
array([ 1.,  2.,  3., 10.], dtype=float16)
```

**Why this works:** The string `"10"` CAN be converted to a float, so NumPy does it successfully. All elements are now `float16`.

**Rule:** `dtype` forces conversion. If every element can be converted to the target type, it succeeds. If any element can't, it raises `ValueError`.

---

#### Key Rule: Arrays are Homogeneous [BEGINNER]

> **Arrays can have elements with only one data type.** This is the fundamental contract of `ndarray`. If you need mixed types, use a Python list, a Pandas DataFrame, or a NumPy structured array.

---

### 2. `np.arange()` — Array Range [BEGINNER]

**What:** Creates an array with evenly spaced values within a given interval. It's the NumPy equivalent of Python's `range()` but returns an ndarray and supports float steps.

**Real-world analogy:** Like marking distances on a ruler — you start at a point, step by a fixed interval, and stop before the end mark.

**Syntax:**

```python
np.arange(start=0, stop, step=1, dtype=None)
```

- `start` — starting value (inclusive, default 0)
- `stop` — end value (**exclusive**, just like Python's `range`)
- `step` — spacing between values (default 1)
- `dtype` — auto-inferred from inputs

#### Example 1: Comparison with Python range [BEGINNER]

```python
list(range(1, 11, 2))
```

**Output:**

```
[1, 3, 5, 7, 9]
```

```python
b = np.arange(1, 11, 2)
b
```

**Output:**

```
array([1, 3, 5, 7, 9])
```

**Key difference:** `range()` returns a lazy iterator and produces a Python list of Python `int` objects. `np.arange()` returns a contiguous ndarray of typed integers — ready for vectorized operations immediately.

**When to use `arange` vs `range`:**
- Use `range()` for loop iteration (it's lazy — memory efficient for loops)
- Use `np.arange()` when you need an array for computation

**Complexity:** O(n) where n = `(stop - start) / step`

---

### 3. `np.linspace()` — Linearly Spaced Array [BEGINNER]

**What:** Creates an array of `num` evenly spaced values between `start` and `stop` (**inclusive** by default). Unlike `arange` where you specify the step, here you specify how many points you want.

**Real-world analogy:** Like dividing a rope into exactly N equal segments — you specify the endpoints and the count, and `linspace` calculates the spacing.

**Syntax:**

```python
np.linspace(start, stop, num=50, endpoint=True, retstep=False, dtype=None)
```

- `start` — start value
- `stop` — end value (**inclusive** by default, unlike `arange`!)
- `num` — number of samples to generate (default 50)
- `endpoint` — whether to include `stop` (default `True`)
- `retstep` — if `True`, also return the step size
- `dtype` — auto-detected (usually `float64`)

#### Example 1: 4 Points Between 1 and 100 [BEGINNER]

```python
np_linspace_1 = np.linspace(1, 100, 4)
print(np_linspace_1)
print(type(np_linspace_1))
```

**Output:**

```
[  1.  34.  67. 100.]
<class 'numpy.ndarray'>
```

**Why these values?** With 4 points from 1 to 100 (inclusive), the step is `(100 - 1) / (4 - 1) = 33`. So: 1, 34, 67, 100.

#### Example 2: 10 Points Between 1 and 10 [BEGINNER]

```python
np_linspace_2 = np.linspace(1, 10, 10)
print(np_linspace_2)
print(type(np_linspace_2))
```

**Output:**

```
[ 1.  2.  3.  4.  5.  6.  7.  8.  9. 10.]
<class 'numpy.ndarray'>
```

Step = `(10 - 1) / (10 - 1) = 1.0`. Note the values are `float64` even though they look like integers — that's `linspace`'s default behavior.

#### Example 3: Iterating Over linspace [BEGINNER]

```python
for num in np.linspace(1, 10, 10):
    print(num)
```

**Output:**

```
1.0
2.0
3.0
4.0
5.0
6.0
7.0
8.0
9.0
10.0
```

Each value is a `numpy.float64`. If you need integers:

#### Example 4: linspace with dtype [INTERMEDIATE]

```python
for num in np.linspace(1, 10, 10, dtype=np.int8):
    print(num)
```

**Output:**

```
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

**Caution:** dtype conversion happens AFTER linspace computes the values. For non-integer-friendly ranges, this can cause duplicate values or unexpected rounding.

#### `arange` vs `linspace`

| Feature | `np.arange()` | `np.linspace()` |
|---------|---------------|-----------------|
| You specify | **step size** | **number of points** |
| Stop value | **exclusive** | **inclusive** (default) |
| Best for | Known step size | Known number of samples |
| Float precision | Can miss endpoint | Guarantees endpoint |
| Use case | `[0, 0.1, 0.2, ...]` | "Give me exactly 100 points from 0 to 2π" |

**Mnemonic:** "**A**range = step **A**mount, **L**inspace = **L**ength (count)"

---

### 4. `np.zeros()` — Zero-Filled Array [BEGINNER]

**What:** Creates an array filled entirely with zeros. Default dtype is `float64`.

**Real-world analogy:** Like a fresh whiteboard — all cells start at zero, ready to be written on. Commonly used to initialize accumulators, output buffers, and placeholder arrays.

**Syntax:**

```python
np.zeros(shape, dtype=float, order='C')
```

- `shape` — int or tuple of ints defining dimensions
- `dtype` — default `float64`

#### Example 1: 1D Zeros [BEGINNER]

```python
npz_1 = np.zeros(5)
npz_1
```

**Output:**

```
array([0., 0., 0., 0., 0.])
```

Shape: `(5,)` — five zeros, dtype `float64` (note the dots).

#### Example 2: 2D Zeros with int8 dtype [BEGINNER]

```python
npz_2 = np.zeros((5, 5), dtype=np.int8)
print(npz_2)
```

**Output:**

```
[[0 0 0 0 0]
 [0 0 0 0 0]
 [0 0 0 0 0]
 [0 0 0 0 0]
 [0 0 0 0 0]]
```

**Memory:** 5 × 5 × 1 byte (int8) = 25 bytes. Compare with float64: 5 × 5 × 8 = 200 bytes.

#### Example 3: 2D Zeros with default dtype [BEGINNER]

```python
npz_2 = np.zeros((5, 5))
print(npz_2)
```

**Output:**

```
[[0. 0. 0. 0. 0.]
 [0. 0. 0. 0. 0.]
 [0. 0. 0. 0. 0.]
 [0. 0. 0. 0. 0.]
 [0. 0. 0. 0. 0.]]
```

The decimal points confirm these are `float64`.

#### Example 4: 3D Zeros [INTERMEDIATE]

```python
npz_3 = np.zeros((5, 5, 5))
print(npz_3)
```

**Output:**

```
[[[0. 0. 0. 0. 0.]
  [0. 0. 0. 0. 0.]
  [0. 0. 0. 0. 0.]
  [0. 0. 0. 0. 0.]
  [0. 0. 0. 0. 0.]]

 [[0. 0. 0. 0. 0.]
  [0. 0. 0. 0. 0.]
  [0. 0. 0. 0. 0.]
  [0. 0. 0. 0. 0.]
  [0. 0. 0. 0. 0.]]

 [[0. 0. 0. 0. 0.]
  [0. 0. 0. 0. 0.]
  [0. 0. 0. 0. 0.]
  [0. 0. 0. 0. 0.]
  [0. 0. 0. 0. 0.]]

 [[0. 0. 0. 0. 0.]
  [0. 0. 0. 0. 0.]
  [0. 0. 0. 0. 0.]
  [0. 0. 0. 0. 0.]
  [0. 0. 0. 0. 0.]]

 [[0. 0. 0. 0. 0.]
  [0. 0. 0. 0. 0.]
  [0. 0. 0. 0. 0.]
  [0. 0. 0. 0. 0.]
  [0. 0. 0. 0. 0.]]]
```

```python
npz_3.ndim
```

**Output:**

```
3
```

**Shape:** `(5, 5, 5)` — a 3D "cube" of zeros. Think of it as 5 layers, each containing a 5×5 grid.

**ASCII diagram:**

```
Layer 0:        Layer 1:        ...  Layer 4:
[0 0 0 0 0]    [0 0 0 0 0]         [0 0 0 0 0]
[0 0 0 0 0]    [0 0 0 0 0]         [0 0 0 0 0]
[0 0 0 0 0]    [0 0 0 0 0]         [0 0 0 0 0]
[0 0 0 0 0]    [0 0 0 0 0]         [0 0 0 0 0]
[0 0 0 0 0]    [0 0 0 0 0]         [0 0 0 0 0]

Shape: (5, 5, 5) → 5 layers × 5 rows × 5 columns
Total elements: 5 × 5 × 5 = 125
```

**Use cases:** Initializing weight matrices, accumulator arrays, image buffers (e.g., blank black image), ML model parameter initialization.

---

### 5. `np.ones()` — Ones-Filled Array [BEGINNER]

**What:** Creates an array filled entirely with ones. Identical API to `np.zeros()`.

**Real-world analogy:** Like a form with every checkbox already ticked — start from "all yes" and selectively clear.

**Syntax:**

```python
np.ones(shape, dtype=float, order='C')
```

#### Example 1: 2D Ones [BEGINNER]

```python
npo_1 = np.ones((2, 3))
print(npo_1)
```

**Output:**

```
[[1. 1. 1.]
 [1. 1. 1.]]
```

Shape: `(2, 3)`, dtype: `float64`.

**Use cases:** Initializing bias vectors, creating masks, broadcasting multipliers, placeholder for matrix operations.

---

### 6. `np.full()` — Fill with Any Value [BEGINNER]

**What:** Creates an array of the given shape, filled with a specified value.

**Real-world analogy:** Like bulk-setting every cell in a spreadsheet to the same default value before entering real data.

**Syntax:**

```python
np.full(shape, fill_value, dtype=None, order='C')
```

- `fill_value` — the value to fill every element with
- `dtype` — inferred from `fill_value` if not specified

#### Example 1: Fill with 10 [BEGINNER]

```python
npf_1 = np.full((2, 3), 10)
print(npf_1)
```

**Output:**

```
[[10 10 10]
 [10 10 10]]
```

**dtype:** `int64` (inferred from `fill_value=10`, which is a Python int).

**When to use `full` vs `zeros`/`ones`:**
- Use `zeros()`/`ones()` for the common cases (0 or 1)
- Use `full()` for any other fill value (e.g., `np.full((3,3), np.nan)` for NaN initialization, `np.full((3,3), -1)` for sentinel values)

---

### 7. `np.eye()` — Identity Matrix [INTERMEDIATE]

**What:** Creates a 2D identity matrix — ones on the diagonal, zeros everywhere else. This is the matrix equivalent of the number 1: any matrix multiplied by the identity matrix gives itself back.

**Real-world analogy:** Think of an identity matrix as a "do nothing" transform — like a photo filter set to zero. Apply it to any image (matrix), and you get the exact same image back.

**Syntax:**

```python
np.eye(N, M=None, k=0, dtype=float)
```

- `N` — number of rows
- `M` — number of columns (defaults to `N` for square matrix)
- `k` — diagonal offset (0 = main diagonal, positive = above, negative = below)
- `dtype` — default is `float64`

**Note from notebook:** "Does not exist in 1D — minimum 2D shape."

#### Example 1: Square Identity 3×3 [BEGINNER]

```python
np.eye(3)
```

**Output:**

```
array([[1., 0., 0.],
       [0., 1., 0.],
       [0., 0., 1.]])
```

**Why it works:** `np.eye(3)` creates a 3×3 matrix. The diagonal (where row index == column index) gets 1.0, everything else gets 0.0. Default dtype is `float64`, hence the decimal points.

#### Example 2: Non-Square — More Columns [INTERMEDIATE]

```python
np.eye(2, 3)
```

**Output:**

```
array([[1., 0., 0.],
       [0., 1., 0.]])
```

Only 2 rows but 3 columns — the diagonal fills where `row == col`, and the extra column stays zero.

#### Example 3: Non-Square — More Rows [INTERMEDIATE]

```python
np.eye(3, 2)
```

**Output:**

```
array([[1., 0.],
       [0., 1.],
       [0., 0.]])
```

The third row has no diagonal element (only 2 columns), so it's all zeros.

#### Example 4: Larger Identity [BEGINNER]

```python
np.eye(7, 7)
```

**Output:**

```
array([[1., 0., 0., 0., 0., 0., 0.],
       [0., 1., 0., 0., 0., 0., 0.],
       [0., 0., 1., 0., 0., 0., 0.],
       [0., 0., 0., 1., 0., 0., 0.],
       [0., 0., 0., 0., 1., 0., 0.],
       [0., 0., 0., 0., 0., 1., 0.],
       [0., 0., 0., 0., 0., 0., 1.]])
```

**When to use:** Linear algebra (matrix inversion, solving systems), initializing transformation matrices, ML weight initialization, one-hot encoding.

**When NOT to use:** When you need a general diagonal matrix with non-one values — use `np.diag([values])` instead.

**Complexity:** O(N×M) — must write every element.

---

### 8. `np.empty()` — Uninitialized Array [INTERMEDIATE]

**What:** Creates an array WITHOUT initializing its values. The contents are whatever random data happens to be in that memory location. This is the fastest array creation function because it skips the initialization step.

**Real-world analogy:** Like getting a used whiteboard that hasn't been erased — there's random leftover scribbles from whoever used it before you. You MUST overwrite everything before reading.

**Syntax:**

```python
np.empty(shape, dtype=float, order='C')
```

#### Example 1: Uninitialized int8 [INTERMEDIATE]

```python
np.empty((2, 2), dtype=np.int8)
```

**Output:**

```
array([[0, 0],
       [0, 1]], dtype=int8)
```

#### Example 2: Uninitialized float16 [INTERMEDIATE]

```python
np.empty((2, 2), dtype=np.float16)
```

**Output:**

```
array([[0.   , 0.   ],
       [0.   , 1.984]], dtype=float16)
```

**WARNING:** These values are NOT zeros — they just happen to be zero (or near-zero garbage) because that memory was recently used. **NEVER rely on `empty` values being zero.** If you need zeros, use `np.zeros()`.

#### Example 3: Performance — empty vs ones [ADVANCED]

The notebook benchmarked creation speed:

```python
start_e = time.perf_counter()
np.empty((5, 5))
end_e = time.perf_counter()
total_e = end_e - start_e
print(total_e)
```

**Output:**

```
4.240000089339446e-05
```

```python
start_we = time.perf_counter()
np.ones((5, 5))
end_we = time.perf_counter()
total_we = end_we - start_we
print(total_we)
```

**Output:**

```
5.390000114857685e-05
```

**Analysis:** `empty` (~0.0000424s) is faster than `ones` (~0.0000539s) because it skips initialization. The difference is small for (5,5) but becomes significant for very large arrays.

**When to use:** When you're going to immediately overwrite all values (e.g., filling row-by-row in a loop, reading from a file). Premature optimization — only use if profiling shows array creation is a bottleneck.

**When NOT to use:** When you read from the array before writing — you'll get garbage values.

---

### 9. `np.random.rand()` — Random Array [BEGINNER]

**What:** Creates an array of the given shape filled with random values uniformly distributed between 0 and 1.

**Real-world analogy:** Like rolling a perfectly fair die with infinite faces between 0 and 1 — every value in that range is equally likely.

**Syntax:**

```python
np.random.rand(d0, d1, ..., dn)
```

- `d0, d1, ...` — dimensions (passed as separate arguments, NOT as a tuple)

#### Example 1: 2×8 Random Array [BEGINNER]

```python
np.random.rand(2, 8)
```

**Output:**

```
array([[0.65339572, 0.3644414 , 0.88792983, 0.65404473, 0.60644969,
        0.1896986 , 0.31136365, 0.32720686],
       [0.39949559, 0.4455743 , 0.39204205, 0.6491078 , 0.91083752,
        0.48799975, 0.73930809, 0.93253408]])
```

**Note:** Your output WILL differ — these are random. For reproducibility, seed the generator first.

**Use case from notebook:** "ML weights random initialization" — neural networks initialize weight matrices with small random values to break symmetry during training.

**NumPy 2.x Recommendation:** Use `np.random.default_rng()` instead of the legacy `np.random` module:

```python
rng = np.random.default_rng(seed=42)
rng.random((2, 8))  # Modern equivalent of np.random.rand(2, 8)
```

---

### Array Creation Functions — Quick Reference

| Function | Purpose | Shape Arg | Default dtype | Key Note |
|----------|---------|-----------|---------------|----------|
| `np.array()` | From existing data | N/A (from input) | Auto-detected | Most common creator |
| `np.arange()` | Range with step | N/A (from params) | Auto-detected | Stop is **exclusive** |
| `np.linspace()` | Range with count | N/A (from params) | `float64` | Stop is **inclusive** |
| `np.zeros()` | All zeros | Tuple | `float64` | Safe default initializer |
| `np.ones()` | All ones | Tuple | `float64` | Bias/mask initializer |
| `np.full()` | All same value | Tuple | From fill_value | Any value initializer |
| `np.eye()` | Identity matrix | N, M | `float64` | Min 2D, diagonal ones |
| `np.empty()` | Uninitialized | Tuple | `float64` | Fastest, but DANGEROUS |
| `np.random.rand()` | Random [0,1) | Separate ints | `float64` | ML weight init |

---

## Array Attributes Reference [BEGINNER]

All examples use:

```python
arr = np.array([[1, 2, 3], [4, 5, 6]])
print(arr)
```

**Output:**

```
[[1 2 3]
 [4 5 6]]
```

### 1. `.shape` — Dimensions as Tuple

```python
print(arr.shape)
```

**Output:**

```
(2, 3)
```

Returns a tuple: `(rows, columns)` for 2D, `(layers, rows, columns)` for 3D, etc.

### 2. `.ndim` — Number of Dimensions

```python
print(arr.ndim)
```

**Output:**

```
2
```

Equivalent to `len(arr.shape)`. A scalar has ndim=0, a vector has ndim=1, a matrix has ndim=2.

### 3. `.size` — Total Element Count

```python
print(arr.size)
```

**Output:**

```
6
```

Product of all shape dimensions: 2 × 3 = 6. Equivalent to `np.prod(arr.shape)`.

### 4. `.dtype` — Data Type

```python
print(arr.dtype)
```

**Output:**

```
int64
```

All elements share this type. Determines memory per element and valid operations.

### 5. `.itemsize` — Bytes Per Element

```python
print(arr.itemsize)
```

**Output:**

```
8
```

`int64` = 64 bits = 8 bytes. Notebook note: "1 byte → 8 bits, int8 → 8 bits, int64 → int8 × 8 → int64"

### 6. `.nbytes` — Total Memory

```python
print(arr.nbytes)
```

**Output:**

```
48
```

Formula: `.nbytes = .size × .itemsize` = 6 × 8 = 48 bytes.

### 7. `.T` — Transpose

```python
print(arr)    # 2 rows, 3 columns
```

**Output:**

```
[[1 2 3]
 [4 5 6]]
```

```python
print(arr.T)
```

**Output:**

```
[[1 4]
 [2 5]
 [3 6]]
```

**What happens:** Rows become columns and columns become rows. Shape changes from `(2, 3)` to `(3, 2)`.

**Critical:** `.T` returns a **view**, not a copy. Modifying the transpose modifies the original.

**ASCII diagram:**

```
Original (2×3):          Transpose (3×2):
[1  2  3]                [1  4]
[4  5  6]                [2  5]
                          [3  6]
```

### Attributes Summary Table

| Attribute | Returns | Example Value | Formula |
|-----------|---------|---------------|---------|
| `.shape` | Dimensions tuple | `(2, 3)` | — |
| `.ndim` | Number of dimensions | `2` | `len(shape)` |
| `.size` | Total elements | `6` | `prod(shape)` |
| `.dtype` | Element data type | `int64` | — |
| `.itemsize` | Bytes per element | `8` | — |
| `.nbytes` | Total bytes | `48` | `size × itemsize` |
| `.T` | Transposed view | Shape `(3, 2)` | Swap axes |

---

## Array Reshaping [INTERMEDIATE]

### `reshape()` — Change Shape Without Changing Data

**What:** Returns a new array with the same data but a different shape. The total number of elements must remain the same.

**Real-world analogy:** Reshaping is like rearranging 12 eggs from a 2×6 carton into a 3×4 carton — the same eggs, different arrangement.

**Syntax:**

```python
ndarray.reshape(new_shape)
```

- `new_shape` — tuple or separate ints defining the new dimensions
- **Rule:** `np.prod(new_shape)` must equal `np.prod(old_shape)`
- Returns a **view** if possible (no data copy)
- Use `-1` to let NumPy auto-calculate one dimension

#### Example 1: 1D → 2D (2×3) [BEGINNER]

```python
arr = np.array([1, 2, 3, 4, 5, 6])
print(arr.shape)
```

**Output:**

```
(6,)
```

```python
reshaped_1 = arr.reshape(2, 3)
print(reshaped_1)
```

**Output:**

```
[[1 2 3]
 [4 5 6]]
```

6 elements → 2 rows × 3 columns = 6 elements. Valid.

#### Example 2: 1D → 2D (3×2) [BEGINNER]

```python
reshaped_2 = arr.reshape(3, 2)
print(reshaped_2)
```

**Output:**

```
[[1 2]
 [3 4]
 [5 6]]
```

Same data, different arrangement. Elements fill row-by-row (C order).

#### Example 3: 1D → 2D (1×6) [INTERMEDIATE]

```python
reshaped_2 = arr.reshape(1, 6)
print(reshaped_2)
print(reshaped_2.ndim)
```

**Output:**

```
[[1 2 3 4 5 6]]
2
```

**Critical insight:** `(6,)` is a 1D array (ndim=1). `(1, 6)` is a 2D row vector (ndim=2). They contain the same data but behave differently in broadcasting and matrix operations. This distinction matters in ML (row vector vs column vector).

#### Example 4: Using -1 for Auto-Inference [INTERMEDIATE]

```python
arr = np.array([1, 2, 3, 4, 5, 6, 56, 100, 789])
print(arr.shape)
```

**Output:**

```
(9,)
```

```python
reshaped_3 = arr.reshape(3, -1)
print(reshaped_3.shape)
```

**Output:**

```
(3, 3)
```

NumPy calculates: 9 / 3 = 3 → shape `(3, 3)`.

```python
reshaped_3 = arr.reshape(-1, 3)
print(reshaped_3.shape)
```

**Output:**

```
(3, 3)
```

NumPy calculates: 9 / 3 = 3 → shape `(3, 3)`.

**Rule:** You can use `-1` for **exactly one** dimension. NumPy infers it as `total_size / product_of_other_dims`.

#### Example 5: Reshape Error — Incompatible Shape [INTERMEDIATE]

```python
reshaped_3 = arr.reshape(2, -1)
print(reshaped_3.shape)
```

**Output:**

```
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
Cell In[101], line 1
----> 1 reshaped_3 = arr.reshape(2, -1)
      2 print(reshaped_3.shape)

ValueError: cannot reshape array of size 9 into shape (2,newaxis)
```

**Why it fails:** 9 / 2 = 4.5 — not an integer. You can't split 9 elements into 2 equal rows. The total must be evenly divisible.

**Notebook comment on valid reshapes for 6 elements:**

```
# 2x3 → Yes (2 × 3 = 6)
# 3x2 → Yes (3 × 2 = 6)
# 4x2 → No  (4 × 2 = 8 ≠ 6)
# 6x1 → Yes (6 × 1 = 6)
```

---

## Comparison Table — Python List vs NumPy Array (MANDATORY)

| Feature | Python List | NumPy Array |
|---------|-------------|-------------|
| **Type** | `list` | `numpy.ndarray` |
| **Element types** | Heterogeneous (mixed) | Homogeneous (single dtype) |
| **Memory layout** | Scattered pointers | Contiguous typed buffer |
| **Memory per int** | ~28 bytes (Python obj) | 1–8 bytes (dtype-dependent) |
| **Element-wise add** | List comprehension + zip | `arr1 + arr2` |
| **Speed (1M add)** | ~0.046 sec | ~0.0019 sec (~24.5x faster) |
| **Broadcasting** | Not supported | Full support |
| **Vectorized ops** | Not supported | Full support |
| **Slicing returns** | Copy (new list) | View (shared memory) |
| **Append/resize** | O(1) amortized | Expensive (new allocation) |
| **Best for** | Mixed data, frequent resizing | Numerical computation, math |

---

## How It Works Internally

### ndarray Memory Layout

An `ndarray` is fundamentally a combination of:

1. **Data buffer** — a contiguous block of memory holding the raw typed values
2. **Shape** — tuple describing dimensions `(2, 3)`
3. **Strides** — bytes to jump for each dimension `(24, 8)` for int64 2×3 array
4. **dtype** — describes the type and size of each element

**ASCII diagram:**

```
ndarray object
┌──────────────────────────┐
│ shape:  (2, 3)           │
│ dtype:  int64            │
│ strides: (24, 8)         │
│ data → ┌────────────────┐│
│        │ 1 │ 2 │ 3 │    ││   ← Row 0 (bytes 0–23)
│        │ 4 │ 5 │ 6 │    ││   ← Row 1 (bytes 24–47)
│        └────────────────┘│
│        Contiguous C-order │
└──────────────────────────┘
```

### Strides Explained

Strides tell NumPy how many bytes to skip to get to the next element along each axis:

- `strides[0] = 24` → jump 24 bytes to go from row 0 to row 1 (3 elements × 8 bytes each)
- `strides[1] = 8` → jump 8 bytes to go from one column to the next (1 element × 8 bytes)

### Views vs Copies

| Operation | Returns | Memory | Mutation propagation |
|-----------|---------|--------|---------------------|
| `arr.reshape()` | View (usually) | Shared | Yes — changes affect original |
| `.T` | View | Shared | Yes |
| Slicing `arr[1:3]` | View | Shared | Yes |
| `.flatten()` | Copy | New allocation | No — independent |
| `.copy()` | Copy | New allocation | No — independent |

**How to check:**

```python
b = arr.reshape(2, 3)
print(np.shares_memory(arr, b))  # True → view
```

### C-Contiguous vs Fortran-Contiguous

- **C order (row-major):** Elements in the same row are adjacent in memory. Default in NumPy.
- **Fortran order (column-major):** Elements in the same column are adjacent. Used in some linear algebra libraries.

---

## Common Mistakes & Debugging

### Mistake 1: Mixed-Type Silent Upcasting [BEGINNER]

**Wrong:**

```python
arr_3 = np.array([1, 2, 3, "hello"])
print(arr_3)
```

**Result:**

```
['1' '2' '3' 'hello']
```

**Why it fails (silently):** You expected an integer array but got strings. NumPy upcasts everything to `str_` because `"hello"` can't be a number.

**Correct:** Don't mix strings with numbers. If you need mixed types, use a Pandas DataFrame or separate arrays.

### Mistake 2: Explicit dtype with Unconvertible String [INTERMEDIATE]

**Wrong:**

```python
arr_5 = np.array([1, 2, 3, "hello"], dtype=np.float16)
```

**Error:**

```
ValueError: could not convert string to float: 'hello'
```

**Why it fails:** `dtype=np.float16` forces conversion, but `"hello"` has no numeric representation.

**Correct:** Remove the string, or convert to a compatible type first.

### Mistake 3: Reshape with Incompatible Size [INTERMEDIATE]

**Wrong:**

```python
arr = np.array([1, 2, 3, 4, 5, 6, 56, 100, 789])  # 9 elements
reshaped = arr.reshape(2, -1)
```

**Error:**

```
ValueError: cannot reshape array of size 9 into shape (2,newaxis)
```

**Why it fails:** 9 is not evenly divisible by 2. Total elements must remain constant after reshape.

**Correct:** Use compatible dimensions: `arr.reshape(3, 3)` or `arr.reshape(9, 1)`.

---

## Edge Cases & Gotchas Catalog

| Edge Case | What Happens | Example |
|-----------|-------------|---------|
| Empty array `np.array([])` | Creates float64 array with shape `(0,)`. Aggregations like `.sum()` return 0.0 | `np.array([]).shape → (0,)` |
| 0-d array `np.array(5)` | Creates a scalar array with shape `()`, ndim=0. Not iterable. | `np.array(5).shape → ()` |
| Single-element `np.array([5])` | Shape `(1,)`, ndim=1. Different from scalar. | `np.array([5]).ndim → 1` |
| `int8` overflow | Wraps silently: `np.int8(127) + np.int8(1) → -128` | No error raised! |
| `np.empty` values | Contains garbage — never assume zeros | May look like zeros but aren't |
| Mixed int + string | Silent upcast to string | `np.array([1, "a"]) → ['1' 'a']` |
| `reshape(-1, -1)` | Error — only one `-1` allowed | `ValueError` |
| `np.zeros(0)` | Valid! Creates empty float64 array | Shape `(0,)` |

---

## Best Practices & Pythonic Way

- **Always specify dtype** when memory matters or when interfacing with external systems
- **Use `np.zeros()` over `np.empty()`** unless profiling proves creation is a bottleneck
- **Use `arr[i, j]` over `arr[i][j]`** — the comma syntax is a single operation (faster)
- **Prefer `np.linspace` over `np.arange` for float ranges** — avoids floating-point step accumulation errors
- **Check `.shape` and `.dtype` obsessively** when debugging — most NumPy bugs are shape/dtype mismatches
- **Use `np.random.default_rng()` over `np.random.rand()`** for reproducible, modern random generation
- **Don't mix types in arrays** — if you see unexpected string results, check for accidental string elements
- **Reshape with `-1` freely** — it makes code more maintainable when array sizes change

---

## When to Use / When NOT to Use

| Use When… | Avoid When… |
|-----------|-------------|
| Numeric computation on large datasets | You need mixed-type records (use Pandas) |
| Matrix operations and linear algebra | Frequent appends/resizes (use Python list, then convert) |
| Image processing (arrays are images) | String processing (use Python strings) |
| ML feature matrices | Small datasets < 100 elements (overhead not worth it) |
| Signal processing | Graph/tree data structures |
| Batch mathematical operations | Sparse data with > 90% zeros (use `scipy.sparse`) |

---

## Related Topics & Connections

- **Array Reshaping & Resizing** → Covered in depth in the [numpy-part-2 notes](numpy-part-2.md) (ravel, flatten, resize, squeeze)
- **Indexing & Slicing** → See [numpy-part-2 notes](numpy-part-2.md) for basic, boolean, fancy indexing, and `np.where`
- **Broadcasting** → Coming in a future module; builds on shape understanding from this lesson
- **Vectorized Operations** → Builds on the performance proof shown here; operations like `+`, `-`, `*`, `/` on arrays
- **Views vs Copies** → Deeper dive in reshaping and slicing modules

---

## Interoperability Guide

| From/To | Method | Notes |
|---------|--------|-------|
| Python list → NumPy | `np.array(my_list)` | Primary creation method covered in this module |
| NumPy → Python list | `arr.tolist()` | Deep conversion — returns nested Python lists |
| NumPy → Pandas | `pd.DataFrame(arr)` | May share memory |
| Pandas → NumPy | `df.to_numpy()` | Preferred over deprecated `.values` |
| NumPy → PyTorch | `torch.from_numpy(arr)` | **Shares memory** — be careful! |
| PyTorch → NumPy | `tensor.numpy()` | Must be on CPU, no grad |
| NumPy → SciPy sparse | `scipy.sparse.csr_matrix(arr)` | For large sparse arrays |

---

## Deprecation & NumPy 2.x Migration Notes

| Old (1.x) | New (2.x) | Notes |
|-----------|-----------|-------|
| `np.bool` | `np.bool_` or `bool` | Type aliases removed in 2.0 |
| `np.int` | `np.int_` or `int` | Type aliases removed in 2.0 |
| `np.float` | `np.float64` or `float` | Type aliases removed in 2.0 |
| `np.complex` | `np.complex128` or `complex` | Type aliases removed in 2.0 |
| `np.object` | `np.object_` or `object` | Type aliases removed in 2.0 |
| `np.random.rand(2,3)` | `rng = np.random.default_rng(); rng.random((2,3))` | Legacy global state vs explicit RNG |
| `np.random.seed(42)` | `rng = np.random.default_rng(42)` | Thread-safe, reproducible |
| `np.array(x, copy=False)` | `np.array(x, copy=None)` | `copy=False` in 2.x raises error if copy needed; use `None` for "copy if needed" |
| `np.str` | `np.str_` | Alias removed |

---

## Interview & Exam Corner

### Frequently Asked Questions

**Q1: What is the difference between a Python list and a NumPy array?**
A: NumPy arrays are homogeneous (single dtype), stored in contiguous memory, support vectorized operations and broadcasting, and are significantly faster for numerical operations. Python lists are heterogeneous, store object pointers, and require explicit loops for element-wise operations.

**Q2: What happens when you put mixed types in `np.array()`?**
A: NumPy upcasts all elements to the most general compatible type. The hierarchy is `bool → int → float → complex → str`. So `np.array([1, 2, "hello"])` gives a string array `['1', '2', 'hello']`.

**Q3: What's the difference between `np.arange()` and `np.linspace()`?**
A: `arange` takes a **step** and has an **exclusive** stop. `linspace` takes a **count** and has an **inclusive** stop. Use `linspace` for float ranges to avoid precision issues.

**Q4: Why is `np.empty()` faster than `np.zeros()`?**
A: `empty` allocates memory but skips initialization (doesn't write zeros). `zeros` allocates AND fills with 0.0. The difference is O(n) initialization time.

**Q5: What does `.T` return — a view or a copy?**
A: A view. Modifying `arr.T` modifies `arr` and vice versa.

**Q6: How much faster is NumPy than Python for adding 1M elements?**
A: In the notebook benchmark, NumPy was ~24.5x faster (0.0019s vs 0.046s).

**Q7: What is `itemsize` and how does it relate to `nbytes`?**
A: `itemsize` is bytes per element (e.g., 8 for int64). `nbytes = size × itemsize` gives total memory. For a (2,3) int64 array: 6 × 8 = 48 bytes.

### Tricky Output Questions

**Q1: What is the output?**

```python
arr = np.array([1, 2, 3, "4"])
print(arr.dtype)
print(arr[0] + arr[1])
```

**A:** `dtype` is `<U21` (Unicode string). `arr[0] + arr[1]` gives `'12'` (string concatenation, NOT 3). The "4" forced all elements to strings.

**Q2: What is the output?**

```python
arr = np.array([1, 2, 3])
print(arr.reshape(1, 3).ndim)
print(arr.ndim)
```

**A:** First prints `2` (reshape to (1,3) is 2D). Second prints `1` (original is still 1D). `reshape` returns a view with different shape but doesn't modify the original's shape.

**Q3: What is the output?**

```python
a = np.empty((2, 2), dtype=np.int8)
print(a.sum() == 0)
```

**A:** **Unpredictable.** The values are uninitialized garbage. It MIGHT be True (if memory happened to be zero) or False. Never rely on `empty` values.

### One-Liner Challenges

**Challenge 1:** Create a 5×5 identity matrix with int8 dtype.

```python
np.eye(5, dtype=np.int8)
```

**Challenge 2:** Create an array [0, 0.5, 1.0, 1.5, 2.0] using linspace.

```python
np.linspace(0, 2, 5)
```

**Challenge 3:** Reshape a 12-element 1D array to 3×4 using -1.

```python
np.arange(12).reshape(3, -1)
```

### Explain the Difference

**Q1: `np.zeros((3,))` vs `np.zeros((1, 3))`**
A: `(3,)` is a 1D array with 3 elements (ndim=1). `(1, 3)` is a 2D row vector (ndim=2). They hold the same data but behave differently in broadcasting and matrix multiplication.

**Q2: `np.array([1, 2, "3"])` vs `np.array([1, 2, "3"], dtype=float)`**
A: Without dtype: all elements become strings → `['1', '2', '3']` with dtype `<U21`. With `dtype=float`: the string `"3"` is converted to `3.0` → `[1., 2., 3.]` with dtype `float64`.

---

## Practice Problems

### Problem 1: Array Inspector (Beginner)

**Task:** Create a 3×4 array of random values and print all 7 attributes (shape, ndim, size, dtype, itemsize, nbytes, T).

**Starter code:**

```python
import numpy as np
arr = np.random.rand(3, 4)
# Print all 7 attributes
```

**Expected Output:**

```
Shape: (3, 4)
Ndim: 2
Size: 12
Dtype: float64
Itemsize: 8
Nbytes: 96
Transpose shape: (4, 3)
```

**Solution:**

```python
import numpy as np
arr = np.random.rand(3, 4)
print(f"Shape: {arr.shape}")
print(f"Ndim: {arr.ndim}")
print(f"Size: {arr.size}")
print(f"Dtype: {arr.dtype}")
print(f"Itemsize: {arr.itemsize}")
print(f"Nbytes: {arr.nbytes}")
print(f"Transpose shape: {arr.T.shape}")
```

### Problem 2: Memory Calculator (Intermediate)

**Task:** Create arrays of 1,000,000 elements using int8, int32, and float64. Compare their memory usage using `.nbytes` and print the savings.

**Starter code:**

```python
import numpy as np
# Create 3 arrays of 1M elements with different dtypes
# Print nbytes for each and calculate savings
```

**Expected Output (approximate):**

```
int8:    1000000 bytes (0.95 MB)
int32:   4000000 bytes (3.81 MB)
float64: 8000000 bytes (7.63 MB)
float64 uses 8.0x more memory than int8
```

**Solution:**

```python
import numpy as np

arr_i8  = np.zeros(1_000_000, dtype=np.int8)
arr_i32 = np.zeros(1_000_000, dtype=np.int32)
arr_f64 = np.zeros(1_000_000, dtype=np.float64)

for name, arr in [("int8", arr_i8), ("int32", arr_i32), ("float64", arr_f64)]:
    print(f"{name:8s}: {arr.nbytes} bytes ({arr.nbytes / 1024**2:.2f} MB)")

print(f"float64 uses {arr_f64.nbytes / arr_i8.nbytes:.1f}x more memory than int8")
```

### Problem 3: Reshape Pipeline (Advanced / Real-World)

**Task:** Simulate a batch of 6 grayscale images (8×8 pixels). Create them, reshape to a flat feature matrix for ML, then reshape back.

**Starter code:**

```python
import numpy as np
# 1. Create 6 "images" as (6, 8, 8) array with random values [0, 255]
# 2. Reshape to (6, 64) feature matrix for ML model input
# 3. Reshape back to (6, 8, 8) to verify no data loss
# 4. Verify the round-trip is lossless
```

**Expected Output:**

```
Original shape: (6, 8, 8)
Feature matrix shape: (6, 64)
Restored shape: (6, 8, 8)
Round-trip lossless: True
```

**Solution:**

```python
import numpy as np

rng = np.random.default_rng(42)
images = rng.integers(0, 256, size=(6, 8, 8), dtype=np.uint8)
print(f"Original shape: {images.shape}")

features = images.reshape(6, -1)
print(f"Feature matrix shape: {features.shape}")

restored = features.reshape(6, 8, 8)
print(f"Restored shape: {restored.shape}")

print(f"Round-trip lossless: {np.array_equal(images, restored)}")
```

---

## Visualization Quick-Reference

```python
import matplotlib.pyplot as plt
import numpy as np

fig, axes = plt.subplots(1, 3, figsize=(12, 4))

axes[0].imshow(np.eye(5), cmap='Blues')
axes[0].set_title('np.eye(5)')

axes[1].imshow(np.zeros((5, 5)), cmap='Blues', vmin=0, vmax=1)
axes[1].set_title('np.zeros((5,5))')

axes[2].imshow(np.random.rand(5, 5), cmap='Blues')
axes[2].set_title('np.random.rand(5,5)')

plt.tight_layout()
plt.show()
```

---

## Mnemonics & Memory Aids

- **"SALON"** — Five key attributes: **S**hape, **A**ttributes (ndim, size), **L**ayout (itemsize, nbytes), **O**rientation (.T), **N**ature (dtype)
- **"A for Amount, L for Length"** — `np.arange()` uses step **A**mount, `np.linspace()` uses **L**ength (count)
- **"One string poisons the whole array"** — Any string element upcasts everything to string
- **"Product must match"** — When reshaping, `np.prod(old_shape) == np.prod(new_shape)`
- **"Negative one = let NumPy think"** — Use `-1` in reshape for one auto-calculated dimension
- **"Empty = unErasedMemory"** — `np.empty()` has random garbage, not zeros
- **"T for Transpose, T for Two-way"** — `.T` is a view, changes go both ways

---

## Conceptual Diagrams (ASCII)

### Array Creation Function Family

```
                    Array Creation Functions
                           │
            ┌──────────────┼──────────────────┐
            │              │                   │
      From Data      From Scratch         Random
            │              │                   │
       np.array()    ┌─────┼─────┐       np.random.rand()
                     │     │     │       np.random.default_rng()
              np.zeros() np.ones() np.full()
              np.empty() np.eye()
              np.arange() np.linspace()
```

### Dimension Hierarchy

```
Scalar (0D)     Vector (1D)     Matrix (2D)      Tensor (3D)
np.array(5)     np.array(       np.array(        np.zeros(
                 [1,2,3])        [[1,2],           (2,3,4))
                                  [3,4]])

shape: ()       shape: (3,)     shape: (2,2)     shape: (2,3,4)
ndim:  0        ndim:  1        ndim:  2         ndim:  3
size:  1        size:  3        size:  4         size:  24
```

### Memory Layout

```
Python List:                    NumPy Array:
┌───┐  ┌──────────┐            ┌──────────────────┐
│ * │→ │ PyObj: 1  │            │ 1 │ 2 │ 3 │ 4 │ 5│  ← contiguous
├───┤  └──────────┘            └──────────────────┘
│ * │→ │ PyObj: 2  │  (scattered)  (one block, typed)
├───┤  └──────────┘
│ * │→ │ PyObj: 3  │            dtype: int64
├───┤  └──────────┘            itemsize: 8 bytes
│ * │→ │ PyObj: 4  │            nbytes: 40 bytes
└───┘  └──────────┘
~28 bytes per element            8 bytes per element
```

---

## Quick-Reference Cheat Sheet

```python
import numpy as np

# === CREATION ===
np.array([1, 2, 3])                  # From list
np.array([[1,2],[3,4]])              # 2D from nested list
np.array([1,2,3], dtype=np.int8)     # With explicit dtype
np.arange(1, 11, 2)                  # [1, 3, 5, 7, 9]
np.linspace(0, 1, 5)                 # [0, 0.25, 0.5, 0.75, 1.0]
np.zeros((3, 4))                     # 3×4 zeros (float64)
np.ones((2, 3))                      # 2×3 ones (float64)
np.full((2, 3), 7)                   # 2×3 filled with 7
np.eye(3)                            # 3×3 identity matrix
np.empty((2, 2))                     # Uninitialized (fast, unsafe)
np.random.rand(2, 3)                 # Random [0,1), shape 2×3

# === ATTRIBUTES ===
arr.shape      # (rows, cols)
arr.ndim       # number of dimensions
arr.size       # total elements
arr.dtype      # element data type
arr.itemsize   # bytes per element
arr.nbytes     # total bytes (size × itemsize)
arr.T          # transpose (view)

# === RESHAPE ===
arr.reshape(2, 3)     # New shape, same data
arr.reshape(-1, 3)    # Auto-calculate first dim
arr.reshape(1, -1)    # Row vector

# === TYPE INFO ===
type(arr)         # numpy.ndarray
type(arr[0])      # numpy.int64 (or dtype-specific)
arr.dtype         # int64, float64, etc.
```

---

## Summary (TL;DR)

1. **NumPy exists because Python lists are slow** for numerical operations — NumPy was ~24.5x faster for adding 1M elements in the notebook benchmark
2. **`np.array()` is the primary constructor** — converts lists to typed, contiguous arrays; beware of silent type upcasting with mixed types
3. **Nine creation functions** cover all needs: `array`, `arange`, `linspace`, `zeros`, `ones`, `full`, `eye`, `empty`, `random.rand`
4. **All arrays are homogeneous** — one dtype for all elements; mixed types trigger upcasting (int+str → str)
5. **Seven key attributes** tell you everything about an array: `shape`, `ndim`, `size`, `dtype`, `itemsize`, `nbytes`, `.T`
6. **`reshape()` changes shape, not data** — total elements must match; use `-1` for auto-inference; returns a view
7. **`dtype` controls memory and precision** — `int8` uses 1 byte vs `int64`'s 8 bytes; explicit dtype is safer than auto-detection
8. **`np.empty()` is fast but dangerous** — values are uninitialized garbage; only use when immediately overwriting

---

## Further Reading & Official Docs

- [NumPy Array Creation](https://numpy.org/doc/stable/user/basics.creation.html)
- [NumPy Array Basics](https://numpy.org/doc/stable/user/basics.html)
- [ndarray Reference](https://numpy.org/doc/stable/reference/arrays.ndarray.html)
- [Data Types](https://numpy.org/doc/stable/reference/arrays.dtypes.html)
- [NumPy for MATLAB Users](https://numpy.org/doc/stable/user/numpy-for-matlab-users.html)
- [NumPy 2.0 Migration Guide](https://numpy.org/doc/stable/numpy_2_0_migration_guide.html)
- [Random Sampling (Modern API)](https://numpy.org/doc/stable/reference/random/generator.html)

---

## Flashcard-Style Recall

**Q:** What does `np.array([1, "hello"])` produce?
**A:** `array(['1', 'hello'], dtype='<U21')` — integers upcasted to strings.

**Q:** Is `stop` inclusive in `arange` or `linspace`?
**A:** `arange` → exclusive. `linspace` → inclusive (by default).

**Q:** What's the difference between `(6,)` and `(1, 6)`?
**A:** `(6,)` is 1D (ndim=1), `(1, 6)` is 2D row vector (ndim=2).

**Q:** How much memory does a (100, 100) float64 array use?
**A:** 100 × 100 × 8 = 80,000 bytes = ~78 KB.

**Q:** Can you reshape a 7-element array to (2, 4)?
**A:** No — 2 × 4 = 8 ≠ 7. Total elements must match.

**Q:** What does `np.eye(3, 2)` look like?
**A:** 3×2 matrix: `[[1, 0], [0, 1], [0, 0]]` — ones on diagonal where row==col.

**Q:** Is `.T` a view or copy?
**A:** View — modifying the transpose modifies the original.

**Q:** What's the default dtype for `np.zeros(5)`?
**A:** `float64`.

**Q:** What does `-1` mean in `reshape(3, -1)`?
**A:** "Auto-calculate this dimension" — NumPy infers it from total size / other dims.

**Q:** Why is `np.empty()` faster than `np.zeros()`?
**A:** It skips memory initialization — allocated but not written to.
