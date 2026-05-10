# Pandas — Index Management, Views vs Copies, Iteration, Sorting & String Operations

> **Source Notebook:** `25-02-2026.ipynb` | **Pandas Version:** 2.x | **Date:** 25 Feb 2026

---

## Prerequisites

- Python fundamentals (lists, dicts, classes, lambdas, loops)
- Pandas Series and DataFrame basics (see `22-02-2026-notes.md`)
- `pd.read_csv()` basics, `.head()`, `.loc[]`, `.iloc[]`
- Understanding of NaN / missing data concepts

---

## Topics Covered in This Notebook

| # | Topic | Key Methods/Concepts |
|---|-------|---------------------|
| 1 | Reading CSV from URL | `pd.read_csv()` |
| 2 | Index Management | `set_index()`, `reset_index()` |
| 3 | Views vs Copies | `SettingWithCopyWarning`, `.copy()`, Copy-on-Write |
| 4 | `set_index()` vs `reindex()` | Comparison, use cases |
| 5 | `reindex()` — Row & Column Alignment | `fill_value`, aligning mismatched DataFrames |
| 6 | Iteration in Pandas | `iterrows()`, `itertuples()`, column iteration |
| 7 | Conditional Column Creation | Loops vs `.apply()` vs vectorized |
| 8 | `axis=0` vs `axis=1` | Concept, how it changes operations |
| 9 | Sorting — `sort_values()` | Single/multi-column, ascending/descending |
| 10 | Sorting — `sort_index()` | Row and column label sorting |
| 11 | Sorting columns alphabetically | `sorted(df.columns)` pattern |
| 12 | String Operations via `.str` | `.lower()`, `.upper()`, `.capitalize()`, `.len()` |

---

## Environment Setup

```python
import pandas as pd

# URLs used in this notebook
titanic_dataset_github_url = "https://raw.githubusercontent.com/datasciencedojo/datasets/refs/heads/master/titanic.csv"
iris_dataset_github_url    = "https://raw.githubusercontent.com/mwaskom/seaborn-data/refs/heads/master/iris.csv"

# Load Titanic dataset
df = pd.read_csv(titanic_dataset_github_url)
```

**Kaggle alternative:** https://www.kaggle.com/datasets/yasserh/titanic-dataset/data

---

---

# Section 1 — Reading CSV from a URL [BEGINNER]

## Concept Overview

`pd.read_csv()` is the most common entry point for tabular data in Pandas. It reads comma-separated values from a **file path**, **URL**, or **file-like object** into a DataFrame. URLs work exactly like local paths — Pandas downloads the file internally using `urllib`.

**Real-world analogy:** It's like opening a Google Sheets link — the data lives somewhere on the internet, but Pandas pulls it directly into your working table.

## Syntax

```python
pd.read_csv(
    filepath_or_buffer,   # path string, URL, or file-like object
    sep=',',              # delimiter (default comma)
    index_col=None,       # column(s) to use as row index
    usecols=None,         # select only specific columns
    dtype=None,           # force column dtypes
    parse_dates=False,    # auto-parse date columns
    na_values=None,       # values to treat as NaN
    nrows=None,           # read only N rows
    chunksize=None,       # read in chunks (for large files)
    encoding='utf-8',     # character encoding
)
```

## Example 1: Load Titanic from GitHub URL [BEGINNER]

```python
import pandas as pd

titanic_url = "https://raw.githubusercontent.com/datasciencedojo/datasets/refs/heads/master/titanic.csv"
df = pd.read_csv(titanic_url)

df.head()
```

**Expected Output (first 5 rows):**

```
   PassengerId  Survived  Pclass                                                 Name     Sex   Age  SibSp  Parch            Ticket     Fare Cabin Embarked
0            1         0       3                              Braund, Mr. Owen Harris    male  22.0      1      0         A/5 21171   7.2500   NaN        S
1            2         1       1  Cumings, Mrs. John Bradley (Florence Briggs Thayer)  female  38.0      0      0          PC 17599  71.2833   C85        C
2            3         1       3                               Heikkinen, Miss. Laina  female  26.0      0      0  STON/O2. 3101282   7.9250   NaN        S
3            4         1       1        Futrelle, Mrs. Jacques Heath (Lily May Peel)  female  35.0      0      0            113803  53.1000  C123        S
4            5         0       3                            Allen, Mr. William Henry    male  35.0      0      0            373450   8.0500   NaN        S
```

**DataFrame shape:** 891 rows × 12 columns

**Line-by-line breakdown:**
- `pd.read_csv(url)` — Pandas calls `urllib` under the hood to fetch and stream the CSV
- `.head()` — returns the first 5 rows; no argument needed for 5 (default)

**SQL equivalent:**
```sql
SELECT * FROM titanic LIMIT 5;
```

### IO Operations Quick Reference

| Format | Read | Write |
|--------|------|-------|
| CSV | `pd.read_csv()` | `df.to_csv()` |
| Excel | `pd.read_excel()` | `df.to_excel()` |
| JSON | `pd.read_json()` | `df.to_json()` |
| Parquet | `pd.read_parquet()` | `df.to_parquet()` |
| SQL | `pd.read_sql()` | `df.to_sql()` |

**Performance tip:** For large datasets, prefer Parquet over CSV — it's columnar, compressed, and 10–50× faster to read.

---

---

# Section 2 — Index Management: `set_index()` & `reset_index()` [BEGINNER → INTERMEDIATE]

## Concept Overview

By default, Pandas assigns a **RangeIndex** (0, 1, 2, …) as the row index. `set_index()` lets you **promote an existing column** to become the row label, enabling label-based `.loc[]` lookups by business keys (names, IDs, dates).

**Real-world analogy:** Like a library catalog — books are physically stored in order 1, 2, 3, …, but the card index lets you look up any book by title or author instantly. `set_index('Name')` makes `Name` your card catalog.

## Syntax

```python
# set_index — promote a column to become the row index
df.set_index(
    keys,           # column name or list of columns
    drop=True,      # remove column from DataFrame (default: True)
    inplace=False,  # modify in-place vs return new DF
)

# reset_index — move the index back to a regular column
df.reset_index(
    drop=False,     # drop=True discards the index instead of making it a column
    inplace=False,
)
```

## Example 1: Set `PassengerId` as Index [BEGINNER]

```python
df_by_passenger_id = df.set_index('PassengerId')
df_by_passenger_id.head()
```

**Expected Output:**

```
             Survived  Pclass                                                 Name     Sex   Age  SibSp  Parch            Ticket     Fare Cabin Embarked
PassengerId
1                   0       3                              Braund, Mr. Owen Harris    male  22.0      1      0         A/5 21171   7.2500   NaN        S
2                   1       1  Cumings, Mrs. John Bradley (Florence Briggs Thayer)  female  38.0      0      0          PC 17599  71.2833   C85        C
3                   1       3                               Heikkinen, Miss. Laina  female  26.0      0      0  STON/O2. 3101282   7.9250   NaN        S
4                   1       1        Futrelle, Mrs. Jacques Heath (Lily May Peel)  female  35.0      0      0            113803  53.1000  C123        S
5                   0       3                            Allen, Mr. William Henry    male  35.0      0      0            373450   8.0500   NaN        S
```

**What changed:** `PassengerId` is now the **index** (bold label on the left), and is no longer a regular column.

## Example 2: Set `Name` as Index + `.loc[]` lookup [INTERMEDIATE]

```python
df_by_name = df.set_index('Name')
df_by_name.head(1)

# Now look up a passenger by name directly
print(df_by_name.loc['Allen, Miss. Elisabeth Walton'])
```

**Expected Output:**

```
Survived         1
Pclass           1
Sex           female
Age             29.0
SibSp            0
Parch            0
Ticket        24160
Fare          211.3375
Cabin            B5
Embarked          S
Name: Allen, Miss. Elisabeth Walton, dtype: object
```

**Why this works:** With `Name` as the index, `.loc['Allen, Miss. Elisabeth Walton']` does an **O(1) label lookup** instead of scanning all 891 rows.

**SQL equivalent:**
```sql
SELECT * FROM titanic WHERE Name = 'Allen, Miss. Elisabeth Walton';
```

## Example 3: `reset_index()` — Restore Column from Index [BEGINNER]

```python
df_by_name = df_by_name.reset_index()
df_by_name.head()
```

**What changed:** `Name` is moved back from the index to a regular column. The index becomes `RangeIndex(0, 1, 2, …)` again.

**Common pattern:**

```python
# After groupby operations, reset_index() is often needed
df.groupby('Sex')['Age'].mean().reset_index()
```

> See [Section 9 — sort_index()] for how index management interacts with sorting.

---

---

# Section 3 — Are Subsets Views or Copies? [INTERMEDIATE → ADVANCED]

## Concept Overview

This is **the #1 Pandas gotcha** that trips up beginners and intermediate users alike. When you filter or slice a DataFrame, you may get either:

- **A view** — a window into the original DataFrame's memory. Modifying it modifies the original.
- **A copy** — an independent object. Modifications stay isolated.

Pandas **does not always tell you which one you got** — leading to silent bugs.

