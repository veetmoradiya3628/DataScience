# NUMPY VECTOR OPERATIONS, MATRIX MULTIPLICATION, AGGREGATION, SORTING, ITERATION, MEMORY & STRING FUNCTIONS

---

## Prerequisites
- NumPy array creation (`np.array()`, `np.arange()`, `np.zeros()`, etc.) — see [numpy-part-1 notes](numpy-part-1.md)
- Array attributes (`.shape`, `.ndim`, `.dtype`) — see [numpy-part-1 notes](numpy-part-1.md)
- Reshaping, indexing, slicing, views — see [numpy-part-2 notes](numpy-part-2.md)
- Python list basics (for comparison examples)
- Basic linear algebra concepts helpful (dot product, matrix multiplication) but not required

---

## Environment Setup

```python
import numpy as np
```

---

## Concept Overview

This module is the **operational heart of NumPy** — where theory meets practical computation. It covers:

1. **Vector & Matrix Operations** — why NumPy addition is fundamentally different from Python list addition, and how to perform mathematically correct dot products and matrix multiplication
2. **Aggregation Functions** — collapsing arrays to summary statistics (`sum`, `min`, `max`, `mean`, `std`, `var`, `median`, `percentile`, `cumsum`)
3. **Sort, Search, Counting** — ordering, finding positions, and counting elements
4. **Iteration** — looping over arrays of any dimensionality
5. **Views vs Copies** — the critical memory model: reference, view, and copy
6. **Stacking** — combining arrays vertically and horizontally
7. **String Operations** — the `np.char` module for vectorized string manipulation

**Why this matters:**
Every data science and ML workflow is built on these primitives. Feature aggregation, model weight updates, data cleaning, sorting ranked predictions, and text preprocessing — all rely on these exact operations.

**Real-world analogy:**
Think of NumPy arrays as spreadsheet columns on steroids. Adding two columns in a spreadsheet adds element-by-element (like NumPy), not appending rows (like Python lists). Aggregation is like the `SUM()`, `AVERAGE()`, and `STDEV()` functions in spreadsheets — but operating on entire matrices with a single command and running at C-level speed.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| `element-wise` | Operation applied independently to each pair of corresponding elements |
| `dot product` | Sum of element-wise products — produces a scalar from two 1D arrays |
| `matrix multiplication` | Rows × Columns summation — produces a 2D result from two 2D arrays |
| `aggregation` | Collapsing an array (or axis) to a single summary value |
| `axis=0` | Operate along rows (collapse rows, keep columns) |
| `axis=1` | Operate along columns (collapse columns, keep rows) |
| `argsort` | Returns indices that would sort the array (not the sorted values) |
| `searchsorted` | Binary search position for inserting a value into a sorted array |
| `bincount` | Count occurrences of each non-negative integer in array |
| `nditer` | NumPy iterator — visits every element regardless of array dimensions |
| `view` | New array object sharing the same underlying data buffer |
| `copy` | Independent duplicate — its own data buffer, mutations don't propagate |
| `vstack` | Stack arrays vertically (along axis=0, increases rows) |
| `hstack` | Stack arrays horizontally (along axis=1, increases columns) |
| `np.char` | NumPy sub-module for vectorized string operations on string arrays |

---

## PART 1: VECTOR OPERATIONS

---

### 1.1 Python List `+` vs NumPy Array `+` — The Critical Difference [BEGINNER]

This is the **first concept the instructor demonstrates** — making clear that `+` on NumPy arrays is NOT the same as `+` on Python lists.

**Python list `+` = concatenation (joins lists end-to-end)**

```python
a_list = [1, 2, 3]
b_list = [4, 5, 6]

print(a_list + b_list)
```

**Output:**
```
[1, 2, 3, 4, 5, 6]
```

The two lists are simply joined — no mathematical operation. This is the Python string/list concatenation behavior extended to lists.

---

**NumPy array `+` = element-wise addition (mathematically correct)**

```python
import numpy as np

a = np.array([1, 2, 3])
b = np.array([4, 5, 6])
print(a + b)
```

**Output:**
```
[5 7 9]
```

**Line-by-line breakdown:**
- `a = np.array([1, 2, 3])` — creates a 1D NumPy array (vector), dtype `int64`, shape `(3,)`
- `b = np.array([4, 5, 6])` — same structure
- `a + b` — adds element pairs: `1+4=5`, `2+5=7`, `3+6=9` — executed in C-level loop, no Python overhead

**Why this works:** NumPy's `+` operator calls the `__add__` dunder method which routes to `np.add()`, a universal function (ufunc) executed in compiled C code over the contiguous memory buffer.

> **Key insight:** Python `+` on lists = concatenation. NumPy `+` on arrays = element-wise arithmetic. This is why you CANNOT just use lists for numerical computation.

---

### 1.2 Element-wise Multiplication [BEGINNER]

```python
a = np.array([1, 2, 3])
b = np.array([4, 5, 6])

a * b  # Element-wise multiplication
```

**Output:**
```
array([ 4, 10, 18])
```

**Line-by-line breakdown:**
- `a * b` — multiplies pairs independently: `1×4=4`, `2×5=10`, `3×6=18`
- Returns a new array; `a` and `b` are unchanged
- **This is NOT the dot product or matrix multiplication** — it's the Hadamard product (element-wise)

**Comparison table:**

| Operation | Python Code | Result | What It Computes |
|-----------|-------------|--------|-----------------|
| List concatenation | `[1,2,3] + [4,5,6]` | `[1,2,3,4,5,6]` | Joins, no math |
| Vector addition | `np.array([1,2,3]) + np.array([4,5,6])` | `[5,7,9]` | Pairwise sum |
| Element-wise mult | `np.array([1,2,3]) * np.array([4,5,6])` | `[4,10,18]` | Pairwise product |
| Dot product | `np.dot(a, b)` | `32` | Weighted sum scalar |

---

### 1.3 Dot Product — 1D Arrays → Scalar [BEGINNER]

```python
a = np.array([1, 2, 3])
b = np.array([4, 5, 6])

print(np.dot(a, b))  # multiplication + summation
```

**Output:**
```
32
```

**How it works — step by step:**
$$\vec{a} \cdot \vec{b} = \sum_{i} a_i \times b_i = (1 \times 4) + (2 \times 5) + (3 \times 6) = 4 + 10 + 18 = 32$$

**Line-by-line breakdown:**
- `np.dot(a, b)` — computes element-wise products then sums them all
- Returns a **scalar** (single number), not an array
- The instructor's comment "multiplication + summation" precisely describes the two steps

**Real-world analogy:** Pricing a shopping cart. If `a` = quantities `[1, 2, 3]` and `b` = prices `[4, 5, 6]`, the dot product gives total cost: `1×$4 + 2×$5 + 3×$6 = $32`.

**Why it matters in ML:** The dot product is the backbone of neural networks. Every neuron computes `w · x + b` (weights dot inputs + bias). This single operation underlies all forward-pass computation.

---

## PART 2: MATRIX MULTIPLICATION

---

### 2.1 2D Matrix Dot Product — np.dot() [INTERMEDIATE]

When both inputs are 2D arrays, `np.dot()` performs matrix multiplication.

```python
a_1 = np.array([[1, 2, 3],
                [4, 5, 6]])
b_1 = np.array([[1, 2, 3],
                [4, 5, 6]])

print(a_1.shape)
print(b_1.shape)
```

**Output:**
```
(2, 3)
(2, 3)
```

Both are `(2, 3)` — 2 rows × 3 columns. To multiply matrices, the inner dimensions must match: `(2, 3) × (3, 2)` → `(2, 2)`. We need to transpose `b_1`.

```python
np.dot(a_1, b_1.T)
```

**Output:**
```
array([[14, 32],
       [32, 77]])
```

**How the multiplication works (ASCII diagram):**
```
a_1 (2×3)      b_1.T (3×2)     result (2×2)
[ 1  2  3 ]   [ 1  4 ]         [ 1×1+2×2+3×3   1×4+2×5+3×6 ]   [ 14  32 ]
[ 4  5  6 ]   [ 2  5 ]    →    [ 4×1+5×2+6×3   4×4+5×5+6×6 ] = [ 32  77 ]
              [ 3  6 ]
```

Verification:
- `[0,0]`: `1×1 + 2×2 + 3×3 = 1 + 4 + 9 = 14` ✓
- `[0,1]`: `1×4 + 2×5 + 3×6 = 4 + 10 + 18 = 32` ✓
- `[1,0]`: `4×1 + 5×2 + 6×3 = 4 + 10 + 18 = 32` ✓
- `[1,1]`: `4×4 + 5×5 + 6×6 = 16 + 25 + 36 = 77` ✓

**Rule:** `np.dot(A, B)` requires `A.shape[1] == B.shape[0]`. Result shape = `(A.shape[0], B.shape[1])`.

