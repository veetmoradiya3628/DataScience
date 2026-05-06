# NUMPY FUNDAMENTALS: ARRAYS, VECTORIZED OPERATIONS, UNIVERSAL FUNCTIONS, INDEXING, SLICING & STATISTICAL OPERATIONS

---

## Prerequisites
- Python basics: variables, data types, loops, functions
- Python lists: creation, indexing, nesting, iteration
- Understanding of basic mathematics: mean, median, standard deviation, variance
- Basic understanding of what a matrix is (rows × columns)
- Python package installation (`pip install`)
- Jupyter Notebook basics (running cells, markdown vs code cells)

---

## Environment Setup

```python
# Installation (choose one method):

# Method 1: Terminal / requirements.txt
# Add 'numpy' to requirements.txt, then:
# pip install -r requirements.txt

# Method 2: Directly in Jupyter Notebook
# !pip install numpy
```

```python
import numpy as np
```

> **Why `as np`?** This is the universally adopted alias. Every NumPy tutorial, Stack Overflow answer, and production codebase uses `np`. Writing `numpy.array()` everywhere is verbose — `np.array()` is the convention. **Never** use a different alias.

---

## Concept Overview

**What:** NumPy (Numerical Python) is the fundamental library for scientific computing in Python. It provides a high-performance multidimensional array object (`ndarray`), along with a vast collection of mathematical functions that operate on these arrays.

**Why it exists:** Python lists are flexible but painfully slow for numerical computation because they store heterogeneous objects with pointer indirection. NumPy solves this by providing a contiguous, homogeneously-typed memory block with operations implemented in optimized C/Fortran code. This makes element-wise mathematical operations **50–100x faster** than equivalent Python loops.

**Real-world analogy:** Think of a Python list as a filing cabinet where each drawer can hold anything — a document, a shoe, a book. Finding and processing items requires opening each drawer individually. A NumPy array is like a specialized spreadsheet with uniform cells — the computer knows exactly where every number lives and can process entire columns in one sweep, like a printing press stamping every page at once instead of handwriting each one.

**Where it fits:**
```
Python Ecosystem for Data Science
├── NumPy          ← Foundation (arrays, math)  ← YOU ARE HERE
├── Pandas         ← DataFrames (built ON NumPy)
├── Matplotlib     ← Visualization
├── Seaborn        ← Statistical Visualization
├── Scikit-learn   ← Machine Learning (uses NumPy internally)
├── TensorFlow     ← Deep Learning (NumPy-compatible tensors)
└── PyTorch        ← Deep Learning (shares memory with NumPy)
```

NumPy is the bedrock — even deep learning frameworks use NumPy arrays for data preprocessing, weight initialization, and result analysis.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| `ndarray` | N-dimensional array — NumPy's core data structure. A grid of values, all of the same type, indexed by a tuple of non-negative integers |
| `shape` | Tuple describing array dimensions, e.g., `(3, 4)` means 3 rows, 4 columns |
| `dtype` | Data type of array elements (e.g., `int32`, `float64`). All elements share the same dtype |
| `ndim` | Number of dimensions (axes). 1D → `ndim=1`, 2D → `ndim=2`, etc. |
| `size` | Total number of elements in the array (product of shape dimensions) |
| `itemsize` | Size in bytes of each element |
| `reshape` | Change array dimensions without changing data (total elements must remain the same) |
| `vectorization` | Applying operations to entire arrays at once instead of looping element-by-element |
| `broadcasting` | NumPy's ability to perform operations on arrays of different shapes by automatically expanding dimensions |
| `ufunc` | Universal function — a function that operates element-wise on ndarrays (e.g., `np.sqrt`, `np.exp`) |
| `boolean indexing` | Selecting array elements using a boolean condition (e.g., `arr[arr > 5]`) |

---

## PART 1: CREATING NUMPY ARRAYS

---

### Syntax & Definition

```python
# Core array creation
np.array(object, dtype=None, copy=True, order='K', subok=False, ndmin=0)
```

| Parameter | Purpose |
|-----------|---------|
| `object` | Input data — list, tuple, or nested sequences |
| `dtype` | Desired data type (e.g., `np.float64`, `np.int32`). Auto-inferred if omitted |
| `copy` | Whether to copy data. **NumPy 2.x change:** `copy=False` now raises an error if a copy is unavoidable; use `copy=None` for old 1.x behavior (copy only if needed) |
| `ndmin` | Minimum number of dimensions in the result |

---

### Example 1: Creating a 1D Array [BEGINNER]

```python
import numpy as np

arr1 = np.array([1, 2, 3, 4, 5])

print(arr1)
print(type(arr1))
print(arr1.shape)
```

**Output:**

```
[1 2 3 4 5]
<class 'numpy.ndarray'>
(5,)
```

**Line-by-line breakdown:**

* `np.array([1, 2, 3, 4, 5])` — Creates a 1D ndarray from a Python list. NumPy allocates a contiguous block of memory and stores all 5 integers sequentially.
* `type(arr1)` — Returns `numpy.ndarray`, confirming this is NOT a Python list. Despite looking similar when printed (no commas between elements), it's a fundamentally different data structure.
* `arr1.shape` → `(5,)` — The trailing comma indicates a **tuple with one element**. This means: 1 dimension with 5 elements. There are no rows or columns — it's a flat vector.

**Why this works:**

A 1D array is the simplest ndarray. The shape `(5,)` means "5 elements along a single axis." Do NOT think of it as "1 row and 5 columns" — that's a 2D concept. A 1D array has **no rows or columns**, only elements along one axis.

**Visual:**

```
1D array (5,):
┌───┬───┬───┬───┬───┐
│ 1 │ 2 │ 3 │ 4 │ 5 │
└───┴───┴───┴───┴───┘
  ↑ single axis, 5 elements, no row/column concept
```

> **Mnemonic:** "One number in shape = one dimension." `(5,)` → 1D. `(2, 5)` → 2D. `(3, 2, 5)` → 3D.

---

### Example 2: Creating a 2D Array [BEGINNER]

**Method 1: Using `reshape()`**

```python
import numpy as np

arr1 = np.array([1, 2, 3, 4, 5])

arr2d = arr1.reshape(1, 5)  # 1 row, 5 columns
print(arr2d)
print(arr2d.shape)
```

**Output:**

```
[[1 2 3 4 5]]
(1, 5)
```

**Line-by-line breakdown:**

* `arr1.reshape(1, 5)` — Reshapes the 1D array of 5 elements into a 2D array with 1 row and 5 columns. The total number of elements (5) must remain unchanged: `1 × 5 = 5 ✓`.
* The output `[[1 2 3 4 5]]` shows **two** brackets opening and closing — this is how you visually identify a 2D array. A 1D array has one bracket level; a 2D array has two; a 3D array has three.

**Why `reshape(1, 4)` would fail:**

```python
arr1.reshape(1, 4)  # ValueError: cannot reshape array of size 5 into shape (1,4)
```

The product of the new shape dimensions **must equal** the total number of elements. `1 × 4 = 4 ≠ 5`, so it fails.

> **Rule:** `np.prod(new_shape) == arr.size` must always be True.

**Method 2: Using nested lists directly**

```python
import numpy as np

arr2 = np.array([[1, 2, 3, 4, 5]])
print(arr2)
print(arr2.shape)
```

**Output:**

```
[[1 2 3 4 5]]
(1, 5)
```

A list inside a list creates a 2D array directly. Each inner list becomes one row.

**Method 3: Multiple rows**

```python
import numpy as np

arr2 = np.array([
    [1, 2, 3, 4, 5],
    [2, 3, 4, 5, 6]
])

print(arr2)
print(arr2.shape)
```

**Output:**

```
[[1 2 3 4 5]
 [2 3 4 5 6]]
(2, 5)
```

**Line-by-line breakdown:**

* Two inner lists → two rows. Each has 5 elements → 5 columns.
* Shape `(2, 5)` → 2 rows, 5 columns. Total elements: `2 × 5 = 10`.