**Real-world analogy:** Think of a Word document and a "linked" vs "embedded" Excel table inside it. A view is a *linked* table — editing the Excel data changes what appears in Word too. A copy is *embedded* — editing it has no effect on the original file.

## The `SettingWithCopyWarning`

```python
# ⚠️ UNSAFE — triggers SettingWithCopyWarning
subset = df[df['Sex'] == 'male']
subset['Age'] = subset['Age'] + 1
# SettingWithCopyWarning: A value is trying to be set on a copy of a slice from a DataFrame
```

**Why it fails:**
- `df[df['Sex'] == 'male']` may return a **view or a copy** depending on internal memory layout
- If it's a view, writing to `subset['Age']` might silently modify `df` — or might not, depending on Pandas internals
- This non-deterministic behavior is the bug — you can't tell what happened without extra checking

## The Safe Fix: `.copy()` [BEGINNER]

```python
# ✅ SAFE — explicit independent copy
subset = df[df['Sex'] == 'male'].copy()
subset['Age'] += 1
```

```python
subset
```

**Expected Output (first 5 rows — Age incremented by 1 for all male passengers):**

```
   PassengerId  Survived  Pclass                            Name   Sex   Age  SibSp  Parch     Ticket     Fare Cabin Embarked
0            1         0       3         Braund, Mr. Owen Harris  male  23.0      1      0  A/5 21171   7.2500   NaN        S
4            5         0       3        Allen, Mr. William Henry  male  36.0      0      0     373450   8.0500   NaN        S
5            6         0       3                Moran, Mr. James  male   NaN      0      0     330877   8.4583   NaN        Q
6            7         0       1         McCarthy, Mr. Timothy J  male  55.0      0      0      17463  51.8625   E46        S
7            8         0       3  Palsson, Master. Gosta Leonard  male   3.0      3      1     349909  21.0750   NaN        S
```

**Note:** `NaN + 1 = NaN` — ages that were missing remain `NaN`. The original `df` is unchanged (verify with `df.head()`).

By calling `.copy()`, you **force an independent copy** in memory. All modifications to `subset` are guaranteed isolated from `df`.

## Views vs Copies Reference Table

| Situation | View or Copy? | Should you use `.copy()`? |
|-----------|---------------|--------------------------|
| `.iloc[]` slicing | Mostly Copy | Optional |
| `.loc[]` slicing | May be View | Recommended |
| Filtering with condition `df[...]` | May be View | **Recommended** |
| You plan to modify the subset | — | **YES — always** |

## Copy-on-Write (CoW) in Pandas 2.x

> **Pandas 2.0+ introduces Copy-on-Write (CoW) as an opt-in, becoming the default in Pandas 3.0.**

With CoW, every subset is a **lazy copy** — it shares memory with the original until you try to modify it, at which point Pandas automatically creates a real copy.

```python
# Enable CoW globally (Pandas 2.x, opt-in)
pd.options.mode.copy_on_write = True

# In Pandas 3.0, CoW will be on by default — SettingWithCopyWarning will become a hard error
```

**Impact:** CoW eliminates the entire view/copy ambiguity. With CoW enabled, there's no `SettingWithCopyWarning` — every write-to-subset automatically creates a copy.

## SettingWithCopyWarning — Safe vs Unsafe Patterns

```python
# ❌ UNSAFE — chained indexing
df[df['Age'] > 30]['Fare'] = 0.0  # May silently fail

# ✅ SAFE — single .loc with boolean mask
df.loc[df['Age'] > 30, 'Fare'] = 0.0

# ✅ SAFE — always use .copy() when you'll modify
subset = df[df['Age'] > 30].copy()
subset['Fare'] = 0.0
```

**Mnemonic:** "When in doubt, `.copy()` it out."

---

---

# Section 4 — `set_index()` vs `reindex()` [INTERMEDIATE]

## Comparison Table

| Feature | `set_index()` | `reindex()` |
|---------|--------------|-------------|
| **What it does** | **Moves a column** to become the index | **Changes the row/column labels** of a DataFrame |
| **Main Use Case** | Change the row labels using an existing column | Match to new labels, add/remove/reorder rows/columns |
| **Affects index** | Yes — creates a **new index from a column** | Yes — **remaps or aligns to a new index** |
| **Adds NaNs?** | No — only restructures | Yes — inserts NaNs for new labels not in original |
| **Removes data?** | No (column moves to index) | No, but may introduce NaN for missing labels |
| **Use case** | Navigating by business keys (names, IDs) | Aligning two DataFrames before arithmetic |

**Mental model:**
- `set_index()` = "Which column becomes my row label?"
- `reindex()` = "Make this DataFrame conform to a new set of labels"

---

---

# Section 5 — `reindex()` — Conforming to a New Label Structure [INTERMEDIATE]

## Concept Overview

`reindex()` changes a DataFrame or Series to conform to a **new set of labels**. For labels not present in the original, it inserts `NaN` (or a `fill_value` you specify). This is critical when aligning two DataFrames before arithmetic operations.

**Real-world analogy:** Imagine two store sales reports from January and February. January has Store A, B, and C. February only has Store A and C. Before computing month-over-month differences, you need to "fill in" Store B for February — that's `reindex()`.

## Syntax

```python
df.reindex(
    labels=None,        # new row index (iterable)
    columns=None,       # new column index (iterable)
    fill_value=np.nan,  # value to use for missing labels
    method=None,        # 'ffill', 'bfill' for filling gaps
)
```

## Example 1: Aligning Mismatched Row Indexes [INTERMEDIATE]

```python
sales_jan = pd.DataFrame({
    'Sales': [250, 400, 300]
}, index=['Store_A', 'Store_B', 'Store_C'])

sales_feb = pd.DataFrame({
    'Sales': [260, 310]
}, index=['Store_A', 'Store_C'])
```

**Problem: Mismatched indexes**

```
sales_jan             sales_feb
         Sales                  Sales
Store_A    250        Store_A    260
Store_B    400        Store_C    310
Store_C    300
```

Store_B is missing from February — direct subtraction would cause index alignment issues (Pandas aligns on labels, so Store_B becomes NaN in the result).

```python
common_index = sales_jan.index
print(common_index)
# Index(['Store_A', 'Store_B', 'Store_C'], dtype='object')
```

```python
sales_feb_aligned = sales_feb.reindex(common_index)
sales_feb_aligned
```

**Expected Output:**

```
         Sales
Store_A  260.0
Store_B    NaN   ← inserted because Store_B wasn't in sales_feb
Store_C  310.0
```

```python
sales_diff = sales_feb_aligned['Sales'] - sales_jan['Sales']
sales_diff
```

**Expected Output:**

```
Store_A    10.0
Store_B     NaN   ← NaN propagates through arithmetic
Store_C    10.0
dtype: float64
```

## Example 2: Using `fill_value` to Avoid NaN [INTERMEDIATE]

```python
sales_feb_aligned = sales_feb.reindex(common_index, fill_value=0)
sales_feb_aligned
```

**Expected Output:**

```
         Sales
Store_A    260
Store_B      0   ← filled with 0 instead of NaN
Store_C    310
```

```python
sales_diff = sales_feb_aligned['Sales'] - sales_jan['Sales']
sales_diff
```

**Expected Output:**

```
Store_A    10
Store_B  -400   ← 0 - 400 = -400 (assumes Store_B had 0 Feb sales)
Store_C    10
dtype: int64
```

**When to use NaN vs `fill_value=0`:**
- Use `NaN` (default) when absence of data = truly missing/unknown → NaN propagates, preventing false results
- Use `fill_value=0` only when absence = zero (e.g., a store had no sales, not "unknown sales")

## Example 3: Aligning Columns [INTERMEDIATE]

```python
source_a = pd.DataFrame({
    'Product': ['Laptop', 'Tablet', 'Phone'],
    'Price': [1000, 500, 800],
    'Stock': [30, 50, 100]
})

source_b = pd.DataFrame({
    'Price': [980, 520, 810],
    'Product': ['Laptop', 'Tablet', 'Phone']
})

# source_b is missing 'Stock' — reindex columns to match source_a
aligned_b = source_b.reindex(columns=source_a.columns, fill_value=0)
aligned_b
```

**Expected Output:**

```
  Product  Price  Stock
0  Laptop    980      0   ← Stock filled with 0
1  Tablet    520      0
2   Phone    810      0
```

```python
stock_diff = source_a['Stock'] - aligned_b['Stock']
stock_diff
```

**Expected Output:**

```
0     30
1     50
2    100
dtype: int64
```

**Line-by-line breakdown:**
- `reindex(columns=source_a.columns, fill_value=0)` — reorders `source_b` columns to match `source_a`, and fills missing `'Stock'` column with 0
- Now `source_a['Stock'] - aligned_b['Stock']` works safely

**SQL equivalent:**
```sql
-- reindex() row alignment is similar to a FULL OUTER JOIN with default fill
SELECT a.Store, a.Sales - COALESCE(b.Sales, 0) AS diff
FROM sales_jan a
LEFT JOIN sales_feb b ON a.Store = b.Store;
```

## When to Use `reindex()`