---

### 2.2 The `@` Operator — Matrix Multiplication Sugar [BEGINNER]

Python 3.5+ introduced the `@` operator as a clean syntax for matrix multiplication (PEP 465):

```python
a_1 @ b_1.T
```

**Output:**
```
array([[14, 32],
       [32, 77]])
```

Exactly the same result as `np.dot(a_1, b_1.T)`. The `@` operator calls `__matmul__` which delegates to the same NumPy routine.

**When to use `@` vs `np.dot()`:**

| Use `@` | Use `np.dot()` |
|---------|----------------|
| Clean, readable matrix multiply | When you need the function (e.g., `functools.reduce`) |
| In ML code (model.forward) | When dealing with 1D dot products (scalars) |
| PEP 8 preferred for 2D | Legacy codebases |

> **NumPy 2.x Note:** Both `@` and `np.dot()` are fully supported. `np.dot()` also handles higher-dimensional arrays as sum-product over last/second-to-last axes. For ND arrays, prefer `np.matmul()` (which `@` calls) for clarity.

---

### 2.3 Real-World E-Commerce Sales Matrix [INTERMEDIATE]

This is the notebook's **flagship applied example** — calculating multi-state revenue from products sold and prices using matrix multiplication.

**Problem Setup:**
- Amazon sells in 2 states: State 1 and State 2
- 5 products: apple, banana, smartphone, toy, furniture
- Each state sells different quantities at different prices

```python
# Products: apple, banana, smartphone, toy, furniture
shop_sold_amazon = np.array([
    [100, 200, 20, 30, 5],   # state-1-product-sold
    [200, 400, 40, 60, 10]   # state-2-product-sold
])
print(shop_sold_amazon.shape)
```

**Output:**
```
(2, 5)
```

```python
price_amazon = np.array([
    [30,  10, 1000, 10, 200],   # state-1-price
    [34,  12, 1100, 11, 220]    # state-2-price
])
print(price_amazon.shape)
```

**Output:**
```
(2, 5)
```

**Step 1: Understand the transpose**
```python
price_amazon.T
```

**Output:**
```
array([[  30,   34],
       [  10,   12],
       [1000, 1100],
       [  10,   11],
       [ 200,  220]])
```

`price_amazon.T` is `(5, 2)` — 5 products × 2 state prices. Now we can multiply `(2, 5) × (5, 2) → (2, 2)`.

**Step 2: Compute revenue matrix**
```python
sales = np.dot(shop_sold_amazon, price_amazon.T)
```

```python
sales
# [[state_1(price_of_state_1), state_1(price_of_state_2)]
# [state_2(price_of_state_1), state_2(price_of_state_2)]]
```

**Output:**
```
array([[26300, 29230],
       [52600, 58460]])
```

**Interpreting the 2×2 result:**

| | State-1 Prices | State-2 Prices |
|--|---------------|---------------|
| **State-1 Sold** | **26300** (own prices) | 29230 (cross prices) |
| **State-2 Sold** | 52600 (cross prices) | **58460** (own prices) |

- `sales[0,0]` = Revenue if State 1 sold at State 1 prices = **$26,300** (actual State 1 revenue)
- `sales[1,1]` = Revenue if State 2 sold at State 2 prices = **$58,460** (actual State 2 revenue)
- The diagonal elements are the **true revenues** (state matched with its own prices)

**Verification of `sales[0,0]`:**
```
100×30 + 200×10 + 20×1000 + 30×10 + 5×200
= 3000 + 2000 + 20000 + 300 + 1000 = 26300 ✓
```

---

### 2.4 Using Identity Matrix to Extract Diagonal Revenue [INTERMEDIATE]

The instructor demonstrates an elegant use of `np.eye()` to isolate only the true revenues (diagonal of the sales matrix):

```python
diag = np.eye(2)
print(diag)
```

**Output:**
```
[[1. 0.]
 [0. 1.]]
```

```python
np.sum(sales * diag)
```

**Output:**
```
np.float64(84760.0)
```

**How it works:**
- `sales * diag` — element-wise multiply: only diagonal cells survive (multiplied by 1), off-diagonal become 0
- `np.sum(...)` — adds all remaining values: `26300 + 58460 = 84760`
- **Result: Total true revenue across both states = $84,760**

This is a clean NumPy idiom: **use an identity matrix as a mask** to extract the diagonal without loops.