**Visual:**

```
2D array (2, 5):
         col0  col1  col2  col3  col4
row0  ┌─────┬─────┬─────┬─────┬─────┐
      │  1  │  2  │  3  │  4  │  5  │
row1  ├─────┼─────┼─────┼─────┼─────┤
      │  2  │  3  │  4  │  5  │  6  │
      └─────┴─────┴─────┴─────┴─────┘
```

> **Dimension Identification Trick:** Count the opening brackets at the start of the printed output.
> - `[1 2 3]` → 1 bracket → 1D
> - `[[1 2 3]]` → 2 brackets → 2D
> - `[[[1 2 3]]]` → 3 brackets → 3D

---

### Example 3: Arrays with Built-in Functions [BEGINNER]

#### `np.arange()` — Range with Step

```python
import numpy as np

arr = np.arange(0, 10, 2)
print(arr)
print(arr.shape)
```

**Output:**

```
[0 2 4 6 8]
(5,)
```

**Breakdown:**
- `np.arange(start, stop, step)` — Like Python's `range()` but returns an ndarray.
- `np.arange(0, 10, 2)` → Start at 0, go up to (not including) 10, step by 2.
- Result: `[0, 2, 4, 6, 8]` — 5 elements.

**Reshape to 2D:**

```python
arr_2d = np.arange(0, 10, 2).reshape(5, 1)
print(arr_2d)
print(arr_2d.shape)
```

**Output:**

```
[[0]
 [2]
 [4]
 [6]
 [8]]
(5, 1)
```

Shape `(5, 1)` → 5 rows, 1 column. A column vector.

---

#### `np.ones()` — Array of Ones

```python
import numpy as np

ones_arr = np.ones((3, 4))
print(ones_arr)
```

**Output:**

```
[[1. 1. 1. 1.]
 [1. 1. 1. 1.]
 [1. 1. 1. 1.]]
```

**Breakdown:**
- `np.ones((3, 4))` — Creates a 3×4 array filled with `1.0` (default dtype is `float64`).
- Note the decimal points (`1.`), confirming float type.
- **Deep learning use case:** Initializing weight matrices to ones (or zeros) before training begins. You could do `np.ones((784, 256))` to create a weight matrix for a neural network layer.

> **Why not build manually?** Creating a 784×256 matrix from nested lists would require writing 200,704 values. `np.ones()` does it in one call.

---

#### `np.zeros()` — Array of Zeros

```python
import numpy as np

zeros_arr = np.zeros((3, 4))
print(zeros_arr)
```

**Output:**

```
[[0. 0. 0. 0.]
 [0. 0. 0. 0.]
 [0. 0. 0. 0.]]
```

**Use case:** Initializing weights to zero, creating placeholder arrays, or building accumulator arrays for aggregation.

---

#### `np.eye()` — Identity Matrix

```python
import numpy as np

identity = np.eye(3)
print(identity)
```

**Output:**

```
[[1. 0. 0.]
 [0. 1. 0.]
 [0. 0. 1.]]
```

**Breakdown:**
- `np.eye(N)` — Creates an N×N identity matrix: diagonal elements are 1, everything else is 0.
- The identity matrix is the multiplicative identity for matrix multiplication: `A @ I = A`.
- Used heavily in linear algebra, solving systems of equations, and as initialization in ML algorithms.

**Non-square identity matrix:**

```python
identity_rect = np.eye(3, 2)
print(identity_rect)
```

**Output:**

```
[[1. 0.]
 [0. 1.]
 [0. 0.]]
```

3 rows, 2 columns — diagonal still has ones, rest zeros.

---

## PART 2: ARRAY ATTRIBUTES

---

### Complete Attributes Reference [BEGINNER]

```python
import numpy as np

arr = np.array([[1, 2, 3], [4, 5, 6]])

print("Array:\n", arr)
print("Shape:", arr.shape)       # dimensions as tuple
print("Ndim:", arr.ndim)         # number of dimensions
print("Size:", arr.size)         # total number of elements
print("Dtype:", arr.dtype)       # data type of elements
print("Itemsize:", arr.itemsize) # bytes per element
print("Nbytes:", arr.nbytes)     # total bytes consumed (size × itemsize)
```

**Output:**

```
Array:
 [[1 2 3]
 [4 5 6]]
Shape: (2, 3)
Ndim: 2
Size: 6
Dtype: int64
Itemsize: 8
Nbytes: 48
```

**Line-by-line breakdown:**

| Attribute | Value | Meaning |
|-----------|-------|---------|
| `shape` | `(2, 3)` | 2 rows, 3 columns |
| `ndim` | `2` | Two-dimensional array |
| `size` | `6` | Total elements: 2 × 3 = 6 |
| `dtype` | `int64` | Each element is a 64-bit integer (platform-dependent; may show `int32` on Windows) |
| `itemsize` | `8` | Each int64 occupies 8 bytes |
| `nbytes` | `48` | Total memory: 6 elements × 8 bytes = 48 bytes |

> **Platform Note:** `dtype` may vary by OS. macOS/Linux typically default to `int64`; older Windows NumPy defaulted to `int32`. **NumPy 2.x change:** Windows now defaults to `int64` as well (aligned with other platforms).

---

## How It Works Internally

### ndarray Memory Layout

```
ndarray object
├── data buffer   → contiguous block of memory holding the actual values
├── shape         → (2, 3) — tells NumPy how to interpret the flat buffer as 2D
├── strides       → (24, 8) — bytes to jump to reach the next row / next column
├── dtype         → int64 — tells NumPy how to interpret each 8-byte chunk
├── flags         → C_CONTIGUOUS, OWNDATA, WRITEABLE, etc.
└── base          → None (if this array owns its data) or reference to parent array (if view)
```

**Memory visualization for `np.array([[1, 2, 3], [4, 5, 6]])` (int64, C-order):**

```
Logical view:          Physical memory (contiguous):
┌───┬───┬───┐
│ 1 │ 2 │ 3 │  ───→  [1][2][3][4][5][6]
├───┼───┼───┤          ↑        ↑
│ 4 │ 5 │ 6 │          row 0    row 1
└───┴───┴───┘

Strides: (24, 8) → jump 24 bytes (3 × 8) to next row, 8 bytes to next column
```

**Why this matters:** Because data is stored contiguously, the CPU can load chunks into cache lines efficiently. Python lists store pointers to scattered objects — each access is a cache miss.

### Views vs Copies

```
VIEWS (shared memory):                COPIES (independent memory):
arr = np.array([1,2,3,4,5])          arr = np.array([1,2,3,4,5])
view = arr[1:4]                       copy = arr[1:4].copy()

arr:  [1][2][3][4][5]                 arr:  [1][2][3][4][5]
       ↑  ↑  ↑                        
view:  [2][3][4]  (same memory!)      copy: [2][3][4]  (separate memory)

view[0] = 99  → arr becomes           copy[0] = 99  → arr unchanged
[1, 99, 3, 4, 5]                      [1, 2, 3, 4, 5]
```

**Key rule:**
- **Basic slicing** (`arr[1:4]`) → returns a **view** (O(1) time, shared memory)
- **Fancy indexing** (`arr[[1,2,3]]`) → returns a **copy** (O(n) time, new memory)
- **Boolean indexing** (`arr[arr > 2]`) → returns a **copy**

---

## PART 3: VECTORIZED OPERATIONS

---

### Concept Overview

**What:** Vectorized operations apply a mathematical operation to every element of an array simultaneously, without writing explicit Python loops.

**Why it exists:** Python `for` loops are interpreted line-by-line and incredibly slow for numerical work. Vectorization delegates the loop to NumPy's C backend, achieving near-C speed with Python syntax.

**Real-world analogy:** Imagine stamping 1000 envelopes. A Python loop is you stamping them one by one. Vectorization is a machine that stamps all 1000 in one press.

---

### Example 1: Element-wise Arithmetic [BEGINNER]