| Use When… | Avoid When… |
|-----------|-------------|
| Aligning two DataFrames before arithmetic | You just want to select specific rows — use `.loc[]` |
| Adding missing date entries to a time series | The DataFrames already share the same index |
| Reordering columns to match a schema/template | You want to add a new column — use `df['new'] = ...` |
| Conforming to a target label structure | You're just renaming — use `.rename()` |

---

---

# Section 6 — Iteration in Pandas [BEGINNER → INTERMEDIATE]

## Concept Overview

Pandas iteration means **looping over rows or columns** of a DataFrame. While Pandas is optimized for **vectorized operations** (which operate on entire columns at once), sometimes row-by-row logic is unavoidable.

**Real-world analogy:** A pandas DataFrame is like a filing cabinet. Vectorized operations are a machine that processes all files in parallel. Iteration is a person opening one drawer at a time.

> **Warning:** Iteration in Pandas is 10–100× slower than vectorized operations. Always ask: "Can I do this with `.apply()` or a vectorized operation?"

## Sample DataFrame Used

```python
import pandas as pd

df = pd.DataFrame({
    'Name': ['Alice', 'Bob', 'Charlie'],
    'Age': [25, 30, 35],
    'City': ['Delhi', 'Mumbai', 'Chennai']
})
```

```
      Name  Age     City
0    Alice   25    Delhi
1      Bob   30   Mumbai
2  Charlie   35  Chennai
```

---

## 6.1 — `iterrows()` — Iterate Over Rows as (index, Series) Pairs [BEGINNER]

```python
for index, row in df.iterrows():
    print(f"{index}: {row['Name']} lives in {row['City']} and is {row['Age']} years old.")
```

**Expected Output:**

```
0: Alice lives in Delhi and is 25 years old.
1: Bob lives in Mumbai and is 30 years old.
2: Charlie lives in Chennai and is 35 years old.
```

**How it works:**
- `iterrows()` yields `(index_label, row_as_Series)` pairs
- Each `row` is a **pandas Series** — so `row['Name']`, `row['Age']` work like dictionary access
- `index` is the row label (0, 1, 2 with RangeIndex)

**Important gotcha:** `iterrows()` does **not preserve dtypes** — numeric columns may be cast to `float64` if the row has mixed types (because a Series must have a single dtype).

**Use when:**
- Debugging — printing row contents
- Prototyping logic before vectorizing
- When the logic genuinely depends on row context and cannot be vectorized

**Never use for:** Performance-critical loops over large DataFrames.

---

## 6.2 — `itertuples()` — Iterate Over Rows as Named Tuples [INTERMEDIATE]

```python
for row in df.itertuples():
    print(row)
    print(row.Index)
    print(f"{row.Name} lives in {row.City}, and is {row.Age} years old.", end='\n\n')
```

**Expected Output:**

```
Pandas(Index=0, Name='Alice', Age=25, City='Delhi')
0
Alice lives in Delhi, and is 25 years old.

Pandas(Index=1, Name='Bob', Age=30, City='Mumbai')
1
Bob lives in Mumbai, and is 30 years old.

Pandas(Index=2, Name='Charlie', Age=35, City='Chennai')
2
Charlie lives in Chennai, and is 35 years old.
```

**How it works:**
- `itertuples()` yields **named tuples** — Python's lightweight immutable row objects
- Fields accessed as **attributes**: `row.Name`, `row.Age`, `row.City`, `row.Index`
- `row.Index` = the row label (0, 1, 2 by default)

### `iterrows()` vs `itertuples()` — Which to Use?

| Feature | `iterrows()` | `itertuples()` |
|---------|-------------|----------------|
| Returns | `(index, Series)` | Named tuple |
| Attribute access | `row['col']` | `row.col` |
| Speed | Slower | **~10× faster** |
| Dtype preservation | May cast to float64 | Preserves original dtypes |
| Column name conflicts | N/A | Column names can't start with `_` |
| **Recommendation** | Debugging/prototyping | **Use this when iteration is needed** |

**Why `itertuples()` is faster:** Named tuples are C-level Python structs. `iterrows()` constructs a full `pd.Series` object per row — heap allocation + dtype coercion overhead.

---

## 6.3 — Iterating Over Columns [BEGINNER]

```python
for col in df.columns:
    print(f"Column: {col}")
    print(df[col].values, end='\n\n')
```

**Expected Output:**

```
Column: Name
['Alice' 'Bob' 'Charlie']

Column: Age
[25 30 35]

Column: City
['Delhi' 'Mumbai' 'Chennai']
```

**How it works:**
- `df.columns` is a Pandas `Index` object holding column labels
- Iterating `for col in df.columns` loops over labels as strings
- `df[col].values` returns the underlying NumPy array for that column

**Useful for:**
- Applying the same transformation to all/selected columns programmatically
- Inspecting column data during EDA

```python
# df.columns as a list
list(df.columns)   # ['Name', 'Age', 'City']

df.columns         # Index(['Name', 'Age', 'City'], dtype='object')
```

---

## 6.4 — Iterating Over a Subset of Columns [BEGINNER]

```python
for i in df[['Age', 'City']].iterrows():
    print(i)
```

**How it works:** Pass a column-filtered DataFrame into `iterrows()`. Each `i` is a `(index, Series)` tuple containing only `Age` and `City` values.

---

---

# Section 7 — Conditional Column Creation: Loops vs Apply [BEGINNER → INTERMEDIATE]

## Concept Overview

A very common data engineering task: **create a new column based on logic applied to existing columns**. There are three approaches, in increasing order of preference:

1. **Python `for` loop with `.iterrows()`** — works but slow
2. **`.apply()` with a lambda or named function** — Pythonic, readable
3. **Fully vectorized operations** (`np.where`, `pd.cut`, boolean masks) — fastest

## Example: Classify passengers as 'young' or 'senior' based on Age

### ❌ Method 1: `for` loop with `iterrows()` [BEGINNER — Avoid in Production]

```python
categories = []

for idx, row in df.iterrows():
    print(row)
    print(row['Age'])

    if row['Age'] < 40:
        categories.append("young")
    else:
        categories.append("senior")

df['Category'] = categories
df.head()
```

**Output (after loop):**

```
      Name  Age     City Category
0    Alice   25    Delhi    young
1      Bob   30   Mumbai    young
2  Charlie   35  Chennai    young
```

**Why this works but is bad at scale:**
- You're calling Python's interpreter for every row — **O(n) Python-level overhead**
- For 1 million rows, this is seconds vs milliseconds with vectorized code
- Requires maintaining a separate `categories` list and reassigning it to the DataFrame

### ✅ Method 2a: `Series.apply()` with inline lambda [INTERMEDIATE]

```python
df['Category_vectorized_1'] = df['Age'].apply(lambda age: 'young' if age < 40 else 'senior')
df['Category_vectorized_1'].head()
```

**Output:**

```
0    young
1    young
2    young
Name: Category_vectorized_1, dtype: object
```

**Why this is better:**
- No external list needed — apply builds the Series in one call
- More readable and idiomatic Pandas
- Still calls a Python function per element, but with far less overhead than `iterrows()`

### ✅ Method 2b: `Series.apply()` with a named lambda variable [INTERMEDIATE]

```python
categorize_age_lambda = lambda age: 'young' if age < 40 else 'senior'

df['Category_vectorized_2'] = df['Age'].apply(categorize_age_lambda)
df['Category_vectorized_2'].head()
```

**When to use:** When you'll reuse the logic across multiple columns or need to name it for clarity.

### ✅ Method 2c: `Series.apply()` with a named function [INTERMEDIATE]

```python
def categorize_age_function(age):
    return 'young' if age < 40 else 'senior'

df['Category_vectorized_3'] = df['Age'].apply(categorize_age_function)
df['Category_vectorized_3'].head()
```

**Best practice:** Named functions are preferred over lambdas for anything beyond a single expression — they're testable, debuggable, and descriptive.

## Example: Multi-Column Logic with `DataFrame.apply(axis=1)` [INTERMEDIATE]

```python
def check_if_delhi_and_young(row):
    print(row)
    city = row['City']
    age = row['Age']
    if city == 'Delhi':
        if age < 40:
            return True
        else:
            return False
    else:
        return False

df['If_young_and_from_delhi'] = df.apply(check_if_delhi_and_young, axis=1)
df.head()
```

**Key point:** When your logic needs **multiple columns** (both `City` AND `Age`), you cannot use `Series.apply()` (which operates on one column). You must use `DataFrame.apply(func, axis=1)`, which passes each **row as a Series** to `func`.

> See [Section 8 — axis=0 vs axis=1] for why `axis=1` means "apply across columns (per row)".

## Performance Hierarchy — Summary

| Approach | Speed | When to Use |
|----------|-------|-------------|
| Vectorized (`np.where`, `pd.cut`, arithmetic) | ⚡⚡⚡⚡ Fastest | Simple conditions, arithmetic |
| `Series.apply()` with built-in | ⚡⚡⚡ Fast | Column-level logic |
| `DataFrame.apply(axis=1)` with function | ⚡⚡ Medium | Multi-column row logic |
| `DataFrame.apply(axis=1)` with lambda | ⚡ Slower | Avoid for large DFs |
| `iterrows()` / `itertuples()` | 🐢 Slowest | Debugging only |