> **See [15-02-2026 notes](15-02-2026-notes.md#7-npeye)** for full coverage of `np.eye()`.

---

## PART 3: AGGREGATION FUNCTIONS

---

### Aggregation Overview [BEGINNER]

Aggregation functions **collapse** arrays (or a specific axis) into summary statistics. They are the NumPy equivalent of SQL's `SUM`, `MIN`, `MAX`, `AVG`, `STDDEV`.

```python
a_2 = np.array([1, 2, 3, 4, 5])
```

---

### 3.1 np.sum() [BEGINNER]

```python
np.sum(a_2)
```

**Output:**
```
np.int64(15)
```

Sums all elements. `1+2+3+4+5 = 15`.

---

### 3.2 np.min() and np.max() [BEGINNER]

```python
np.min(a_2)
```

**Output:**
```
np.int64(1)
```

```python
np.max(a_2)
```

**Output:**
```
np.int64(5)
```

Returns the minimum and maximum element values in the array.

---

### 3.3 np.argmax() and np.argmin() — Index Finders [BEGINNER]

> The instructor explicitly notes: "not an aggregation function" — these return **positions**, not values.

```python
# [1, 2, 100, 45, 450, 2, 5]
a_3 = [1, 2, 100, 45, 450, 2, 5]
np.argmax(a_3)  # index of the max element
```

**Output:**
```
np.int64(4)
```

`450` is the maximum value, located at index `4`.

```python
np.argmin(a_3)
```

**Output:**
```
np.int64(0)
```

`1` is the minimum value, located at index `0`.

**Line-by-line breakdown:**
- `a_3 = [1, 2, 100, 45, 450, 2, 5]` — indices: 0,1,2,3,4,5,6
- `argmax` → index 4 (value 450) — "arg" means "argument" (index)
- `argmin` → index 0 (value 1)

**Real-world use:**
- Find which product has the highest sales: `np.argmax(sales_array)`
- Find the epoch with the lowest loss: `np.argmin(loss_history)`
- Classify images: model output `argmax` = predicted class label

---

### 3.4 Axis Operations with np.sum() [INTERMEDIATE]

Setup: 3-row price matrix for 5 products:

```python
price_amazon = np.array([
    [30,  10, 1000, 10, 200],   # state-1-price
    [34,  12, 1100, 11, 220],   # state-2-price
    [34,  12, 1100, 11, 220]    # state-3-price (same as state-2)
])
```

**Global sum (no axis):**

```python
np.sum(price_amazon)
```

**Output:**
```
np.int64(4004)
```

Sums every single element across the entire 3×5 matrix.

**axis=0 — Sum over rows, keep columns:**

```python
np.sum(price_amazon, axis=0)
# axis->0 means sum over rows, keep columns
```

**Output:**
```
array([  98,   34, 3200,   32,  640])
```

**ASCII Diagram — axis=0:**
```
        col0  col1  col2  col3  col4
row0: [   30,   10, 1000,   10,  200 ]
row1: [   34,   12, 1100,   11,  220 ]
row2: [   34,   12, 1100,   11,  220 ]
          ↓     ↓     ↓     ↓     ↓   ← collapse along rows (axis=0)
      [   98,   34, 3200,   32,  640 ]  ← result shape: (5,)
```

Result: One value per **column** — total price of each product across all states.

**axis=1 — Sum over columns, keep rows:**

```python
np.sum(price_amazon, axis=1)
# axis->1 means sum over columns, keep rows
```

**Output:**
```
array([1250, 1377, 1377])
```

**ASCII Diagram — axis=1:**
```
        col0  col1  col2  col3  col4
row0: [   30,   10, 1000,   10,  200 ]  → 1250  ←  collapse columns (axis=1)
row1: [   34,   12, 1100,   11,  220 ]  → 1377
row2: [   34,   12, 1100,   11,  220 ]  → 1377
                                           ↑
                                    result shape: (3,)
```

Result: One value per **row** — total cost of all products in each state.

**Mnemonic: "axis=0 collapses rows → result is column-shaped; axis=1 collapses columns → result is row-shaped"**

Or remember: **axis=0 goes DOWN (through rows), axis=1 goes ACROSS (through columns).**

---

### 3.5 np.mean() [BEGINNER]

```python
np.mean(a_3)
```

**Output:**
```
np.float64(86.42857142857143)
```

`(1+2+100+45+450+2+5) / 7 = 605 / 7 ≈ 86.43`

```python
np.mean(price_amazon)
```

**Output:**
```
np.float64(266.93333333333334)
```

Global mean: sum of all 15 elements ÷ 15.

```python
np.mean(price_amazon, axis=1)
```

**Output:**
```
array([250. , 275.4, 275.4])
```

Mean per row (state): `(30+10+1000+10+200)/5 = 1250/5 = 250` for state-1. ✓

---

### 3.6 np.std() — Standard Deviation [INTERMEDIATE]

```python
np.std(price_amazon, axis=1)
```

**Output:**
```
array([381.73289091, 419.66539052, 419.66539052])
```

Population standard deviation per row. High std → prices vary widely within a state (e.g., smartphone at 1000 vs banana at 10 drives huge spread).

**Formula:**
$$\sigma = \sqrt{\frac{1}{n}\sum_{i=1}^{n}(x_i - \bar{x})^2}$$

**Real-world use:** Measure price volatility, feature scaling assessment, checking if a dataset is spread out or clustered.

---

### 3.7 np.var() — Variance [INTERMEDIATE]

```python
np.var(price_amazon, axis=1)
```

**Output:**
```
array([145720.  , 176119.04, 176119.04])
```

Variance = std². Notice: `381.73² ≈ 145720` ✓.

**When to use std vs var:**
- `std` has the same units as your data → easier to interpret
- `var` is preferred in mathematical derivations (e.g., PCA, ANOVA)
- `std = np.sqrt(var)` always

---

### 3.8 np.cumsum() — Cumulative Sum [BEGINNER]

```python
np.cumsum([1, 2, 4, 5, 6, 100])
```

**Output:**
```
array([  1,   3,   7,  12,  18, 118])
```

Each element is the running total up to that position:
- `1` → `1`
- `1+2` → `3`
- `1+2+4` → `7`
- `1+2+4+5` → `12`
- `1+2+4+5+6` → `18`
- `1+2+4+5+6+100` → `118`

**Real-world use:** Running totals, cumulative revenue over time, calculating CDFs (cumulative distribution functions), building prefix sums for range queries.

---

### 3.9 np.median() [BEGINNER]

```python
np.median(price_amazon, axis=1)
```

**Output:**
```
array([30., 34., 34.])
```

The middle value when elements are sorted per row. For state-1: `[10, 10, 30, 200, 1000]` sorted → median = `30` ✓.

**Median vs Mean:** Median is **robust to outliers**. The smartphone price (1000) skews the mean to 250 but the median is just 30 — much more representative of "typical" price.

---

### 3.10 np.percentile() [INTERMEDIATE]

```python
data = np.array([1, 3, 5, 7, 9])
print(np.percentile(data, 50))
```

**Output:**
```
5.0
```

The 50th percentile is the median. `np.percentile(arr, 50)` = `np.median(arr)`.

**Syntax:** `np.percentile(a, q)` — `q` is the percentile (0–100).

**Common use cases:**
- `np.percentile(arr, 25)` — 1st quartile (Q1)
- `np.percentile(arr, 75)` — 3rd quartile (Q3)
- `np.percentile(arr, 95)` — 95th percentile (outlier detection threshold)
- `np.percentile(arr, [25, 50, 75])` — entire IQR at once

**Real-world use:** Salary benchmarks ("you're in the 90th percentile"), SLA monitoring (p99 latency), outlier detection.

---

### Aggregation Functions Quick Reference

| Function | What It Returns | Example |
|----------|----------------|---------|
| `np.sum(arr)` | Sum of all elements | `np.sum([1,2,3])` → `6` |
| `np.min(arr)` | Minimum value | `np.min([3,1,2])` → `1` |
| `np.max(arr)` | Maximum value | `np.max([3,1,2])` → `3` |
| `np.argmin(arr)` | Index of minimum | `np.argmin([3,1,2])` → `1` |
| `np.argmax(arr)` | Index of maximum | `np.argmax([3,1,2])` → `0` |
| `np.mean(arr)` | Arithmetic mean | `np.mean([1,2,3])` → `2.0` |
| `np.std(arr)` | Standard deviation | `np.std([1,2,3])` → `0.816` |
| `np.var(arr)` | Variance | `np.var([1,2,3])` → `0.667` |
| `np.cumsum(arr)` | Running sum | `np.cumsum([1,2,3])` → `[1,3,6]` |
| `np.median(arr)` | Middle value | `np.median([1,3,5])` → `3.0` |
| `np.percentile(arr, q)` | q-th percentile | `np.percentile([1,3,5,7,9], 50)` → `5.0` |

---

## PART 4: SORT, SEARCH, COUNTING FUNCTIONS

---

### 4.1 np.sort() and np.argsort() [BEGINNER]

```python
arr = np.array([3, 6, 1, 1, 1, 1, 8, 2, 5, 6])

print("Sorted:", np.sort(arr))
print("Sorted indices:", np.argsort(arr))
# Instead of returning the sorted values themselves,
# it returns an array of indices that, when used to index
# the original array, would produce a sorted sequence.
```

**Output:**
```
Sorted: [1 1 1 1 2 3 5 6 6 8]
Sorted indices: [2 3 4 5 7 0 8 1 9 6]
```

**Line-by-line breakdown:**
- `np.sort(arr)` — returns a **new** sorted array (does NOT modify `arr`)
- `np.argsort(arr)` — returns the **indices** that would sort the array
  - Index 2 → value 1 (first 1)
  - Index 3 → value 1 (second 1)
  - Index 4 → value 1 (third 1)
  - ... and so on until index 6 → value 8 (last)

**Verification:** `arr[[2, 3, 4, 5, 7, 0, 8, 1, 9, 6]]` = `arr` sorted ✓

**Real-world use of `argsort`:**
- Sort prices but track which product each price belongs to
- Rank ML predictions: `np.argsort(probabilities)[::-1]` → top-N indices
- Sort a DataFrame column while keeping row alignment

**`len(arr)` first:**
```python
len(arr)
```
**Output:** `10` — confirms 10 elements before sorting.

---

### 4.2 np.searchsorted() — Binary Search [INTERMEDIATE]

```python
print("Insert 4 position:", np.searchsorted(np.sort(arr), 4))
```

**Output:**
```
Insert 4 position: 6
```

`np.searchsorted(sorted_arr, value)` finds the index where `value` should be inserted to maintain sorted order.

Sorted array: `[1, 1, 1, 1, 2, 3, 5, 6, 6, 8]`  
Insert `4` → between index 5 (value=3) and index 6 (value=5) → **position 6** ✓

**Important:** The input array must already be sorted. Uses binary search internally → **O(log n)** time.

**Real-world use:** Efficiently locate where a new record fits in a sorted dataset, bucket assignment in histograms.

---

### 4.3 np.unique() — Deduplicate [BEGINNER]

```python
print("Unique elements:", np.unique(arr))
```

**Output:**
```
Unique elements: [1 2 3 5 6 8]
```

Returns sorted unique values. `arr = [3, 6, 1, 1, 1, 1, 8, 2, 5, 6]` → unique sorted values: `[1, 2, 3, 5, 6, 8]`.

**Useful parameters:**
```python
values, counts = np.unique(arr, return_counts=True)
# values: [1 2 3 5 6 8]
# counts: [4 1 1 1 2 1]
```

---

### 4.4 np.bincount() — Integer Frequency Count [BEGINNER]

```python
print("Count of each element:", np.bincount(arr))
# 0,1,2,3,4,5,6,7,8
# 0,4,1....
```

**Output:**
```
Count of each element: [0 4 1 1 0 1 2 0 1]
```

`np.bincount(arr)` counts how many times each **non-negative integer** from `0` to `max(arr)` appears.

Reading the output:
- Index 0 → value 0 appears **0** times
- Index 1 → value 1 appears **4** times (`[1,1,1,1]` in arr ✓)
- Index 2 → value 2 appears **1** time
- Index 3 → value 3 appears **1** time
- Index 4 → value 4 appears **0** times (not in arr)
- Index 5 → value 5 appears **1** time
- Index 6 → value 6 appears **2** times
- Index 7 → value 7 appears **0** times
- Index 8 → value 8 appears **1** time

**Constraint:** Input must contain non-negative **integers**. Floats will cause a `TypeError` — see the Common Mistakes section below.

---

### 4.5 Common Mistake: np.bincount() with Floats [DEBUGGING]

```python
arr_new = np.array([3, 6, 1, 1, 1, 1, 1.2, 8, 2, 5, 6])
```

```python
arr_new
```

**Output:**
```
array([3. , 6. , 1. , 1. , 1. , 1. , 1.2, 8. , 2. , 5. , 6. ])
```

Note that including `1.2` caused the entire array to be upcast to `float64` (NumPy's type coercion).

```python
print("Count of each element:", np.bincount(arr_new), rul)
```

**Error:**
```
TypeError: Cannot cast array data from dtype('float64') to dtype('int64') according to the rule 'safe'
```

**Why it fails:**
- `np.bincount` requires non-negative **integer** dtype
- `arr_new` is `float64` because `1.2` forced upcasting
- NumPy refuses to silently truncate float→int (that would lose information)
- Additionally, `rul` is an undefined variable — this would also cause `NameError` if the TypeError didn't fire first

**Fix:**
```python
# Option 1: Ensure integer input
arr_int = arr.astype(np.int64)
np.bincount(arr_int)

# Option 2: Use np.unique with return_counts for float arrays
values, counts = np.unique(arr_new, return_counts=True)
```

---

## PART 5: ITERATION

---

### 5.1 For Loop on 1D Array [BEGINNER]

```python
arr = np.array([1, 2, 3, 4])
for x in arr:
    print(x)
```

**Output:**
```
1
2
3
4
```

Iterating over a 1D array yields individual scalar elements (each is a 0-d NumPy scalar, not a Python `int`).

---

### 5.2 For Loop on 2D Array — Iterates Rows [BEGINNER]

```python
arr_2d = np.array([[1, 2], [3, 4]])

for x in arr_2d:
    print(x)
```

**Output:**
```
[1 2]
[3 4]
```

**Key insight:** Iterating over a 2D array with `for` yields **rows** (1D sub-arrays), NOT individual elements. Each `x` is a NumPy array of shape `(2,)`.

This generalizes: iterating over an N-dimensional array always yields slices along the **first axis** (axis=0).

---

### 5.3 Python range vs np.arange — Brief Comparison [BEGINNER]

The notebook revisits the `list(range(...))` comparison before transitioning to 3D array iteration:

```python
list(range(0, 11))
```

**Output:**
```
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

Returns a plain Python `list` of 11 integers. Unlike `np.arange`, this gives no vectorized operations, dtype control, or memory efficiency.

> **See [numpy-part-1 notes](numpy-part-1.md) for full `np.arange` vs `range` coverage.**

---

### 5.4 np.nditer() — Flat Element-by-Element Iteration [INTERMEDIATE]

The instructor demonstrates the difference between `for` (axis-0 slices) and `np.nditer` (every scalar element, regardless of shape).

**Setup — 3D array:**

```python
arr = np.arange(8).reshape(2, 2, 2)
arr
```

**Output:**
```
array([[[0, 1],
        [2, 3]],

       [[4, 5],
        [6, 7]]])
```

```python
arr.ndim   # attribute of the class
```

**Output:**
```
3
```

**For loop on 3D array → 2D slices:**
```python
for i in arr:
    print(i)
```

**Output:**
```
[[0 1]
 [2 3]]
[[4 5]
 [6 7]]
```

Each iteration yields the two 2×2 "layers" — axis-0 slices of the 3D array.

**np.nditer — visits every scalar:**
```python
for x in np.nditer(arr):
    print(x)
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
```

`np.nditer` flattens the traversal — it visits every element regardless of how many dimensions exist, following C-order (row-major) by default.

**When to use `np.nditer`:**
- When you need element-by-element access over N-dimensional arrays
- When writing general code that works for any array shape
- **Note:** In practice, use vectorized operations instead of `np.nditer` — it's slower than ufuncs but more flexible for custom iteration logic

**Note on `np.arange(8)` with reshape hints:**
```python
np.arange(8)
# 2,4 → reshape(2,4)
# 1,8 → reshape(1,8)
# 2,2,2 → reshape(2,2,2) ← used here
# 2,6 (BEST for 12 elements)
```

The notebook's comments show the instructor exploring reshape targets before settling on `(2,2,2)`.

> **See [18-02-2026 notes](18-02-2026-notes.md#1-reshape) for full reshape coverage.**

---

## PART 6: VIEWS VS COPIES — MEMORY MODEL

---

### Memory Model Overview [INTERMEDIATE]

This is one of the most **critical and commonly misunderstood** aspects of NumPy. There are **three** ways to "assign" a NumPy array to another variable:

| Assignment Type | Shares Object? | Shares Data? | Mutations Propagate? |
|----------------|---------------|-------------|---------------------|
| `b = a` (reference) | ✅ Same object | ✅ Yes | ✅ Yes — same object |
| `b = a.view()` | ❌ Different objects | ✅ Yes | ✅ Yes — same buffer |
| `b = a.copy()` | ❌ Different objects | ❌ No | ❌ No — independent |

---

### 6.1 Reference Assignment — Same Object [BEGINNER]

```python
a = np.array([1, 2, 3])
b = a
```

```python
print(id(a))
print(id(b))
```

**Output (example — addresses vary per run):**
```
4445531152
4445531152
```

`id(a) == id(b)` — they are the **exact same Python object**. `b` is just another name (alias) pointing to the same object.

```python
b[0] = 100
```

```python
print(a)
```

**Output:**
```
[100   2   3]
```

**Mutating `b` mutates `a`** — because they are the same object. This is like two people sharing the same house — if one repaints a wall, the other person sees it too.

---

### 6.2 copy() — Independent Duplicate [BEGINNER]

```python
b = a.copy()

print(id(a))
print(id(b))
```

**Output (example):**
```
4445531152
4444192368
```

Different `id()` values → different Python objects AND different data buffers. Mutating `b` will NOT affect `a`.

---

### 6.3 view() — Shared Data, Different Object [INTERMEDIATE]

```python
a = np.array([1, 2, 3])
b = a.view()
```

```python
print(id(a))
print(id(b))
```

**Output (example):**
```
4445531536
4445531152
```

Different `id()` values (different Python objects), but the underlying data is **shared**.

```python
print(np.shares_memory(a, b))
```

**Output:**
```
True
```

`np.shares_memory(a, b)` is the definitive check — it examines the actual memory buffer, not the Python object identity.

**Why `id()` is misleading for views:**  
`id()` returns the Python object identifier (the container's address). A view creates a new container/wrapper but points its data pointer to the same buffer. Same `id()` → same container. Different `id()` but `shares_memory=True` → different containers, same buffer.

**ASCII Memory Diagram:**
```
Reference:  a ──→ [buffer: 1, 2, 3]
            b ──→ [same buffer]        id(a) == id(b)

Copy:       a ──→ [buffer A: 1, 2, 3]
            b ──→ [buffer B: 1, 2, 3]  id(a) != id(b), no shared memory

View:       a ──→ [wrapper A] ──→ [buffer: 1, 2, 3]
            b ──→ [wrapper B] ──→ [same buffer]      id(a) != id(b), shares_memory = True
```

**When mutations propagate:**
- `b = a` → mutating `b` changes `a` (same object)
- `b = a.view()` → mutating `b[i]` changes `a[i]` (same buffer)
- `b = a.copy()` → mutating `b` does NOT change `a` (independent buffer)

> **See [18-02-2026 notes](18-02-2026-notes.md#ravel-view-vs-copy) for how this applies to `ravel()` (view) vs `flatten()` (copy).**

**Best practice:**
```python
# When you want isolation (e.g., before modifying a subset):
processed = original_data.copy()
processed[processed < 0] = 0  # clip negatives — original_data untouched

# When you want memory efficiency (e.g., reshaping for model input):
batch = data[start:end].reshape(-1, 28*28)  # view when possible
```

---

## PART 7: STACKING — COMBINING ARRAYS

---

### 7.1 np.vstack() — Vertical Stack [BEGINNER]

```python
a = np.array([[1, 2]])
b = np.array([[3, 4]])
```

```python
print(np.vstack((a, b)))
```

**Output:**
```
[[1 2]
 [3 4]]
```

`np.vstack` stacks arrays vertically — it increases the **number of rows** (concatenates along axis=0).

- `a` shape: `(1, 2)` + `b` shape: `(1, 2)` → result shape: `(2, 2)`

**ASCII Diagram:**
```
a: [[1  2]]      b: [[3  4]]

vstack:
[[1  2]
 [3  4]]
```

**Real-world use:** Combine training batches, stack multiple feature vectors into a matrix, append new rows to a dataset.

---

### 7.2 np.hstack() — Horizontal Stack [BEGINNER]

```python
print(np.hstack((a, b)))
```

**Output:**
```
[[1 2 3 4]]
```

`np.hstack` stacks arrays horizontally — it increases the **number of columns** (concatenates along axis=1).

- `a` shape: `(1, 2)` + `b` shape: `(1, 2)` → result shape: `(1, 4)`

**ASCII Diagram:**
```
a: [[1  2]]      b: [[3  4]]

hstack:
[[1  2  3  4]]
```

**Real-world use:** Combine feature columns from different sources, concatenate embeddings, merge multiple feature arrays side-by-side.

**Comparison:**

| Function | Axis | Adds | Example `(1,2) + (1,2)` |
|----------|------|------|-------------------------|
| `np.vstack` | 0 | More rows | → `(2, 2)` |
| `np.hstack` | 1 | More columns | → `(1, 4)` |
| `np.concatenate((a,b), axis=0)` | 0 | More rows (general) | → `(2, 2)` |
| `np.concatenate((a,b), axis=1)` | 1 | More columns (general) | → `(1, 4)` |

---

## PART 8: NUMPY STRING OPERATIONS — np.char MODULE

---

### String Operations Overview [BEGINNER]

NumPy provides the `np.char` sub-module for **vectorized string operations** on arrays of strings (`dtype='<U...'`). All operations work element-wise across the entire string array — no loops needed.

**General pattern:** `np.char.<function_name>(array, ...)`

---

### 8.1 np.char.add() — String Concatenation [BEGINNER]

```python
# "hello" + "hello"
a = np.array(['hello', 'world'])
b = np.array(['_user', '_123'])

print(np.char.add(a, b))
```

**Output:**
```
['hello_user' 'world_123']
```

Element-wise string concatenation — `'hello'+'_user'`, `'world'+'_123'`. This is the string equivalent of `a + b` for numeric arrays.

---

### 8.2 np.char.multiply() — String Repetition [BEGINNER]

```python
print(np.char.multiply(a, 2))
```

**Output:**
```
['hellohello' 'worldworld']
```

Repeats each string `n` times element-wise. Equivalent to Python's `'hello' * 2 = 'hellohello'`.

---

### 8.3 np.char.upper() / lower() / capitalize() — Case Conversion [BEGINNER]

```python
a = np.array(['hello', 'WORLD'])

print(np.char.upper(a))
print(np.char.lower(a))
print(np.char.capitalize(a))
```

**Output:**
```
['HELLO' 'WORLD']
['hello' 'world']
['Hello' 'World']
```

All case conversion functions work element-wise:
- `upper()` → all uppercase
- `lower()` → all lowercase
- `capitalize()` → first letter uppercase, rest lowercase

**Real-world use:** Normalize text data before NLP preprocessing, standardize product names, clean scraped web data.

---

### 8.4 np.char.center() — Centered Padding [INTERMEDIATE]

```python
print(np.char.center(a, 11, fillchar='*'))
```

**Output:**
```
['***hello***' '***WORLD***']
```

Centers each string in a field of width 11, padded with `'*'` on both sides.  
`'hello'` (5 chars) in width 11 → `(11-5)/2 = 3` stars on each side → `'***hello***'` ✓

**Real-world inspired example — Password Masking:**

```python
def password(value):
    p = np.array([value[-4:]])        # take last 4 characters
    print(p)
    print(np.char.center(p, 30, fillchar='*'))

password("canwedoitlaterpass")
```

**Output:**
```
['pass']
['*************pass*************']
```

This demonstrates a practical pattern: mask sensitive data by showing only the last 4 characters of a password, centered in a 30-character field of stars.

---

### 8.5 np.char.replace() — Substring Replacement [BEGINNER]

```python
a = np.array(['data_science', 'machine_learning'])

print(np.char.replace(a, '_', ' '))
```

**Output:**
```
['data science' 'machine learning']
```

Replaces every occurrence of `'_'` with `' '` in each string element. Vectorized equivalent of Python's `str.replace()`.

**Real-world use:** Clean column names (replace spaces/underscores), normalize text, sanitize user input.

---

### 8.6 np.char.split() — String Splitting [BEGINNER]

```python
print(np.char.split(a, sep='_'))
```

**Output:**
```
[list(['data', 'science']) list(['machine', 'learning'])]
```

Splits each string at the separator. Returns an array of lists (object dtype) — each element is a Python list of substrings.

**Note:** The result is an object array (each cell holds a Python list), not a clean 2D string array. For structured text parsing, this is often a first step before further processing.

---

### 8.7 np.char.find() — Substring Position Search [INTERMEDIATE]

```python
print(np.char.find(a, 'learn'))
# ['data_science', 'machine_learning']
```

**Output:**
```
[-1  8]
```

Returns the **index** of the first occurrence of the substring in each element:
- `'data_science'` → `'learn'` not found → `-1`
- `'machine_learning'` → `'learn'` starts at index `8` → `8`

Returns `-1` when not found (same as Python's `str.find()`). Use `np.char.index()` if you want an error on not found.

---

### 8.8 np.char.count() — Substring Occurrence Count [INTERMEDIATE]

```python
print(np.char.count(a, 'a'))
print(np.char.count(a, 'data'))
```

**Output:**
```
[2 2]
[1 0]
```

- `np.char.count(a, 'a')`:
  - `'data_science'` → 'a' at positions 1, 3 → **2**
  - `'machine_learning'` → 'a' at positions 1, 13 → **2**
- `np.char.count(a, 'data')`:
  - `'data_science'` → `'data'` appears **1** time
  - `'machine_learning'` → `'data'` appears **0** times

---

### np.char Functions Quick Reference

| Function | What It Does | Example Output |
|----------|-------------|----------------|
| `np.char.add(a, b)` | Element-wise concatenation | `['hello_user', 'world_123']` |
| `np.char.multiply(a, n)` | Repeat each string n times | `['hellohello', 'worldworld']` |
| `np.char.upper(a)` | Uppercase | `['HELLO', 'WORLD']` |
| `np.char.lower(a)` | Lowercase | `['hello', 'world']` |
| `np.char.capitalize(a)` | First letter upper | `['Hello', 'World']` |
| `np.char.center(a, w, fillchar)` | Center-pad to width `w` | `['***hello***', '***WORLD***']` |
| `np.char.replace(a, old, new)` | Replace substrings | `['data science', 'machine learning']` |
| `np.char.split(a, sep)` | Split at separator | `[['data','science'], ...]` |
| `np.char.find(a, sub)` | Index of first match (-1 if not found) | `[-1, 8]` |
| `np.char.count(a, sub)` | Count occurrences | `[2, 2]` |

> **NumPy 2.x Note:** `np.char` is preserved in NumPy 2.x. For new code working with text data at scale, consider `np.strings` (added in NumPy 2.0) which provides a more modern interface. `np.char` functions call `np.strings` under the hood in NumPy 2.x.

---

## Common Mistakes & Debugging

---

### Mistake 1: Using `+` on Lists Expecting Element-wise Addition

**Wrong:**
```python
a_list = [1, 2, 3]
b_list = [4, 5, 6]
print(a_list + b_list)
```

**Actual output (not an error — just wrong behavior):**
```
[1, 2, 3, 4, 5, 6]
```

**Why it fails:** Python lists use `+` for concatenation, not element-wise math.

**Correct:**
```python
a = np.array([1, 2, 3])
b = np.array([4, 5, 6])
print(a + b)  # [5 7 9]
```

---

### Mistake 2: np.dot() on Incompatible Shapes

**Wrong:**
```python
a = np.array([[1, 2, 3], [4, 5, 6]])   # shape (2, 3)
b = np.array([[1, 2, 3], [4, 5, 6]])   # shape (2, 3)
np.dot(a, b)  # inner dims: 3 != 2
```

**Error:**
```
ValueError: shapes (2,3) and (2,3) not aligned: 3 (dim 1) != 2 (dim 0)
```

**Why it fails:** Matrix multiplication `(m×n) · (p×q)` requires `n == p`. `(2,3)·(2,3)` fails because `3 ≠ 2`.

**Correct:**
```python
np.dot(a, b.T)  # (2,3) · (3,2) → (2,2) ✓
```

---

### Mistake 3: np.bincount() with Float Array

**Wrong:**
```python
arr_new = np.array([3, 6, 1, 1, 1, 1, 1.2, 8, 2, 5, 6])
np.bincount(arr_new)
```

**Error:**
```
TypeError: Cannot cast array data from dtype('float64') to dtype('int64') according to the rule 'safe'
```

**Why it fails:** `np.bincount` requires non-negative integers. Including `1.2` upcast the entire array to `float64`.

**Correct:**
```python
# Use unique+counts for float arrays
values, counts = np.unique(arr_new, return_counts=True)

# Or cast to int if the floats are whole-valued
np.bincount(arr_new.astype(np.int64))
```

---

### Mistake 4: Mutating a View / Reference Unexpectedly

**Wrong (when you intended independence):**
```python
a = np.array([1, 2, 3])
b = a        # or b = a.view()
b[0] = 999
print(a)     # oops: [999   2   3]
```

**Why it fails:** `b = a` is a reference (same object); `b = a.view()` shares the same data buffer.

**Correct:**
```python
b = a.copy()
b[0] = 999
print(a)     # [1 2 3] — unaffected ✓
```

**Check before modifying:**
```python
print(np.shares_memory(a, b))  # True = dangerous, False = safe
```

---

### Mistake 5: For Loop on 2D Array Expecting Scalars

**Wrong (expecting individual numbers):**
```python
arr_2d = np.array([[1, 2], [3, 4]])
for x in arr_2d:
    print(x + 1)   # x is a row array, not a scalar
```

**Actual output (not an error — but unexpected):**
```
[2 3]
[4 5]
```

`x` is `[1, 2]` (first row), then `[3, 4]` — not scalars.

**Correct (if you need every element):**
```python
for x in np.nditer(arr_2d):
    print(x + 1)   # 2, 3, 4, 5
```

**Or vectorized (preferred):**
```python
print(arr_2d + 1)  # no loop needed
```

---

## Before NumPy vs After NumPy — Side-by-Side Comparison

### Task: Element-wise addition of two large lists

**Pure Python:**
```python
a_list = list(range(1000000))
b_list = list(range(1000000))
c = [x + y for x, y in zip(a_list, b_list)]
```

**NumPy:**
```python
a = np.array(list(range(1000000)))
b = np.array(list(range(1000000)))
c = a + b
```

**Difference:** NumPy executes in C; Python loops call Python object methods for each element.

---

### Task: Sum each row of a 2D price matrix

**Pure Python:**
```python
prices = [[30, 10, 1000, 10, 200],
          [34, 12, 1100, 11, 220]]
row_sums = [sum(row) for row in prices]
```

**NumPy:**
```python
prices = np.array([[30, 10, 1000, 10, 200],
                   [34, 12, 1100, 11, 220]])
row_sums = np.sum(prices, axis=1)
```

One line, no loop, works on matrices of any size.

---

### Task: Revenue calculation (5 products × 2 states)

**Pure Python:**
```python
sales = [[100,200,20,30,5],[200,400,40,60,10]]
prices = [[30,10,1000,10,200],[34,12,1100,11,220]]
result = [[sum(s*p for s,p in zip(sr,pc)) for pc in prices] for sr in sales]
```

**NumPy:**
```python
result = np.dot(shop_sold_amazon, price_amazon.T)
```

One line, handles broadcasting, dtype, and C-level optimization.

---

## How It Works Internally

### Matrix Multiplication Memory

`np.dot(A, B)` computes `C[i,j] = Σ_k A[i,k] * B[k,j]`. NumPy uses BLAS (Basic Linear Algebra Subprograms) — a battle-tested, hardware-optimized library that leverages:
- SIMD (Single Instruction, Multiple Data) CPU instructions
- Cache-line-optimized memory access patterns
- Multi-core parallelism (in linked BLAS implementations like OpenBLAS, MKL)

This is why `np.dot` on large matrices is orders of magnitude faster than Python loops.

### View Memory Layout

```
a = np.array([1, 2, 3])

Memory buffer: | 1 | 2 | 3 |   ← contiguous int64 values
               ^
               base pointer

b = a.view()
               | 1 | 2 | 3 |   ← SAME buffer
               ^
               base pointer (same address)

b = a.copy()
               | 1 | 2 | 3 |   ← NEW buffer (different address)
```

---

## Edge Cases & Gotchas Catalog

| Edge Case | What Happens | Example |
|-----------|-------------|---------|
| `np.dot` of 1D × 1D | Returns scalar | `np.dot([1,2],[3,4])` → `11` |
| `np.dot` of 2D × 1D | Returns 1D array | `np.dot([[1,2],[3,4]], [1,1])` → `[3,7]` |
| `np.sort` on 2D with no axis | Sorts flattened | Use `axis=0` or `axis=1` |
| `np.argsort` ties | Stable by default | Equal values preserve original order |
| `np.bincount([])` | Returns `array([], dtype=int64)` | Empty array → empty count |
| `np.unique` on 2D | Flattens first | Add `axis=0` to keep rows |
| `np.hstack` on 1D | Concatenates flat | `np.hstack([[1,2],[3,4]])` → `[1,2,3,4]` |
| `np.vstack` on 1D | Stacks as 2D rows | `np.vstack([[1,2],[3,4]])` → `[[1,2],[3,4]]` |
| `np.char.find` on no match | Returns `-1` | Unlike `np.char.index` which raises |
| `np.percentile` with interpolation | Default='linear' in 2.x | Behavior changed from 1.x |
| `np.cumsum` on 2D no axis | Flattens | Use `axis=0` or `axis=1` explicitly |

---

## Best Practices & Pythonic Way

- **Prefer `@` over `np.dot()`** for 2D matrix multiplication — more readable
- **Always transpose before matmul** when inner dims don't align — use `.T`
- **Specify `axis=` explicitly** in aggregation calls for clarity — don't rely on default (flatten)
- **Use `np.argmax/argmin`** over `arr.tolist().index(max(arr))` — it's O(n) in C vs O(n+overhead)
- **Use `.copy()` defensively** when passing arrays to functions that modify in-place
- **Use `np.shares_memory(a, b)`** to verify memory sharing before assuming independence
- **Prefer `np.unique(arr, return_counts=True)`** over `np.bincount` when working with float or non-contiguous integer arrays
- **Use `np.nditer` only when needed** — prefer vectorized operations; `nditer` is still slower than ufuncs

---

## When to Use / When NOT to Use

| Operation | Use When... | Avoid When... |
|-----------|-------------|---------------|
| `np.dot` / `@` | Matrix operations, ML forward pass | Simple element-wise ops (use `*`) |
| `np.sum(axis=...)` | Aggregating rows/columns | You need running total (use `cumsum`) |
| `np.argmax` | Finding the index of the max | You need the value itself (use `np.max`) |
| `np.sort` | You need a new sorted array | You want to sort in-place (use `arr.sort()`) |
| `np.bincount` | Counting integer frequencies | Float arrays or sparse integer ranges |
| `np.nditer` | Custom iteration over N-D arrays | Vectorized operation is possible |
| `.copy()` | You need mutation isolation | Memory is tight and sharing is safe |
| `.view()` | Memory-efficient reshape/slice without copy | You plan to modify independently |
| `np.vstack/hstack` | Combining small arrays | Concatenating many arrays in a loop (use `np.concatenate` with pre-allocated list) |
| `np.char.*` | Vectorized string ops on string arrays | Large-scale NLP (use pandas/spaCy) |

---

## Related Topics & Connections

- **Array creation** — `np.arange`, `np.eye`, `np.zeros` used in these examples → [numpy-part-1 notes](numpy-part-1.md)
- **Reshaping & views** — `.reshape()`, `.view()`, `.copy()` foundations → [numpy-part-2 notes](numpy-part-2.md)
- **Broadcasting** — element-wise ops like `sales * diag` use broadcasting rules → [numpy-part-1 notes](numpy-part-1.md)
- **Boolean indexing** — `arr[arr > 5]` complements `np.where()` → [numpy-part-2 notes](numpy-part-2.md)

---

## Interoperability Guide

| From/To | Method | Notes |
|---------|--------|-------|
| NumPy → Pandas | `pd.DataFrame(arr)` | Column names default to 0, 1, 2... |
| Pandas → NumPy | `df.to_numpy()` | `.values` works but is legacy |
| NumPy → PyTorch | `torch.from_numpy(arr)` | **Shares memory** — modifications propagate! |
| PyTorch → NumPy | `tensor.numpy()` | Must be on CPU, no grad |
| NumPy → Python list | `arr.tolist()` | Deep conversion, loses dtype info |
| NumPy → SciPy | `scipy.sparse.csr_matrix(arr)` | For sparse matrix operations |
| String array → Pandas | `pd.Series(str_arr)` | Full string method access via `.str` |

---

## Deprecation & NumPy 2.x Migration Notes

| Old (1.x) | New (2.x) | Notes |
|-----------|-----------|-------|
| `np.char.*` | `np.strings.*` (added in 2.0) | `np.char` still works but `np.strings` is recommended for new code |
| `np.bool`, `np.int`, `np.float` type aliases | Use `bool`, `int`, `float` or `np.bool_`, `np.int64`, `np.float64` | **Removed in 2.0** |
| `np.random.seed()` | `np.random.default_rng()` | New Generator API is preferred |
| `np.VisibleDeprecationWarning` on jagged arrays | Hard error in 2.0 | Use `dtype=object` explicitly |
| `np.percentile` interpolation kwarg | `method` kwarg renamed in 1.22, behavior differences in 2.0 | Check output values when upgrading |

---

## Interview & Exam Corner

### Frequently Asked Questions

**Q1: What is the difference between `a + b` for Python lists and NumPy arrays?**  
A: Python list `+` concatenates (joins) the lists end-to-end. NumPy `+` performs element-wise addition. To add lists element-wise in pure Python, you need `[x+y for x,y in zip(a,b)]`.

**Q2: What is the difference between `np.dot()` and element-wise multiplication (`*`)?**  
A: `*` multiplies corresponding elements, producing an array of the same shape. `np.dot` on 1D arrays computes the sum of element-wise products (a scalar). On 2D arrays, `np.dot` performs matrix multiplication (rows × columns summation).

**Q3: When would you use `axis=0` vs `axis=1` in aggregation?**  
A: `axis=0` collapses along rows (results have shape equal to the number of columns — one value per column). `axis=1` collapses along columns (results have shape equal to the number of rows — one value per row). Mnemonic: axis=0 goes **down**, axis=1 goes **across**.

**Q4: What is the difference between a view and a copy in NumPy?**  
A: A view shares the underlying data buffer — mutations through the view affect the original. A copy has an independent data buffer — mutations are isolated. Use `np.shares_memory(a, b)` to check. `.ravel()` returns a view when possible; `.flatten()` always returns a copy.

**Q5: Why does `np.bincount()` fail on float arrays?**  
A: `np.bincount` works by using array values as indices into a count array, which requires non-negative integers. Float values cannot safely index memory positions. Use `np.unique(arr, return_counts=True)` for float arrays.

**Q6: What does `np.argsort()` return and how is it different from `np.sort()`?**  
A: `np.sort()` returns the sorted values. `np.argsort()` returns the **indices** that would produce a sorted sequence when used to index the original array. `arr[np.argsort(arr)]` equals `np.sort(arr)`.

**Q7: What is `np.nditer` and when would you use it over a `for` loop?**  
A: `np.nditer` creates a flat iterator that visits every element of an N-dimensional array regardless of shape. A regular `for` loop over an N-D array iterates along axis=0 only. Use `np.nditer` when you need element-by-element access over arrays of unknown or arbitrary dimensionality.

**Q8: What is the `@` operator in NumPy?**  
A: The `@` operator performs matrix multiplication (calls `np.matmul()`). It was introduced in Python 3.5 (PEP 465) specifically for linear algebra. `A @ B` is equivalent to `np.matmul(A, B)` and nearly equivalent to `np.dot(A, B)` for 2D arrays.

**Q9: How do you find the total revenue for each state separately (not combined)?**  
A: Use `np.sum(sales_matrix, axis=1)` to get per-row totals, or extract the diagonal with `np.diag(sales_matrix)` if using a square revenue matrix.

---

### Tricky Output Questions

**Q1: What does this print?**
```python
a = np.array([1, 2, 3])
b = a
b[0] = 99
print(a[0])
```
**A:** `99` — `b = a` is a reference, same object, mutation propagates.

---

**Q2: What does this print?**
```python
arr = np.array([3, 6, 1, 1, 1, 1, 8, 2, 5, 6])
print(np.bincount(arr)[1])
```
**A:** `4` — value `1` appears 4 times in `arr`, at index 1 of `bincount`.

---

**Q3: What is the output of this?**
```python
a = np.array([[1, 2], [3, 4]])
print(a.sum(axis=0))
print(a.sum(axis=1))
```
**A:**
```
[4 6]   # axis=0: sum each column: 1+3=4, 2+4=6
[3 7]   # axis=1: sum each row:    1+2=3, 3+4=7
```

---

### One-Liner Challenges

**Challenge 1:** Given `prices = np.array([30, 10, 1000, 10, 200])` and `quantities = np.array([100, 200, 20, 30, 5])`, calculate total revenue in one line.
```python
np.dot(prices, quantities)  # → 26300
```

**Challenge 2:** Find the index of the second largest element in an array.
```python
np.argsort(arr)[-2]
```

**Challenge 3:** Normalize an array to [0, 1] range in one line.
```python
(arr - arr.min()) / (arr.max() - arr.min())
```

---

### Explain the Difference

**Q: What is the difference between `np.sort(arr)` and `arr.sort()`?**  
A: `np.sort(arr)` returns a **new** sorted array and leaves `arr` unchanged. `arr.sort()` sorts **in-place** (modifies `arr` directly) and returns `None`. Use `np.sort()` when you need the original preserved; use `arr.sort()` for memory efficiency.

**Q: What is the difference between `np.argmax` and `np.max`?**  
A: `np.max(arr)` returns the **maximum value** in the array. `np.argmax(arr)` returns the **index** (position) of the maximum value. `arr[np.argmax(arr)] == np.max(arr)` is always True.

---

## Practice Problems

### Problem 1 — Beginner: Revenue Calculator
**Task:** Given `units_sold = np.array([10, 5, 3])` and `price_per_unit = np.array([100, 200, 500])`, calculate:
1. Total revenue (scalar)
2. Revenue per product (array)

**Starter code:**
```python
import numpy as np
units_sold = np.array([10, 5, 3])
price_per_unit = np.array([100, 200, 500])
```

**Expected output:**
```
Total revenue: 3500
Per-product revenue: [1000 1000 1500]
```

**Solution:**
```python
total = np.dot(units_sold, price_per_unit)           # 10*100 + 5*200 + 3*500 = 3500
per_product = units_sold * price_per_unit             # [1000, 1000, 1500]
print("Total revenue:", total)
print("Per-product revenue:", per_product)
```

---

### Problem 2 — Intermediate: Sales Statistics Report
**Task:** Given a 2D sales matrix (3 regions × 4 quarters), compute:
1. Total sales per region (row sums)
2. Best quarter globally (argmax)
3. Top region (argmax of row sums)
4. Cumulative sales over quarters for each region

**Starter code:**
```python
sales = np.array([
    [120, 150, 130, 170],   # North
    [200, 220, 180, 250],   # South
    [80,  90,  95, 110]     # East
])
```

**Expected output:**
```
Per-region totals: [570 850 375]
Best quarter (global): 7  (index of 250)
Top region index: 1  (South)
Cumulative: [[120 270 400 570]
             [200 420 600 850]
             [ 80 170 265 375]]
```

**Solution:**
```python
region_totals = np.sum(sales, axis=1)
best_quarter = np.argmax(sales)          # flattened index: row 1, col 3 → 1*4+3 = 7
top_region = np.argmax(region_totals)
cumulative = np.cumsum(sales, axis=1)

print("Per-region totals:", region_totals)
print("Best quarter (global):", best_quarter)
print("Top region index:", top_region)
print("Cumulative:\n", cumulative)
```

---

### Problem 3 — Advanced / Real-World: Text Preprocessing Pipeline
**Task:** Given a product name array with messy formatting, build a NumPy char pipeline that:
1. Normalizes spaces (replace `_` with ` `)
2. Capitalizes each name
3. Masks names containing `'sale'` with `'***SALE***'` padding to width 15

**Starter code:**
```python
products = np.array(['laptop_pro', 'flash_sale_item', 'desk_chair', 'clearance_sale'])
```

**Expected output:**
```
Normalized:  ['laptop pro' 'flash sale item' 'desk chair' 'clearance sale']
Capitalized: ['Laptop Pro' 'Flash Sale Item' 'Desk Chair' 'Clearance Sale']
Sale mask:   ['  laptop pro  ' '***SALE***' '  desk chair  ' '***SALE***']  (approx widths)
```

**Solution:**
```python
normalized = np.char.replace(products, '_', ' ')
capitalized = np.char.capitalize(normalized)
has_sale = np.char.find(normalized, 'sale') >= 0
masked = np.where(has_sale,
                  np.char.center(np.array(['SALE'] * len(products)), 10, '*'),
                  np.char.center(capitalized, 14))
print("Normalized: ", normalized)
print("Capitalized:", capitalized)
print("Sale mask:  ", masked)
```

---

## Visualization Quick-Reference

```python
import numpy as np
import matplotlib.pyplot as plt

# Visualize aggregation results
price_amazon = np.array([
    [30, 10, 1000, 10, 200],
    [34, 12, 1100, 11, 220],
    [34, 12, 1100, 11, 220]
])
products = ['Apple', 'Banana', 'Smartphone', 'Toy', 'Furniture']

# Bar chart: sum per product (axis=0)
product_totals = np.sum(price_amazon, axis=0)
plt.bar(products, product_totals)
plt.title('Total Price Across States Per Product')
plt.ylabel('Total Price')
plt.show()

# Line chart: cumulative sum
plt.plot(np.cumsum([1, 2, 4, 5, 6, 100]))
plt.title('Cumulative Sum')
plt.xlabel('Index')
plt.ylabel('Cumulative Value')
plt.show()

# Heatmap: sales matrix
sales = np.array([[26300, 29230], [52600, 58460]])
plt.imshow(sales, cmap='YlOrRd')
plt.colorbar(label='Revenue ($)')
plt.title('Sales Revenue Matrix')
plt.xticks([0,1], ['State-1 Prices', 'State-2 Prices'])
plt.yticks([0,1], ['State-1 Sold', 'State-2 Sold'])
plt.show()
```

---

## Mnemonics & Memory Aids

- **"axis=0 goes DOWN, axis=1 goes ACROSS"** — axis=0 reduces rows (result is column-shaped), axis=1 reduces columns (result is row-shaped)
- **"dot = multiply THEN sum"** — np.dot always multiplies element-wise first, then sums along the shared dimension
- **"@ for matrices, * for elements"** — use `@` when you want matrix math, use `*` for Hadamard (element-wise) product
- **"bincount needs bins"** — bins are integer indices; floats can't be indices → TypeError
- **"argsort = address sort"** — returns addresses (indices), not values
- **"view = viewing the same TV, copy = buying your own TV"** — a view shows the same data, a copy is independent
- **"vstack adds VERTICAL rows, hstack adds HORIZONTAL columns"** — v=vertical=rows, h=horizontal=columns
- **"np.char.find returns -1 on miss"** — just like Python's `str.find()`, returns -1 when substring is absent

---

## Conceptual Diagrams (ASCII)

### Matrix Multiplication Shape Rule
```
      A (m×n)    ×    B (n×p)    =    C (m×p)
                        ↑↑
                  inner dims must match!
```

### Axis Direction in 2D
```
         axis=1 →→→→→→→
       ┌─────────────────┐
  a    │  30   10  1000  │
  x    │  34   12  1100  │ 
  i    │  34   12  1100  │
  s    └─────────────────┘
  =             ↓
  0     axis=0 collapses rows
                → result shape (5,)

  axis=1 collapses columns → result shape (3,)
```

### View vs Copy Memory
```
┌─────────────────────────────────────────┐
│   Reference (b = a)                     │
│   Python variable 'a' ──→ [obj: id=X]  │
│   Python variable 'b' ──→ [obj: id=X]  │  ← SAME object
│                              ↓          │
│                        [data: 1,2,3]   │
└─────────────────────────────────────────┘

┌─────────────────────────────────────────┐
│   View (b = a.view())                   │
│   'a' ──→ [obj: id=X] ──→ [data:1,2,3]│
│   'b' ──→ [obj: id=Y] ──→ [data:1,2,3]│  ← different objects, SAME data buffer
└─────────────────────────────────────────┘

┌─────────────────────────────────────────┐
│   Copy (b = a.copy())                   │
│   'a' ──→ [obj: id=X] ──→ [data:1,2,3]│
│   'b' ──→ [obj: id=Y] ──→ [data:1,2,3]│  ← different objects, DIFFERENT buffers
└─────────────────────────────────────────┘
```

---

## Quick-Reference Cheat Sheet

```python
import numpy as np

# ── VECTOR OPERATIONS ──────────────────────────
a + b                          # element-wise addition
a * b                          # element-wise multiplication (Hadamard)
np.dot(a, b)                   # dot product (1D→scalar, 2D→matrix mult)
a @ b                          # matrix multiplication (same as np.dot for 2D)

# ── AGGREGATION ────────────────────────────────
np.sum(arr)                    # sum all elements
np.sum(arr, axis=0)            # sum over rows → per-column result
np.sum(arr, axis=1)            # sum over columns → per-row result
np.min(arr)                    # minimum value
np.max(arr)                    # maximum value
np.argmin(arr)                 # index of minimum
np.argmax(arr)                 # index of maximum
np.mean(arr)                   # arithmetic mean
np.mean(arr, axis=1)           # mean per row
np.std(arr, axis=1)            # std deviation per row
np.var(arr, axis=1)            # variance per row
np.cumsum(arr)                 # running cumulative sum
np.median(arr, axis=1)         # median per row
np.percentile(arr, q)          # q-th percentile (q: 0-100)

# ── SORT / SEARCH / COUNT ──────────────────────
np.sort(arr)                   # new sorted array (original unchanged)
arr.sort()                     # in-place sort (returns None)
np.argsort(arr)                # indices that sort arr
np.searchsorted(sorted_arr, v) # binary search insertion point
np.unique(arr)                 # sorted unique values
np.unique(arr, return_counts=True)  # unique + counts
np.bincount(arr)               # count each integer 0..max (integers only!)

# ── ITERATION ──────────────────────────────────
for x in arr:                  # iterates axis-0 slices (rows for 2D)
for x in np.nditer(arr):       # flat element iteration (all dims)
arr.ndim                       # number of dimensions

# ── VIEWS VS COPIES ────────────────────────────
b = a                          # reference (same object, same data)
b = a.copy()                   # independent copy
b = a.view()                   # new object, shared data buffer
np.shares_memory(a, b)         # True if same underlying buffer

# ── STACKING ───────────────────────────────────
np.vstack((a, b))              # stack vertically (more rows)
np.hstack((a, b))              # stack horizontally (more columns)
np.concatenate((a, b), axis=0) # general concatenation

# ── STRING OPERATIONS (np.char) ────────────────
np.char.add(a, b)              # element-wise string concatenation
np.char.multiply(a, n)         # repeat each string n times
np.char.upper(a)               # uppercase
np.char.lower(a)               # lowercase
np.char.capitalize(a)          # capitalize first letter
np.char.center(a, w, fillchar) # center-pad to width w
np.char.replace(a, old, new)   # replace substring
np.char.split(a, sep)          # split at separator → array of lists
np.char.find(a, sub)           # index of sub (-1 if not found)
np.char.count(a, sub)          # count occurrences of sub
```

---

## Summary (TL;DR)

- **NumPy `+` is element-wise addition**, NOT list concatenation — this is the most common beginner mistake
- **`np.dot(a, b)`** is dot product for 1D (→ scalar) and matrix multiplication for 2D — `@` is the clean alternative
- **Aggregations collapse arrays**: `np.sum/mean/std/var/median/percentile` all support `axis=` to collapse specific dimensions
- **`axis=0` collapses rows** (result is column-shaped), **`axis=1` collapses columns** (result is row-shaped)
- **`np.argmax/argmin`** return **indices** (not values); `np.max/min` return values — crucial difference
- **`np.bincount` requires non-negative integers** — use `np.unique(..., return_counts=True)` for floats
- **Three memory models:** reference (`b = a`, same object), view (`b = a.view()`, shared buffer), copy (`b = a.copy()`, independent) — use `np.shares_memory()` to verify
- **`np.nditer`** visits every element element-by-element regardless of array shape, unlike `for` which yields axis-0 slices

---

## Further Reading & Official Docs

- [NumPy Linear Algebra — np.dot, matmul](https://numpy.org/doc/stable/reference/routines.linalg.html)
- [NumPy Statistics — aggregation functions](https://numpy.org/doc/stable/reference/routines.statistics.html)
- [NumPy Sorting — sort, argsort, searchsorted](https://numpy.org/doc/stable/reference/routines.sort.html)
- [NumPy Iteration — nditer](https://numpy.org/doc/stable/reference/arrays.nditer.html)
- [NumPy Memory — views and copies](https://numpy.org/doc/stable/user/basics.copies.html)
- [NumPy String — np.char module](https://numpy.org/doc/stable/reference/routines.char.html)
- [NumPy 2.0 Migration Guide](https://numpy.org/doc/stable/release/2.0.0-notes.html)
- [BLAS — the library behind np.dot performance](https://www.netlib.org/blas/)

---

## Flashcard-Style Recall

**Q: `[1,2,3] + [4,5,6]` in Python → ?**  
A: `[1,2,3,4,5,6]` — list concatenation

**Q: `np.array([1,2,3]) + np.array([4,5,6])` → ?**  
A: `array([5,7,9])` — element-wise addition

**Q: `np.dot(np.array([1,2,3]), np.array([4,5,6]))` → ?**  
A: `32` — `1×4 + 2×5 + 3×6 = 4+10+18 = 32`

**Q: What does `np.sum(arr, axis=0)` do on a 3×5 matrix?**  
A: Sums over rows → returns array of shape `(5,)` (one value per column)

**Q: `np.argmax([1,2,100,45,450,2,5])` → ?**  
A: `4` — index of value `450`

**Q: Why does `np.bincount(np.array([1.2, 2.0, 3.0]))` fail?**  
A: `bincount` requires integer dtype; `1.2` forces `float64`, which can't be safely cast to `int64`

**Q: What does `np.shares_memory(a, b)` return after `b = a.copy()`?**  
A: `False` — copy has its own buffer

**Q: What does `np.shares_memory(a, b)` return after `b = a.view()`?**  
A: `True` — view shares the same buffer

**Q: What does `np.char.find(arr, 'x')` return when `'x'` is not found?**  
A: `-1` (same as Python `str.find()`)

**Q: `np.vstack((np.array([[1,2]]), np.array([[3,4]])))` → shape?**  
A: `(2, 2)` — two rows stacked vertically

**Q: `np.hstack((np.array([[1,2]]), np.array([[3,4]])))` → shape?**  
A: `(1, 4)` — two arrays joined horizontally

**Q: What is the difference between `for x in arr` and `for x in np.nditer(arr)` on a 2D array?**  
A: `for x in arr` yields rows (1D arrays). `for x in np.nditer(arr)` yields individual scalar elements.