```python
import numpy as np

arr1 = np.array([1, 2, 3, 4, 5])
arr2 = np.array([10, 20, 30, 40, 50])

print("Addition:", arr1 + arr2)
print("Subtraction:", arr1 - arr2)
print("Multiplication:", arr1 * arr2)
print("Division:", arr1 / arr2)
```

**Output:**

```
Addition: [11 22 33 44 55]
Subtraction: [ -9 -18 -27 -36 -45]
Multiplication: [ 10  40  90 160 250]
Division: [0.1 0.1 0.1 0.1 0.1]
```

**Line-by-line breakdown:**

* `arr1 + arr2` — Element at index 0 of `arr1` (1) is added to element at index 0 of `arr2` (10) → 11. Same for all positions.
* `arr1 * arr2` — Element-wise multiplication: `1×10=10`, `2×20=40`, `3×30=90`, etc.
* `arr1 / arr2` — Returns `float64` results (division always produces floats in NumPy).

**Critical:** These are **element-wise** operations, NOT matrix multiplication. `arr1 * arr2` multiplies corresponding elements. For matrix multiplication, use `@` operator or `np.dot()`.

**Visual:**

```
arr1:  [ 1,  2,  3,  4,  5]
         ×   ×   ×   ×   ×     ← element-wise
arr2:  [10, 20, 30, 40, 50]
         ↓   ↓   ↓   ↓   ↓
result:[10, 40, 90,160,250]
```

**Return type:** All results are `numpy.ndarray`, never Python lists.

---

## Before NumPy vs After NumPy (Side-by-Side)

**Task:** Add corresponding elements of two sequences of 5 numbers.

**Pure Python:**

```python
list1 = [1, 2, 3, 4, 5]
list2 = [10, 20, 30, 40, 50]

result = []
for i in range(len(list1)):
    result.append(list1[i] + list2[i])
print(result)  # [11, 22, 33, 44, 55]
```

**NumPy:**

```python
import numpy as np

arr1 = np.array([1, 2, 3, 4, 5])
arr2 = np.array([10, 20, 30, 40, 50])

result = arr1 + arr2
print(result)  # [11 22 33 44 55]
```

**Speedup:** ~50–100x faster for large arrays (millions of elements) | **Lines of code:** reduced from 4 to 1

**Why the performance difference?**

| Aspect | Python Loop | NumPy Vectorized |
|--------|-------------|------------------|
| Execution | Interpreted Python bytecode per iteration | Single C-level loop over contiguous memory |
| Type checking | Checks type of every element every iteration | Knows dtype once, applies uniformly |
| Memory access | Pointer chasing (objects scattered in heap) | Sequential scan of contiguous buffer (cache-friendly) |
| Overhead | Function call overhead per `append()` | One function call for entire operation |

---

## PART 4: UNIVERSAL FUNCTIONS (ufuncs)

---

### Concept Overview

**What:** Universal functions (ufuncs) are NumPy functions that operate element-wise on ndarrays. They are the vectorized wrappers around simple functions, implemented in compiled C code.

**Why they exist:** Instead of writing loops to apply `sqrt()` to every element, ufuncs let you write `np.sqrt(arr)` — one call processes the entire array at C speed.

**Real-world analogy:** A ufunc is like a factory assembly line. Instead of one worker computing square roots one number at a time, the assembly line processes all numbers simultaneously on a conveyor belt.

---

### Example: Common ufuncs [BEGINNER]

```python
import numpy as np

arr = np.array([1, 2, 3, 4, 5, 6])

print("Square root:", np.sqrt(arr))
print("Exponential:", np.exp(arr))
print("Sine:", np.sin(arr))
print("Natural log:", np.log(arr))
```

**Output:**

```
Square root: [1.         1.41421356 1.73205081 2.         2.23606798 2.44948975]
Exponential: [  2.71828183   7.3890561   20.08553692  54.59815003 148.4131591
 403.42879349]
Sine: [ 0.84147098  0.90929743  0.14112001 -0.7568025  -0.95892427 -0.2794155 ]
Natural log: [0.         0.69314718 1.09861229 1.38629436 1.60943791 1.79175947]
```

**Line-by-line breakdown:**

* `np.sqrt(arr)` — Computes the square root of each element. `√1=1`, `√2≈1.414`, `√3≈1.732`, etc. Returns `float64`.
* `np.exp(arr)` — Computes `e^x` for each element. `e^1≈2.718`, `e^2≈7.389`, etc. Grows exponentially fast.
* `np.sin(arr)` — Computes the sine of each element (input in **radians**, not degrees). To use degrees: `np.sin(np.radians(arr))`.
* `np.log(arr)` — Computes the natural logarithm (base *e*) of each element. `ln(1)=0`, `ln(2)≈0.693`. For `log10`, use `np.log10()`. For `log2`, use `np.log2()`.

**Common ufuncs reference:**

| Function | Operation | Notes |
|----------|-----------|-------|
| `np.sqrt(x)` | √x | Equivalent to `x ** 0.5` |
| `np.exp(x)` | eˣ | Base-e exponential |
| `np.log(x)` | ln(x) | Natural log; `np.log2()`, `np.log10()` for other bases |
| `np.sin(x)` | sin(x) | Input in radians |
| `np.cos(x)` | cos(x) | Input in radians |
| `np.abs(x)` | \|x\| | Absolute value |
| `np.power(x, n)` | xⁿ | Element-wise power |
| `np.round(x, d)` | Round to d decimals | |
| `np.floor(x)` | ⌊x⌋ | Round down |
| `np.ceil(x)` | ⌈x⌉ | Round up |

> **Performance:** Every ufunc executes in compiled C. `np.sqrt(arr)` on a million-element array is ~100x faster than `[math.sqrt(x) for x in list_data]`.

---

## PART 5: ARRAY INDEXING & SLICING

---

### Concept Overview

**What:** Indexing selects specific elements from an array by position. Slicing extracts sub-arrays (contiguous chunks). Together, they are the primary tools for reading and modifying array data.

**Why it exists:** Real-world data lives in matrices (images, spreadsheets, sensor readings). You constantly need to extract specific rows, columns, regions, or elements meeting certain criteria.

**Real-world analogy:** An array is a spreadsheet. Indexing is clicking on a specific cell (e.g., row 2, column 3). Slicing is selecting a range of cells (e.g., rows 1–3, columns 2–4) — you highlight a rectangular block.

---

### 2D Array Indexing [BEGINNER]

```python
import numpy as np

arr = np.array([
    [1,  2,  3,  4],
    [5,  6,  7,  8],
    [9, 10, 11, 12]
])

print("Array:\n", arr)
print("Shape:", arr.shape)
```

**Output:**

```
Array:
 [[ 1  2  3  4]
 [ 5  6  7  8]
 [ 9 10 11 12]]
Shape: (3, 4)
```

**Visual index map:**

```
           col0  col1  col2  col3
row0   ┌─────┬─────┬─────┬─────┐
       │  1  │  2  │  3  │  4  │
row1   ├─────┼─────┼─────┼─────┤
       │  5  │  6  │  7  │  8  │
row2   ├─────┼─────┼─────┼─────┤
       │  9  │ 10  │ 11  │ 12  │
       └─────┴─────┴─────┴─────┘
```

---

### Accessing a Single Element [BEGINNER]

```python
element = arr[0, 0]
print("Element at [0,0]:", element)
```

**Output:**

```
Element at [0,0]: 1
```

**Syntax:** `arr[row_index, column_index]`

- First number = **which row** (0-indexed)
- Second number = **which column** (0-indexed)
- `arr[0, 0]` → row 0, column 0 → value `1`
- `arr[1, 2]` → row 1, column 2 → value `7`
- `arr[2, 3]` → row 2, column 3 → value `12`

**Big-O:** O(1) — direct memory address calculation using strides. No iteration needed.

---

### Slicing Sub-Arrays [INTERMEDIATE]

**Syntax:** `arr[row_start:row_stop, col_start:col_stop]`