**Best-practice alternative for the age example:**

```python
import numpy as np
df['Category_best'] = np.where(df['Age'] < 40, 'young', 'senior')
# Pure NumPy vectorized — fastest possible
```

---

---

# Section 8 — `axis=0` vs `axis=1` [BEGINNER → INTERMEDIATE]

## Concept Overview

The `axis` parameter is one of Pandas' most confusing concepts — it controls **which dimension** an operation reduces or iterates along.

**Real-world analogy:** Think of a DataFrame as a table:
- `axis=0` = "going **down**" — moving along rows (reducing row count)
- `axis=1` = "going **across**" — moving along columns (reducing column count)

## ASCII Diagram

```
         Col_A  Col_B  Col_C
Row_0  [  a00    a01    a02  ]  ← axis=1 travels this direction (across columns)
Row_1  [  a10    a11    a12  ]
Row_2  [  a20    a21    a22  ]
          ↓       ↓       ↓
       axis=0 travels this direction (down rows)
```

## Axis in `DataFrame.apply()`

```python
# axis=0 (default): apply function to each COLUMN (function receives a column Series)
df.apply(func, axis=0)   # func gets: Series = one column

# axis=1: apply function to each ROW (function receives a row Series)
df.apply(func, axis=1)   # func gets: Series = one row
```

From the notebook:

```python
# axis=0: keep column constant, get all rows
# axis=1: keep row constant, get all columns

df['If_young_and_from_delhi'] = df.apply(check_if_delhi_and_young, axis=1)
```

`axis=1` here means: "Call `check_if_delhi_and_young` once per row, giving it the entire row (all columns)."

## Axis in `drop()`, `sum()`, `mean()`, `concat()`

```python
df.drop('Age', axis=1)       # drop a COLUMN (axis=1 = column axis)
df.drop(0, axis=0)           # drop a ROW (axis=0 = row axis)

df.sum(axis=0)               # sum each COLUMN (result: one value per column)
df.sum(axis=1)               # sum each ROW   (result: one value per row)

pd.concat([df1, df2], axis=0)  # stack rows vertically
pd.concat([df1, df2], axis=1)  # stack columns horizontally
```

## Mnemonic

> **axis=0 ↓ = Down the rows (operations collapse row count)**
> **axis=1 → = Across the columns (operations collapse column count)**

Or: `df.apply(f, axis=1)` — `1` reminds you the function sees `1` row (all columns).

Readable aliases: `axis='index'` = `axis=0`, `axis='columns'` = `axis=1`

---

---

# Section 9 — Sorting: `sort_values()` [BEGINNER → INTERMEDIATE]

## Concept Overview

`sort_values()` rearranges the **rows** of a DataFrame (or elements of a Series) based on the values in one or more columns. It's the Pandas equivalent of `ORDER BY` in SQL.

**Real-world analogy:** Sorting passengers by age is like arranging a guest list — you can sort by name, ticket price, cabin class, or any combination.

## Syntax

```python
DataFrame.sort_values(
    by,                      # str or list of str — column(s) to sort by
    axis=0,                  # 0 = sort rows (default)
    ascending=True,          # bool or list of bool — True = low to high
    inplace=False,           # modify DataFrame in-place vs return new one
    na_position='last',      # 'first' or 'last' — where NaN lands
    ignore_index=False,      # reset index in result if True
    key=None,                # custom sort key function
)
```

## Understanding `inplace` — The Concept via a Python Analogy

The notebook teaches `inplace` using a Python class analogy:

```python
class Cal:
    def __init__(self, a):
        self.a = a
    def add(self, b):           # ← inplace=False behavior: returns result, doesn't modify self
        c = self.a + b
        return c
    def inplace_add(self, b):   # ← inplace=True behavior: modifies self directly
        self.a = self.a + b
        return self.a

c_cal = Cal(10)
c_cal.a        # → 10

c_cal.add(10)  # → 20, but c_cal.a is still 10
c_cal.a        # → 10 (unchanged)

c_cal.a = c_cal.add(10)  # ← explicit reassignment = "inplace=False" pattern
c_cal.a        # → 20

c_cal.inplace_add(10)    # → 30, and c_cal.a IS modified
c_cal.a        # → 30
```

**Pandas analogy:**
- `df.sort_values('Age')` = `.add()` — returns a NEW sorted DataFrame, `df` unchanged
- `df.sort_values('Age', inplace=True)` = `.inplace_add()` — modifies `df` directly

> **Pandas 2.x note:** `inplace=True` is being discouraged in favor of method chaining and explicit reassignment (`df = df.sort_values(...)`). Many `inplace` parameters are deprecated in 2.x.

## Example 1: Sort by Single Column (Age, Ascending) [BEGINNER]

```python
df = pd.read_csv(titanic_url)
df['Age'] = df['Age'].fillna(0).astype('int64')

sorted_df = df.sort_values(by='Age')
print(sorted_df[['Name', 'Age']].head(10))
```

**Expected Output (youngest passengers first):**

```
                       Name  Age
803  Thomas, Master. Assad Alexander    0
755    Hamalainen, Master. Viljo    0
644   Baclini, Miss. Eugenie    0
469   Baclini, Miss. Helene Barbara    0
...
```

*(Age 0 = missing filled with 0, then children appear)*

```python
print(sorted_df[['Name', 'Age']].tail(10))
```

**Expected Output (oldest passengers last):**

```
                              Name  Age
630             Barkworth, Mr. Algernon Henry Wilson   80
851                      Svensson, Mr. Johan    74
...
```

```python
sorted_df.iloc[200:300, :]
```

**Expected Output (rows 200–299 — passengers in the middle age range, ~21–22 years old):**

```
     PassengerId  Survived  Pclass                         Name   Sex   Age  SibSp  Parch        Ticket     Fare Cabin Embarked
836          837         0       3             Pasic, Mr. Jakob  male  21.0      0      0        315097   8.6625   NaN        S
624          625         0       3  Bowen, Mr. David John "Dai"  male  21.0      0      0         54636  16.1000   NaN        S
391          392         1       3       Jansson, Mr. Carl Olof  male  21.0      0      0        350034   7.7958   NaN        S
...
```

**Use case:** `iloc[200:300, :]` lets you inspect a specific positional slice of the sorted result — useful for exploring the middle of a distribution without head/tail.

**Note on filling NaN with 0 before sort:**

```python
df['Age'] = df['Age'].astype('int64')           # ❌ FAILS if Age has NaN values
# ValueError: Cannot convert non-finite values (NA or inf) to integer

df['Age'] = df['Age'].fillna(0).astype('int64') # ✅ Fill NaN first, then cast
```

> See [Section 11 — String Operations] for why `object` dtype matters for string columns.

**SQL equivalent:**
```sql
SELECT Name, Age FROM titanic ORDER BY Age ASC;
```

## Example 2: Sort by Multiple Columns [INTERMEDIATE]

```python
# Sort by Pclass ascending, then by Fare descending within each class
df_sorted = df.sort_values(by=['Pclass', 'Fare'], ascending=[True, False])
df_sorted.head()
```

**Expected Output:**

```
   PassengerId  Survived  Pclass                                            Name     Sex  Age  ...   Fare
258          259         1       1                         Ward, Miss. Anna    female   35  ...  512.329
737          738         1       1            Lesurer, Mr. Gustave J    male   35  ...  512.329
...
```

**Line-by-line breakdown:**
- `by=['Pclass', 'Fare']` — list of columns = multi-key sort
- `ascending=[True, False]` — a list of booleans, **one per column** in `by`
  - `True` for `Pclass` = class 1, 2, 3 (ascending)
  - `False` for `Fare` = highest fares first within each class
- Result: Class 1 passengers appear first, with the highest fare at the top

**SQL equivalent:**
```sql
SELECT * FROM titanic ORDER BY Pclass ASC, Fare DESC;
```

## `na_position` — Controlling NaN placement

```python
df.sort_values(by='Age', na_position='first')  # NaN rows appear at top
df.sort_values(by='Age', na_position='last')   # NaN rows appear at bottom (default)
```

## Big-O Complexity

| Operation | Complexity |
|-----------|-----------|
| `sort_values()` | O(n log n) — TimSort |
| Sorting by multiple columns | O(n log n) — same, lexicographic comparison |

---

---

# Section 10 — Sorting: `sort_index()` [BEGINNER → INTERMEDIATE]

## Concept Overview

`sort_index()` sorts the DataFrame by its **row index labels** (default) or **column labels** (`axis=1`). This is useful after `.set_index()` when the index is out of order, or when you want columns in alphabetical order.

**Real-world analogy:** After filing documents (rows) by category name, you sort the cabinet alphabetically — that's `sort_index()`.

## Syntax

```python
DataFrame.sort_index(
    axis=0,          # 0 = sort by row index (default), 1 = sort by column labels
    ascending=True,  # True = A→Z / 0→9 (default)
    inplace=False,
)
```

