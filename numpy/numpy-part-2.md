# NUMPY ARRAY RESHAPING, RESIZING, INDEXING & SLICING

---

## Prerequisites
- Understanding of NumPy array creation (`np.array()`, `np.zeros()`, etc.) — see [numpy-part-1 notes](numpy-part-1.md)
- Array attributes: `.shape`, `.ndim`, `.size`, `.dtype` — see [numpy-part-1 notes](numpy-part-1.md)
- Basic reshape (`arr.reshape()`) — introduced in [numpy-part-1 notes](numpy-part-1.md)
- Python list slicing (`list[start:stop:step]`)
- Understanding of memory concepts (pointers, addresses) is helpful

---

## Environment Setup

```python
import numpy as np
```

---

## Concept Overview

This module covers two critical families of NumPy operations:

**1. Reshaping & Resizing:** Changing the structure of arrays without (or with) changing the data. This includes `reshape()`, `ravel()`, `flatten()`, `resize()`, and `squeeze()` — each with fundamentally different memory behaviors (views vs copies vs in-place).

**2. Indexing & Slicing:** Accessing specific elements, rows, columns, or sub-arrays. NumPy extends Python's slicing syntax with powerful features: multi-dimensional indexing, boolean masking, fancy indexing, and conditional selection with `np.where()`.

**Why this matters:**
Every data pipeline involves transforming array shapes (reshaping model inputs, flattening images for ML, squeezing extra dimensions from neural network outputs) and selecting/filtering data (extracting features, applying conditions, modifying subsets). Mastery of these operations is non-negotiable for efficient NumPy usage.

**Real-world analogy:**
- **Reshaping** is like rearranging the same set of playing cards from a single row into multiple rows/columns, or stacking them into a pile — same cards, different arrangement.
- **Indexing/Slicing** is like reaching into a filing cabinet and pulling out specific files by position, by label, or by a search criterion ("give me all files from 2024").

---

## Key Terminology

| Term | Meaning |
|------|---------|
| `reshape` | Change array dimensions while keeping total elements constant |
| `ravel` | Flatten to 1D, returning a **view** (shared memory) |
| `flatten` | Flatten to 1D, returning a **copy** (independent memory) |
| `resize` | Change array size in-place — can truncate or pad with zeros |
| `squeeze` | Remove axes of length 1 (e.g., `(1,3,1)` → `(3,)`) |
| `view` | A new array object sharing the same underlying data buffer |
| `copy` | An independent array with its own data buffer |
| `in-place` | Modifying data without creating a new object or reassignment |
| `basic indexing` | Accessing elements by integer position — `arr[0]`, `arr[1:3]` |
| `boolean indexing` | Selecting elements where a condition is True — `arr[arr > 5]` |
| `fancy indexing` | Selecting elements by an array of indices — `arr[[0, 2, 4]]` |
| `np.where` | Returns indices where a condition is True |

---

## PART 1: ARRAY RESHAPING & RESIZING

---

### Reshaping Functions Comparison Table

| Function | Purpose | In-place | Returns Copy/View |
|----------|---------|----------|-------------------|
| `reshape()` | Change shape (same size) | No | View (if possible) |
| `resize()` | Resize array (can change size) | Yes | None |
| `ravel()` | Flatten array (1D) | No | View |
| `flatten()` | Flatten array (1D) | No | Copy |
| `squeeze()` | Remove dimensions of size 1 | No | New array |

---

### 1. `reshape()` — Change Shape Without Changing Data [BEGINNER]

> **Note:** `reshape()` was introduced in the [numpy-part-1 notes](numpy-part-1.md). This section provides the notebook's opening context from 18-02-2026.

**Syntax:**

```python
ndarray.reshape(new_shape)
# or
np.reshape(arr, new_shape)
```

**Key rules (from notebook):**
- The total number of elements must remain the same
- `reshape()` returns a new **view** if possible (no copy)
- You can use `-1` to let NumPy infer one dimension

**Complexity:** O(1) when returning a view (just changes metadata). O(n) if a copy is required (non-contiguous data).

---

### 2. `ravel()` — Flatten to 1D (Returns a View) [INTERMEDIATE]

**What:** Collapses a multi-dimensional array into a 1D array. Returns a **view** — meaning it shares memory with the original. Changes to the raveled array affect the original, and vice versa.

**Real-world analogy:** Like unrolling a scroll — the text (data) is the same, you're just reading it as a single continuous line instead of in rows. The ink is still on the same paper.

**Syntax:**

```python
ndarray.ravel(order='C')
```

- `order='C'` — row-major (default, C-style): elements are read row by row
- `order='F'` — column-major (Fortran-style): elements are read column by column
- `order='K'` — preserve element order in memory

#### Example 1: Basic ravel [BEGINNER]

```python
import numpy as np

a = np.array([[1, 2], [3, 4]])
a.shape
```

**Output:**

```
(2, 2)
```

```python
print(a.ravel())
```

**Output:**

```
[1 2 3 4]
```

```python
print(a)
```

**Output:**

```
[[1 2]
 [3 4]]
```

**Critical observation:** `a` is still `(2, 2)` after ravel — `ravel()` does NOT modify the original. It returns a new array object that shares the same data.

#### Example 2: Proving ravel Returns a View [ADVANCED]

The notebook uses Python's `id()` and NumPy's `__array_interface__['data'][0]` to prove that `ravel()` shares memory.

**`id()` — Python Object Identity:**

```python
a_ravel = a.ravel()
print(a_ravel)
print(id(a))
print(id(a.ravel()))
print(id(a_ravel))
```

**Output:**

```
[1 2 3 4]
2150665186416
2150665188048
2149884750768
```

**Wait — the IDs are different!** Does that mean they DON'T share memory?

**No.** `id()` gives you the Python **object** identity, not the **data buffer** identity. Each call to `a.ravel()` creates a new Python wrapper object (new `id`), but they all point to the **same underlying data buffer**.

**`__array_interface__['data'][0]` — Actual Memory Address:**

```python
print(f"Data address of 'a': {a.__array_interface__['data'][0]}")
```

**Output:**

```
Data address of 'a': 2149868715696
```

```python
print(f"Data address of a.ravel() directly: {a.ravel().__array_interface__['data'][0]}")
```

**Output:**

```
Data address of a.ravel() directly: 2149868715696
```

```python
print(f"Data address of 'a_raven': {a_ravel.__array_interface__['data'][0]}")
```

**Output:**

```
Data address of 'a_raven': 2149868715696
```

**All three have the SAME data address: `2149868715696`.** This proves that `a`, `a.ravel()`, and `a_ravel` all share the same memory buffer. They are different Python objects wrapping the same data.

**Key takeaway:** `id()` tells you about the Python wrapper object. `__array_interface__['data'][0]` tells you about the actual data in memory. For views, the data address is shared.

#### Example 3: View Mutation — Changes Propagate [INTERMEDIATE]