- `start` is **inclusive**, `stop` is **exclusive** (same as Python lists)
- Omitting `start` means "from the beginning" (index 0)
- Omitting `stop` means "to the end" (last index inclusive)

**Example 1: Extract `7, 8, 11, 12`**

```python
import numpy as np

arr = np.array([
    [1,  2,  3,  4],
    [5,  6,  7,  8],
    [9, 10, 11, 12]
])

sub = arr[1:, 2:]
print(sub)
```

**Output:**

```
[[ 7  8]
 [11 12]]
```

**Step-by-step reasoning:**

1. **Target elements:** `7, 8, 11, 12`
2. **Which rows?** Row 1 (`[5,6,7,8]`) and Row 2 (`[9,10,11,12]`) → `1:` (from row 1 to end)
3. **Which columns?** Column 2 and Column 3 → `2:` (from column 2 to end)
4. **Combined:** `arr[1:, 2:]`

**Visual:**

```
           col0  col1  col2  col3
row0       │  1  │  2  │  3  │  4  │
row1       │  5  │  6  │ [7] │ [8] │  ← row 1:
row2       │  9  │ 10  │[11] │[12] │  ← row 2 (end)
                        ↑col2  ↑col3
```

---

**Example 2: Extract `3, 4, 7, 8`**

```python
sub = arr[0:2, 2:]
print(sub)
```

**Output:**

```
[[3 4]
 [7 8]]
```

**Reasoning:** Rows 0–1 (`0:2`), Columns 2–end (`2:`).

---

**Example 3: Extract `6, 7, 10, 11`**

```python
sub = arr[1:, 1:3]
print(sub)
```

**Output:**

```
[[ 6  7]
 [10 11]]
```

**Reasoning:**
1. `6, 7` are in row 1; `10, 11` are in row 2 → rows `1:` (row 1 to end)
2. `6, 10` are in column 1; `7, 11` are in column 2 → columns `1:3` (column 1 and 2, stop before 3)

---

### Slicing Quick-Reference [INTERMEDIATE]

| Syntax | Meaning | Example on (3,4) array |
|--------|---------|------------------------|
| `arr[0]` | Entire row 0 | `[1, 2, 3, 4]` |
| `arr[:, 0]` | Entire column 0 | `[1, 5, 9]` |
| `arr[0, 0]` | Single element | `1` |
| `arr[1:, 2:]` | Bottom-right block | `[[7,8],[11,12]]` |
| `arr[:2, :2]` | Top-left block | `[[1,2],[5,6]]` |
| `arr[1:, 1:3]` | Middle-bottom block | `[[6,7],[10,11]]` |
| `arr[::2]` | Every other row | `[[1,2,3,4],[9,10,11,12]]` |

**Big-O:** Slicing is O(1) because it creates a **view** — just a new shape/strides metadata object pointing to the same data buffer. No data is copied.

---

## PART 6: MODIFYING ARRAY ELEMENTS

---

### Single Element Modification [BEGINNER]

```python
import numpy as np

arr = np.array([
    [1,  2,  3,  4],
    [5,  6,  7,  8],
    [9, 10, 11, 12]
])

arr[0, 0] = 100
print(arr)
```

**Output:**

```
[[100   2   3   4]
 [  5   6   7   8]
 [  9  10  11  12]]
```

**Breakdown:** Direct assignment to `arr[row, col]` modifies the element **in-place**. No new array is created.

---

### Bulk Modification via Slicing [INTERMEDIATE]

```python
arr[1:] = 100  # Set all elements from row 1 onward to 100
print(arr)
```

**Output:**

```
[[100   2   3   4]
 [100 100 100 100]
 [100 100 100 100]]
```

**Breakdown:** `arr[1:]` selects rows 1 and 2 (all columns). Assigning a scalar broadcasts `100` to every selected element. This is an **in-place** operation — the original array is modified.

> **Warning:** When you modify a **view** (a slice), the original array is also modified because they share memory. This is a common source of bugs.

```python
original = np.array([1, 2, 3, 4, 5])
view = original[1:4]
view[0] = 99
print(original)  # [1, 99, 3, 4, 5] — original changed!
```

To avoid this, use `.copy()`: `safe_slice = original[1:4].copy()`.

---

## PART 7: STATISTICAL OPERATIONS

---

### Normalization (Standard Scaling) [INTERMEDIATE]

**What:** Normalization (specifically, **standardization** or **z-score normalization**) transforms data so that it has a mean of 0 and a standard deviation of 1. This is fundamental in machine learning for ensuring features are on comparable scales.

**Formula:** `z = (x - μ) / σ`
- `x` = original value
- `μ` (mu) = mean of the data
- `σ` (sigma) = standard deviation of the data

```python
import numpy as np

data = np.array([1, 2, 3, 4, 5, 6, 7, 8, 9, 10], dtype=float)

mu = np.mean(data)
sigma = np.std(data)

normalized_data = (data - mu) / sigma

print("Original data:", data)
print("Mean:", mu)
print("Std deviation:", sigma)
print("Normalized data:", normalized_data)
print("Normalized mean:", np.mean(normalized_data))
print("Normalized std:", np.std(normalized_data))
```

**Output:**

```
Original data: [ 1.  2.  3.  4.  5.  6.  7.  8.  9. 10.]
Mean: 5.5
Std deviation: 2.8722813232690143
Normalized data: [-1.5666989  -1.21854359 -0.87038828 -0.52223297 -0.17407766
  0.17407766  0.52223297  0.87038828  1.21854359  1.5666989 ]
Normalized mean: 0.0
Normalized std: 1.0
```

**Line-by-line breakdown:**

* `np.mean(data)` → Computes the arithmetic mean: `(1+2+...+10)/10 = 5.5`
* `np.std(data)` → Computes the population standard deviation: `√(Σ(xᵢ-μ)²/N) ≈ 2.872`
* `(data - mu) / sigma` → **Vectorized operation.** Subtracts `5.5` from every element (broadcasting), then divides every result by `2.872` (broadcasting again). No loop needed.
* After normalization: `mean → 0.0`, `std → 1.0` ✓

**Why this matters in ML:**
- Features with different scales (e.g., age: 0–100, salary: 0–1,000,000) would cause gradient descent to oscillate wildly
- Normalization puts all features on the same scale, enabling faster and more stable convergence
- This exact operation is what `sklearn.preprocessing.StandardScaler` does internally

---

### Statistical Functions Reference [BEGINNER]

```python
import numpy as np

data = np.array([10, 20, 30, 40, 50, 60, 70, 80, 90, 100])

print("Mean:", np.mean(data))
print("Median:", np.median(data))
print("Std Deviation:", np.std(data))
print("Variance:", np.var(data))
```

**Output:**

```
Mean: 55.0
Median: 55.0
Std Deviation: 28.722813232690143
Variance: 825.0
```

| Function | Formula | Description |
|----------|---------|-------------|
| `np.mean(arr)` | Σxᵢ / N | Average of all elements |
| `np.median(arr)` | Middle value when sorted | 50th percentile; robust to outliers |
| `np.std(arr)` | √(Σ(xᵢ-μ)²/N) | Spread around the mean (population std) |
| `np.var(arr)` | Σ(xᵢ-μ)²/N | Square of standard deviation |
| `np.min(arr)` | Minimum value | |
| `np.max(arr)` | Maximum value | |
| `np.sum(arr)` | Σxᵢ | Sum of all elements |
| `np.cumsum(arr)` | Running total | `[10, 30, 60, 100, ...]` |

> **Note on `std()` and `var()`:** By default, NumPy computes the **population** variance/std (`ddof=0`). For **sample** variance/std (what Pandas uses by default), pass `ddof=1`: `np.std(data, ddof=1)`.

---

### Axis Operations [INTERMEDIATE]

```python
import numpy as np

arr = np.array([
    [1, 2, 3],
    [4, 5, 6]
])

print("Sum all:", np.sum(arr))         # 21
print("Sum axis=0:", np.sum(arr, axis=0))  # [5 7 9]   column-wise
print("Sum axis=1:", np.sum(arr, axis=1))  # [6 15]    row-wise
```