## Example 1: Sort Rows by Index After `set_index()` [BEGINNER]

```python
df_sort_2 = df.set_index('PassengerId')
df_sorted_by_index = df_sort_2.sort_index()

df_sorted_by_index.head()
```

**Expected Output (index sorted 1, 2, 3, 4, 5):**

```
             Survived  Pclass                                                 Name     Sex   Age  ...
PassengerId
1                   0       3                              Braund, Mr. Owen Harris    male   22  ...
2                   1       1  Cumings, Mrs. John Bradley (Florence Briggs Thayer)  female   38  ...
3                   1       3                               Heikkinen, Miss. Laina  female   26  ...
4                   1       1        Futrelle, Mrs. Jacques Heath (Lily May Peel)  female   35  ...
5                   0       3                            Allen, Mr. William Henry    male   35  ...
```

**When is `sort_index()` necessary?**
- After `pd.concat()` when merged DataFrames have non-sequential indices
- After `.set_index()` on a non-sorted column (e.g., setting Name as index — not alphabetical order)
- After `groupby().apply()` which can produce unsorted hierarchical indices

**SQL equivalent:**
```sql
SELECT * FROM titanic ORDER BY PassengerId ASC;
```

---

---

# Section 11 — Sorting Columns Alphabetically [INTERMEDIATE]

## Concept Overview

When a DataFrame has many columns in arbitrary order, you can **reorder columns alphabetically** using Python's built-in `sorted()` on `df.columns`.

## Example: Alphabetically Reorder Titanic Columns [INTERMEDIATE]

```python
df.columns
```

**Expected Output:**

```
Index(['PassengerId', 'Survived', 'Pclass', 'Name', 'Sex', 'Age', 'SibSp',
       'Parch', 'Ticket', 'Fare', 'Cabin', 'Embarked'],
      dtype='object')
```

```python
sorted(df.columns)
```

**Expected Output:**

```
['Age', 'Cabin', 'Embarked', 'Fare', 'Name', 'Parch', 'PassengerId',
 'Pclass', 'Sex', 'SibSp', 'Survived', 'Ticket']
```

```python
df_sorted = df[sorted(df.columns)]
df_sorted.head()
```

**Expected Output (columns in alphabetical order):**

```
    Age Cabin Embarked      Fare                                            Name  Parch  PassengerId  Pclass     Sex  SibSp  Survived            Ticket
0  22.0   NaN        S    7.2500                         Braund, Mr. Owen Harris      0            1       3    male      1         0         A/5 21171
...
```

**Line-by-line breakdown:**
- `sorted(df.columns)` — Python built-in `sorted()` returns a plain Python list of column names in alphabetical order
- `df[sorted(df.columns)]` — column selection with a list reorders the DataFrame's columns

**Note:** `sort_index(axis=1)` achieves the same result:

```python
df.sort_index(axis=1).head()  # equivalent — sorts columns (axis=1) alphabetically
```

**When to use:**
- Making wide DataFrames with 20+ columns easier to scan
- Before writing to a report to ensure consistent column ordering
- Aligning two DataFrames that have the same columns but in different orders

**Quick column inspection:**

```python
df.columns              # Index of all column names
list(df.columns)        # ['PassengerId', 'Survived', ...]
sorted(df.columns)      # Sorted list
df[['Ticket', 'Name']]  # Manually select specific columns in any order
```

---

---

# Section 12 — Working with Text Data: `.str` Accessor [BEGINNER → INTERMEDIATE]

## Concept Overview

Pandas provides a special `.str` accessor on `object` or `string` dtype Series that exposes **vectorized string operations** — the same operations you'd use on a single Python string, but applied to every element of the Series at once.

**Real-world analogy:** Without `.str`, applying string operations requires a loop: "Go through every passenger name and lowercase it." With `.str`, you say "lowercase all names" — like a find-and-replace across an entire column in Excel, but with full Python string power.

**Why vectorized `.str` is faster than `.apply()`:**

```python
# ❌ Slower — Python function call overhead per element
df['Name'].apply(str.lower)

# ✅ Faster — Pandas dispatches to optimized C-level string routines
df['Name'].str.lower()
```

## Syntax

```python
Series.str.method_name(parameters)
```

No special import needed — `.str` is a property of any `object`/`string` dtype Series.

## Example 1: `.str.lower()` — Lowercase [BEGINNER]

```python
df['Name'].str.lower()
```

**Expected Output (first 5):**

```
0                              braund, mr. owen harris
1    cumings, mrs. john bradley (florence briggs th...
2                               heikkinen, miss. laina
3          futrelle, mrs. jacques heath (lily may peel)
4                            allen, mr. william henry
Name: Name, dtype: object
```

**SQL equivalent:**
```sql
SELECT LOWER(Name) FROM titanic;
```

## Example 2: `.str.upper()` — Uppercase [BEGINNER]

```python
df['Name'].str.upper()
```

**Expected Output (first 5):**

```
0                              BRAUND, MR. OWEN HARRIS
1    CUMINGS, MRS. JOHN BRADLEY (FLORENCE BRIGGS TH...
2                               HEIKKINEN, MISS. LAINA
...
Name: Name, dtype: object
```

## Example 3: `.str.capitalize()` — Capitalize First Letter [BEGINNER]

```python
df['Name'].str.capitalize()
```

**Expected Output (first 5):**

```
0                              Braund, mr. owen harris
1    Cumings, mrs. john bradley (florence briggs th...
2                               Heikkinen, miss. laina
...
Name: Name, dtype: object
```

**Key difference — `.capitalize()` vs `.title()`:**

| Method | Input | Output |
|--------|-------|--------|
| `.str.capitalize()` | `'alice BOB'` | `'Alice bob'` — only first char of string uppercased, rest lowercased |
| `.str.title()` | `'alice BOB'` | `'Alice Bob'` — first char of **each word** uppercased |

## Example 4: `.str.len()` — String Length [BEGINNER]

```python
df['Name'].str.len()
```

**Expected Output (first 5):**

```
0    23
1    51
2    22
3    44
4    24
Name: Name, dtype: int64
```

**Use case:** Finding the longest/shortest names:

```python
df.loc[df['Name'].str.len().idxmax(), 'Name']   # Longest name
df.loc[df['Name'].str.len().idxmin(), 'Name']   # Shortest name
```

## Full `.str` Accessor Reference

| Method | What It Does | Example |
|--------|-------------|---------|
| `.str.lower()` | All lowercase | `'ALICE'` → `'alice'` |
| `.str.upper()` | All uppercase | `'alice'` → `'ALICE'` |
| `.str.capitalize()` | First letter uppercase only | `'alice bob'` → `'Alice bob'` |
| `.str.title()` | Title case each word | `'alice bob'` → `'Alice Bob'` |
| `.str.strip()` | Remove leading/trailing whitespace | `' alice '` → `'alice'` |
| `.str.lstrip()` / `.str.rstrip()` | Strip left/right only | — |
| `.str.len()` | String length | `'Alice'` → `5` |
| `.str.contains('pattern')` | Boolean mask — contains substring | `df['Name'].str.contains('Miss')` |
| `.str.startswith('pre')` | Starts with prefix | — |
| `.str.endswith('suf')` | Ends with suffix | — |
| `.str.replace('old', 'new')` | Replace substring (supports regex) | — |
| `.str.split(',')` | Split by delimiter → list | — |
| `.str.extract('(regex)')` | Extract first regex match group | — |
| `.str.cat(sep=', ')` | Concatenate strings in Series | — |
| `.str.count('pattern')` | Count occurrences | — |
| `.str.get(i)` | Get element i from list/string | — |
| `.str.slice(start, stop)` | Slice each string | — |

## `string` dtype vs `object` dtype

```python
# Legacy — object dtype (NumPy-backed, can hold mixed types)
df['Name'].dtype   # dtype('O')

# Modern — string dtype (Pandas 1.0+, nullable, ArrowDtype in 2.x)
df['Name'].astype('string').dtype   # StringDtype()
```

**Why prefer `string` dtype (Pandas 2.x)?**
- Better `NA` handling — uses `pd.NA` instead of `np.nan`
- More predictable `.str` behavior
- Required for future Arrow-backed string operations

```python
# Convert column to string dtype
df['Name'] = df['Name'].astype('string')
```

## Pandas 2.x Deprecation Note

> In Pandas 2.x, the backing of `object` dtype string columns is transitioning to Arrow-backed `string[pyarrow]` for performance. Use `pd.StringDtype()` or `'string'` as the dtype when creating/casting string columns.

---

---

# Section 13 — Type Conversion & `astype()` [INTERMEDIATE]

## Concept Overview

Type conversion (`astype()`) changes the dtype of a column. It's critical because Pandas infers dtypes on load — `float64` for columns with NaN, `object` for mixed types — and you often need to correct them.

## The Problem with `Age` in Titanic

```python
print(df['Age'].dtype)  # float64 — because Age has missing values
```

Pandas loads `Age` as `float64` because it contains `NaN` values. `NaN` cannot exist in an integer dtype in NumPy (though it can in Pandas nullable `Int64`).