```python
a[0][0] = 11
```

```python
a
```

**Output:**

```
array([[11,  2],
       [ 3,  4]])
```

```python
print(f"Data address of 'a': {a.__array_interface__['data'][0]}")
```

**Output:**

```
Data address of 'a': 2149868715696
```

The address didn't change — the data was modified in-place.

```python
print(a_ravel)
```

**Output:**

```
[11  2  3  4]
```

**The ravel changed too!** We modified `a[0][0]` to 11, and `a_ravel[0]` automatically reflects that change because they share the same memory. This is the defining behavior of a **view**.

**ASCII diagram — View Relationship:**

```
a (2×2):              a_ravel (4,):
┌──────┐              ┌──────────────┐
│11 │ 2│              │11 │ 2 │ 3 │ 4│
│ 3 │ 4│              └──────────────┘
└──────┘                     │
      │                      │
      └───── SAME DATA ──────┘
             (address: 2149868715696)
```

---

### 3. `flatten()` — Flatten to 1D (Returns a Copy) [INTERMEDIATE]

**What:** Collapses a multi-dimensional array into 1D, just like `ravel()`, but returns a **copy** — a completely independent array with its own memory buffer. Changes to the flattened array do NOT affect the original.

**Real-world analogy:** Like photocopying a multi-page document onto a single continuous page. You get a new physical copy — writing on the copy doesn't change the original.

**Syntax:**

```python
ndarray.flatten(order='C')
```

#### Example 1: flatten vs ravel — Memory Independence [INTERMEDIATE]

```python
a
```

**Output:**

```
array([[11,  2],
       [ 3,  4]])
```

```python
b = a.flatten()
```

```python
print(f"Data address of 'a': {a.__array_interface__['data'][0]}")
print(f"Data address of 'b': {b.__array_interface__['data'][0]}")
```

**Output:**

```
Data address of 'a': 2149868715696
Data address of 'b': 2149868715216
```

**Different addresses!** `a` is at `...5696` and `b` is at `...5216`. They are completely independent memory buffers. Modifying `b` will NOT affect `a`.

```python
b
```

**Output:**

```
array([11,  2,  3,  4])
```

#### `ravel()` vs `flatten()` — Complete Comparison

| Feature | `ravel()` | `flatten()` |
|---------|-----------|-------------|
| **Returns** | View (shared memory) | Copy (independent memory) |
| **Speed** | O(1) — just metadata change | O(n) — copies all data |
| **Memory** | No extra memory | Doubles memory usage |
| **Mutation** | Changes propagate to original | Changes are independent |
| **When to use** | Read-only operations, performance-critical | When you need to modify without affecting original |
| **Safety** | Dangerous if you modify accidentally | Safe — no side effects |

**Mnemonic:** "**R**avel = **R**eference (view), **F**latten = **F**resh copy"

---

### 4. `resize()` — Resize Array In-Place [INTERMEDIATE]

**What:** Changes the shape AND potentially the size of an array **in-place** (modifies the original, no return value). Unlike `reshape()`, it can change the total number of elements — truncating data or padding with zeros.

**Real-world analogy:** Like physically cutting or gluing extra paper onto a document. You're modifying the actual document — not creating a new one. If you make it bigger, the new space is blank (zeros). If smaller, you lose data.

**Syntax:**

```python
ndarray.resize(new_shape, refcheck=True)
```

- `new_shape` — tuple of new dimensions
- `refcheck` — if `True`, checks that no other array references the same data. Set to `False` to bypass (as the notebook does).

#### Example 1: reshape Fails, resize Succeeds [INTERMEDIATE]

```python
a = np.array([[1, 2, 3], [4, 5, 6]])
```

**Attempting reshape to incompatible size:**

```python
a = a.reshape((3, 3))
```

**Output:**

```
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
Cell In[24], line 1
----> 1 a.reshape((3,3))

ValueError: cannot reshape array of size 6 into shape (3,3)
```

**Why it fails:** `reshape` requires the total element count to stay the same. 6 elements cannot fill a 3×3 (9 elements) grid.

```python
a
```

**Output:**

```
array([[1, 2, 3],
       [4, 5, 6]])
```

The array is unchanged because `reshape` failed.

**Now using resize:**

```python
a = np.array([[1, 2, 3], [4, 5, 6]])
a.resize((3, 3), refcheck=False)
```

```python
a
```

**Output:**

```
array([[1, 2, 3],
       [4, 5, 6],
       [0, 0, 0]])
```