**ASCII Diagram:**

```
        col0  col1  col2
row0  [  1  ,  2  ,  3  ]  ─→ axis=1 sum → 6
row1  [  4  ,  5  ,  6  ]  ─→ axis=1 sum → 15
         ↓     ↓     ↓
axis=0: [5]   [7]   [9]    ← collapses rows (operates DOWN columns)
```

> **Mnemonic:** "axis=0 collapses ROWS (operates DOWN), axis=1 collapses COLUMNS (operates ACROSS)"

---

## PART 8: LOGICAL (BOOLEAN) OPERATIONS

---

### Concept Overview

**What:** Boolean operations apply conditions to arrays, producing boolean masks. These masks can then filter arrays to extract elements meeting specific criteria.

**Why it exists:** In data analysis, you constantly need to filter data — "give me all temperatures above 30°C" or "find all transactions over $1000". Boolean indexing makes this a one-liner.

**Real-world analogy:** It's like a bouncer at a club with a checklist. The boolean mask is the checklist (True = allowed in, False = rejected). The bouncer scans the entire array and only lets through elements marked True.

---

### Example 1: Simple Condition [BEGINNER]

```python
import numpy as np

data = np.array([1, 2, 3, 4, 5, 6, 7, 8, 9, 10])

mask = data > 5
print("Mask:", mask)
print("Filtered:", data[mask])
print("Direct:", data[data > 5])
```

**Output:**

```
Mask: [False False False False False  True  True  True  True  True]
Filtered: [ 6  7  8  9 10]
Direct: [ 6  7  8  9 10]
```

**Line-by-line breakdown:**

* `data > 5` — Applies the comparison element-wise. Each element is compared to 5. Returns a boolean array of the same shape.
* `data[mask]` — **Boolean indexing.** Only elements where `mask` is `True` are selected. This returns a **copy**, not a view.
* `data[data > 5]` — Shorthand combining both steps. This is the most common pattern in production code.

---

### Example 2: Compound Conditions [INTERMEDIATE]

```python
import numpy as np

data = np.array([1, 2, 3, 4, 5, 6, 7, 8, 9, 10])

filtered = data[(data >= 5) & (data <= 8)]
print(filtered)
```

**Output:**

```
[5 6 7 8]
```

**Critical syntax rules:**

| Do This | NOT This | Why |
|---------|----------|-----|
| `(data >= 5) & (data <= 8)` | `data >= 5 and data <= 8` | Python's `and` doesn't work on arrays — it can't evaluate element-wise. Use `&` (bitwise AND) |
| `(data >= 5) \| (data <= 2)` | `data >= 5 or data <= 2` | Use `\|` (bitwise OR) instead of `or` |
| `~(data > 5)` | `not (data > 5)` | Use `~` (bitwise NOT) instead of `not` |
| **Parentheses required** | `data >= 5 & data <= 8` | Without parentheses, `&` binds tighter than `>=`, causing unexpected behavior |

**Common Error:**

```python
# WRONG — causes ValueError
data[data >= 5 and data <= 8]
# ValueError: The truth value of an array with more than one element is ambiguous.

# CORRECT — use & with parentheses
data[(data >= 5) & (data <= 8)]
```

**Why the error:** Python's `and` tries to evaluate the *entire array* as a single boolean. But an array of booleans isn't one True/False — it's ambiguous. The `&` operator performs element-wise AND, which is what you want.

---

## Comparison Table: Python List vs NumPy Array

| Feature | Python List | NumPy Array |
|---------|-------------|-------------|
| **Type** | `list` | `numpy.ndarray` |
| **Element types** | Heterogeneous (mixed types) | Homogeneous (single dtype) |
| **Memory** | Stores pointers to objects (scattered) | Contiguous memory block |
| **Speed** | Slow for math (interpreted loops) | Fast (C-compiled vectorization) |
| **Element-wise ops** | Manual loop required | Native (`+`, `-`, `*`, `/`) |
| **Broadcasting** | Not supported | Automatic shape expansion |
| **Multidimensional** | Nested lists (no shape concept) | True N-dimensional with shape/strides |
| **Memory per element** | ~28 bytes (int object) + 8 bytes (pointer) | 4–8 bytes (raw int32/int64) |
| **Boolean filtering** | List comprehension | `arr[arr > 5]` |
| **Built-in math** | None | `mean`, `std`, `sum`, `min`, `max`, etc. |

---

## Common Mistakes & Debugging

### Mistake 1: Shape Mismatch in Reshape

**Wrong:**

```python
arr = np.array([1, 2, 3, 4, 5])
arr.reshape(1, 4)
```

**Error:**

```
ValueError: cannot reshape array of size 5 into shape (1,4)
```

**Why it fails:** `1 × 4 = 4 ≠ 5`. The product of the new dimensions must equal the total number of elements.

**Correct:**

```python
arr.reshape(1, 5)   # 1 × 5 = 5 ✓
arr.reshape(5, 1)   # 5 × 1 = 5 ✓
```

---

### Mistake 2: Using `and`/`or` Instead of `&`/`|`

**Wrong:**

```python
data = np.array([1, 2, 3, 4, 5])
result = data[data > 2 and data < 5]
```

**Error:**

```
ValueError: The truth value of an array with more than one element is ambiguous.
```

**Why it fails:** Python's `and` expects a single boolean, not an array of booleans.

**Correct:**

```python
result = data[(data > 2) & (data < 5)]  # [3, 4]
```

---

### Mistake 3: Confusing `shape` (attribute) with `reshape` (method)

**Wrong:**

```python
arr = np.array([1, 2, 3, 4, 5])
arr.shape(1, 5)  # TypeError: 'tuple' object is not callable
```

**Why it fails:** `shape` is an **attribute** (a property), not a method. You can't call it with parentheses.

**Correct:**

```python
arr.reshape(1, 5)    # Method — call with ()
arr.shape            # Attribute — access without ()
arr.shape = (1, 5)   # You CAN assign to .shape (modifies in-place)
```

---

### Mistake 4: Modifying Views Unintentionally

**Wrong (or rather, unexpected):**

```python
original = np.array([1, 2, 3, 4, 5])
sliced = original[1:4]
sliced[0] = 99
print(original)  # [1, 99, 3, 4, 5] — original changed!
```

**Why it happens:** Slicing creates a **view**, not a copy. Both variables reference the same memory.

**Correct (if you want independence):**

```python
sliced = original[1:4].copy()
sliced[0] = 99
print(original)  # [1, 2, 3, 4, 5] — original unchanged
```

---

## Edge Cases & Gotchas Catalog

| Edge Case | What Happens | Example |
|-----------|-------------|---------|
| Empty array aggregation | `np.mean([])` → RuntimeWarning + `nan` | `np.mean(np.array([]))` → `nan` |
| 0-d array vs scalar | 0-d array is an ndarray with `ndim=0`, not a plain int/float | `np.array(5).ndim` → `0`, `type(np.array(5))` → `ndarray` |
| Single-element shape | Shape is `(1,)`, not `(1)` (the comma matters for tuples) | `np.array([5]).shape` → `(1,)` |
| Integer overflow (int8) | Wraps around silently — no error raised | `np.int8(127) + np.int8(1)` → `-128` |
| NaN in int arrays | Cannot store NaN in int arrays; must convert to float first | `np.array([1, np.nan])` → `[1., nan]` (auto-cast to float) |
| Division by zero | Returns `inf` or `nan` with a RuntimeWarning, does NOT raise an exception | `np.array([1.0]) / 0` → `[inf]` |
| Boolean mask length mismatch | IndexError if mask length ≠ array length | `arr[np.array([True, False])]` on len-5 array → error |
| Reshape returns view | Modifying reshaped array modifies original | `b = a.reshape(2,3); b[0,0]=99` changes `a` |

---