```python
# ❌ Direct cast fails if NaN values exist
df['Age'] = df['Age'].astype('int64')
# ValueError: Cannot convert non-finite values (NA or inf) to integer

# ✅ Fill NaN first, then cast
df['Age'] = df['Age'].fillna(0).astype('int64')
```

**Verify the conversion — last 10 rows (Age is now int, NaN → 0):**

```python
print(df[['Name', 'Age']].tail(10))
```

**Expected Output:**

```
                                         Name  Age
881                        Markun, Mr. Johann   33
882              Dahlberg, Miss. Gerda Ulrika   22
883             Banfield, Mr. Frederick James   28
884                    Sutehall, Mr. Henry Jr   25
885      Rice, Mrs. William (Margaret Norton)   39
886                     Montvila, Rev. Juozas   27
887              Graham, Miss. Margaret Edith   19
888  Johnston, Miss. Catherine Helen "Carrie"    0
889                     Behr, Mr. Karl Howell   26
890                       Dooley, Mr. Patrick   32
```

*Row 888 (`NaN` age) → `0` after `fillna(0)`.*

**Re-sort after type fix and check oldest 20:**

```python
sorted_df = df.sort_values(by='Age')
print(sorted_df[['Name', 'Age']].tail(20))
```

**Expected Output (oldest 20 passengers, now all integers):**

```
                                          Name  Age
625                      Sutton, Mr. Frederick   61
829  Stone, Mrs. George Nelson (Martha Evelyn)   62
555                         Wright, Mr. George   62
252                  Stead, Mr. William Thomas   62
570                         Harris, Mr. George   62
483                     Turkula, Mrs. (Hedwig)   63
275          Andrews, Miss. Kornelia Theodosia   63
438                          Fortune, Mr. Mark   64
545               Nicholson, Mr. Arthur Ernest   64
456                   Millet, Mr. Francis Davis  65
280                           Duane, Mr. Frank   65
54              Ostby, Mr. Engelhart Cornelius   65
33                       Wheadon, Mr. Edward H   66
672                Mitchell, Mr. Henry Michael   70
745               Crosby, Capt. Edward Gifford   70
116                       Connors, Mr. Patrick   70
493                    Artagaveytia, Mr. Ramon   71
96                   Goldschmidt, Mr. George B   71
851                        Svensson, Mr. Johan   74
630       Barkworth, Mr. Algernon Henry Wilson   80
```

**Key difference from the earlier sort:** Now ages are clean integers — no `NaN` rows at the start, no floating-point display. The oldest passenger is confirmed as **80** (Mr. Barkworth).

## Common Type Conversions

```python
df['Age'].astype('int64')           # float → int (fails with NaN)
df['Age'].astype('Int64')           # float → nullable Int64 (handles NaN → pd.NA)
df['Age'].astype('float32')         # downcast for memory savings
df['Name'].astype('string')         # object → Pandas StringDtype
df['Survived'].astype('bool')       # int → bool
df['Pclass'].astype('category')     # int → category (memory efficient for low cardinality)
```

## Safer Alternatives to `astype()`

```python
pd.to_numeric(df['Age'], errors='coerce')    # NaN for anything non-numeric
pd.to_datetime(df['Date'], errors='coerce')  # NaT for anything non-parseable
```

`errors='coerce'` makes type conversion **safe** — failed conversions become `NaN`/`NaT` instead of raising exceptions.

---

---

# Common Mistakes & Debugging [INTERMEDIATE]

## Mistake 1: `SettingWithCopyWarning` — Modifying a filtered subset

**Wrong:**

```python
subset = df[df['Sex'] == 'male']
subset['Age'] = subset['Age'] + 1
# SettingWithCopyWarning: A value is trying to be set on a copy of a slice from a DataFrame
```

**Why it fails:** Pandas doesn't guarantee whether the filter returns a view or a copy. Writing to a possible view produces undefined behavior.

**Correct:**

```python
subset = df[df['Sex'] == 'male'].copy()
subset['Age'] += 1
```

---

## Mistake 2: `astype('int64')` on a column with NaN

**Wrong:**

```python
df['Age'] = df['Age'].astype('int64')
# ValueError: Cannot convert non-finite values (NA or inf) to integer
```

**Why it fails:** NumPy's `int64` cannot represent `NaN`. Titanic's `Age` column has 177 missing values.

**Correct:**

```python
df['Age'] = df['Age'].fillna(0).astype('int64')
# Or use nullable integer dtype:
df['Age'] = df['Age'].astype('Int64')  # capital I — supports pd.NA
```

---

## Mistake 3: `iterrows()` dtype coercion

**Wrong:**

```python
for idx, row in df.iterrows():
    age = row['Age']
    # If df has mixed types, Age may be float even if originally int
```

**Why it fails:** A row Series must have a single dtype, so mixed-type columns get upcast.

**Correct:** Use `itertuples()` instead — preserves original column dtypes.

---

## Mistake 4: `reindex()` producing NaN when `fill_value=0` was intended

**Wrong:**

```python
# Forgot fill_value — NaN silently introduced
aligned = df_small.reindex(target_index)
result = aligned['Sales'] - df_large['Sales']   # NaN propagates silently
```

**Correct:**

```python
aligned = df_small.reindex(target_index, fill_value=0)
result = aligned['Sales'] - df_large['Sales']   # Clean subtraction
```

---

## Mistake 5: Sorting a column with NaN — unexpected NaN placement

**Wrong (assuming NaN appears last by default):**

```python
sorted_df = df.sort_values(by='Age')
# NaN rows appear at END (default) — sorted_df.tail() shows NaN rows
```

**Correct if you want NaN first:**

```python
sorted_df = df.sort_values(by='Age', na_position='first')
```

---

---

# Edge Cases & Gotchas Catalog

| Edge Case | What Happens | Safe Approach |
|-----------|-------------|---------------|
| `set_index('col')` where col has duplicates | Duplicate index labels — `.loc[]` returns multiple rows | Use `verify_integrity=True` to catch this |
| `reindex()` with `method='ffill'` | Forward-fills NaN from previous label | Be careful — may fill with wrong data |
| `iterrows()` on a column with float64 | Integer columns become float in row Series | Use `itertuples()` |
| `sort_values()` with `inplace=True` | Returns `None` — common bug: `df = df.sort_values(..., inplace=True)` sets df to None | Never capture inplace result |
| `df[sorted(df.columns)]` on non-string columns | Fails if columns are integers or mixed types — `sorted()` requires comparable types | Ensure column names are strings |
| `.str.lower()` on NaN values | NaN remains NaN — does not raise error | Chain with `.fillna('')` first if needed |
| `astype('int64')` on float column with NaN | Raises `ValueError` | Use `.fillna(0)` or `Int64` nullable dtype |
| `df.apply(func, axis=1)` with a function that returns a dict | DataFrame with dict keys as columns | Specify `result_type='expand'` if needed |

---

---

# Best Practices & Idiomatic Pandas

- **Always `.copy()` filtered subsets you plan to modify** — avoids `SettingWithCopyWarning` and silent bugs
- **Prefer `itertuples()` over `iterrows()`** when iteration is genuinely needed — 10× faster, dtype-safe
- **Prefer `.apply()` over Python `for` loops** — but prefer vectorized over `.apply()`
- **Use `df.loc[mask, col] = val` for conditional assignment** — never chained indexing
- **Enable Copy-on-Write for Pandas 2.x projects:** `pd.options.mode.copy_on_write = True`
- **Use `fill_value=0` in `reindex()` intentionally** — only when absence = zero, not unknown
- **Name your lambda functions when reusing them** — enables testing and debugging
- **Use `ascending=[True, False]` list for multi-column sort** — one bool per column
- **Use `sort_index(axis=1)` to sort columns** — cleaner than `df[sorted(df.columns)]`
- **Prefer `astype('Int64')` over `fillna(0).astype('int64')`** when preserving missingness is important

---

---

# SQL ↔ Pandas Translation Guide

| SQL | Pandas |
|-----|--------|
| `SELECT * FROM t ORDER BY Age ASC` | `df.sort_values('Age')` |
| `SELECT * FROM t ORDER BY Age DESC` | `df.sort_values('Age', ascending=False)` |
| `ORDER BY Pclass ASC, Fare DESC` | `df.sort_values(['Pclass','Fare'], ascending=[True,False])` |
| `LOWER(Name)` | `df['Name'].str.lower()` |
| `UPPER(Name)` | `df['Name'].str.upper()` |
| `LENGTH(Name)` | `df['Name'].str.len()` |
| `WHERE Sex = 'male'` (for mutation) | `subset = df[df['Sex']=='male'].copy()` |
| `ALTER TABLE SET index = col` | `df.set_index('col')` |
| `FULL OUTER JOIN` with fill | `df1.reindex(df2.index, fill_value=0)` |
| `SELECT * FROM t WHERE col IS NULL` | `df[df['col'].isna()]` |
| `CAST(Age AS INT)` | `df['Age'].astype('int64')` |
| `COALESCE(Age, 0)` | `df['Age'].fillna(0)` |

---

---

# Interoperability Guide