**What happened:** `resize` expanded the array from 6 elements to 9 elements. The original 6 values are preserved, and the 3 new positions are filled with zeros. This is an **in-place operation** — no assignment needed (notice we didn't write `a = a.resize(...)`).

**ASCII diagram:**

```
Before resize (2×3, 6 elements):     After resize (3×3, 9 elements):
[1  2  3]                            [1  2  3]
[4  5  6]                            [4  5  6]
                                     [0  0  0]  ← padded with zeros
```

**Key difference from reshape:**

| Feature | `reshape()` | `resize()` |
|---------|-------------|------------|
| Size change | NOT allowed (total must match) | Allowed (truncate or pad) |
| Operation | Returns new view/copy | Modifies array **in-place** |
| Padding | N/A | Zeros for new elements |
| Truncation | N/A | Data lost for removed elements |
| Return value | New array | `None` |

**Warning:** `resize` is destructive and irreversible. If you truncate, data is gone. Always keep a backup if needed.

---

### 5. `squeeze()` — Remove Single-Dimensional Axes [INTERMEDIATE]

**What:** Removes axes (dimensions) that have length 1. Useful for cleaning up shapes from operations that introduce unnecessary singleton dimensions.

**Real-world analogy:** Like flattening unnecessary packaging — if a box contains a single tray, which contains a single row of items, `squeeze` removes the redundant box and tray, leaving just the items.

**Syntax:**

```python
np.squeeze(arr, axis=None)
```

- `axis` — specific axis to squeeze (if None, squeezes all length-1 axes)

**Common use case from notebook:** "Useful for removing extra dimensions from image or model output shapes like `(1, 28, 28, 1)`" — neural networks often output tensors with batch and channel dimensions of size 1.

#### Example 1: Squeezing a 3D Array [INTERMEDIATE]

```python
a = np.array([[[1], [2], [3]]])
print(a.shape)
```

**Output:**

```
(1, 3, 1)
```

**Note:** `[[[1], [2], [3]]]` has shape `(1, 3, 1)`: the outer list has 1 element (a single 2D sub-array), that sub-array has 3 rows, each row has 1 element.

```python
b = np.squeeze(a)
```

```python
print(b.shape)
```

**Output:**

```
(3,)
```

```python
b
```

**Output:**

```
array([1, 2, 3])
```

**What happened:** `squeeze` removed BOTH length-1 dimensions:
- Axis 0: length 1 → removed
- Axis 2: length 1 → removed
- Axis 1: length 3 → kept

Result: `(1, 3, 1)` → `(3,)`

**ASCII diagram:**

```
Before squeeze (1, 3, 1):          After squeeze (3,):
[[[1],                              [1, 2, 3]
  [2],
  [3]]]

Shape: (1, 3, 1)                   Shape: (3,)
ndim: 3                             ndim: 1
```

**Real-world ML example:**

```python
model_output = np.array([[[[0.85]]]]) # shape (1, 1, 1, 1) — batch=1, single prediction
prediction = np.squeeze(model_output)  # shape () — scalar 0.85
```

---

## PART 2: INDEXING AND SLICING

---

### Syntax & Definition

NumPy indexing extends Python's sequence indexing with multi-dimensional support:

```python
# 1D: same as Python lists
arr[index]                  # Single element
arr[start:stop:step]        # Slice

# 2D: comma-separated indices
arr[row, col]               # Single element (preferred)
arr[row_start:row_stop, col_start:col_stop]  # Sub-matrix

# Boolean
arr[arr > value]            # Elements where condition is True

# Fancy
arr[[idx1, idx2, idx3]]     # Elements at specific positions

# np.where
np.where(condition)          # Indices where condition is True
```

---

### 1. Basic Indexing — 1D Arrays [BEGINNER]

**What:** Accessing individual elements and slices from a 1D array by position. Follows the same `[start:stop:step]` syntax as Python lists, but returns **views** (not copies).

**Real-world analogy:** Like reaching into a numbered row of mailboxes. You can grab a specific box by number, or sweep a range of consecutive boxes.

**Syntax:**

```python
array[start:stop:step]
```

- `start` — beginning index (inclusive, default 0)
- `stop` — ending index (**exclusive**, default end)
- `step` — stride between elements (default 1)
- Negative indices count from the end: `-1` = last element

#### Example 1: Single Element Access [BEGINNER]

```python
arr = np.array([10, 20, 30, 40, 50])
```

```python
print(arr[0])
```

**Output:**

```
10
```

First element (0-indexed).

```python
print(arr[-1])
```

**Output:**

```
50
```

Last element (negative indexing).

```python
print(arr[4])
```

**Output:**

```
50
```

Same as `arr[-1]` for a 5-element array. `4 == len(arr) - 1`.

**Complexity:** O(1) — direct memory offset calculation.

#### Example 2: Basic Slicing [BEGINNER]

```python
# arr = np.array([10, 20, 30, 40, 50])
# Syntax: array[start:stop(exclusive):step]

print(arr[1:4])
```

**Output:**

```
[20 30 40]
```

Elements at indices 1, 2, 3 (stop=4 is exclusive).

```python
print(arr[1:-1])
```

**Output:**

```
[20 30 40]
```

From index 1 to one-before-last (index -1 = 4, exclusive).

```python
print(arr[0:3])
```

**Output:**

```
[10 20 30]
```

```python
print(arr[:3])
```

**Output:**

```
[10 20 30]
```

Omitting `start` defaults to 0. `arr[:3]` is identical to `arr[0:3]`.

**Complexity:** O(1) for creating the slice (returns a view). O(k) only if you materialize the view.

#### Example 3: Slicing with Step [INTERMEDIATE]

```python
# arr = np.array([10, 20, 30, 40, 50])
# Get [10, 30, 50] — every other element

arr[0:5:2]
```

**Output:**

```
array([10, 30, 50])
```

```python
arr[::2]
```

**Output:**

```
array([10, 30, 50])
```

Omitting start and stop defaults to "entire array". Step=2 takes every other element.

#### Example 4: Reversing an Array [BEGINNER]

```python
arr[::-1]
```

**Output:**

```
array([50, 40, 30, 20, 10])
```

**How it works:** `[::-1]` means "start at the end, go to the beginning, step by -1". This is the standard reversal idiom in both Python and NumPy.

**Python list comparison:**

```python
list_a = [1, 2, 3, 4, 5, 6, 7]
list_a[::-1]
```

**Output:**

```
[7, 6, 5, 4, 3, 2, 1]
```

Same syntax works for Python lists. Key difference: NumPy's `[::-1]` returns a **view** (no copy), while Python's returns a **new list** (copy).

**ASCII diagram — 1D Slicing:**

```
Index:    0    1    2    3    4
arr:   [ 10 | 20 | 30 | 40 | 50 ]
         ^              ^
arr[0:3] ←——————————————→  = [10, 20, 30]
              ^         ^
arr[1:4]      ←————————→    = [20, 30, 40]
         ^         ^         ^
arr[::2] ←————————→←————————→ = [10, 30, 50]

Negative:  -5   -4   -3   -2   -1
```

---

### 2. Indexing in 2D Arrays [INTERMEDIATE]

**What:** Accessing elements, rows, columns, and sub-matrices from 2D arrays using row/column index pairs.

**Real-world analogy:** Like looking up a value in a spreadsheet by row number and column letter — you need two coordinates to pinpoint a single cell, or ranges to select a block.

#### Example 1: Setup and Basic Access [BEGINNER]

```python
mat = np.array([[1, 2, 3],
                [4, 5, 6],
                [7, 8, 9]])
```

**ASCII representation:**

```
         col0  col1  col2
row0  [   1     2     3  ]
row1  [   4     5     6  ]
row2  [   7     8     9  ]
```

#### Example 2: Two Indexing Syntaxes [BEGINNER]

```python
mat[0][1]
```

**Output:**

```
np.int64(2)
```

```python
mat[0, 1]
```

**Output:**

```
np.int64(2)
```

Both return element at row 0, column 1 (value: 2). **BUT they work differently:**

| Syntax | Mechanism | Performance |
|--------|-----------|-------------|
| `mat[0][1]` | Chained: first creates temp row array `mat[0]`, then indexes into it | 2 operations |
| `mat[0, 1]` | Tuple: single operation directly to element | 1 operation (faster) |

**Best practice:** Always use `mat[0, 1]` (comma syntax) — it's faster, clearer, and enables advanced features like mixed slicing.

#### Example 3: Column Selection [INTERMEDIATE]

```python
mat[:, 2]
```

**Output:**

```
array([3, 6, 9])
```

**How to read this:** "All rows (`:`) at column 2". This selects the entire third column.

**ASCII diagram:**

```
mat[:, 2] → Select ALL rows at column 2:
[1  2  →3←]     result: [3, 6, 9]
[4  5  →6←]
[7  8  →9←]
```

#### Example 4: Sub-Matrix Selection [INTERMEDIATE]

```python
# Goal: Extract the 2×2 sub-matrix:
# [[2 3]
#  [5 6]]

mat[0:2, 1:]
```

**Output:**

```
array([[2, 3],
       [5, 6]])
```

**How to read:** "Rows 0 to 2 (exclusive), columns 1 to end". This selects a rectangular sub-block.

**ASCII diagram:**

```
mat[0:2, 1:]:
         col0  col1  col2
row0  [   1   →[2     3]← ]   rows 0:2
row1  [   4   →[5     6]← ]   cols 1:
row2  [   7     8     9   ]   (excluded)

Result:
[[2 3]
 [5 6]]
```

#### Example 5: Full Matrix Reversal [INTERMEDIATE]

```python
# Goal: Reverse both rows and columns
# [[9 8 7]
#  [6 5 4]
#  [3 2 1]]

mat[::-1, ::-1]
```

**Output:**

```
array([[9, 8, 7],
       [6, 5, 4],
       [3, 2, 1]])
```

**How it works:** `[::-1]` on the first axis reverses row order. `[::-1]` on the second axis reverses column order. Combined, it's like rotating the matrix 180°.

**ASCII diagram:**

```
Original:           mat[::-1, ::-1]:
[1 2 3]             [9 8 7]
[4 5 6]      →      [6 5 4]
[7 8 9]             [3 2 1]
```

**Important:** This returns a **view**, not a copy. The data isn't duplicated in memory.

---

### 3. Boolean Indexing [INTERMEDIATE]

**What:** Selecting elements that satisfy a condition. You pass a boolean array (same shape as the original) as an index, and NumPy returns only the `True` elements.

**Real-world analogy:** Like using a filter on a spreadsheet — "show me only rows where the value exceeds 15." The filter doesn't change the data; it just selects matching entries.

#### Example 1: The Old Way (Python Loop) vs Boolean Indexing [BEGINNER]

**Setup:**

```python
arr = np.array([5, 10, 15, 20, 25])
```

**Python loop approach:**

```python
l = []
for i in arr:
    if i > 15:
        l.append(i)
l = np.array(l)
print(l)
```

**Output:**

```
[20 25]
```

This works but requires a loop, a temporary list, and conversion — verbose and slow for large arrays.

**NumPy boolean indexing:**

Step 1 — Create the boolean mask:

```python
arr > 15
```

**Output:**

```
array([False, False, False,  True,  True])
```

Step 2 — Apply the mask:

```python
arr[arr > 15]
```

**Output:**

```
array([20, 25])
```

**Line-by-line breakdown:**
- `arr > 15` — vectorized comparison: each element is compared to 15, producing a boolean array of the same shape
- `arr[boolean_array]` — returns a **new array** containing only elements where the mask is `True`

**Complexity:** O(n) for creating the mask + O(k) for extracting k matching elements.

**Critical note:** Boolean indexing returns a **copy**, not a view. Unlike basic slicing, the resulting array has its own memory.

**ASCII diagram:**

```
arr:      [ 5  | 10 | 15 | 20 | 25 ]
mask:     [ F  |  F |  F |  T |  T ]
                            ↓     ↓
result:                   [ 20 | 25 ]
```

---

### 4. Fancy Indexing [INTERMEDIATE]

**What:** Selecting multiple elements at specific (non-contiguous) positions using a list or array of indices. Unlike slicing (which must be contiguous), fancy indexing can grab any arbitrary set of elements.

**Real-world analogy:** Like cherry-picking items from a shelf — instead of grabbing a continuous section, you pick item #1, #5, and #8 specifically.

#### Example 1: 1D Fancy Indexing [BEGINNER]

```python
arr = np.array([100, 200, 300, 400, 500])
indices = [0, 2, 4]
print(arr[indices])
```

**Output:**

```
[100 300 500]
```

Selects elements at positions 0, 2, and 4. Order follows the index list.

**Note:** Fancy indexing returns a **copy**, not a view.

#### Example 2: Boolean Indexing on 2D Array [INTERMEDIATE]

```python
mat = np.array([[1, 2, 3],
                [4, 5, 6],
                [7, 8, 9]])
```

```python
mat[mat > 5]
```

**Output:**

```
array([6, 7, 8, 9])
```

**Important:** Boolean indexing on a 2D array returns a **1D array** of matching elements (the structure is flattened). The result contains all elements > 5, regardless of their row/column position.

#### Example 3: Paired Index Selection (Advanced Fancy Indexing) [ADVANCED]

```python
mat[[0, 1],
    [2, 2]]
```

**Output:**

```
array([3, 6])
```

**How it works:** This selects specific (row, col) pairs:
- First pair: row=0, col=2 → value 3
- Second pair: row=1, col=2 → value 6

**The notation `mat[[rows], [cols]]` zips the two lists together:**

```
rows: [0, 1]     → (0, ?) and (1, ?)
cols: [2, 2]     → (?, 2) and (?, 2)
pairs:           → (0, 2) and (1, 2)
values:          → mat[0,2]=3 and mat[1,2]=6
result:          → array([3, 6])
```

**ASCII diagram:**

```
         col0  col1  col2
row0  [   1     2    →3← ]   ← mat[0, 2]
row1  [   4     5    →6← ]   ← mat[1, 2]
row2  [   7     8     9   ]

Result: [3, 6]
```

**Common confusion:** `mat[[0, 1], [2, 2]]` does NOT mean "rows 0-1, columns 2-2". It means "pairs (0,2) and (1,2)". For a sub-matrix, use slicing: `mat[0:2, 2:3]`.

---

### 5. Modifying with Indexing [INTERMEDIATE]

**What:** Using indexing syntax on the LEFT side of an assignment to change specific elements.

**Real-world analogy:** Like erasing specific cells in a spreadsheet and writing new values — you target by position or condition.

#### Example 1: Single Element Modification [BEGINNER]

```python
arr = np.array([1, 2, 3, 4])
arr[2] = 10
print(arr)
```

**Output:**

```
[ 1  2 10  4]
```

Direct assignment to a specific index. This modifies the array **in-place**.

#### Example 2: Conditional Modification [INTERMEDIATE]

```python
arr[arr > 2] = 100
print(arr)
```

**Output:**

```
[  1   2 100 100]
```

**How it works:**
1. `arr > 2` creates mask: `[False, False, True, True]` (elements 10 and 4 are > 2)
2. `arr[mask] = 100` sets ALL matching positions to 100

**Use case:** Data cleaning — replace outliers, cap values, fill missing data. For example: `prices[prices < 0] = 0` to eliminate negative prices.

---

### 6. Indexing with `np.where()` [INTERMEDIATE]

**What:** Returns the **indices** (positions) where a condition is True, rather than the values themselves. This is powerful when you need to know WHERE something is, not just WHAT it is.

**Real-world analogy:** Like asking "which rows in the spreadsheet have values > 20?" — you get back the row numbers, not the values. Then you can use those row numbers for further operations.

**Syntax:**

```python
np.where(condition)               # Returns tuple of index arrays
np.where(condition, x, y)         # Returns x where True, y where False
```

#### Example 1: Finding Indices [BEGINNER]

```python
arr = np.array([10, 15, 20, 25, 30])
```

```python
idx = np.where(arr > 20)
```

```python
print(idx)
```

**Output:**

```
(array([3, 4]),)
```

**How to read:** `np.where` returns a tuple of arrays (one per dimension). For a 1D array, it's a tuple with one array. The indices where `arr > 20` are `[3, 4]` — positions of 25 and 30.

**Note the tuple wrapping:** `(array([3, 4]),)` — note the trailing comma. This is because `np.where` always returns a tuple (one array per dimension). For 2D arrays, you'd get `(row_indices, col_indices)`.

```python
print(arr[idx])
```

**Output:**

```
[25 30]
```

Use the returned indices to fetch the actual values.

**When to use `np.where` vs boolean indexing:**

| Method | Returns | Use When |
|--------|---------|----------|
| `arr[arr > 20]` | Values | You just need the matching values |
| `np.where(arr > 20)` | Indices | You need to know the positions (for other operations) |
| `np.where(cond, x, y)` | Conditional array | You want to create a new array with different values for True/False |

---

## Array Attributes Reference (Reshaping Context)

| Attribute | Returns | Example | Output |
|-----------|---------|---------|--------|
| `.shape` | Dimension tuple | `np.array([[1,2],[3,4]]).shape` | `(2, 2)` |
| `.ndim` | Number of dimensions | `np.zeros((5,5,5)).ndim` | `3` |
| `.__array_interface__['data'][0]` | Raw memory address | `a.__array_interface__['data'][0]` | Integer (memory address) |
| `.base` | Original array if view, else None | `a.ravel().base is a` | `True` |

---

## Comparison Table — Indexing Methods (MANDATORY)

| Method | Syntax | Returns | View/Copy | Use Case |
|--------|--------|---------|-----------|----------|
| Basic indexing | `arr[0]`, `arr[1:3]` | Element or slice | View | Simple positional access |
| Negative indexing | `arr[-1]` | Single element | View | Access from end |
| Step slicing | `arr[::2]` | Every Nth element | View | Downsample, reverse |
| 2D indexing | `mat[0, 1]` | Single element | — | Spreadsheet-style lookup |
| 2D slicing | `mat[0:2, 1:]` | Sub-matrix | View | Block extraction |
| Boolean indexing | `arr[arr > 5]` | Matching elements | **Copy** | Filtering/conditions |
| Fancy indexing | `arr[[0, 2, 4]]` | Selected elements | **Copy** | Cherry-picking |
| Paired fancy | `mat[[0,1],[2,2]]` | Specific cells | **Copy** | Diagonal elements, scattered access |
| `np.where` | `np.where(arr > 5)` | Index tuple | — | Finding positions |

---

## Before NumPy vs After NumPy (Side-by-Side)

### Task 1: Filter elements > 15

**Pure Python:**

```python
arr_list = [5, 10, 15, 20, 25]
result = [x for x in arr_list if x > 15]
# result: [20, 25]
```

**NumPy:**

```python
arr = np.array([5, 10, 15, 20, 25])
result = arr[arr > 15]
# result: array([20, 25])
```

**Advantage:** Vectorized condition, no explicit loop, works on million-element arrays at C speed.

### Task 2: Flatten a 2D array

**Pure Python:**

```python
matrix = [[1, 2], [3, 4]]
flat = [elem for row in matrix for elem in row]
# flat: [1, 2, 3, 4]
```

**NumPy:**

```python
mat = np.array([[1, 2], [3, 4]])
flat = mat.ravel()  # O(1) — just a view!
# flat: array([1, 2, 3, 4])
```

**Advantage:** `ravel()` is O(1) — no iteration, no data copy. The Python approach is O(n).

---

## How It Works Internally

### How Views Work (ravel / slicing)

When you create a view (via `ravel`, slicing, `reshape`, `.T`), NumPy does NOT copy data. Instead, it creates a new array object with:

1. **Same data pointer** — points to the original memory buffer
2. **Different shape** — describes the new arrangement
3. **Different strides** — describes how to traverse the new arrangement

```
Original: a = [[1, 2], [3, 4]]
  shape:  (2, 2)
  strides: (16, 8)    # 16 bytes per row, 8 bytes per element (int64)
  data → [1, 2, 3, 4] at address 0x7F...

View: a_ravel = a.ravel()
  shape:  (4,)
  strides: (8,)       # 8 bytes between consecutive elements
  data → [1, 2, 3, 4] at address 0x7F...  ← SAME ADDRESS

Both shapes describe different ways to walk through the SAME contiguous block.
```

### Boolean Indexing Internals

Boolean indexing creates a copy because the selected elements are generally non-contiguous in memory — there's no way to describe them with a simple shape+strides combination.

```
arr:    [ 5 | 10 | 15 | 20 | 25 ]  (contiguous)
mask:   [ F |  F |  F |  T |  T ]

NumPy must extract indices 3 and 4 into a NEW contiguous array:
result: [ 20 | 25 ]  (new allocation, contiguous)
```

---

## Common Mistakes & Debugging

### Mistake 1: reshape — Incompatible Size [INTERMEDIATE]

**Wrong:**

```python
a = np.array([[1, 2, 3], [4, 5, 6]])
a = a.reshape((3, 3))
```

**Error:**

```
ValueError: cannot reshape array of size 6 into shape (3,3)
```

**Why it fails:** 6 elements cannot fill a 3×3 grid (9 positions). The product of old shape must equal the product of new shape.

**Correct:** Use `resize()` if you need to change total size:

```python
a.resize((3, 3), refcheck=False)  # Pads with zeros
```

### Mistake 2: Confusing `id()` with Shared Memory [ADVANCED]

**Wrong assumption:**

```python
a_ravel = a.ravel()
print(id(a) == id(a_ravel))  # False — they're different objects!
# Conclusion: "They don't share memory" ← WRONG
```

**Correct check:**

```python
print(a.__array_interface__['data'][0] == a_ravel.__array_interface__['data'][0])
# True — same data buffer!
# Or use:
print(np.shares_memory(a, a_ravel))  # True
```

**Why:** `id()` gives Python object identity. `__array_interface__['data'][0]` gives the actual C data buffer address. Views have different Python objects but the same data buffer.

### Mistake 3: Modifying a View Unexpectedly [INTERMEDIATE]

**Wrong:**

```python
a = np.array([[1, 2], [3, 4]])
b = a.ravel()
b[0] = 999
print(a)  # [[999, 2], [3, 4]] — Original changed!
```

**Why it happens:** `ravel()` returns a view. Modifying `b` modifies `a`.

**Correct:** Use `flatten()` for an independent copy:

```python
b = a.flatten()
b[0] = 999
print(a)  # [[1, 2], [3, 4]] — Original unchanged
```

### Mistake 4: `list.append()` Called Without Object [BEGINNER]

The notebook contains:

```python
list.append()
```

This would raise `TypeError: descriptor 'append' of 'list' object needs an argument`. It appears to be a stray cell. `list.append()` must be called on a list instance: `my_list.append(item)`.

---

## Edge Cases & Gotchas Catalog

| Edge Case | What Happens | Example |
|-----------|-------------|---------|
| Squeeze with no length-1 axes | Returns the array unchanged | `np.squeeze(np.array([1,2,3]))` → `[1,2,3]` |
| Squeeze specific axis | Only that axis is removed | `np.squeeze(a, axis=0)` only removes axis 0 |
| Boolean mask all False | Returns empty array | `arr[arr > 1000]` → `array([], dtype=int64)` |
| Boolean mask all True | Returns copy of entire array | `arr[arr > -1]` → copy of arr |
| Fancy index with duplicates | Element selected twice | `arr[[0, 0, 1]]` → `[arr[0], arr[0], arr[1]]` |
| Negative step slicing | Traverses backwards | `arr[::-2]` → every other element, reversed |
| ravel of Fortran-order | May return copy, not view | `np.asfortranarray(arr).ravel('C')` copies |
| Resize to smaller | Data truncated (lost) | `a.resize((1,2))` keeps only first 2 elements |
| Resize to larger | New elements are 0 | `a.resize((5,5))` pads with zeros |
| Assign scalar to slice | Broadcasts to all positions | `arr[1:3] = 0` sets indices 1 and 2 to 0 |

---

## Best Practices & Pythonic Way

- **Use `mat[i, j]` over `mat[i][j]`** — single operation vs chained (faster, cleaner)
- **Use `ravel()` for read-only flattening** — zero memory overhead
- **Use `flatten()` when you'll modify the result** — avoids accidental view mutations
- **Use `np.shares_memory()` to check view status** — never rely on `id()`
- **Prefer boolean indexing over loops** for filtering — it's vectorized
- **Use `np.where` when you need positions** — boolean indexing gives values, `where` gives locations
- **Always check `.shape` after reshaping** — verify the operation did what you expected
- **Use `squeeze()` to clean ML model outputs** — removes batch/channel dimensions of size 1
- **Avoid `resize()` unless you truly need size changes** — it's destructive and in-place

---

## When to Use / When NOT to Use

| Use When… | Avoid When… |
|-----------|-------------|
| `ravel()` — need fast 1D view for read-only processing | `ravel()` — you'll modify the result (use `flatten()`) |
| `flatten()` — need independent 1D copy to modify | `flatten()` — array is huge and you only read (wastes memory) |
| `resize()` — truly need to change array size in-place | `resize()` — you just need a different shape (use `reshape()`) |
| `squeeze()` — cleaning output from ML models / APIs | `squeeze()` — you need the singleton dims for broadcasting |
| Boolean indexing — filtering by condition | Boolean indexing — you need to preserve 2D structure (use masking with `np.ma`) |
| Fancy indexing — selecting specific non-contiguous elements | Fancy indexing — selecting contiguous ranges (use slicing, it's a view) |
| `np.where()` — need positions, not just values | `np.where()` — you just need the values (boolean indexing is simpler) |

---

## Related Topics & Connections

- **Array Creation** → See [numpy-part-1 notes](numpy-part-1.md) for `np.array()`, `np.zeros()`, etc.
- **Array Attributes** → `.shape`, `.ndim`, `.dtype` covered in [numpy-part-1 notes](numpy-part-1.md)
- **Basic reshape** → Introduced in [numpy-part-1 notes](numpy-part-1.md), expanded here
- **Broadcasting** → Future module; builds on shape understanding and indexing
- **Vectorized Operations** → Uses the same element-wise approach as boolean indexing
- **Structured Arrays** → For mixed-type data that `ndarray` can't handle
- **Masked Arrays (`np.ma`)** → Advanced alternative to boolean indexing for handling missing data

---

## Interoperability Guide

| From/To | Method | Notes |
|---------|--------|-------|
| NumPy slice → Pandas | `pd.Series(arr[1:3])` | Slice is a view; wrapping in Pandas may copy |
| Boolean index → Pandas | `df[df['col'] > 5]` | Pandas uses same syntax, built on NumPy |
| NumPy ravel → ML | `images.reshape(n, -1)` | Flatten images for sklearn/model input |
| Squeeze → TensorFlow | `tf.squeeze(tensor)` | Same concept, different framework |
| np.where → Pandas | `pd.DataFrame.where()` | Similar but keeps shape, fills NaN |

---

## Deprecation & NumPy 2.x Migration Notes

| Old (1.x) | New (2.x) | Notes |
|-----------|-----------|-------|
| `np.bool` | `np.bool_` or `bool` | Boolean masks still work; the alias was removed |
| `np.int` | `np.int_` or `int` | Indexing with Python ints still works fine |
| `a.resize(new_shape)` | Same | `refcheck=True` is still default; be explicit |
| `np.object` arrays | `np.object_` | Rare for indexing; usually in structured arrays |
| Implicit copy in `np.array(x, copy=False)` | `np.array(x, copy=None)` | Views from indexing are unaffected |

---

## Interview & Exam Corner

### Frequently Asked Questions

**Q1: What is the difference between `ravel()` and `flatten()`?**
A: `ravel()` returns a **view** (shared memory, O(1), changes propagate). `flatten()` returns a **copy** (independent memory, O(n), changes are isolated).

**Q2: Does slicing a NumPy array return a view or a copy?**
A: Basic slicing (`arr[1:3]`) returns a **view**. Boolean indexing (`arr[arr > 5]`) and fancy indexing (`arr[[0,2]]`) return **copies**.

**Q3: How do you select a column from a 2D array?**
A: `mat[:, col_index]` — the `:` selects all rows, the integer selects the column. Example: `mat[:, 2]` gives the third column.

**Q4: What does `mat[0, 1]` do vs `mat[0][1]`?**
A: Same result, but `mat[0, 1]` is a single indexing operation (faster), while `mat[0][1]` chains two operations (creates a temporary array first).

**Q5: What does `np.where()` return?**
A: A tuple of arrays — one per dimension — containing the indices where the condition is True. For 1D: `(array_of_indices,)`.

**Q6: What happens when you resize an array to a larger shape?**
A: The original data is preserved and new positions are filled with zeros. It's an in-place operation.

**Q7: Can `reshape()` change the total number of elements?**
A: No — that's the key difference from `resize()`. `reshape` requires the total to stay the same; `resize` can change it.

### Tricky Output Questions

**Q1: What is the output?**

```python
a = np.array([[1, 2], [3, 4]])
b = a.ravel()
b[0] = 99
print(a[0, 0])
```

**A:** `99`. `ravel()` returns a view, so modifying `b[0]` changes `a[0, 0]`.

**Q2: What is the output?**

```python
a = np.array([[1, 2], [3, 4]])
b = a.flatten()
b[0] = 99
print(a[0, 0])
```

**A:** `1`. `flatten()` returns a copy, so modifying `b` doesn't affect `a`.

**Q3: What is the output?**

```python
mat = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])
print(mat[[0, 2], [2, 0]])
```

**A:** `[3, 7]`. Fancy indexing pairs: `(0, 2) → 3` and `(2, 0) → 7`.

### One-Liner Challenges

**Challenge 1:** Reverse a 2D matrix both horizontally and vertically.

```python
mat[::-1, ::-1]
```

**Challenge 2:** Extract all even numbers from an array.

```python
arr[arr % 2 == 0]
```

**Challenge 3:** Flatten a 3D array to 1D without copying data.

```python
arr_3d.ravel()
```

### Explain the Difference

**Q1: `a.ravel()` vs `a.flatten()` vs `a.reshape(-1)`**
A: All produce 1D arrays from the same data. `ravel()` → view (usually). `flatten()` → always a copy. `reshape(-1)` → view if possible (same as `ravel()` in most cases). The practical difference is mutation safety: use `flatten()` when you'll modify the result, `ravel()`/`reshape(-1)` for read-only.

**Q2: `arr[arr > 5]` vs `np.where(arr > 5)`**
A: `arr[arr > 5]` returns the **values** that match (e.g., `[6, 7, 8]`). `np.where(arr > 5)` returns the **indices** where the condition is True (e.g., `(array([5, 6, 7]),)`). Use boolean indexing when you want values; use `np.where` when you need positions.

---

## Practice Problems

### Problem 1: View Detective (Beginner)

**Task:** Create a 3×3 array, make a raveled version and a flattened version. Modify the first element of each. Print the original to show which operation created a view.

**Starter code:**

```python
import numpy as np
original = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])
# Create raveled and flattened versions
# Modify first element of each
# Print original after each modification
```

**Expected Output:**

```
After modifying ravel: original[0,0] = 99 (view — change propagated)
After modifying flatten: original[0,0] = 99 (copy — no further change)
```

**Solution:**

```python
import numpy as np
original = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])

raveled = original.ravel()
raveled[0] = 99
print(f"After modifying ravel: original[0,0] = {original[0,0]} (view — change propagated)")

flattened = original.flatten()
flattened[0] = 0
print(f"After modifying flatten: original[0,0] = {original[0,0]} (copy — no further change)")
```

### Problem 2: Data Filter Pipeline (Intermediate)

**Task:** Given a matrix of test scores, (1) find students who scored above 80, (2) get their indices, (3) replace scores below 50 with 50 (grade floor).

**Starter code:**

```python
import numpy as np
scores = np.array([45, 82, 67, 91, 38, 76, 88, 55, 93, 42])
# 1. Get all scores > 80
# 2. Get indices of scores > 80 using np.where
# 3. Replace all scores < 50 with 50
```

**Expected Output:**

```
High scorers: [82 91 88 93]
High scorer positions: [1 3 6 8]
After grade floor: [50 82 67 91 50 76 88 55 93 50]
```

**Solution:**

```python
import numpy as np
scores = np.array([45, 82, 67, 91, 38, 76, 88, 55, 93, 42])

high_scores = scores[scores > 80]
print(f"High scorers: {high_scores}")

positions = np.where(scores > 80)
print(f"High scorer positions: {positions[0]}")

scores[scores < 50] = 50
print(f"After grade floor: {scores}")
```

### Problem 3: Image Batch Processing (Advanced / Real-World)

**Task:** Simulate processing a batch of 4 grayscale 8×8 images: (1) Create random images, (2) flatten each to a 1D feature vector using ravel (explain why ravel is appropriate here), (3) select only pixels above a brightness threshold, (4) squeeze any singleton dimensions from a model output.

**Starter code:**

```python
import numpy as np
rng = np.random.default_rng(42)
images = rng.integers(0, 256, size=(4, 8, 8), dtype=np.uint8)
# 1. Print batch shape
# 2. Reshape to (4, 64) feature matrix
# 3. Find positions of pixels > 200 in the first image
# 4. Simulate model output shape (1, 4, 1) and squeeze it
```

**Expected Output (shapes):**

```
Batch shape: (4, 8, 8)
Feature matrix shape: (4, 64)
Bright pixel count in image 0: [count varies]
Model output shape: (1, 4, 1) → squeezed: (4,)
```

**Solution:**

```python
import numpy as np
rng = np.random.default_rng(42)
images = rng.integers(0, 256, size=(4, 8, 8), dtype=np.uint8)

print(f"Batch shape: {images.shape}")

features = images.reshape(4, -1)
print(f"Feature matrix shape: {features.shape}")

bright_positions = np.where(images[0] > 200)
print(f"Bright pixel count in image 0: {len(bright_positions[0])}")

model_output = np.array([[[0.1], [0.8], [0.3], [0.9]]])
print(f"Model output shape: {model_output.shape} → squeezed: {np.squeeze(model_output).shape}")
```

---

## Visualization Quick-Reference

```python
import matplotlib.pyplot as plt
import numpy as np

mat = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])

fig, axes = plt.subplots(1, 3, figsize=(15, 4))

axes[0].imshow(mat, cmap='viridis', vmin=0, vmax=10)
axes[0].set_title('Original Matrix')
for i in range(3):
    for j in range(3):
        axes[0].text(j, i, str(mat[i,j]), ha='center', va='center', color='white', fontsize=14)

mask = mat > 5
axes[1].imshow(mask.astype(int), cmap='RdYlGn', vmin=0, vmax=1)
axes[1].set_title('Boolean Mask: mat > 5')
for i in range(3):
    for j in range(3):
        axes[1].text(j, i, str(mask[i,j]), ha='center', va='center', fontsize=10)

reversed_mat = mat[::-1, ::-1]
axes[2].imshow(reversed_mat, cmap='viridis', vmin=0, vmax=10)
axes[2].set_title('Reversed: mat[::-1, ::-1]')
for i in range(3):
    for j in range(3):
        axes[2].text(j, i, str(reversed_mat[i,j]), ha='center', va='center', color='white', fontsize=14)

plt.tight_layout()
plt.show()
```

---

## Mnemonics & Memory Aids

- **"Ravel = Reference, Flatten = Fresh copy"** — Ravel shares memory, Flatten allocates new
- **"Comma is King"** — Always prefer `mat[0, 1]` over `mat[0][1]`
- **"Boolean = Values, Where = Positions"** — `arr[mask]` gives values, `np.where(mask)` gives indices
- **"Resize = Risky, Reshape = Reliable"** — Resize can lose data, Reshape preserves all
- **"Squeeze the Ones"** — `squeeze()` removes axes of length 1 only
- **"Slice = View, Fancy = Copy"** — Basic slicing shares memory, fancy/boolean indexing copies
- **"Step -1 = Reverse"** — `[::-1]` reverses any axis, any dimension

---

## Conceptual Diagrams (ASCII)

### Reshaping Function Family

```
                  Array Shape Operations
                         │
         ┌───────────────┼───────────────┐
         │               │               │
   Same Total Size   Size Can Change   Dimension Removal
         │               │               │
    reshape()        resize()         squeeze()
    ravel()         (in-place,        (removes
    flatten()        pads/truncates)   length-1 axes)
         │
    ┌────┴────┐
    │         │
  View      Copy
  ravel()   flatten()
  reshape()
```

### Indexing Method Family

```
                    NumPy Indexing
                         │
        ┌────────────────┼────────────────┐
        │                │                │
   By Position      By Condition      By Index List
        │                │                │
   Basic Indexing   Boolean Indexing   Fancy Indexing
   arr[0]           arr[arr > 5]      arr[[0, 2, 4]]
   arr[1:3]                           mat[[0,1],[2,2]]
   mat[0, 1]
   mat[:, 2]             │                │
        │           np.where(cond)         │
        │           (returns indices)      │
     Returns             │           Returns
      VIEW            Returns          COPY
                       COPY
```

### Memory: View vs Copy

```
VIEW (ravel, slice, reshape):
┌─────────────┐     ┌─────────────┐
│ Original    │     │ View        │
│ shape:(2,2) │     │ shape:(4,)  │
│ strides:(16,8)│   │ strides:(8,)│
│ data → ────────── │ data → ─────│──┐
└─────────────┘     └─────────────┘  │
                                      ▼
                            [1, 2, 3, 4]  ← SINGLE DATA BUFFER

COPY (flatten, boolean index):
┌─────────────┐     ┌─────────────┐
│ Original    │     │ Copy        │
│ data → ─────│──┐  │ data → ─────│──┐
└─────────────┘  │  └─────────────┘  │
                  ▼                    ▼
         [1, 2, 3, 4]        [1, 2, 3, 4]  ← TWO DATA BUFFERS
```

---

## Quick-Reference Cheat Sheet

```python
import numpy as np

# === RESHAPING ===
arr.reshape(2, 3)            # New shape, same data (view)
arr.reshape(-1)              # Flatten to 1D (view)
arr.ravel()                  # Flatten to 1D (view, fast)
arr.flatten()                # Flatten to 1D (copy, safe)
arr.resize((3, 3), refcheck=False)  # In-place resize (can change size)
np.squeeze(arr)              # Remove length-1 dimensions

# === 1D INDEXING ===
arr[0]                       # First element
arr[-1]                      # Last element
arr[1:4]                     # Elements 1, 2, 3 (view)
arr[:3]                      # First 3 elements (view)
arr[::2]                     # Every other element (view)
arr[::-1]                    # Reversed (view)

# === 2D INDEXING ===
mat[0, 1]                    # Element at row 0, col 1
mat[0]                       # Entire row 0
mat[:, 2]                    # Entire column 2
mat[0:2, 1:]                 # Sub-matrix (rows 0-1, cols 1+)
mat[::-1, ::-1]              # Fully reversed matrix

# === BOOLEAN INDEXING ===
arr[arr > 5]                 # Elements > 5 (copy)
arr[arr > 5] = 0             # Set elements > 5 to 0

# === FANCY INDEXING ===
arr[[0, 2, 4]]               # Elements at positions 0, 2, 4 (copy)
mat[[0, 1], [2, 2]]          # Elements at (0,2) and (1,2)

# === np.where ===
np.where(arr > 5)            # Indices where condition is True
np.where(arr > 5, arr, 0)   # Values where True, else 0

# === MEMORY CHECKS ===
np.shares_memory(a, b)                      # True if view
a.__array_interface__['data'][0]             # Raw memory address
```

---

## Summary (TL;DR)

1. **`ravel()` returns a view** — flattens to 1D with zero memory cost, but changes propagate to the original array
2. **`flatten()` returns a copy** — flattens to 1D with independent memory; safe to modify without side effects
3. **`resize()` is the only in-place reshaping function** — can change total size, pads with zeros or truncates
4. **`squeeze()` cleans up singleton dimensions** — essential for ML model outputs with shapes like `(1, N, 1)`
5. **Basic slicing returns views** (`arr[1:3]`, `mat[:, 2]`) — efficient but mutation-propagating
6. **Boolean and fancy indexing return copies** — `arr[arr > 5]` and `arr[[0, 2]]` allocate new memory
7. **Use `mat[i, j]` not `mat[i][j]`** — the comma syntax is a single fast operation
8. **`np.where()` returns indices, boolean indexing returns values** — use `where` when you need positions

---

## Further Reading & Official Docs

- [NumPy Indexing](https://numpy.org/doc/stable/user/basics.indexing.html)
- [Advanced Indexing](https://numpy.org/doc/stable/reference/arrays.indexing.html)
- [ndarray.reshape](https://numpy.org/doc/stable/reference/generated/numpy.reshape.html)
- [ndarray.ravel](https://numpy.org/doc/stable/reference/generated/numpy.ravel.html)
- [ndarray.flatten](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.flatten.html)
- [ndarray.resize](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.resize.html)
- [np.squeeze](https://numpy.org/doc/stable/reference/generated/numpy.squeeze.html)
- [np.where](https://numpy.org/doc/stable/reference/generated/numpy.where.html)
- [NumPy 2.0 Migration Guide](https://numpy.org/doc/stable/numpy_2_0_migration_guide.html)
- [Internal Memory Layout](https://numpy.org/doc/stable/reference/internals.html)

---

## Flashcard-Style Recall

**Q:** Does `ravel()` return a view or a copy?
**A:** View — shared memory with the original.

**Q:** Does `flatten()` return a view or a copy?
**A:** Copy — independent memory.

**Q:** What does `resize()` do that `reshape()` can't?
**A:** Change the total number of elements (truncate or pad with zeros). Also operates in-place.

**Q:** What does `squeeze()` remove?
**A:** Axes (dimensions) of length 1. E.g., `(1, 3, 1)` → `(3,)`.

**Q:** What does `arr[::-1]` do?
**A:** Reverses the array. Returns a view, not a copy.

**Q:** What's the difference between `arr[arr > 5]` and `np.where(arr > 5)`?
**A:** `arr[arr > 5]` returns the **values** matching the condition. `np.where(arr > 5)` returns the **indices**.

**Q:** Does boolean indexing return a view or copy?
**A:** Copy — because selected elements are non-contiguous.

**Q:** How to select a column from a 2D array?
**A:** `mat[:, col_index]` — colon for "all rows", integer for column.

**Q:** What does `mat[[0,1],[2,2]]` select?
**A:** Elements at paired positions: `(0,2)` and `(1,2)`.

**Q:** How do you verify two arrays share memory?
**A:** `np.shares_memory(a, b)` or compare `a.__array_interface__['data'][0]`.

**Q:** What happens with `arr[arr > 2] = 100`?
**A:** All elements greater than 2 are replaced with 100, in-place.

**Q:** Why prefer `mat[0, 1]` over `mat[0][1]`?
**A:** Single operation vs chained (avoids temporary array creation, faster).
