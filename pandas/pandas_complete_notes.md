# PANDAS — COMPLETE COURSE NOTES
### Covering: 22-02-2026 · 25-02-2026 · 28-02-2026
> **Pandas Version:** 2.x | **Python:** 3.x | **Dataset:** Titanic CSV

---

## Prerequisites
- Python basics (lists, dicts, loops, functions, f-strings)
- Basic NumPy understanding (arrays, dtypes)
- Familiarity with tabular data (spreadsheets, SQL tables)

---

## Environment Setup

```python
# Install pandas
# Using uv (recommended)
uv add pandas

# Using pip
pip install pandas

# Standard imports used throughout
import pandas as pd
import numpy as np
```

---

## Table of Contents
1. [What is Pandas?](#1-what-is-pandas)
2. [pd.Series — 1D Labeled Array](#2-pdseries--1d-labeled-array)
3. [pd.DataFrame — 2D Labeled Table](#3-pddataframe--2d-labeled-table)
4. [Loading Data — pd.read_csv()](#4-loading-data--pdread_csv)
5. [DataFrame Inspection](#5-dataframe-inspection)
6. [Accessing Columns & Rows](#6-accessing-columns--rows)
7. [Conditional Selection — Boolean Indexing](#7-conditional-selection--boolean-indexing)
8. [Slicing Rows and Columns Together](#8-slicing-rows-and-columns-together)
9. [set_index(), reset_index(), reindex()](#9-set_index-reset_index-reindex)
10. [Views vs Copies — SettingWithCopyWarning](#10-views-vs-copies--settingwithcopywarning)
11. [Iteration in Pandas](#11-iteration-in-pandas)
12. [Sorting — sort_values() & sort_index()](#12-sorting--sort_values--sort_index)
13. [String Operations — .str Accessor](#13-string-operations--str-accessor)
14. [Regex in Pandas & Python](#14-regex-in-pandas--python)
15. [Categorical Data — pd.Categorical](#15-categorical-data--pdcategorical)
16. [Date & DateTime Operations](#16-date--datetime-operations)

---

## 1. What is Pandas?
`[BEGINNER]`

### Concept Overview

**Pandas** is an open-source Python library for data manipulation and analysis. Its name comes from **"Panel Data"** — an econometrics term for multidimensional structured data. It is built on top of NumPy, and gives you powerful, fast, flexible data structures for working with labeled tabular data.

> **Real-World Analogy:** Think of Pandas as Excel + SQL + Python's ETL toolkit — all in one library. If Excel is a calculator, Pandas is a programmable, scalable, automatable version of it.

### Why Pandas? What Problem Does It Solve?

**Before Pandas**, developers used raw Python lists, dicts, or NumPy arrays. These lacked:
- Column/row labels
- Grouping & aggregation
- Join/merge semantics
- Missing data handling
- Direct CSV/Excel/SQL I/O

Real-world datasets (CSV, JSON, Excel, SQL) are structured in **rows and columns** — not flat arrays. Pandas was built for exactly this shape of data.

### Where is Pandas Used?

| Field | How Pandas Helps |
|---|---|
| Data Science | Load, clean, explore, transform, model data |
| Machine Learning | Feature engineering, preprocessing large datasets |
| Finance | Time series, stock prices, trade records |
| Web/Analytics | ETL pipelines, server logs, product usage stats |
| Healthcare | Patient records, diagnostics, hospital statistics |

### Core Pandas Objects

| Object | Description |
|---|---|
| `Series` | 1D labeled array — like a single column |
| `DataFrame` | 2D table — like an Excel sheet or SQL table |
| `Panel` | ⚠️ Deprecated in Pandas 1.x — replaced by MultiIndex |

---

## 2. pd.Series — 1D Labeled Array
`[BEGINNER]`

### Concept Overview

A **Series** is a one-dimensional labeled array — think of it as a single column with an associated index. Every Series has:
- A **values array** (backed by NumPy)
- An **index** (labels for each value)
- A **dtype** (data type of the values)
- An optional **name**

> **Real-World Analogy:** A Series is like a single column in an Excel sheet, where the row numbers on the left are the index, and the values in the column are the data.

---

### Creating a Series

#### From a Python list (default integer index)

```python
import pandas as pd

data = [1, 2, 3, 4, 5, 6]
s1 = pd.Series(data)
print(s1)
```

**Expected Output:**
```
0    1
1    2
2    3
3    4
4    5
5    6
dtype: int64
```

**Line-by-line breakdown:**
- `pd.Series(data)` — wraps the list in a Series
- Pandas automatically assigns a **default integer index** starting at 0
- `dtype: int64` — Pandas inferred the type from the list values

---

#### From a list with custom index and name

```python
s2 = pd.Series(data, index=['a', 'b', 'c', 'd', 'e', 'f'], name='Numbers')
print(s2)
```

**Expected Output:**
```
a    1
b    2
c    3
d    4
e    5
f    6
Name: Numbers, dtype: int64
```

**Key Points:**
- `index` must be the **same length** as `data`
- `name` becomes the label for the Series (useful when converting to a DataFrame column)

---

#### From a Python dictionary

```python
data = {'a': 10, 'b': 20, 'c': 30}
s3 = pd.Series(data)
print(s3)
```

**Expected Output:**
```
a    10
b    20
c    30
dtype: int64
```

**Key Points:**
- Dict **keys** become the index labels
- Dict **values** become the data values
- Ordering is preserved (Python 3.7+ dicts are insertion-ordered)

---

#### From a NumPy array

```python
import numpy as np

arr = np.array([5, 10, 15])
s4 = pd.Series(arr, index=['x', 'y', 'z'])
print(s4)
```

**Expected Output:**
```
x     5
y    10
z    15
dtype: int64
```

---

### Series Attributes & Properties

```python
s2 = pd.Series([10, 20, 30, 40], index=['a', 'b', 'c', 'd'], name='Scores')

print(s2.index)     # Index object → Index(['a', 'b', 'c', 'd'], dtype='object')
print(s2.values)    # NumPy array → [10 20 30 40]
print(s2.dtype)     # int64
print(s2.name)      # 'Scores'
print(s2.shape)     # (4,)
```

| Attribute | Type | Description |
|---|---|---|
| `.index` | `Index` | The label array |
| `.values` | `ndarray` | Raw NumPy array of values |
| `.dtype` | `dtype` | Data type of elements |
| `.name` | `str` or `None` | Name of the Series |
| `.shape` | `tuple` | `(n,)` for a Series of length n |
| `.size` | `int` | Total number of elements |
| `.ndim` | `int` | Always `1` for a Series |

---

### Series Indexing & Access

#### 1. Positional Indexing — `.values[]` and `.iloc[]`

```python
# Direct numpy array access (zero-based)
print(s2.values[0])    # 10
print(s2.values[3])    # 40

# iloc: integer-location based — ALWAYS positional
print(s2.iloc[0])      # 10
print(s2.iloc[1:3])    # Returns Series with index ['b', 'c'], values [20, 30]
```

**When to use `.iloc`:** When you know the position (row number) and don't care about the label.

---

#### 2. Label-based Indexing — `.loc[]`

```python
s2 = pd.Series([10, 20, 30, 40], index=['a', 'b', 'c', 'd'], name='Scores')

print(s2['a'])           # 10 — single label
print(s2['b'])           # 20
print(s2[['b', 'd']])    # Series with b=20, d=40 — multiple labels (list of labels)
print(s2.loc[['b', 'd']])  # Equivalent, explicitly using loc
```

**⚠️ FutureWarning Trap — Using integers on a non-integer index:**

```python
# ⚠️ This triggers a FutureWarning in Pandas 2.x:
print(s2[0])
# FutureWarning: Series.__getitem__ treating keys as positions is deprecated.
# In a future version, integer keys will always be treated as labels.

# ✅ Safe alternatives:
print(s2.iloc[0])   # Explicit positional access
print(s2['a'])      # Explicit label access
```

> **Mnemonic:** Use `loc` for Labels, `iloc` for Integers (position).

---

#### 3. Integer-indexed Series — `.loc` vs `.iloc`

```python
s3 = pd.Series([10, 20, 30, 40])  # default integer index 0,1,2,3

s3.iloc[[1, 2]]   # positional: returns values at positions 1 and 2 → [20, 30]
s3.iloc[1:3]      # positional slice → [20, 30]
s3.loc[[1, 2]]    # label-based: labels 1 and 2 → [20, 30]
                  # (same result here because index IS integers)
```

**Key insight:** When the index is integers, `.loc` and `.iloc` appear identical but behave differently conceptually — `.loc` uses labels, `.iloc` uses positions. They only differ when the integer index is non-contiguous (e.g., after filtering).

---

### Indexing Summary Table

| Accessor | Uses | Returns |
|---|---|---|
| `s['a']` | Label (may warn for integers) | Scalar |
| `s[['a','b']]` | List of labels | Series |
| `s.iloc[0]` | Integer position | Scalar |
| `s.iloc[1:3]` | Positional slice | Series |
| `s.loc['a']` | Label | Scalar |
| `s.loc[['a','b']]` | List of labels | Series |

---

## 3. pd.DataFrame — 2D Labeled Table
`[BEGINNER]`

### Concept Overview

A **DataFrame** is a 2D labeled data structure — essentially a **dict of Series** where each Series is a column. It is the core workhorse of Pandas for all data manipulation.

> **Real-World Analogy:** A DataFrame is a database table or Excel spreadsheet — rows are records, columns are features/fields. The index is the row identifier (like a primary key).

**Internal structure:** Each column is stored as a separate array (or `ExtensionArray` for nullable types). The Index and column labels are stored separately.

---

### Creating a DataFrame

#### From a dictionary of lists

```python
data = {
    'Name': ['Hemanth', 'Aditya', 'Rajbeer'],
    'Age':  [30, 31, 32],
    'City': ['Delhi', 'Mumbai', 'Bangalore']
}

df = pd.DataFrame(data)
print(df)
```

**Expected Output:**
```
      Name  Age       City
0  Hemanth   30      Delhi
1   Aditya   31     Mumbai
2  Rajbeer   32  Bangalore
```

**Line-by-line breakdown:**
- Dict **keys** become column names
- Dict **values** (lists) become column data
- Pandas auto-assigns a RangeIndex `0, 1, 2, ...` as the row index
- All lists **must be the same length** — else `ValueError`

---

#### From a list of dicts

```python
records = [
    {'Name': 'Hemanth', 'Age': 30, 'City': 'Delhi'},
    {'Name': 'Aditya',  'Age': 31, 'City': 'Mumbai'},
    {'Name': 'Rajbeer', 'Age': 32, 'City': 'Bangalore'}
]

df = pd.DataFrame(records)
print(df)
```

**Key Points:**
- Each **dict** becomes one row
- Dict **keys** become column names
- Missing keys in any dict become `NaN` for that row

---

#### From a NumPy array

```python
arr = np.array([[1, 2], [3, 4], [5, 6]])
df = pd.DataFrame(arr, columns=['A', 'B'])
print(df)
```

**Expected Output:**
```
   A  B
0  1  2
1  3  4
2  5  6
```

**Key Points:**
- All elements share the same dtype (NumPy array is homogeneous)
- You must provide `columns` explicitly or get default `0, 1, ...`

---

### Accessing Data in a DataFrame

```python
data = {
    'Name': ['Hemanth', 'Aditya', 'Rajbeer'],
    'Age':  [30, 31, 32],
    'City': ['Delhi', 'Mumbai', 'Bangalore']
}
df = pd.DataFrame(data)

print(df['Name'])           # Series — single column
print(df[['Name', 'Age']]) # DataFrame — multiple columns (note double brackets)
print(df.iloc[0])           # Row by integer position → returns a Series
print(df.loc[0])            # Row by index label → returns a Series
```

> **Key difference:** `df['col']` accesses a **column**. `df.loc[n]` accesses a **row**.

---

## 4. Loading Data — pd.read_csv()
`[BEGINNER]`

### Concept Overview

`pd.read_csv()` is the most common way to load data into Pandas. It reads a CSV file (local or from a URL) and returns a DataFrame.

---

### Loading from a URL

```python
titanic_url = "https://raw.githubusercontent.com/datasciencedojo/datasets/refs/heads/master/titanic.csv"

df = pd.read_csv(titanic_url)
```

**How it works:**
- Pandas uses `urllib` internally to fetch the URL
- The CSV is parsed into a DataFrame with the first row as column headers

---

### Loading from a local file

```python
df = pd.read_csv("titanic.csv")  # relative path — file must be in current working directory
```

---

### Key Parameters

| Parameter | Purpose | Example |
|---|---|---|
| `index_col` | Use a column as the row index | `index_col='PassengerId'` |
| `parse_dates` | Parse columns as datetime | `parse_dates=['DateCol']` |
| `dtype` | Force column dtypes | `dtype={'Age': float}` |
| `usecols` | Load only specific columns | `usecols=['Name', 'Age']` |
| `nrows` | Load only first N rows | `nrows=100` |
| `sep` / `delimiter` | Column separator | `sep=';'` |
| `encoding` | File encoding | `encoding='utf-8'` |
| `chunksize` | Read in chunks (large files) | `chunksize=10000` |

---

### IO Format Reference

| Format | Read | Write |
|---|---|---|
| CSV | `pd.read_csv()` | `df.to_csv()` |
| Excel | `pd.read_excel()` | `df.to_excel()` |
| JSON | `pd.read_json()` | `df.to_json()` |
| Parquet | `pd.read_parquet()` | `df.to_parquet()` |
| SQL | `pd.read_sql()` | `df.to_sql()` |

> **Performance tip:** For large datasets, prefer **Parquet** over CSV — it's 5–10× faster to read and 3–5× smaller on disk due to columnar compression.

---

## 5. DataFrame Inspection
`[BEGINNER]`

### Concept Overview

Before analyzing any dataset, you need to understand its structure — number of rows, column names, data types, missing values, and basic statistics. Pandas provides a rich set of inspection methods.

---

### Core Inspection Methods

```python
df = pd.read_csv("titanic.csv")

df.head()        # First 5 rows (default)
df.head(10)      # First 10 rows
df.tail()        # Last 5 rows (default)
df.head(3)       # First 3 rows
```

---

#### `.shape` — Dimensions of the DataFrame

```python
df.shape    # (891, 12) → 891 rows, 12 columns
```

Returns a **tuple** `(num_rows, num_columns)`. Does not include the index.

---

#### `.columns` — Column Names

```python
df.columns
# Index(['PassengerId', 'Survived', 'Pclass', 'Name', 'Sex', 'Age', 
#        'SibSp', 'Parch', 'Ticket', 'Fare', 'Cabin', 'Embarked'], dtype='object')
```

---

#### `.info()` — Schema Summary

```python
df.info()
```

**Expected Output (Titanic):**
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 891 entries, 0 to 890
Data columns (total 12 columns):
 #   Column       Non-Null Count  Dtype  
---  ------       --------------  -----  
 0   PassengerId  891 non-null    int64  
 1   Survived     891 non-null    int64  
 2   Pclass       891 non-null    int64  
 3   Name         891 non-null    object 
 4   Sex          891 non-null    object 
 5   Age          714 non-null    float64
 6   SibSp        891 non-null    int64  
 7   Parch        891 non-null    int64  
 8   Ticket       891 non-null    object 
 9   Fare         891 non-null    float64
10   Cabin        204 non-null    object 
11   Embarked     889 non-null    object 
dtype: object
```

**What `.info()` reveals:**
- Total rows and column count
- Column name + non-null count → immediately shows where data is missing
- dtype per column
- Memory usage

---

#### `.describe()` — Statistical Summary

```python
df.describe()           # Numeric columns only (by default)
df['PassengerId'].describe()  # Single column
```

**Expected Output (numeric columns):**
- `count`, `mean`, `std`, `min`, `25%`, `50%` (median), `75%`, `max`

---

#### `dir(df)` — Discover All Attributes/Methods

```python
dir(df)   # Returns all attributes and methods available on the DataFrame
```

Useful for tab-completion-style exploration in scripts.

---

#### `display()` vs `print()`

```python
display(df.head(3))   # Renders rich HTML table in Jupyter
print(df.head(3))     # Plain text representation

# You can chain multiple displays:
display(df.head(3))
display(df.head(1))
```

`display()` is a Jupyter-specific function — it renders a styled HTML table. `print()` works everywhere but outputs plain text.

---

## 6. Accessing Columns & Rows
`[BEGINNER → INTERMEDIATE]`

### Concept Overview

Pandas has multiple indexing systems. Knowing which to use — and when — is the most critical skill in Pandas.

> **Real-World Analogy:** Think of a DataFrame like a spreadsheet. `df['col']` is like clicking a column header. `df.loc[row]` is like looking up a row by its label (like a name). `df.iloc[n]` is like going to row number n.

---

### Accessing Columns

```python
df['Name']              # Returns a Series — single column
df[['Name', 'Age']]     # Returns a DataFrame — multiple columns (double brackets)
```

**Common Mistake:**
```python
df['Name', 'Age']   # ❌ KeyError — this looks for a column named ('Name', 'Age')
df[['Name', 'Age']] # ✅ Correct — double brackets pass a list
```

---

### Accessing Rows — `.loc[]` (Label-Based)

```python
df.loc[0]           # Row with index label 0 → returns a Series
df.loc[0]['Name']   # Chain: get row 0, then column 'Name' → scalar
df.loc[0, 'Name']   # ✅ Better: single .loc call with row + column → scalar
```

**`.loc` is label-based:**
- Row label is the index value (usually `0, 1, 2, ...` for default RangeIndex)
- When you `set_index('Name')`, `.loc['Allen, Miss. Elisabeth Walton']` works

---

### Accessing Rows — `.iloc[]` (Position-Based)

```python
df.iloc[0]          # First row (position 0) → Series
df.iloc[:5]         # First 5 rows → DataFrame
df.iloc[0, 3]       # Scalar: row position 0, column position 3
df.iloc[0:5, 3]     # Slice: rows 0-4, column 3 → Series
```

**`.iloc` is always positional:**
- Row 0 = first row, regardless of the index label
- Column 3 = fourth column, regardless of column name

---

### The Full Indexing Table

| Accessor | Type | Input | Returns | Speed |
|---|---|---|---|---|
| `df['col']` | Column by label | String | Series | Fast — O(1) |
| `df[['col1','col2']]` | Multiple columns | List | DataFrame | Fast |
| `df.loc[row, col]` | Label-based row + col | Label | Scalar/Series/DataFrame | Medium |
| `df.iloc[row, col]` | Position-based row + col | Integer | Scalar/Series/DataFrame | Fast |
| `df.at[row, col]` | Single label-based value | Labels | Scalar | Fastest |
| `df.iat[row, col]` | Single position-based value | Integers | Scalar | Fastest |
| `df[bool_mask]` | Boolean filter | bool Series | DataFrame | Fast (vectorized) |

> **Rule of thumb:** Use `df.loc` when working with labels (after `set_index`). Use `df.iloc` when you know positions. Use `df.at` / `df.iat` when you need a single scalar as fast as possible.

---

## 7. Conditional Selection — Boolean Indexing
`[BEGINNER → INTERMEDIATE]`

### Concept Overview

Boolean indexing lets you filter rows by applying a condition that evaluates to `True`/`False` for each row. It is the Pandas equivalent of SQL `WHERE`.

> **Real-World Analogy:** In SQL: `SELECT * FROM passengers WHERE Age > 70`. In Pandas: `df[df['Age'] > 70]`.

---

### Single Condition

```python
# Step 1: Create a boolean mask
mask = df['Age'] > 70
# Returns: 0      False, 1     False, ..., 630   True, dtype: bool

# Step 2: Apply the mask to filter rows
df[df['Age'] > 70]

# Store in a variable
older_passengers = df[df['Age'] > 70]
display(older_passengers.head())
```

After filtering, you can still run all inspection methods:
```python
older_passengers.shape        # e.g., (5, 12)
older_passengers.info()
older_passengers.describe()
older_passengers['PassengerId'].describe()
```

---

### Multiple Conditions — `&` and `|`

```python
# AND: Both conditions must be True
df[(df['Sex'] == 'female') & (df['Survived'] == 1)]

# ⚠️ CRITICAL: Each condition MUST be wrapped in parentheses ( )
# Without parens, Python's operator precedence breaks the expression
```

**Wrong:**
```python
df[df['Sex'] == 'female' & df['Survived'] == 1]   # ❌ SyntaxError or wrong result
```

**Right:**
```python
df[(df['Sex'] == 'female') & (df['Survived'] == 1)]  # ✅
```

---

### Combining Boolean Filter with Column Selection

```python
# Method 1: Chain selection (may trigger SettingWithCopyWarning if modifying)
female_survivors = df[(df['Sex'] == 'female') & (df['Survived'] == 1)][['Name', 'Age']]

# Method 2: Use .loc — preferred, single indexing operation
df.loc[(df['Sex'] == 'female') & (df['Survived'] == 1), ['Name', 'Age']]
```

**Why `.loc` is preferred:** It avoids chained indexing, which can silently operate on a copy instead of the original. See [Section 10](#10-views-vs-copies--settingwithcopywarning).

---

## 8. Slicing Rows and Columns Together
`[BEGINNER → INTERMEDIATE]`

### `.loc` Slicing (Label-Based)

```python
# Rows 0 to 4 (INCLUSIVE in .loc), columns Name and Age
df.loc[:4, ['Name', 'Age']]

# Note: .loc slicing is INCLUSIVE on both ends
# df.loc[0:4] returns rows with labels 0, 1, 2, 3, 4 — 5 rows
```

```python
# Row 0 to 3, columns Name and Survived
df.loc[0:3, ['Name', 'Survived']]
```

---

### `.iloc` Slicing (Position-Based)

```python
# First 3 rows, first 2 columns
df.iloc[0:3, 0:2]   # rows 0,1,2 — columns 0,1 (EXCLUSIVE end)

# First 3 rows, specific columns by position
df.iloc[0:3, [3, 1]]  # columns at positions 3 and 1 (Name, Survived)
```

**Key difference from `.loc`:**
- `.iloc[0:3]` returns rows at positions 0, 1, 2 — **3 rows** (exclusive end)
- `.loc[0:3]` returns rows with labels 0, 1, 2, 3 — **4 rows** (inclusive end)

> **Mnemonic:** `iloc` behaves like Python list slicing (exclusive end). `loc` is inclusive on both ends.

---

### Titanic Column Reference

```
Position:  0           1         2       3     4    5     6      7       8       9      10       11
Columns:   PassengerId Survived  Pclass  Name  Sex  Age   SibSp  Parch   Ticket  Fare   Cabin  Embarked
```

---

## 9. set_index(), reset_index(), reindex()
`[INTERMEDIATE]`

### Concept Overview

| Method | What it does | Creates NaN? |
|---|---|---|
| `set_index()` | Moves a column into the row index | No |
| `reset_index()` | Moves the index back to a column | No |
| `reindex()` | Remaps to new labels (can add/remove rows or columns) | Yes — for missing labels |

---

### `set_index()` — Use a Column as the Row Index

```python
df_by_passenger_id = df.set_index('PassengerId')
df_by_passenger_id.head()
# 'PassengerId' is now the index — no longer a column
```

```python
df_by_name = df.set_index('Name')
print(df_by_name.loc['Allen, Miss. Elisabeth Walton'])
# Now you can look up rows by passenger name
```

**When to use:** When you want meaningful row labels for fast label-based lookup with `.loc`.

---

### `reset_index()` — Move Index Back to a Column

```python
df_by_name = df_by_name.reset_index()
df_by_name.head()
# 'Name' is now back as a regular column; index is RangeIndex again
```

---

### `reindex()` — Align to a New Index
`[INTERMEDIATE]`

**Problem scenario:** Two DataFrames have different indexes — operations between them produce `NaN`.

```python
sales_jan = pd.DataFrame({'Sales': [250, 400, 300]},
                          index=['Store_A', 'Store_B', 'Store_C'])

sales_feb = pd.DataFrame({'Sales': [260, 310]},
                          index=['Store_A', 'Store_C'])  # Store_B missing!
```

**❌ Problem:** Indexes are mismatched. Subtracting them will produce `NaN` for `Store_B`.

```python
# ✅ Solution: Use reindex() to align
common_index = sales_jan.index                # ['Store_A', 'Store_B', 'Store_C']
sales_feb_aligned = sales_feb.reindex(common_index)
# Store_B row is inserted with NaN

sales_diff = sales_feb_aligned['Sales'] - sales_jan['Sales']
# Store_B → NaN (missing in feb)
```

**With `fill_value` to avoid NaN:**

```python
sales_feb_aligned = sales_feb.reindex(common_index, fill_value=0)
# Store_B row now has Sales=0

sales_diff = sales_feb_aligned['Sales'] - sales_jan['Sales']
# Store_A: 260-250=10, Store_B: 0-400=-400, Store_C: 310-300=10
```

---

### `reindex()` on Columns

```python
source_a = pd.DataFrame({
    'Product': ['Laptop', 'Tablet', 'Phone'],
    'Price': [1000, 500, 800],
    'Stock': [30, 50, 100]
})

source_b = pd.DataFrame({
    'Price': [980, 520, 810],
    'Product': ['Laptop', 'Tablet', 'Phone']
    # 'Stock' column is missing!
})

# Align source_b to have the same columns as source_a
aligned_b = source_b.reindex(columns=source_a.columns, fill_value=0)
# Now aligned_b has columns: Product, Price, Stock (with Stock=0)

stock_diff = source_a['Stock'] - aligned_b['Stock']
# 30-0=30, 50-0=50, 100-0=100
```

---

## 10. Views vs Copies — SettingWithCopyWarning
`[INTERMEDIATE → ADVANCED]`

### Concept Overview

This is the **#1 most common Pandas gotcha**. When you slice or filter a DataFrame, you may get either a **view** (still linked to the original) or a **copy** (independent). Modifying a view modifies the original — or worse, you *think* you modified the original but actually modified a copy.

> **Real-World Analogy:** A view is like a window into a room — you can see and change what's inside. A copy is like a photograph — changes to the photo don't affect the room.

---

### The Problem

```python
# ⚠️ Dangerous — may trigger SettingWithCopyWarning
subset = df[df['Sex'] == 'male']
subset['Age'] = subset['Age'] + 1
# SettingWithCopyWarning: A value is trying to be set on a copy of a slice from a DataFrame
```

**Why this is dangerous:**
- `df[df['Sex'] == 'male']` returns either a view or a copy — Pandas does not guarantee which
- If it's a copy, your modification is silently discarded
- If it's a view, you accidentally modified the original `df`

---

### The Solution — Always use `.copy()` when you intend to modify

```python
# ✅ Safe
subset = df[df['Sex'] == 'male'].copy()
subset['Age'] += 1
# Now subset is guaranteed to be an independent copy
```

---

### View vs Copy Reference

| Situation | View or Copy? | Use `.copy()`? |
|---|---|---|
| `.iloc[]` slicing | Mostly Copy | Optional |
| `.loc[]` slicing | May be View | Recommended |
| Boolean filter `df[...]` | May be View | Recommended |
| You plan to modify the subset | N/A | **YES — always** |

---

### Pandas 2.x — Copy-on-Write (CoW)
`[ADVANCED]`

In **Pandas 2.0+**, Copy-on-Write (CoW) is available and becomes the **default in Pandas 3.0**. Under CoW:
- All indexing operations return a copy
- A copy is only materialized when you actually write to it
- `SettingWithCopyWarning` effectively disappears

```python
# Enable CoW explicitly in Pandas 2.x:
pd.options.mode.copy_on_write = True
```

With CoW enabled, chained assignment (`df[cond]['col'] = val`) simply doesn't work — it raises a `ChainedAssignmentError` which is clearer and safer than the old warning.

---

## 11. Iteration in Pandas
`[INTERMEDIATE]`

### Concept Overview

Pandas is designed for **vectorized operations**, not iteration. But sometimes you need to loop — for complex conditional logic, debugging, or building custom outputs.

> **Real-World Analogy:** Vectorized Pandas is like a factory assembly line — everything happens in parallel. Iteration (`iterrows`) is like a craftsman manually processing each item one-by-one. The factory is 100× faster.

---

### `iterrows()` — Iterate Over Rows as (index, Series) Tuples

```python
df = pd.DataFrame({
    'Name': ['Alice', 'Bob', 'Charlie'],
    'Age':  [25, 30, 35],
    'City': ['Delhi', 'Mumbai', 'Chennai']
})

for index, row in df.iterrows():
    print(f"{index}: {row['Name']} lives in {row['City']} and is {row['Age']} years old.")
```

**Expected Output:**
```
0: Alice lives in Delhi and is 25 years old.
1: Bob lives in Mumbai and is 30 years old.
2: Charlie lives in Chennai and is 35 years old.
```

**Key Points:**
- `index` = the row index label
- `row` = a Series of all column values for that row
- Accessing columns: `row['ColumnName']`

---

### `itertuples()` — Faster Iteration as Named Tuples

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
...
```

**Key Points:**
- Returns `namedtuple` objects — attribute access is `row.Name`, not `row['Name']`
- **Significantly faster** than `iterrows()` — no Series overhead
- `row.Index` = the DataFrame index value (capital I)

---

### Iterating Over Columns

```python
for col in df.columns:
    print(f"Column: {col}")
    print(df[col].values, end='\n\n')
```

```python
list(df.columns)     # ['Name', 'Age', 'City'] — list of column names
df.columns           # Index(['Name', 'Age', 'City'], dtype='object')
```

---

### Conditional Logic with `iterrows()` — Add a Derived Column

**Goal:** Classify passengers as 'young' (Age < 40) or 'senior' (Age ≥ 40).

```python
categories = []

for idx, row in df.iterrows():
    if row['Age'] < 40:
        categories.append("young")
    else:
        categories.append("senior")

df['Category'] = categories
df.head()
```

---

### Better Way: Vectorized Approach with `.apply()`

All three approaches below are equivalent but progressively more idiomatic:

```python
# Method 1: Inline lambda
df['Category_v1'] = df['Age'].apply(lambda age: 'young' if age < 40 else 'senior')

# Method 2: Named lambda (reusable)
categorize_age_lambda = lambda age: 'young' if age < 40 else 'senior'
df['Category_v2'] = df['Age'].apply(categorize_age_lambda)

# Method 3: Named function (most readable, debuggable)
def categorize_age_function(age):
    return 'young' if age < 40 else 'senior'

df['Category_v3'] = df['Age'].apply(categorize_age_function)
```

All three produce the same result. Use Method 3 for complex logic (easier to debug and test).

---

### `DataFrame.apply()` with `axis` — Row-Wise Operations

When the logic depends on **multiple columns** of the same row, use `df.apply(..., axis=1)`.

```python
def check_if_delhi_and_young(row):
    city = row['City']
    age  = row['Age']
    if city == 'Delhi' and age < 40:
        return True
    return False

df['If_young_and_from_delhi'] = df.apply(check_if_delhi_and_young, axis=1)
```

**Understanding axis:**

```
axis=0 (default): Apply function DOWN each COLUMN (function receives a column as Series)
axis=1:           Apply function ACROSS each ROW (function receives a row as Series)

      Col_A  Col_B  Col_C
Row0    1      2      3     ← axis=1 passes this row as input
Row1    4      5      6
Row2    7      8      9
  ↑
axis=0 passes this column as input
```

> **Mnemonic:** `axis=1` keeps rows constant and moves across columns (left→right). `axis=0` keeps columns constant and moves down rows (top→bottom).

---

### Performance Hierarchy — Never Use Loops When You Can Avoid It

| Method | Speed | Use When |
|---|---|---|
| Vectorized operations (`df['col'] + 1`) | ⚡⚡⚡⚡⚡ Fastest | Pure arithmetic/string/datetime |
| `.apply()` with built-in functions | ⚡⚡⚡⚡ Fast | Aggregations |
| `.apply()` with lambdas | ⚡⚡⚡ Moderate | Simple custom element logic |
| `.apply()` with Python functions | ⚡⚡⚡ Moderate | Complex element logic |
| `itertuples()` | ⚡⚡ Slow | Rarely — only when apply won't work |
| `iterrows()` | ⚡ Slowest | Only for debugging or printing |

---

## 12. Sorting — sort_values() & sort_index()
`[BEGINNER → INTERMEDIATE]`

### Concept Overview

Sorting helps organize data for readability, analysis, and downstream operations. Pandas offers two main sorting methods:
1. `sort_values()` — sort by data values
2. `sort_index()` — sort by row or column labels

---

### Understanding `inplace` (Important Concept)

Before sorting, understand `inplace=True` vs `inplace=False`:

```python
class Cal:
    def __init__(self, a):
        self.a = a
    def add(self, b):          # Returns new value — does NOT change self.a
        return self.a + b
    def inplace_add(self, b):  # Modifies self.a directly
        self.a = self.a + b
        return self.a

c = Cal(10)
c.add(10)          # returns 20, but c.a is still 10
c.a = c.add(10)    # Manual "inplace=False" pattern
c.inplace_add(10)  # c.a becomes 30
```

**In Pandas:**
```python
# inplace=False (default) — returns a new DataFrame, original unchanged
sorted_df = df.sort_values(by='Age')

# inplace=True — modifies df directly, returns None
df.sort_values(by='Age', inplace=True)
```

> **Pandas 2.x Note:** Many `inplace` operations are being soft-deprecated in favor of method chaining. Prefer `df = df.sort_values(...)` over `df.sort_values(inplace=True)`.

---

### `sort_values()` — Sort by Data Values

**Syntax:**
```python
DataFrame.sort_values(by, axis=0, ascending=True, inplace=False, na_position='last')
```

| Argument | Purpose |
|---|---|
| `by` | Column name(s) to sort by |
| `axis` | 0=rows (default), 1=columns |
| `ascending` | `True`=low→high, `False`=high→low |
| `inplace` | `True`=modify in place |
| `na_position` | `'first'` or `'last'` for NaN placement |

---

#### Example 1: Sort by a single column

```python
# Sort by Age (ascending — youngest first)
sorted_df = df.sort_values(by='Age')
print(sorted_df[['Name', 'Age']].head(10))   # Youngest passengers
print(sorted_df[['Name', 'Age']].tail(10))   # Oldest passengers
```

**Handling NaN before sorting (Titanic Age column has missing values):**

```python
print(df['Age'].dtype)   # float64

# Fill NaN before converting
df['Age'] = df['Age'].fillna(0).astype('int64')

sorted_df = df.sort_values(by='Age')
print(sorted_df[['Name', 'Age']].tail(20))
```

---

#### Example 2: Sort by multiple columns

```python
# First sort by Pclass (ascending), then by Fare (descending)
df_sorted = df.sort_values(by=['Pclass', 'Fare'], ascending=[True, False])
df_sorted.head()
```

**Line-by-line breakdown:**
- `by=['Pclass', 'Fare']` — primary sort on `Pclass`, secondary on `Fare`
- `ascending=[True, False]` — Pclass low→high, Fare high→low within each class
- All passengers in class 1 will be sorted by their fare descending, then class 2, etc.

---

### `sort_index()` — Sort by Row/Column Labels

**Syntax:**
```python
DataFrame.sort_index(axis=0, ascending=True, inplace=False)
```

```python
df_with_id_index = df.set_index('PassengerId')
df_sorted_by_index = df_with_id_index.sort_index()
df_sorted_by_index.head()
# Rows sorted by PassengerId (1, 2, 3, 4, ...)
```

---

### Sorting Columns Alphabetically

```python
df.columns
# Index(['PassengerId', 'Survived', 'Pclass', 'Name', 'Sex', 'Age', ...])

sorted(df.columns)
# ['Age', 'Cabin', 'Embarked', 'Fare', 'Name', 'Parch', 'PassengerId', ...]

df_sorted = df[sorted(df.columns)]
df_sorted.head()
# Columns are now in alphabetical order
```

**When to use:** When combining DataFrames from different sources where column order differs — sorting alphabetically makes visual comparison easier.

---

## 13. String Operations — .str Accessor
`[INTERMEDIATE]`

### Concept Overview

Pandas provides a `.str` accessor on string Series that vectorizes Python string methods across all rows. Instead of looping and applying `str.lower()` manually, you call `df['col'].str.lower()`.

> **Real-World Analogy:** The `.str` accessor is like a "find and replace" tool that works on an entire column at once — no loops needed.

---

### Case Operations

```python
df['Name'].str.lower()       # All lowercase
df['Name'].str.upper()       # ALL UPPERCASE
df['Name'].str.capitalize()  # First letter capitalized, rest lowercase
```

---

### String Length

```python
df['Name'].str.len()   # Series of integer lengths for each name
```

---

### Replace Substrings

```python
# Simple string replacement (no regex)
df['Name'].str.replace("Mr.", "Sir", regex=False).head()
```

**Parameters:**
- `regex=False` — treat the pattern as a plain string, not a regex
- Default in Pandas 2.x is `regex=True` — always pass `regex=False` explicitly for plain strings

---

### Finding Position of a Substring

```python
df['Name'].str.find("Mr")   # Returns position of first occurrence (or -1 if not found)
```

**Application — Extracting substrings using find:**

```python
# For a single value:
name = df.loc[1, 'Name']
start = name.find("(")
end   = name.find(")")
result = name[0:start] + name[end+1:]
```

---

### Removing Parenthetical Substrings with `apply()`

```python
def remove_substring(name):
    start = name.find("(")
    end   = name.find(")")
    new_name = name[0:start] + name[end+1:]
    return new_name.strip()

df["Name_trimmed"] = df["Name"].apply(remove_substring)
df["Name_trimmed"].head()
```

**Before and after comparison:**
```python
df.loc[1, 'Name']          # Original: "Cumings, Mrs. John Bradley (Florence Briggs Thayer)"
df.loc[1, 'Name_trimmed']  # Trimmed:  "Cumings, Mrs. John Bradley"
```

---

### Strip Whitespace

```python
df['Name'].str.strip()    # Remove leading AND trailing whitespace
df['Name'].str.lstrip()   # Remove leading (left) whitespace only
df['Name'].str.rstrip()   # Remove trailing (right) whitespace only
```

**When to use:** After reading messy CSV files where text columns have accidental spaces.

---

### Check if Substring Exists — `.str.contains()`

```python
# Count passengers with "Miss" in name
count = df['Name'].str.contains("Miss").sum()
print("Number of 'Miss':", count)
```

**Key Points:**
- Returns a boolean Series (`True`/`False` per row)
- `.sum()` on a boolean Series counts `True` values (since `True == 1`)
- Supports regex patterns (default) — use `regex=False` for plain strings

---

### Split and Access Elements

```python
df['Name'][0]                     # Raw string: 'Braund, Mr. Owen Harris'
df['Name'].str.split(",")[0]      # ['Braund', ' Mr. Owen Harris'] — split each name
df['Name'].str.split().str[0]     # First word after whitespace split (vectorized)
print(df['Name'].str.split()[0][0])  # 'Braund' — first word of first name
```

**The double `.str` pattern:**
- First `.str.split()` — splits each string into a list
- Second `.str[0]` — accesses element 0 from each list (vectorized)

---

### Pad Strings — `.str.pad()`

```python
df['Name'].str.pad(width=20, side='left', fillchar='-')
# Right-aligns names in a 20-character field, filling with '-'
# e.g., '--------John Doe'
```

| Parameter | Options |
|---|---|
| `width` | Minimum total width |
| `side` | `'left'`, `'right'`, `'both'` |
| `fillchar` | Character to pad with (default space) |

---

### String Method Reference

| Method | Description |
|---|---|
| `.str.lower()` | Lowercase all characters |
| `.str.upper()` | Uppercase all characters |
| `.str.capitalize()` | Capitalize first letter |
| `.str.strip()` / `.lstrip()` / `.rstrip()` | Remove whitespace |
| `.str.len()` | Length of each string |
| `.str.replace(pat, repl)` | Replace substring/regex |
| `.str.contains(pat)` | Boolean: does string contain pattern? |
| `.str.find(sub)` | Position of first occurrence |
| `.str.split(pat)` | Split string into list |
| `.str.pad(width, side, fillchar)` | Pad to fixed width |
| `.str.cat(sep)` | Concatenate strings |
| `.str.extract(pat)` | Extract regex groups into columns |

---

## 14. Regex in Pandas & Python
`[INTERMEDIATE]`

### Concept Overview

**Regular expressions (regex)** are patterns for matching, searching, and extracting text. Pandas integrates regex natively into `.str.replace()`, `.str.contains()`, `.str.extract()`.

---

### Removing Parenthetical Substrings with Regex

```python
# Replace any substring of the form "(anything)" with empty string
df['Name'].str.replace(r"\(.*\)", "", regex=True).head()
```

**Regex Pattern Breakdown:**

| Part | Meaning |
|---|---|
| `r` | Raw string — tells Python not to interpret `\` as escape |
| `\(` | Escaped `(` — matches a literal opening parenthesis |
| `.*` | `.` matches any character, `*` means zero or more |
| `\)` | Escaped `)` — matches a literal closing parenthesis |

So `r"\(.*\)"` matches: `(` + anything + `)` → e.g., `(Florence Briggs Thayer)`.

---

### Email Validation with `re` Module

```python
import re

def is_valid_email(email):
    # Pattern breakdown:
    # ^[a-zA-Z0-9._%+-]+  → username: alphanumeric + common symbols
    # @                   → mandatory @ symbol
    # [a-zA-Z0-9.-]+      → domain: alphanumeric, dots, or hyphens
    # \.[a-zA-Z]{2,}$     → TLD: dot + 2+ letters at end
    pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'

    if re.fullmatch(pattern, email):
        return True
    return False

emails = ["user@example.com", "invalid-email", "name.surname@sub.domain.org", "hello@ghail@abc.com"]
for e in emails:
    print(f"{e}: {'Valid' if is_valid_email(e) else 'Invalid'}")
```

**Expected Output:**
```
user@example.com: Valid
invalid-email: Invalid
name.surname@sub.domain.org: Valid
hello@ghail@abc.com: Invalid
```

**Why `re.fullmatch()`?** It requires the pattern to match the **entire** string (not just a part of it). `re.search()` would match a pattern anywhere inside the string.

---

## 15. Categorical Data — pd.Categorical
`[INTERMEDIATE]`

### Concept Overview

**Categorical data** is data that represents discrete categories or groups — like gender (`male`/`female`), class (`G1`, `G2`, `G3`, `G4`), or boarding port (`C`, `Q`, `S`).

By default, Pandas stores string columns as `object` dtype (a Python object array) which is memory-heavy and has no sense of order. Converting to `category` dtype provides:
1. **Memory savings** — stores an integer code internally, not the full string
2. **Performance gains** — groupby, filter, sort are faster on category
3. **Ordering semantics** — you can define a custom order and do comparisons like `G1 > G4`

> **Real-World Analogy:** Think of ordered categories like T-shirt sizes: `S < M < L < XL`. You want Pandas to know that `L > M` when comparing.

---

### Problem: String Comparison Is Alphabetical, Not Meaningful

```python
class_data = {
    "Name":   ["A", "B", "C", "D"],
    "Groups": ["G1", "G2", "G3", "G4"]
}
class_df = pd.DataFrame(class_data)
class_df.info()
# Groups column: dtype=object
```

With `object` dtype, `"G1" > "G4"` is just alphabetical — meaningless for our domain.

---

### Creating an Ordered Categorical Column

```python
class_df['Group_cat'] = pd.Categorical(
    class_df['Groups'],
    categories=['G3', 'G1', 'G2', 'G4'],  # Custom order: G3 < G1 < G2 < G4
    ordered=True
)
# Internally: 'G3'→0, 'G1'→1, 'G2'→2, 'G4'→3
```

**After conversion:**
```python
class_df.info()
# Group_cat column: dtype=category (ordered: True)

class_df['Group_cat'].cat.categories
# Index(['G3', 'G1', 'G2', 'G4'], dtype='object')

# Now comparisons use the defined order:
class_df['Group_cat'] >= "G4"   # Returns boolean Series based on custom order
```

---

### Auto-Categorical from `astype('category')`

```python
# Titanic Embarked column: C, Q, S, N
df['Embarked'] = df['Embarked'].fillna("N")  # Replace NaN with 'N'

# Auto-categorical (alphabetical order, unordered)
df['Embarked_auto_cat'] = df['Embarked'].astype('category')
df['Embarked_auto_cat'].cat.categories
# Index(['C', 'N', 'Q', 'S'], dtype='object') — alphabetical, unordered
```

---

### Custom Ordered Categorical — Titanic Embarked

```python
# Business requirement: N < S < C < Q (custom domain order)
df['Embarked_cust_cat'] = pd.Categorical(
    df['Embarked'],
    categories=['N', 'S', 'C', 'Q'],  # N=0, S=1, C=2, Q=3
    ordered=True
)

df['Embarked_cust_cat'].cat.categories
# Index(['N', 'S', 'C', 'Q'], dtype='object')

# Now filter: passengers who embarked at a port ranked higher than 'S'
df['Embarked_cust_cat'] > 'S'
# True for 'C' and 'Q', False for 'N' and 'S'
```

---

### Accessing Category Codes

```python
df['Embarked_cust_cat'].cat.codes.head()
# Numeric codes: N→0, S→1, C→2, Q→3
# 0    1   (S)
# 1    2   (C)
# 2    1   (S)
# dtype: int8
```

**Use case:** Machine learning models require numeric input — `.cat.codes` gives you the integer encoding directly.

---

### Encoding Sex Column — Three Equivalent Approaches

```python
df['Sex'].unique()   # ['male', 'female']

# Method 1: Via category codes
df['Sex_cat'] = df['Sex'].astype('category').cat.codes
# female→0, male→1 (alphabetical)

# Method 2: Via replace
df['Sex_cat'] = df['Sex'].replace(['female', 'male'], [0, 1])

# Method 3: Via apply/lambda
df['Sex_cat'] = df['Sex'].apply(lambda sex: 1 if sex == 'male' else 0)
```

All three produce a numeric 0/1 column. Method 2 (`replace`) is the most readable for small mappings. For many categories, prefer `pd.Categorical` + `.cat.codes`.

---

### Memory Advantage of `category` dtype

| Column | dtype | Memory |
|---|---|---|
| `df['Embarked']` | `object` | ~57 KB (stores full strings) |
| `df['Embarked_cust_cat']` | `category` | ~1 KB (stores int8 codes + small lookup) |

For columns with low cardinality (few unique values relative to row count), `category` dtype can reduce memory by **50–95%**.

---

## 16. Date & DateTime Operations
`[INTERMEDIATE]`

### Concept Overview

Pandas has first-class support for datetime data. Dates stored as strings (`object` dtype) have no temporal meaning — Pandas can't compute "how many days between?" or "filter events after March 2025". Converting to `datetime64` dtype unlocks the full `.dt` accessor.

> **Real-World Analogy:** A date stored as a string is like a note that says "March 15th" — you can read it but can't calculate how many days until then. A `datetime64` value is like a calendar entry your computer understands.

---

### Converting Strings to Datetime — `pd.to_datetime()`

```python
df = pd.DataFrame({
    'event': ['Concert', 'Conference', 'Wedding'],
    'date': ['2025-01-01', '2025-03-15', '2025-07-20']
})

df.info()
# date column: dtype=object (just strings — no date awareness)
```

```python
df['date_pandas'] = pd.to_datetime(df['date'])
# Optionally specify format: pd.to_datetime(df['date'], format='%d/%m/%Y')

df.info()
# date_pandas column: dtype=datetime64[us] (Pandas 2.x uses microsecond resolution by default)
```

**Pandas 2.x Change:** In Pandas 1.x, `pd.to_datetime()` always produced `datetime64[ns]`. In Pandas 2.x, it uses resolution-inferred types like `datetime64[s]`, `datetime64[ms]`, `datetime64[us]`.

---

### The `.dt` Accessor — Extract Components

Once a column has `datetime64` dtype, use the `.dt` accessor:

```python
df['date_pandas'].dt.year       # 2025, 2025, 2025
df['date_pandas'].dt.month      # 1, 3, 7
df['date_pandas'].dt.day        # 1, 15, 20
df['date_pandas'].dt.weekday    # 0=Monday, ..., 6=Sunday
df['date_pandas'].dt.day_name() # 'Wednesday', 'Saturday', 'Sunday'
```

---

### Datetime Filtering

```python
# Filter events after April 1st 2025
df[df['date_pandas'] > '2025-04-01']
# Pandas automatically parses the string '2025-04-01' for comparison

# Filter events in a date range (inclusive)
df[df['date_pandas'].between('2025-01-01', '2025-06-01')]
```

---

### Creating Datetime Ranges — `pd.date_range()`

```python
# Daily range
pd.date_range(start='2026-02-01', end='2026-02-10', freq='D')
# DatetimeIndex(['2026-02-01', '2026-02-02', ..., '2026-02-10'], dtype='datetime64[ns]', freq='D')

# Month-end frequency
pd.date_range(start='2024-01-01', end='2024-03-10', freq='ME')
# → 2024-01-31, 2024-02-29 (leap year)

# Month-start frequency
pd.date_range(start='2024-01-01', end='2024-03-10', freq='MS')
# → 2024-01-01, 2024-02-01, 2024-03-01
```

**Frequency Aliases Reference:**

| Alias | Meaning |
|---|---|
| `'D'` | Calendar day |
| `'ME'` | Month-end |
| `'MS'` | Month-start |
| `'W'` | Weekly (Sundays) |
| `'h'` | Hourly |
| `'min'` | Minutely |
| `'s'` | Secondly |

> **Pandas 2.x Change:** Old aliases `'M'`, `'H'`, `'T'`, `'S'` are deprecated. Use `'ME'`, `'h'`, `'min'`, `'s'` instead.

---

### Date Arithmetic — `pd.Timedelta` and `pd.Timestamp`

```python
# Add 7 days to every event date
df['next_week'] = df['date_pandas'] + pd.Timedelta(days=7)

# Inspect a Timedelta value
pd.Timedelta(days=7)
# Timedelta('7 days 00:00:00')

# Get today's date as a Timestamp
pd.Timestamp.today()
# Timestamp('2026-05-10 14:23:01.123456')

# Calculate days until each event
df['days_to_event'] = df['date_pandas'] - pd.Timestamp.today()
# Result: Timedelta Series (can be negative for past events)
df.head()
```

**Expected Output (approximate, depends on run date):**
```
       event date_pandas  next_week days_to_event
0    Concert  2025-01-01 2025-01-08  -494 days ...
1 Conference  2025-03-15 2025-03-22  -421 days ...
2    Wedding  2025-07-20 2025-07-27  -294 days ...
```

---

### `.dt` Accessor Reference

| Accessor | Returns | Example |
|---|---|---|
| `.dt.year` | Integer year | `2025` |
| `.dt.month` | Integer month (1-12) | `3` |
| `.dt.day` | Integer day (1-31) | `15` |
| `.dt.hour` | Integer hour (0-23) | `14` |
| `.dt.minute` | Integer minute | `30` |
| `.dt.weekday` | 0=Monday, 6=Sunday | `2` |
| `.dt.day_name()` | Day name as string | `'Wednesday'` |
| `.dt.date` | Python `date` object | `datetime.date(2025, 3, 15)` |
| `.dt.quarter` | Quarter (1-4) | `1` |
| `.dt.is_leap_year` | Boolean | `True` |

---

## Pandas 2.x Migration Cheat Sheet

| Old (1.x) Pattern | New (2.x) Pattern | Status |
|---|---|---|
| `df.append(row)` | `pd.concat([df, new_row])` | **Removed** in 2.0 |
| `df.applymap(func)` | `df.map(func)` | `applymap` deprecated 2.1+ |
| `datetime64[ns]` always | `datetime64[s/ms/us/ns]` | Resolution-flexible |
| Silent downcasting in `fillna/where/mask` | Explicit casting required | `FutureWarning` → Error |
| `freq='M'` | `freq='ME'` | Deprecated aliases |
| Copy behavior unpredictable | Copy-on-Write (CoW) | Default in 3.0 |
| `inplace=True` everywhere | Method chaining preferred | Soft-deprecated |

---

## Quick Reference — Most-Used Operations

```python
import pandas as pd
import numpy as np

df = pd.read_csv("file.csv")

# Inspect
df.head(n)           # First n rows
df.tail(n)           # Last n rows
df.shape             # (rows, cols)
df.info()            # Schema + nulls
df.describe()        # Stats summary
df.columns           # Column names
df.dtypes            # Column dtypes

# Select
df['col']                        # Series (column)
df[['col1', 'col2']]             # DataFrame (columns)
df.loc[row_label, col_label]     # Label-based
df.iloc[row_pos, col_pos]        # Position-based

# Filter
df[df['col'] > value]
df[(df['col1'] == 'x') & (df['col2'] > 5)]
df.loc[df['col'] > value, ['col1', 'col2']]

# Mutate
df['new_col'] = df['col'].apply(func)
df['new_col'] = df['col'].str.lower()
df['new_col'] = pd.to_datetime(df['date_col'])
df['new_col'] = df['col'].astype('category')

# Sort
df.sort_values(by='col', ascending=False)
df.sort_values(by=['col1', 'col2'], ascending=[True, False])
df.sort_index()

# Index
df.set_index('col')
df.reset_index()
df.reindex(new_labels, fill_value=0)

# String
df['col'].str.lower() / .upper() / .strip()
df['col'].str.contains('pattern')
df['col'].str.replace('old', 'new', regex=False)
df['col'].str.split(',').str[0]

# DateTime
pd.to_datetime(df['col'])
df['col'].dt.year / .month / .day / .day_name()
pd.date_range(start, end, freq='D')
df['col'] + pd.Timedelta(days=7)
```

---

*Notes generated from Pandas course notebooks: 22-02-2026, 25-02-2026, 28-02-2026*