| From/To | Method | Notes |
|---------|--------|-------|
| Pandas → NumPy | `df.to_numpy()` | Column-by-column; dtype coercion possible |
| NumPy → Pandas | `pd.DataFrame(arr, columns=[...])` | Index defaults to RangeIndex |
| Pandas → dict | `df.to_dict(orient='records')` | List of row dicts |
| dict → Pandas | `pd.DataFrame([{'a':1}, {'a':2}])` | Standard DataFrame creation |
| Pandas → CSV | `df.to_csv('file.csv', index=False)` | `index=False` avoids saving RangeIndex |
| Pandas → Parquet | `df.to_parquet('file.parquet')` | Best format for large DataFrames |

---

---

# Deprecation & Pandas 2.x Migration Notes

| Old (1.x) Pattern | New (2.x) Pattern | Notes |
|------------------|------------------|-------|
| `df.append(row)` | `pd.concat([df, new_row_df])` | `append()` removed in 2.0 |
| `inplace=True` everywhere | Prefer `df = df.method(...)` | Many `inplace` params deprecated |
| `df.applymap(func)` | `df.map(func)` | `applymap()` deprecated in 2.1+ |
| `object` dtype strings | `'string'` / `StringDtype()` | Arrow-backed strings in 2.x |
| Silent downcasting in `fillna()` | Must cast explicitly | `FutureWarning` → error in 2.x |
| Copy by default | Copy-on-Write (CoW) | Enable: `pd.options.mode.copy_on_write=True` |
| `datetime64[ns]` always | Resolution-flexible `datetime64` | Can be `[s]`, `[ms]`, `[us]`, `[ns]` |

---

---

# Related Topics & Connections

- **Filtering & Boolean Indexing** — `.copy()` is critical after filtering (→ `22-02-2026-notes.md`)
- **GroupBy** — `reset_index()` is almost always used after GroupBy operations
- **MultiIndex** — `set_index([col1, col2])` creates a MultiIndex, requires `sort_index()` for performance
- **Missing Data** — `fillna()` is covered here in the context of type conversion; see dedicated missing data section
- **Apply vs Vectorized** — see performance hierarchy table in [Section 7]
- **DateTime Sorting** — `sort_values('date_col')` works naturally with `datetime64` dtype

---

---

# Interview & Exam Corner

## Frequently Asked Questions

**Q1: What is the difference between `set_index()` and `reindex()`?**

`set_index()` promotes an existing column to become the row index — it restructures the DataFrame without adding/removing data. `reindex()` changes the index labels to conform to a new set, inserting `NaN` (or `fill_value`) for labels not present in the original. Use `set_index()` to navigate by business keys; use `reindex()` to align two DataFrames before arithmetic.

**Q2: When do you get a view vs a copy in Pandas?**

There is no guaranteed rule — it depends on memory layout and how the slice was performed. `.iloc[]` typically returns a copy; `.loc[]` and boolean filter `df[mask]` may return a view. The safe practice is to always call `.copy()` on any subset you intend to modify. In Pandas 2.x with Copy-on-Write enabled, all subsets behave as lazy copies — the ambiguity is eliminated.

**Q3: What is `SettingWithCopyWarning` and how do you fix it?**

It's a warning that Pandas raises when you try to modify a filtered subset that might be a view into the original DataFrame. Fix: call `.copy()` on the filtered subset before modifying it. In Pandas 3.0, CoW will make this a hard error.

**Q4: What is the difference between `iterrows()` and `itertuples()`?**

`iterrows()` yields `(index, Series)` pairs — each row is a full Series object with potential dtype coercion. `itertuples()` yields named tuples — ~10× faster, preserves dtypes, attributes accessed via dot notation (`row.Name`). Always prefer `itertuples()` when iteration is necessary.

**Q5: What is `axis=0` vs `axis=1` in Pandas?**

`axis=0` operates down the rows (reduces row count or applies function per column). `axis=1` operates across columns (reduces column count or applies function per row). For `df.apply(func, axis=1)`, the function receives one row (all columns) per call. For `df.apply(func, axis=0)`, the function receives one column (all rows) per call.

**Q6: When should you use `sort_index()` vs `sort_values()`?**

Use `sort_values()` to sort by data values in columns (like SQL `ORDER BY`). Use `sort_index()` to sort by the index labels. After operations like `pd.concat()` or `groupby().apply()` that may produce unsorted indices, `sort_index()` restores natural order.

**Q7: How do you safely convert a float column with NaN to int?**

Option 1: `df['col'].fillna(0).astype('int64')` — replaces NaN with 0 before casting. Option 2: `df['col'].astype('Int64')` — Pandas nullable integer dtype that supports `pd.NA` alongside integers. Use Option 2 when you want to preserve missingness.

**Q8: What does `.str.capitalize()` do differently from `.str.title()`?**

`.str.capitalize()` uppercases only the first character of the entire string and lowercases the rest. `.str.title()` uppercases the first character of every word. `'alice BOB'.capitalize()` → `'Alice bob'`; `'alice BOB'.title()` → `'Alice Bob'`.

## Tricky Output Questions

**Q: What does this print?**

```python
import pandas as pd

df = pd.DataFrame({'A': [1, 2, 3]})
sorted_df = df.sort_values('A', ascending=False, inplace=True)
print(sorted_df)
```

**A:** `None` — `inplace=True` modifies `df` in place and returns `None`. Assigning the result of an `inplace=True` call sets the variable to `None`. This is a very common bug.

---

**Q: What happens here?**

```python
sales_jan = pd.Series([100, 200], index=['A', 'B'])
sales_feb = pd.Series([110], index=['A'])
print(sales_feb.reindex(sales_jan.index))
```

**A:**

```
A    110.0
B      NaN
dtype: float64
```

`B` is not in `sales_feb`, so it becomes `NaN`. The dtype is promoted to `float64` because `NaN` can't exist in integer Series.

---

**Q: What does this produce?**

```python
df = pd.DataFrame({'Name': ['alice', 'BOB', 'Charlie']})
print(df['Name'].str.capitalize().tolist())
```

**A:** `['Alice', 'Bob', 'Charlie']` — `.capitalize()` lowercases everything except the first character.

## One-Liner Challenges

**Q: Sort a DataFrame by column `Score` descending, reset the integer index.**

```python
df_sorted = df.sort_values('Score', ascending=False).reset_index(drop=True)
```

**Q: Set `Name` as index, then sort the index alphabetically, in one chain.**

```python
df_sorted = df.set_index('Name').sort_index()
```

**Q: Create a new column `Name_upper` with all-uppercase names.**

```python
df['Name_upper'] = df['Name'].str.upper()
```

## Explain the Difference

**`iterrows()` vs `itertuples()`:**
- `iterrows()` returns `(index, Series)` — flexible but slow and may coerce dtypes
- `itertuples()` returns named tuples — ~10× faster, dtype-safe, attribute access

**`set_index()` vs `reindex()`:**
- `set_index()` moves a column into the index — changes shape, no NaN introduced
- `reindex()` changes the index labels to match a new list — may introduce NaN for missing labels

---

---

# Practice Problems

## Problem 1 — Beginner

**Task:** Load the Titanic CSV, set `PassengerId` as the index, then sort by `Fare` descending. Show the top 5 passengers who paid the most.

**Starter code:**

```python
import pandas as pd
url = "https://raw.githubusercontent.com/datasciencedojo/datasets/refs/heads/master/titanic.csv"
df = pd.read_csv(url)
# Your code here
```

**Expected output columns:** `Name`, `Fare` (top 5 entries, highest fare first)

**Solution:**

```python
df_indexed = df.set_index('PassengerId')
top_fares = df_indexed.sort_values('Fare', ascending=False)[['Name', 'Fare']].head(5)
print(top_fares)
```

---

## Problem 2 — Intermediate

**Task:** Using the Titanic dataset, create a new column `AgeGroup` that labels passengers as `'Child'` (Age < 18), `'Adult'` (18–59), or `'Senior'` (60+). Handle NaN ages by filling with the median age. Use `Series.apply()` (not a loop).

**Starter code:**

```python
import pandas as pd
url = "https://raw.githubusercontent.com/datasciencedojo/datasets/refs/heads/master/titanic.csv"
df = pd.read_csv(url)
median_age = df['Age'].median()
df['Age'] = df['Age'].fillna(median_age)
```

**Solution:**

```python
def classify_age(age):
    if age < 18:
        return 'Child'
    elif age < 60:
        return 'Adult'
    else:
        return 'Senior'

df['AgeGroup'] = df['Age'].apply(classify_age)
print(df['AgeGroup'].value_counts())
```

---

## Problem 3 — Advanced / Real-World

**Task:** Two store sales DataFrames exist for Q1 and Q2. Q2 has new stores not in Q1. Use `reindex()` to align Q1 to Q2's index with `fill_value=0`, compute the quarter-over-quarter growth rate (`(Q2 - Q1) / Q1 * 100`), and sort by growth rate descending. Handle division by zero (Q1 = 0).

**Starter code:**

```python
import pandas as pd
import numpy as np

q1 = pd.DataFrame({'Sales': [500, 300, 450]}, index=['Store_A', 'Store_B', 'Store_C'])
q2 = pd.DataFrame({'Sales': [550, 280, 500, 200]}, index=['Store_A', 'Store_B', 'Store_C', 'Store_D'])
```