## In-Place Operations & Memory

```python
import numpy as np

arr = np.array([1, 2, 3, 4, 5], dtype=float)

arr += 10      # In-place: modifies arr directly, no new array allocated
arr *= 2       # In-place: same memory address
arr -= 5       # In-place
arr /= 3       # In-place
```

**Why it matters:** For large arrays (millions of elements), `arr = arr + 10` creates a **new** array and copies all data. `arr += 10` modifies in-place — saving both memory and time.

**The `out=` parameter in ufuncs:**

```python
result = np.empty_like(arr)
np.sqrt(arr, out=result)  # Writes directly into pre-allocated array
```

This avoids creating a temporary array, which matters when `arr` is gigabytes in size.

---

## dtype Coercion & `astype()`

```python
import numpy as np

int_arr = np.array([1, 2, 3])
float_arr = int_arr.astype(np.float64)
print(float_arr.dtype)  # float64

mixed = np.array([1, 2.5, 3])
print(mixed.dtype)  # float64 — int was auto-upcast
```

**Rules:**
- NumPy **upcasts** to the most general type: `int + float → float`
- `astype()` always returns a **copy** (new memory)
- **Overflow risk:** `np.array([300]).astype(np.int8)` → `[44]` (silent overflow, no warning!)

---

## NaN Handling

```python
import numpy as np

data = np.array([1.0, 2.0, np.nan, 4.0, 5.0])

print("Has NaN:", np.isnan(data).any())       # True
print("Mean (with NaN):", np.mean(data))       # nan (NaN poisons the result)
print("Mean (ignore NaN):", np.nanmean(data))  # 3.0
print("Sum (ignore NaN):", np.nansum(data))    # 12.0
print("Replace NaN:", np.nan_to_num(data, nan=0.0))  # [1. 2. 0. 4. 5.]
```

> **Critical:** NaN propagates — any arithmetic with NaN produces NaN. Use `np.nan*` functions (`nanmean`, `nansum`, `nanstd`, etc.) to handle missing data.

---

## Best Practices & Pythonic Way

1. **Always use vectorization** — if you're writing a `for` loop over array elements, you're probably doing it wrong
2. **Use built-in NumPy functions** (`np.mean`, `np.sum`) instead of Python builtins (`sum`, `len`) — they're faster and handle axes
3. **Avoid unnecessary copies** — use views (slicing) when you only need to read data
4. **Use `copy()` explicitly** when you need independent data to avoid accidental mutation
5. **Pre-allocate arrays** — `np.empty()` + fill is faster than growing arrays with `np.append()`
6. **Specify `dtype`** when you know the data type to avoid unnecessary upcasting
7. **Use in-place operators** (`+=`, `*=`) for large arrays to reduce memory allocation
8. **Wrap compound boolean conditions** in parentheses: `(cond1) & (cond2)`

---

## When to Use / When NOT to Use

| **Use NumPy When…** | **Avoid NumPy When…** |
|---------------------|----------------------|
| Working with large numerical datasets | Small data (< 100 elements) where list overhead doesn't matter |
| Need element-wise mathematical operations | Data is non-numeric (strings, mixed objects) |
| Matrix/linear algebra computations | Need frequent appending/resizing (use Python lists, then convert) |
| Feature engineering in ML pipelines | Logic requires complex branching per element (use Pandas or Python) |
| Image/signal processing (pixel arrays) | Simple iteration without math |
| Scientific simulation | Building web applications or non-numerical software |
| Need statistical aggregations (mean, std, etc.) | Working with sparse data (use SciPy sparse instead) |

---

## Related Topics & Connections

- **Python lists** → The precursor; understand lists first, then appreciate why NumPy exists
- **Pandas** → Built on NumPy; DataFrames use ndarrays internally for numeric columns
- **Matplotlib/Seaborn** → Visualization libraries that accept NumPy arrays as input
- **Scikit-learn** → ML library; all input data must be NumPy arrays or compatible
- **Linear algebra** → `np.linalg` module extends NumPy with matrix decomposition, eigenvalues, etc.
- **Deep learning** → TensorFlow/PyTorch tensors are conceptually identical to ndarrays; weight matrices are initialized with `np.ones()`, `np.zeros()`, `np.random.*`
- See [Broadcasting] in future sessions → automatic shape expansion for operations on different-shaped arrays
- See [Fancy Indexing] in future sessions → advanced selection with arrays of indices

---

## Interoperability Guide

| From/To | Method | Notes |
|---------|--------|-------|
| NumPy → Pandas | `pd.DataFrame(arr, columns=['a','b','c'])` | Shares memory if possible |
| Pandas → NumPy | `df.to_numpy()` | `.values` is legacy; prefer `.to_numpy()` |
| NumPy → PyTorch | `torch.from_numpy(arr)` | Shares memory — modifying one changes the other! |
| PyTorch → NumPy | `tensor.numpy()` | Tensor must be on CPU and not require grad |
| NumPy → TensorFlow | `tf.constant(arr)` or `tf.convert_to_tensor(arr)` | Creates a copy |
| NumPy → Python list | `arr.tolist()` | Deep conversion to native Python types |
| Python list → NumPy | `np.array(list)` | Creates a copy |
| NumPy → SciPy sparse | `scipy.sparse.csr_matrix(arr)` | For sparse data (mostly zeros) |

---

## Deprecation & NumPy 2.x Migration Notes

| Old (1.x) Pattern | New (2.x) Pattern | Notes |
|--------------------|--------------------|-------|
| `np.bool` | `np.bool_` | Type aliases removed in NumPy 2.0 |
| `np.int` | `np.int_` or `np.intp` | Use Python's `int` or `np.int64` |
| `np.float` | `np.float64` | Use Python's `float` or explicit `np.float64` |
| `np.complex` | `np.complex128` | |
| `np.object` | `np.object_` | |
| `np.array(x, copy=False)` | `np.array(x, copy=None)` | In 2.x, `copy=False` raises error if copy needed |
| `np.random.seed(42)` | `rng = np.random.default_rng(42)` | `RandomState` is legacy; use `Generator` API |
| `numpy.distutils` | Meson build system | Removed entirely in NumPy 2.0 |

> **Action:** If the transcript or your existing code uses `np.float`, `np.int`, `np.bool`, etc., replace them with the explicit types shown above. These aliases were removed in NumPy 2.0 and will raise `AttributeError`.

---

## Interview & Exam Corner

### Frequently Asked Questions

**1. Q: Why is NumPy faster than Python lists?**

A: NumPy stores data in a contiguous, homogeneously-typed memory block (C-style array). Operations are executed by pre-compiled C/Fortran code that processes the entire block in a single pass. Python lists store pointers to scattered heap objects, requiring type checks and pointer dereferencing per element — each causing cache misses and interpreter overhead.

**2. Q: What is the difference between `shape` and `reshape`?**

A: `shape` is an **attribute** (property) that returns the current dimensions as a tuple. `reshape` is a **method** that returns a new array (view if possible) with different dimensions. You read with `arr.shape`; you transform with `arr.reshape(new_shape)`.

**3. Q: What does `(5,)` mean as a shape?**

A: It's a 1D array with 5 elements. The trailing comma makes it a Python tuple (not just `5` in parentheses). There are no rows or columns — just 5 elements along a single axis.

**4. Q: What is vectorization?**

A: Vectorization is applying an operation to an entire array at once (element-wise) without an explicit Python loop. E.g., `arr * 2` multiplies every element by 2 in one C-level call, instead of looping through each element in Python.

**5. Q: What are ufuncs?**

A: Universal functions — NumPy functions implemented in compiled C that operate element-wise on arrays. Examples: `np.sqrt()`, `np.exp()`, `np.sin()`. They accept arrays and return arrays of the same shape.

**6. Q: What's the difference between `np.mean()` and Python's `sum()/len()`?**

A: `np.mean()` is implemented in C (fast), supports the `axis` parameter for row/column means, handles multidimensional arrays natively, and uses `float64` precision. Python's `sum()/len()` is interpreted, only works on 1D iterables, and requires manual implementation.