**Solution:**

```python
q1_aligned = q1.reindex(q2.index, fill_value=0)
growth = (q2['Sales'] - q1_aligned['Sales']) / q1_aligned['Sales'].replace(0, np.nan) * 100
growth_df = growth.rename('Growth_%').sort_values(ascending=False).reset_index()
growth_df.columns = ['Store', 'Growth_%']
print(growth_df)
```

---

---

# Visualization Quick-Reference

```python
import matplotlib.pyplot as plt

# Sort by Age and plot
df.sort_values('Age')['Age'].hist(bins=20, figsize=(10, 4))
plt.title('Age Distribution (Titanic)')
plt.xlabel('Age')
plt.ylabel('Count')
plt.show()

# Horizontal bar — top fares
df.nlargest(10, 'Fare').set_index('Name')['Fare'].sort_values().plot.barh(figsize=(10, 6))
plt.title('Top 10 Highest Fares')
plt.show()

# String length distribution
df['Name'].str.len().hist(bins=15)
plt.title('Name Length Distribution')
plt.show()
```

---

---

# Mnemonics & Memory Aids

| Concept | Mnemonic |
|---------|---------|
| `set_index()` vs `reindex()` | **SET** = promote a column to the INDEX. **RE**index = REshape to new labels |
| `iterrows()` vs `itertuples()` | **rows** = slow Series. **tuples** = fast named tuples |
| `axis=0` vs `axis=1` | **0 = Down** (rows). **1 = Right** (columns) |
| `.copy()` rule | "When in doubt, `.copy()` it out" |
| `sort_values()` vs `sort_index()` | `sort_values` = sort by DATA. `sort_index` = sort by LABELS |
| `ascending=[True, False]` | One bool per column in `by` list — match lengths! |
| `na_position` | Missing last by default — use `'first'` to push NaN to top |
| `.str.capitalize()` vs `.str.title()` | `capitalize` = first letter of STRING. `title` = first letter of each WORD |

---

---

# Quick-Reference Cheat Sheet

```python
import pandas as pd

# ── IO ─────────────────────────────────────────────
df = pd.read_csv(url_or_path)                          # Load CSV (supports URLs)
df = pd.read_csv(path, index_col='col', parse_dates=['date_col'])

# ── Index Management ───────────────────────────────
df.set_index('col')                                    # Promote column to index
df.reset_index()                                       # Move index back to column
df.reset_index(drop=True)                              # Discard old index

# ── reindex ────────────────────────────────────────
df.reindex(new_index)                                  # Rows — NaN for missing
df.reindex(new_index, fill_value=0)                    # Rows — fill with 0
df.reindex(columns=source.columns, fill_value=0)       # Columns — align + fill

# ── Views vs Copies ────────────────────────────────
subset = df[mask].copy()                               # Safe — independent copy
df.loc[mask, 'col'] = val                              # Safe — direct assignment
pd.options.mode.copy_on_write = True                   # Enable CoW (Pandas 2.x)

# ── Iteration ──────────────────────────────────────
for idx, row in df.iterrows(): ...                     # Slow — Series per row
for row in df.itertuples(): row.col_name               # Fast — named tuple
for col in df.columns: df[col]                         # Column iteration

# ── Apply ──────────────────────────────────────────
df['col'].apply(func)                                  # Series.apply — per element
df['col'].apply(lambda x: 'a' if x > 0 else 'b')      # Lambda version
df.apply(func, axis=1)                                 # DataFrame.apply — per row
df.apply(func, axis=0)                                 # DataFrame.apply — per col

# ── Sorting ────────────────────────────────────────
df.sort_values('col')                                  # By single column, ascending
df.sort_values('col', ascending=False)                 # Descending
df.sort_values(['col1', 'col2'], ascending=[True, False])  # Multi-column
df.sort_values('col', na_position='first')             # NaN at top
df.sort_index()                                        # Sort by row index
df.sort_index(axis=1)                                  # Sort columns alphabetically
df[sorted(df.columns)]                                 # Also sorts columns alphabetically

# ── Type Conversion ────────────────────────────────
df['col'].astype('int64')                              # Cast (fails with NaN)
df['col'].fillna(0).astype('int64')                    # Fill then cast
df['col'].astype('Int64')                              # Nullable integer (handles NaN)
df['col'].astype('string')                             # Pandas StringDtype
pd.to_numeric(df['col'], errors='coerce')              # NaN for non-numeric

# ── String Operations (.str accessor) ──────────────
df['col'].str.lower()                                  # Lowercase all
df['col'].str.upper()                                  # Uppercase all
df['col'].str.capitalize()                             # First char uppercase only
df['col'].str.title()                                  # Title case each word
df['col'].str.strip()                                  # Remove whitespace
df['col'].str.len()                                    # String length
df['col'].str.contains('pattern')                      # Boolean mask
df['col'].str.replace('old', 'new', regex=True)        # Replacement (regex supported)
df['col'].str.split(',')                               # Split → list column
df['col'].str.extract(r'(\d+)')                        # Regex group extraction
```

---

---

# Summary (TL;DR)

- **`set_index(col)`** promotes a column to the row index enabling `.loc[]` label lookups; **`reset_index()`** reverses it.
- **`reindex(new_labels)`** conforms a DataFrame to a new label structure, inserting `NaN` (or `fill_value`) for missing labels — critical for aligning DataFrames before arithmetic.
- **`SettingWithCopyWarning`** means Pandas can't guarantee whether your filtered subset is a view or a copy — fix it with `.copy()`. Pandas 2.x Copy-on-Write eliminates this ambiguity.
- **Iteration** (`iterrows()`, `itertuples()`) should be a last resort — always prefer vectorized operations or `.apply()`. `itertuples()` is 10× faster than `iterrows()` and preserves dtypes.
- **`sort_values(by, ascending)`** sorts rows by data values (single or multi-column); **`sort_index()`** sorts by index labels; both O(n log n).
- **`axis=0`** = down the rows; **`axis=1`** = across the columns — critical for `apply()`, `drop()`, `sum()`, `concat()`.
- **`.str` accessor** provides vectorized string operations (`.lower()`, `.upper()`, `.capitalize()`, `.len()`, etc.) on `object`/`string` columns — always faster than `.apply(lambda x: ...)`.
- **`astype('int64')` fails with NaN** — use `.fillna(0)` first or use nullable `Int64` dtype.

---

---

# Further Reading & Official Docs

| Topic | Link |
|-------|------|
| `set_index()` | https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.set_index.html |
| `reindex()` | https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.reindex.html |
| Copy-on-Write guide | https://pandas.pydata.org/docs/user_guide/copy_on_write.html |
| `iterrows()` | https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.iterrows.html |
| `itertuples()` | https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.itertuples.html |
| `sort_values()` | https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.sort_values.html |
| `sort_index()` | https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.sort_index.html |
| `.str` accessor | https://pandas.pydata.org/docs/user_guide/text.html |
| Pandas 2.0 migration | https://pandas.pydata.org/docs/whatsnew/v2.0.0.html |
| SettingWithCopyWarning | https://pandas.pydata.org/docs/user_guide/indexing.html#returning-a-view-versus-a-copy |

---

---

# Flashcard-Style Recall

**Q:** How do you set a column as the DataFrame index?
**A:** `df.set_index('col_name')` — returns a new DataFrame with that column as the index.

---

**Q:** How do you add a missing store back into a sales DataFrame with 0 sales?
**A:** `df.reindex(full_index, fill_value=0)`

---

**Q:** What does `SettingWithCopyWarning` mean and how do you fix it?
**A:** Pandas warns that you're modifying a possible view. Fix: `subset = df[mask].copy()` before modifying.

---

**Q:** Which is faster — `iterrows()` or `itertuples()`?
**A:** `itertuples()` — ~10× faster; returns named tuples instead of Series objects, no dtype coercion.

---

**Q:** What does `axis=1` mean in `df.apply(func, axis=1)`?
**A:** Apply `func` to each row — `func` receives a Series containing all columns of one row.

---

**Q:** How do you sort a DataFrame by two columns, first ascending then descending?
**A:** `df.sort_values(['col1', 'col2'], ascending=[True, False])`

---

**Q:** How do you sort columns alphabetically?
**A:** `df[sorted(df.columns)]` or `df.sort_index(axis=1)`

---

**Q:** What does `.str.capitalize()` do vs `.str.title()`?
**A:** `capitalize()` → first char of string uppercase, rest lowercase. `title()` → first char of **each word** uppercase.

---

**Q:** Why does `df['Age'].astype('int64')` fail on the Titanic dataset?
**A:** The `Age` column has `NaN` values; NumPy `int64` cannot hold `NaN`. Fix: `df['Age'].fillna(0).astype('int64')`.

---

**Q:** What does Copy-on-Write (CoW) in Pandas 2.x change?
**A:** Every subset is a lazy copy — shared memory until a write occurs. Eliminates view/copy ambiguity and `SettingWithCopyWarning`.