**7. Q: How does boolean indexing work?**

A: A comparison like `arr > 5` creates a boolean mask (same shape, True/False values). Passing this mask as an index `arr[arr > 5]` selects only elements where the mask is True. Important: boolean indexing always returns a **copy**, not a view.

**8. Q: What's the difference between `np.eye()` and `np.identity()`?**

A: `np.identity(n)` creates only square n×n identity matrices. `np.eye(N, M)` is more flexible — it can create non-square matrices with ones on the diagonal. `np.eye(3, 2)` creates a 3×2 matrix.

**9. Q: Can you use Python's `and` with NumPy arrays?**

A: No. Python's `and` evaluates the entire array as a single boolean, which is ambiguous. Use `&` (element-wise AND) with **parentheses** around each condition: `(arr > 2) & (arr < 8)`. Similarly, use `|` instead of `or`, and `~` instead of `not`.

---

### Tricky Output Questions

**Question 1:**

```python
import numpy as np
a = np.array([1, 2, 3])
b = a
b[0] = 10
print(a)
```

**Answer:** `[10  2  3]`

**Why:** `b = a` does NOT copy the array. Both `a` and `b` point to the same ndarray object in memory. Modifying `b` modifies `a`. To copy: `b = a.copy()`.

---

**Question 2:**

```python
import numpy as np
arr = np.array([1, 2, 3, 4, 5])
print(arr.reshape(1, 5).shape)
print(arr.reshape(1, 5).ndim)
print(arr.shape)
print(arr.ndim)
```

**Answer:**

```
(1, 5)
2
(5,)
1
```

**Why:** `reshape` returns a **new view** — it doesn't modify the original array. The reshaped version is 2D (ndim=2), but the original remains 1D (ndim=1).

---

**Question 3:**

```python
import numpy as np
arr = np.array([1, 2, 3])
result = arr + 1.5
print(result.dtype)
```

**Answer:** `float64`

**Why:** `arr` is `int64`, but adding `1.5` (float) triggers **upcasting**. NumPy promotes the result to `float64` to avoid precision loss.

---

### One-Liner Challenges

1. **Challenge:** Create a 3×3 identity matrix
   **Solution:** `np.eye(3)`

2. **Challenge:** Get all elements greater than the mean from an array
   **Solution:** `arr[arr > np.mean(arr)]`

3. **Challenge:** Normalize an array to have mean=0, std=1
   **Solution:** `(arr - np.mean(arr)) / np.std(arr)`

4. **Challenge:** Create an array of 10 evenly spaced values between 0 and 1
   **Solution:** `np.linspace(0, 1, 10)`

5. **Challenge:** Flatten a 2D array to 1D
   **Solution:** `arr.ravel()` (view) or `arr.flatten()` (copy)

---

### Explain the Difference

**1. Q: What's the difference between `arr.reshape(5, 1)` and `arr.reshape(1, 5)`?**

A: Both are 2D arrays, but with different orientations:
- `reshape(5, 1)` → **Column vector:** 5 rows, 1 column. Shape `(5, 1)`.
- `reshape(1, 5)` → **Row vector:** 1 row, 5 columns. Shape `(1, 5)`.
- They contain the same data but have different shapes, which matters for broadcasting and matrix operations.

**2. Q: What's the difference between `np.ones((3, 4))` and `np.ones([3, 4])`?**

A: Functionally identical. `np.ones()` accepts the shape as a tuple `(3, 4)` or a list `[3, 4]`. The tuple form is conventional and preferred.

---

## Practice Problems

### Problem 1: Array Basics [BEGINNER]

**Task:** Create a 1D array with values 10 to 50 (step 10), reshape it to a 5×1 column vector, and print its shape, ndim, and dtype.

**Starter code:**

```python
import numpy as np

arr = # Your code here
col_vector = # Your code here

print("Array:", arr)
print("Column vector:\n", col_vector)
print("Shape:", col_vector.shape)
print("Ndim:", col_vector.ndim)
print("Dtype:", col_vector.dtype)
```

**Expected output:**

```
Array: [10 20 30 40 50]
Column vector:
 [[10]
 [20]
 [30]
 [40]
 [50]]
Shape: (5, 1)
Ndim: 2
Dtype: int64
```

**Solution:**

```python
import numpy as np

arr = np.arange(10, 60, 10)
col_vector = arr.reshape(5, 1)

print("Array:", arr)
print("Column vector:\n", col_vector)
print("Shape:", col_vector.shape)
print("Ndim:", col_vector.ndim)
print("Dtype:", col_vector.dtype)
```

---

### Problem 2: Slicing and Statistics [INTERMEDIATE]

**Task:** Given a 4×4 matrix, extract the bottom-right 2×2 sub-matrix and compute its mean, sum, and standard deviation.

**Starter code:**

```python
import numpy as np

matrix = np.array([
    [10, 20, 30, 40],
    [50, 60, 70, 80],
    [90, 100, 110, 120],
    [130, 140, 150, 160]
])

sub_matrix = # Your code here
print("Sub-matrix:\n", sub_matrix)
print("Mean:", # Your code here)
print("Sum:", # Your code here)
print("Std:", # Your code here)
```

**Expected output:**

```
Sub-matrix:
 [[110 120]
 [150 160]]
Mean: 135.0
Sum: 540
Std: 18.708286933869708
```

**Solution:**

```python
import numpy as np

matrix = np.array([
    [10, 20, 30, 40],
    [50, 60, 70, 80],
    [90, 100, 110, 120],
    [130, 140, 150, 160]
])

sub_matrix = matrix[2:, 2:]
print("Sub-matrix:\n", sub_matrix)
print("Mean:", np.mean(sub_matrix))
print("Sum:", np.sum(sub_matrix))
print("Std:", np.std(sub_matrix))
```

---

### Problem 3: Boolean Filtering & Normalization [ADVANCED]

**Task:** Given temperature readings, filter out readings below 20°C and above 40°C (keep only 20–40 range), then normalize the filtered data to z-scores. Print the filtered data and its normalized form.

**Starter code:**

```python
import numpy as np

temps = np.array([5, 15, 22, 28, 35, 42, 50, 18, 30, 25, 38, 12, 8, 33, 27])

filtered = # Your code here — keep only temps between 20 and 40 (inclusive)
mu = # mean of filtered
sigma = # std of filtered
normalized = # z-score normalization

print("Original:", temps)
print("Filtered (20-40°C):", filtered)
print("Normalized:", normalized)
print("Normalized mean:", np.round(np.mean(normalized), 10))
print("Normalized std:", np.round(np.std(normalized), 10))
```

**Expected output:**

```
Original: [ 5 15 22 28 35 42 50 18 30 25 38 12  8 33 27]
Filtered (20-40°C): [22 28 35 30 25 38 33 27]
Normalized: [-1.46059349 -0.29211870  0.07302967  0.07302967 -0.87635609  0.65726706
  0.36514837 -0.43817805  0.14605935 -0.58423740  0.51120771 -0.73029675
 -0.87635609  0.29211870 -0.14605935]
Normalized mean: 0.0
Normalized std: 1.0
```

**Solution:**

```python
import numpy as np

temps = np.array([5, 15, 22, 28, 35, 42, 50, 18, 30, 25, 38, 12, 8, 33, 27])

filtered = temps[(temps >= 20) & (temps <= 40)]
mu = np.mean(filtered)
sigma = np.std(filtered)
normalized = (filtered - mu) / sigma

print("Original:", temps)
print("Filtered (20-40°C):", filtered)
print("Normalized:", normalized)
print("Normalized mean:", np.round(np.mean(normalized), 10))
print("Normalized std:", np.round(np.std(normalized), 10))
```

---

## Visualization Quick-Reference

```python
import numpy as np
import matplotlib.pyplot as plt

data = np.random.rand(5, 5)
plt.imshow(data, cmap='viridis')
plt.colorbar()
plt.title('2D Array Heatmap')
plt.show()
```

- Use `plt.plot(arr)` to visualize 1D arrays as line plots
- Use `plt.imshow(arr)` for 2D arrays as heatmaps
- Use `plt.hist(arr.flatten(), bins=30)` to inspect value distributions
- Use `plt.bar(range(len(arr)), arr)` for bar charts of 1D arrays

---

## Mnemonics & Memory Aids

- **Shape tuple reading:** "Shape reads as (rows, columns, depth, ...)" — first number is always the outermost dimension
- **Bracket counting:** Count opening brackets `[` in printed output = number of dimensions
- **Reshape rule:** "Product must match" — `np.prod(new_shape) == arr.size`
- **Axis trick:** "axis=0 collapses ROWS (operates DOWN), axis=1 collapses COLUMNS (operates ACROSS)"
- **View vs Copy:** "Slicing = View (shared memory), Fancy indexing = Copy (independent memory)"
- **Boolean ops:** "No `and`/`or`/`not` — use `&`/`|`/`~` with PARENTHESES"
- **Broadcasting rule:** "Right-align shapes, each dimension must match or be 1"

---

## Conceptual Diagrams (ASCII)

### Array Creation Family

```
ARRAY CREATION FUNCTIONS:
np.array([1,2,3])     ──→ from existing data (list/tuple)
np.arange(0,10,2)     ──→ evenly spaced by step (like range())
np.linspace(0,1,5)    ──→ evenly spaced by count
np.zeros((3,4))       ──→ all zeros
np.ones((3,4))        ──→ all ones
np.eye(3)             ──→ identity matrix (diagonal ones)
np.empty((3,4))       ──→ uninitialized (garbage values — fast!)
np.full((3,4), 7)     ──→ filled with specified value
```

### Reshape Family

```
RESHAPE FAMILY:
arr.reshape(m,n)  ──→ returns VIEW (if possible), new shape
arr.ravel()       ──→ flatten to 1D, returns VIEW (C-order)
arr.flatten()     ──→ flatten to 1D, ALWAYS returns COPY
arr.resize(m,n)   ──→ modifies IN-PLACE, can change total size
arr.T             ──→ transpose (swaps rows ↔ columns), returns VIEW
```

### Statistical Functions Flow

```
STATISTICAL PIPELINE:
raw_data ──→ np.mean()   ──→ central tendency
         ──→ np.median() ──→ robust central tendency
         ──→ np.std()    ──→ spread
         ──→ np.var()    ──→ spread (squared)
         ──→ normalize   ──→ (data - mean) / std ──→ z-scores
```

---

## Quick-Reference Cheat Sheet

```
NUMPY FUNDAMENTALS CHEAT SHEET
===============================
CREATION:
  np.array([1,2,3])        → from list
  np.arange(0,10,2)        → range with step
  np.ones((r,c))           → all 1s
  np.zeros((r,c))          → all 0s
  np.eye(n)                → identity matrix

ATTRIBUTES:
  arr.shape                → (rows, cols)
  arr.ndim                 → num dimensions
  arr.size                 → total elements
  arr.dtype                → data type

RESHAPING:
  arr.reshape(r, c)        → new shape (view)
  arr.ravel()              → flatten (view)
  arr.flatten()            → flatten (copy)

INDEXING/SLICING:
  arr[r, c]                → single element
  arr[r1:r2, c1:c2]       → sub-array (view)
  arr[arr > 5]             → boolean filter (copy)

MATH:
  arr1 + arr2              → element-wise add
  arr1 * arr2              → element-wise multiply
  np.sqrt(arr)             → square root
  np.exp(arr)              → exponential
  np.log(arr)              → natural log

STATISTICS:
  np.mean(arr)             → average
  np.median(arr)           → middle value
  np.std(arr)              → standard deviation
  np.var(arr)              → variance

BOOLEAN OPS:
  (arr > 5) & (arr < 10)  → compound condition
  arr[(cond1) | (cond2)]  → OR filter
  ~(arr > 5)               → NOT
```

---

## Summary (TL;DR)

- **NumPy** is the foundational library for numerical computing in Python — everything in the data science stack builds on it
- The core data structure is **ndarray** — a homogeneous, contiguous-memory, N-dimensional array
- **Array creation:** `np.array()`, `np.arange()`, `np.ones()`, `np.zeros()`, `np.eye()`
- **Attributes:** `shape`, `ndim`, `size`, `dtype`, `itemsize` tell you everything about an array's structure
- **Vectorized operations** (`+`, `-`, `*`, `/`) work element-wise and are 50–100x faster than Python loops
- **Universal functions** (`np.sqrt`, `np.exp`, `np.sin`, `np.log`) apply C-compiled math element-wise
- **Indexing** (`arr[row, col]`) and **slicing** (`arr[r1:r2, c1:c2]`) extract data; slicing returns views (shared memory)
- **Boolean indexing** (`arr[arr > 5]`) filters data by condition — use `&`/`|`/`~` with parentheses, NOT `and`/`or`/`not`
- **Statistical functions** (`np.mean`, `np.median`, `np.std`, `np.var`) are essential for data analysis
- **Normalization** `(x - μ) / σ` transforms data to mean=0, std=1 — critical for ML
- **Views** share memory (fast, but mutation propagates); **copies** are independent (safe, but use more memory)
- NumPy is NOT optional — it's the foundation for Pandas, Matplotlib, Scikit-learn, TensorFlow, and PyTorch

---

## Further Reading & Official Docs

- **NumPy Quickstart Tutorial:** https://numpy.org/doc/stable/user/quickstart.html
- **Array Creation:** https://numpy.org/doc/stable/reference/routines.array-creation.html
- **Indexing & Slicing:** https://numpy.org/doc/stable/user/basics.indexing.html
- **Universal Functions (ufuncs):** https://numpy.org/doc/stable/reference/ufuncs.html
- **Broadcasting:** https://numpy.org/doc/stable/user/basics.broadcasting.html
- **NumPy 2.0 Migration Guide:** https://numpy.org/doc/stable/numpy_2_0_migration_guide.html
- **"From Python to NumPy" by Nicolas Rougier:** https://www.labri.fr/perso/nrougier/from-python-to-numpy/
- **NumPy source code (GitHub):** https://github.com/numpy/numpy

---

## Flashcard-Style Recall

- **Q:** What is ndarray?
  **A:** NumPy's core N-dimensional array — homogeneous dtype, contiguous memory, supports vectorized operations.

- **Q:** What does `shape (5,)` mean?
  **A:** 1D array with 5 elements. No rows/columns — just a flat vector.

- **Q:** What does `shape (2, 5)` mean?
  **A:** 2D array with 2 rows and 5 columns. Total elements: 10.

- **Q:** Why is NumPy fast?
  **A:** Contiguous memory layout, homogeneous dtypes, operations in compiled C — no interpreter overhead per element.

- **Q:** What's the difference between a view and a copy?
  **A:** View shares the same memory buffer (modifications propagate). Copy has independent memory (modifications are isolated).

- **Q:** How do you filter an array by condition?
  **A:** Boolean indexing: `arr[arr > 5]`. Use `&`/`|` (not `and`/`or`) for compound conditions with parentheses.

- **Q:** What's normalization?
  **A:** `z = (x - mean) / std` — transforms data to mean=0, std=1. Essential for ML feature scaling.

- **Q:** What does `np.ones((3, 4))` create?
  **A:** A 3×4 array filled with 1.0 (float64). Used for weight initialization in neural networks.

- **Q:** What's the difference between `np.arange()` and `np.linspace()`?
  **A:** `arange(start, stop, step)` specifies step size. `linspace(start, stop, num)` specifies number of points. `linspace` includes the endpoint by default.

- **Q:** Can NumPy arrays hold mixed types?
  **A:** No. All elements share the same `dtype`. If you pass mixed types, NumPy upcasts to the most general type (e.g., `int` + `float` → `float64`).
