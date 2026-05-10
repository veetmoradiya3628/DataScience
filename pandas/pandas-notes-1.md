# Pandas — Introduction, Series, DataFrame, Indexing & Boolean Selection

> **Source Notebook:** `22-02-2026.ipynb` | **Pandas Version:** 2.x | **Date:** 22 Feb 2026

---

## Prerequisites

- Python fundamentals (lists, dicts, loops, functions)
- Basic understanding of NumPy arrays (`np.array`, shapes, dtypes)
- Comfort with Jupyter notebooks (cells, output, `display()`)

---

## Environment Setup

```bash
# Using pip
pip install pandas numpy

# Using uv (modern Python package manager)
uv add pandas
```

Inside the notebook, installing inline:

```python
# !pip install pandas
# !uv add pandas  ← recommended if you're using uv-managed projects
```

**Standard imports used throughout this notebook:**

```python
import pandas as pd
import numpy as np
```

---

## Concept Overview — What is Pandas?

Pandas is an open-source Python library for **data manipulation and analysis**. It provides fast, flexible, and expressive data structures designed to work with structured (tabular, relational) data.

> **Built on NumPy.** The name comes from **"Panel Data"** — an econometrics term for multidimensional structured datasets.

### Real-World Analogy

Think of Pandas as:

> **📊 Excel + 🧮 NumPy + 🗂️ SQL + 🧰 ETL tools — all inside Python**

A spreadsheet lets you view, filter, sort, and compute on tabular data. Pandas does all of that and more — with code, automation, and scale.

### Why Does Pandas Exist? What Problem Does It Solve?

**Before Pandas**, data scientists used:
- Raw Python lists and dicts — no labels, no alignment, no grouping
- NumPy arrays — great for math, but no column names or row labels
- No built-in missing data handling, join/merge, or IO for real-world formats (CSV, Excel, JSON)

Real-world data is tabular. Pandas was designed to bridge the gap.

### Where Is Pandas Used?

| Field | How Pandas Helps |
|-------|-----------------|
| Data Science | Load, clean, explore, transform, and model data |
| Machine Learning | Feature engineering, preprocessing, train/test splits |
| Finance | Time series, stock prices, portfolio records |
| Web/Data Analytics | ETL pipelines, server logs, product usage metrics |
| Healthcare | Patient records, diagnostics, hospital statistics |

---

## Core Objects in Pandas

| Core Object | Description | Analogy |
|-------------|-------------|---------|
| `Series` | 1D labeled array | One column in Excel |
| `DataFrame` | 2D labeled table | A full spreadsheet or SQL table |
| `Panel` *(deprecated)* | 3D data container | Replaced by MultiIndex in 2.x |

---

## Key Terminology

| Term | Meaning |
|------|---------|
| `Series` | 1D array with an index |
| `DataFrame` | 2D table; a dict of Series sharing a common index |
| `Index` | Labels for rows (or columns); enables label-based access |
| `RangeIndex` | Default integer index (0, 1, 2, …) when none is specified |
| `dtype` | Data type of a column/Series (`int64`, `float64`, `object`, etc.) |
| `axis=0` | Operates along rows (down) |
| `axis=1` | Operates along columns (across) |
| `NaN` | "Not a Number" — Pandas representation of missing numeric data |
| `vectorized` | Operation applied to entire array at once (no Python loop) |
| `boolean mask` | A Series of `True`/`False` values used to filter rows |

---

## Part 1 — `pd.Series`

---

### 1.1 What Is a Series? [BEGINNER]

A **Series** is a one-dimensional labeled array. It holds any data type: integers, floats, strings, Python objects.

Every Series has two parts:

```
Index:  a  b  c  d
Values: 10 20 30 40
```

**Real-world analogy:** One column in an Excel spreadsheet — each row has a label (index) and a value.

**SQL equivalent:** A single-column `SELECT` result with row numbers as the index.

---

### 1.2 Creating a Series [BEGINNER]

#### Full Syntax

```python
pd.Series(data, index=None, dtype=None, name=None, copy=False)
```

| Parameter | Description | Default |
|-----------|-------------|---------|
| `data` | List, dict, NumPy array, or scalar | required |
| `index` | Custom labels for each element | `RangeIndex(0, n)` |
| `dtype` | Force a specific dtype | inferred from data |
| `name` | Name of the Series (shows as column name in DataFrame) | `None` |
| `copy` | Copy input data | `False` (CoW in Pandas 2.x) |

---

#### Example 1: From a Python List [BEGINNER]

```python
import pandas as pd

data = [1, 2, 3, 4, 5, 6]
s1 = pd.Series(data)
print(s1)
```

**Output:**

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
- `data` is a plain Python list of integers
- `pd.Series(data)` converts it into a Series with a default `RangeIndex` (0, 1, 2, …)
- The left column (0–5) is the **index**; the right column is the **values**
- `dtype: int64` — Pandas inferred the dtype from the data

---

#### Example 2: From a List with Custom Index and Name [BEGINNER]

```python
data = [1, 2, 3, 4, 5, 6]
s2 = pd.Series(data, index=['a', 'b', 'c', 'd', 'e', 'f'], name='Numbers')
print(s2)
```

**Output:**

```
a    1
b    2
c    3
d    4
e    5
f    6
Name: Numbers, dtype: int64
```

**Line-by-line breakdown:**
- `index=['a', 'b', 'c', 'd', 'e', 'f']` — replaces the default integer index with letter labels
- `name='Numbers'` — names the Series; appears as the column header when the Series becomes a DataFrame column
- Index must have the **same length** as `data` — otherwise `ValueError: Length of values does not match length of index`

---

#### Example 3: From a Python Dictionary [BEGINNER]

```python
data = {'a': 10, 'b': 20, 'c': 30}
s3 = pd.Series(data)
print(s3)
```

**Output:**

```
a    10
b    20
c    30
dtype: int64
```

**Why this works:** When `data` is a dict, the **keys become the index** and **values become the data**. The order is preserved (Python 3.7+ dicts are ordered).

**Interview insight:** If you pass `index=['b', 'c', 'd']` with a dict input, Pandas **aligns by key** — `'d'` would appear as `NaN` because it's missing from the dict.

---

#### Example 4: From a NumPy Array [BEGINNER]

```python
import numpy as np

arr = np.array([5, 10, 15])
s4 = pd.Series(arr, index=['x', 'y', 'z'])
print(s4)
```

**Output:**

```
x     5
y    10
z    15
dtype: int64
```

**Why this works:** Pandas wraps the NumPy array without copying data (zero-copy by default). The dtype is inherited from the array. This is why Pandas is so fast — it reuses NumPy's C-level storage.

---

### 1.3 Series Attributes & Properties [BEGINNER]

```python
s2 = pd.Series([10, 20, 30, 40], index=['a', 'b', 'c', 'd'], name='Scores')
```

```python
print(s2)
print(s2.index)    # Index object
print(s2.values)   # NumPy array of values
print(s2.dtype)    # Data type
print(s2.name)     # Name of the Series
print(s2.shape)    # Tuple: (n,)
type(s2.shape)     # <class 'tuple'>
```

**Output:**

```
a    10
b    20
c    30
d    40
Name: Scores, dtype: int64
Index(['a', 'b', 'c', 'd'], dtype='object')
[10 20 30 40]
int64
Scores
(4,)
<class 'tuple'>
```

**Attributes Reference Table:**

| Attribute | Returns | Example Output |
|-----------|---------|---------------|
| `.index` | Index object (labels) | `Index(['a','b','c','d'], dtype='object')` |
| `.values` | NumPy ndarray of values | `array([10, 20, 30, 40])` |
| `.dtype` | dtype of the data | `int64` |
| `.name` | Name of the Series | `'Scores'` |
| `.shape` | Tuple `(n,)` | `(4,)` |
| `.size` | Total number of elements | `4` |
| `.ndim` | Number of dimensions (always 1) | `1` |

**Why `.values` returns a NumPy array:** Pandas internally stores data in NumPy arrays. `.values` gives you a direct view into that storage — useful for interoperability with libraries that expect NumPy.

---

### 1.4 Series Indexing & Access [BEGINNER → INTERMEDIATE]

```python
s2 = pd.Series([10, 20, 30, 40], index=['a', 'b', 'c', 'd'], name='Scores')
```

#### Method 1: `.values[i]` — Positional via NumPy Array

```python
print(s2.values[0])   # 10
print(s2.values[3])   # 40
```

**Output:**

```
10
40
```

- `.values` extracts the underlying NumPy array
- `[0]` is then standard NumPy positional indexing
- Returns a Python scalar, not a Pandas object
- **Use when:** You need a raw NumPy value and don't need the index

---

#### Method 2: `.iloc[]` — Integer-Location Based [BEGINNER]

```python
print(s2.iloc[0])     # 10
print(s2.iloc[1:3])   # slice from position 1 up to (not including) 3
```

**Output:**

```
10
b    20
c    30
Name: Scores, dtype: int64
```

- `iloc` = **i**nteger **loc**ation — always uses **position**, never label
- Slicing with `iloc` follows Python rules: **end is exclusive** (`iloc[1:3]` → positions 1 and 2)
- Returns a scalar for single access, a Series for slices/lists

---

#### Method 3: `.loc[]` — Label-Based [BEGINNER]

```python
print(s2['a'])          # 10  — bracket label access
print(s2['b'])          # 20
print(s2[['b', 'd']])   # multi-label selection
print(s2.loc[['b', 'd']])  # same, explicit
```

**Output:**

```
10
20
b    20
d    40
Name: Scores, dtype: int64
b    20
d    40
Name: Scores, dtype: int64
```

- `loc` = **l**abel-based **loc**ation — uses **index labels**, not positions
- `s2[['b', 'd']]` passes a list → returns a **Series** (not a scalar)
- `s2.loc[['b', 'd']]` is the explicit, recommended form

---

#### ⚠️ CRITICAL: FutureWarning — `s2[0]` on Non-Integer Index

```python
# s2 has index ['a', 'b', 'c', 'd'] — NOT integer-based
print(s2[0])
```

**Output (with warning):**

```
10

FutureWarning: Series.__getitem__ treating keys as positions is deprecated.
In a future version, integer keys will always be treated as labels
(consistent with DataFrame behavior).
To access a value by position, use `ser.iloc[pos]`
```

**Why this happens:** Pandas 2.x is removing the ambiguity between label-based and positional access. When the index is NOT integer-based (e.g., `['a', 'b', 'c', 'd']`), `s2[0]` is ambiguous — is `0` a label or a position? Pandas 1.x treated it as a position (fallback). **Pandas 2.x will always treat it as a label** — so `s2[0]` would raise a `KeyError` if `0` is not in the index.

**Fix:** Always use `.iloc[0]` for position-based access.

| ❌ Old (1.x / ambiguous) | ✅ New (2.x / explicit) |
|--------------------------|------------------------|
| `s2[0]` on non-int index | `s2.iloc[0]` |
| `s2[0]` on int index | `s2.loc[0]` or `s2.iloc[0]` |

---

#### Accessing on Integer-Default Index Series

```python
s3 = pd.Series([10, 20, 30, 40])  # RangeIndex: 0, 1, 2, 3

s3.iloc[[1, 2]]   # positions 1 and 2
s3.iloc[1:3]      # slice: positions 1 up to (not including) 3
s3.loc[[1, 2]]    # labels 1 and 2 — same as positions here!
```

**Output for `s3.iloc[[1, 2]]` and `s3.iloc[1:3]` and `s3.loc[[1, 2]]`:**

```
1    20
2    30
dtype: int64
```

> **Gotcha:** When the index IS integer-based (RangeIndex), `.loc[1]` and `.iloc[1]` return the same result — but for different reasons. `.loc[1]` matches the **label** `1`; `.iloc[1]` matches **position** `1`. They happen to be equal here, but diverge when you reset or reorder the index.

---

#### Indexing Comparison Table [INTERMEDIATE]

| Accessor | Basis | Slice endpoint | Use for |
|----------|-------|----------------|---------|
| `s.values[i]` | Position (NumPy) | Exclusive | Raw NumPy extraction |
| `s.iloc[i]` | Position | Exclusive | Positional access |
| `s.loc[label]` | Label | **Inclusive** | Label-based access |
| `s['label']` | Label | N/A | Shorthand for `loc` |
| `s[['a','b']]` | Label (list) | N/A | Multi-label selection |

**Mnemonic:** `loc` = **L**abel, `iloc` = **I**nteger position

---

## Part 2 — `pd.DataFrame`

---

### 2.1 What Is a DataFrame? [BEGINNER]

A **DataFrame** is a 2D labeled data structure with columns that can hold different dtypes — like a spreadsheet or SQL table.

Think of it as:
- **A dict of Series** sharing a common index
- **A table** with labeled rows (index) and labeled columns

**Real-world analogy:** An Excel spreadsheet where:
- Each row is a record (e.g., one passenger)
- Each column is an attribute (e.g., Name, Age, City)
- The row number (or ID) is the index

| Concept | Real-Life Analogy |
|---------|------------------|
| `DataFrame` | Excel sheet or SQL table |
| `Series` | One column of that sheet |
| `Index` | Row number or unique ID |
| Columns | Named headers |

**SQL equivalent:**

```sql
-- A DataFrame is equivalent to:
SELECT PassengerId, Survived, Pclass, Name, Age FROM titanic;
```

---

### 2.2 Creating a DataFrame [BEGINNER]

#### From a Dictionary of Lists (most common)

```python
import pandas as pd

data = {
    'Name': ['Hemanth', 'Aditya', 'rajbeer'],
    'Age': [30, 31, 32],
    'City': ['Delhi', 'Mumbai', 'Bangalore']
}

df = pd.DataFrame(data)
print(df)
```

**Output:**

```
      Name  Age       City
0  Hemanth   30      Delhi
1   Aditya   31     Mumbai
2  rajbeer   32  Bangalore
```

**Why this works:**
- Each key becomes a **column name**
- Each list becomes the **column values**
- Pandas automatically creates a `RangeIndex` (0, 1, 2) for rows
- All lists must have the **same length** — otherwise `ValueError`

**SQL equivalent:**

```sql
SELECT 'Hemanth' AS Name, 30 AS Age, 'Delhi' AS City
UNION ALL SELECT 'Aditya', 31, 'Mumbai'
UNION ALL SELECT 'rajbeer', 32, 'Bangalore';
```

---

#### From a List of Dicts [BEGINNER]

```python
records = [
    {'Name': 'Hemanth', 'Age': 30, 'City': 'Delhi'},
    {'Name': 'Aditya',  'Age': 31, 'City': 'Mumbai'},
    {'Name': 'Rajbeer', 'Age': 32, 'City': 'Bangalore'}
]

df = pd.DataFrame(records)
print(df)
```

**Output:**

```
      Name  Age       City
0  Hemanth   30      Delhi
1   Aditya   31     Mumbai
2  Rajbeer   32  Bangalore
```

**When to use:** When data naturally comes as a list of records (e.g., API responses in JSON format, database row results). Each dict is one row; keys are automatically unified into column names.

---

#### From a NumPy Array [BEGINNER]

```python
import numpy as np

arr = np.array([[1, 2], [3, 4], [5, 6]])
df = pd.DataFrame(arr, columns=['A', 'B'])
print(df)
```

**Output:**

```
   A  B
0  1  2
1  3  4
2  5  6
```

**Limitation:** All columns inherit the same dtype from the NumPy array. Use a dict-based constructor for mixed-type columns.

---

### 2.3 Accessing DataFrame Data [BEGINNER → INTERMEDIATE]

```python
data = {
    'Name': ['Hemanth', 'Aditya', 'rajbeer'],
    'Age': [30, 31, 32],
    'City': ['Delhi', 'Mumbai', 'Bangalore']
}
df = pd.DataFrame(data)
```

#### Single Column → Series

```python
print(df['Name'])
```

**Output:**

```
0    Hemanth
1     Aditya
2    rajbeer
Name: Name, dtype: object
```

Returns a **Series** — not a DataFrame. The `dtype: object` means it's storing Python strings.

#### Multiple Columns → DataFrame

```python
print(df[['Name', 'Age']])
```

**Output:**

```
      Name  Age
0  Hemanth   30
1   Aditya   31
2  rajbeer   32
```

Double brackets `[[ ]]` — outer `[]` is the indexing operator, inner `[]` creates a Python list of column names.

#### Row by Integer Position — `.iloc[0]`

```python
print(df.iloc[0])
```

**Output:**

```
Name    Hemanth
Age          30
City      Delhi
Name: 0, dtype: object
```

Returns a **Series** where the index is the column names. `dtype: object` because the Series mixes strings and ints.

#### Row by Label — `.loc[0]`

```python
print(df.loc[0])
```

**Output:**

```
Name    Hemanth
Age          30
City      Delhi
Name: 0, dtype: object
```

With default `RangeIndex`, `.loc[0]` and `.iloc[0]` return the same row — but `.loc` uses the **label** `0`, and `.iloc` uses the **position** `0`.

---

## Part 3 — Reading Real-World Data

---

### 3.1 `pd.read_csv()` — Loading Data [BEGINNER]

```python
# From a URL
titanic_dataset_github_url = "https://raw.githubusercontent.com/datasciencedojo/datasets/refs/heads/master/titanic.csv"
df = pd.read_csv(titanic_dataset_github_url)

# From a local file
titanic_dataset_local_url = "titanic.csv"
df = pd.read_csv(titanic_dataset_local_url)
```

**Why it matters:** `pd.read_csv()` is the most common entry point into any Pandas workflow. It handles:
- Automatic dtype inference per column
- Header detection (first row as column names)
- Index creation (`RangeIndex` by default)
- Handling of missing values (`NaN` for empty cells)

**Key Parameters:**

| Parameter | Purpose | Example |
|-----------|---------|---------|
| `filepath_or_buffer` | Path, URL, or file object | `'titanic.csv'` |
| `index_col` | Use a column as the row index | `index_col='PassengerId'` |
| `parse_dates` | Auto-parse date columns | `parse_dates=['Date']` |
| `dtype` | Force dtype for columns | `dtype={'Age': float}` |
| `usecols` | Load only specific columns | `usecols=['Name', 'Age']` |
| `nrows` | Load only N rows | `nrows=100` |
| `sep` | Delimiter (default `,`) | `sep='\t'` for TSV |
| `encoding` | File encoding | `encoding='utf-8'` |
| `chunksize` | Read in chunks (large files) | `chunksize=10000` |

**SQL equivalent:**

```sql
-- pd.read_csv('titanic.csv') is equivalent to:
SELECT * FROM titanic;
```

---

### 3.2 Checking the Type

```python
type(df)
```

**Output:**

```
<class 'pandas.core.frame.DataFrame'>
```

---

### 3.3 DataFrame Inspection Methods [BEGINNER]

#### `.head()` — First N Rows

```python
df.head()        # default: 5 rows
df.head(3)       # first 3 rows
df.head(10)      # first 10 rows

print(df.head())
print(df.head(3))

display(df.head(3))   # Jupyter: renders as HTML table
display(df.head(1))   # Jupyter: renders as HTML table
```

**Output (text form):**

```
   PassengerId  Survived  Pclass  \
0            1         0       3
1            2         1       1
2            3         1       3
3            4         1       1
4            5         0       3

                                                Name     Sex   Age  SibSp  \
0                            Braund, Mr. Owen Harris    male  22.0      1
1  Cumings, Mrs. John Bradley (Florence Briggs Th...  female  38.0      1
2                             Heikkinen, Miss. Laina  female  26.0      0
3       Futrelle, Mrs. Jacques Heath (Lily May Peel)  female  35.0      1
4                           Allen, Mr. William Henry    male  35.0      0
...
```

**`display()` vs `print()`:** In Jupyter notebooks, `display()` renders an interactive HTML table. `print()` outputs plain text. For multiple displays in one cell, always use `display()` — the last expression in a cell auto-displays, but earlier ones won't show without it.

**SQL equivalent:**

```sql
SELECT * FROM titanic LIMIT 5;
SELECT * FROM titanic LIMIT 3;
```

---

#### `.tail()` — Last N Rows

```python
df.tail()    # last 5 rows
```

**SQL equivalent:**

```sql
SELECT * FROM titanic ORDER BY rowid DESC LIMIT 5;
```

---

#### `.columns` — Column Labels

```python
df.columns
```

**Output:**

```
Index(['PassengerId', 'Survived', 'Pclass', 'Name', 'Sex', 'Age', 'SibSp',
       'Parch', 'Ticket', 'Fare', 'Cabin', 'Embarked'],
      dtype='object')
```

Returns an `Index` object containing all column names. Iterable — `list(df.columns)` gives a plain Python list.

---

#### `.shape` — Dimensions

```python
df.shape
```

**Output:**

```
(891, 12)
```

Returns a tuple `(rows, columns)`. The Titanic dataset has **891 rows** and **12 columns**.

**Complexity:** O(1) — shape is stored as metadata.

---

#### `.info()` — Summary

```python
older_passengers.info()
```

**Output:**

```
<class 'pandas.core.frame.DataFrame'>
Index: 5 entries, 33 to 851
Data columns (total 12 columns):
 #   Column       Non-Null Count  Dtype
---  ------       --------------  -----
 0   PassengerId  5 non-null      int64
 1   Survived     5 non-null      int64
 2   Pclass       5 non-null      int64
 3   Name         5 non-null      object
 4   Sex          5 non-null      object
 5   Age          5 non-null      float64
 6   SibSp        5 non-null      int64
 7   Parch        5 non-null      int64
 8   Ticket       5 non-null      object
 9   Fare         5 non-null      float64
10   Cabin        2 non-null      object
11   Embarked     5 non-null      object
dtypes: float64(2), int64(5), object(5)
memory usage: 584.0+ bytes
```

Shows: total rows, column names, non-null counts, dtypes, and memory usage. **Use this first** on any new dataset.

---

#### `.describe()` — Statistical Summary

```python
older_passengers.describe()
older_passengers['PassengerId'].describe()
```

**Output (for the full DataFrame):**

```
       PassengerId  Survived      Pclass         Age  SibSp  Parch        Fare
count     5.000000       5.0    5.000000    5.000000    5.0    5.0    5.000000
mean    422.600000       0.4    2.200000   74.600000    0.2    0.0  130.136000
std     310.066443       0.548  1.095445    3.847077    0.447  0.0  155.847394
min      33.000000       0.0    1.000000   71.000000    0.0    0.0   29.700000
25%     116.500000       0.0    1.000000   71.000000    0.0    0.0   29.700000
50%     457.000000       0.0    2.000000   74.000000    0.0    0.0   52.554200
75%     653.500000       1.0    3.000000   77.000000    0.0    0.0  135.633300
max     852.000000       1.0    3.000000   80.000000    1.0    0.0  512.329200
```

**What it shows:** count, mean, std, min, 25th/50th/75th percentile, max — for **numeric columns only**. String/object columns are excluded unless you pass `include='all'`.

---

#### `dir(df)` — All Attributes and Methods

```python
dir(df)
```

Lists every attribute and method available on a DataFrame. Useful for discovery in an interactive session.

---

## Part 4 — Column & Row Selection (Indexing Deep Dive)

---

### 4.1 Column Selection [BEGINNER]

```python
df['Name']            # Single column → Series
df[['Name', 'Age']]   # Multiple columns → DataFrame
```

**Output (single column):**

```
0                              Braund, Mr. Owen Harris
1    Cumings, Mrs. John Bradley (Florence Briggs Th...
...
Name: Name, dtype: object
```

**Output (multi-column):**

```
                                                Name   Age
0                            Braund, Mr. Owen Harris  22.0
1  Cumings, Mrs. John Bradley (Florence Briggs Th...  38.0
...
```

| Syntax | Returns | Example |
|--------|---------|---------|
| `df['col']` | Series | `df['Name']` |
| `df[['col1','col2']]` | DataFrame | `df[['Name','Age']]` |

**Common mistake:** `df['Name', 'Age']` → `KeyError: ('Name', 'Age')` — Pandas interprets the tuple as a single key. Always use double brackets for multiple columns.

---

### 4.2 Row Selection with `.loc[]` — Label-Based [BEGINNER → INTERMEDIATE]

```python
df.loc[0]           # Row with label 0 → Series
df.loc[0]['Name']   # Cell: row label 0, column 'Name'
df.loc[0, 'Name']   # Same cell, more efficient single-step access
```

**Output for `df.loc[0]`:**

```
PassengerId                          1
Survived                             0
Pclass                               3
Name           Braund, Mr. Owen Harris
Sex                               male
Age                               22.0
SibSp                                1
Parch                                0
Ticket                       A/5 21171
Fare                              7.25
Cabin                              NaN
Embarked                             S
Name: 0, dtype: object
```

**`df.loc[0]['Name']` vs `df.loc[0, 'Name']`:**

| Pattern | Steps | Performance | Warning |
|---------|-------|-------------|---------|
| `df.loc[0]['Name']` | 2 (chained) | Slower | May trigger `SettingWithCopyWarning` on assignment |
| `df.loc[0, 'Name']` | 1 (single) | Faster | Safe for reading and writing |

**Always prefer `df.loc[row, col]` over chained indexing.**

---

### 4.3 Row Selection with `.iloc[]` — Position-Based [BEGINNER → INTERMEDIATE]

```python
df.iloc[0]          # First row (position 0)
df.iloc[:5]         # First 5 rows (positions 0-4)
df.iloc[0, 3]       # Row position 0, column position 3
df.iloc[0:5, 3]     # Rows 0-4, column position 3
```

**Output for `df.iloc[0, 3]`:**

```
'Braund, Mr. Owen Harris'
```

(Column position 3 = `Name` in the Titanic dataset)

**Output for `df.iloc[0:5, 3]`:**

```
0                            Braund, Mr. Owen Harris
1    Cumings, Mrs. John Bradley (Florence Briggs Th...
2                             Heikkinen, Miss. Laina
3       Futrelle, Mrs. Jacques Heath (Lily May Peel)
4                           Allen, Mr. William Henry
Name: Name, dtype: object
```

---

### 4.4 Indexing Accessor Full Comparison [INTERMEDIATE]

| Accessor | Basis | Row input | Col input | Returns |
|----------|-------|-----------|-----------|---------|
| `df['col']` | Label | — | Column name | Series |
| `df[['c1','c2']]` | Label | — | List of names | DataFrame |
| `df.loc[r, c]` | Label | Row label | Column name | Scalar / Series / DataFrame |
| `df.iloc[r, c]` | Position | Integer | Integer | Scalar / Series / DataFrame |
| `df.at[r, c]` | Label | Row label | Column name | Scalar (fastest single cell) |
| `df.iat[r, c]` | Position | Integer | Integer | Scalar (fastest single cell) |
| `df[bool_mask]` | Boolean | Boolean Series | — | DataFrame |

**Mnemonic:** `loc` = **L**abel, `iloc` = **I**nteger position, `at`/`iat` = **A**tomic access (single value)

---

## Part 5 — Slicing Rows and Columns Together

---

### 5.1 `.loc[]` Slicing [INTERMEDIATE]

```python
# Rows 0 to 4 (inclusive!), only Name and Age columns
df.loc[:4, ['Name', 'Age']]
# Equivalent to df.loc[0:4, ['Name', 'Age']]
```

**Output:**

```
                                                Name   Age
0                            Braund, Mr. Owen Harris  22.0
1  Cumings, Mrs. John Bradley (Florence Briggs Th...  38.0
2                             Heikkinen, Miss. Laina  26.0
3       Futrelle, Mrs. Jacques Heath (Lily May Peel)  35.0
4                           Allen, Mr. William Henry  35.0
```

**Critical rule:** `.loc` slicing is **label-inclusive** on both ends. `df.loc[0:4]` returns rows with labels `0, 1, 2, 3, 4` — that's **5 rows**, not 4 like Python slicing.

```python
# Rows 0 to 3, Name and Survived columns
df.loc[0:3, ['Name', 'Survived']]
```

**Output:**

```
                                                Name  Survived
0                            Braund, Mr. Owen Harris         0
1  Cumings, Mrs. John Bradley (Florence Briggs Th...         1
2                             Heikkinen, Miss. Laina         1
3       Futrelle, Mrs. Jacques Heath (Lily May Peel)         1
```

---

### 5.2 `.iloc[]` Slicing [INTERMEDIATE]

```python
# First 3 rows, first 2 columns (exclusive endpoint)
df.iloc[0:3, 0:2]
# same as df.iloc[:3, :2]
```

**Output:**

```
   PassengerId  Survived
0            1         0
1            2         1
2            3         1
```

```python
# First 3 rows, Name (col 3) and Survived (col 1) — non-contiguous columns by position
df.iloc[0:3, [3, 1]]
```

**Output:**

```
                              Name  Survived
0          Braund, Mr. Owen Harris         0
1  Cumings, Mrs. John Bradley ...         1
2           Heikkinen, Miss. Laina         1
```

**`.iloc` vs `.loc` slicing difference:**

| Feature | `.loc` | `.iloc` |
|---------|--------|---------|
| Basis | Labels | Integer positions |
| Slice endpoint | **Inclusive** | **Exclusive** |
| Column selection | Name strings | Integer positions |
| Multi-col (non-adjacent) | `['col1','col3']` | `[0, 2]` |

---

## Part 6 — Conditional Selection (Boolean Indexing)

---

### 6.1 Single Condition [BEGINNER]

```python
# Step 1: Create a boolean mask
df['Age'] > 70
```

This produces a **boolean Series** — one `True` or `False` per row:

```
0      False
1      False
...
33      True
...
Name: Age, dtype: bool
```

```python
# Step 2: Apply the mask to filter rows
df[df['Age'] > 70]
```

**Output:**

```
     PassengerId  Survived  Pclass                                   Name   Sex   Age  ...
33            34         0       2                 Wheadon, Mr. Edward H  male  66.0  ...
54            55         0       1  Ostby, Mr. Engelhart Cornelius         male  65.0  ...
...
```

Only rows where `Age > 70` pass through. All 12 columns are returned.

**SQL equivalent:**

```sql
SELECT * FROM titanic WHERE Age > 70;
```

---

### 6.2 Inspecting Filtered Results [BEGINNER]

```python
older_passengers = df[df['Age'] > 70]

display(older_passengers.head())  # See first few
older_passengers.shape             # How many rows?
older_passengers.info()            # Column types and nulls
older_passengers.describe()        # Statistics
older_passengers['PassengerId'].describe()  # Stats for one column
```

**`older_passengers.shape` output:**

```
(5, 12)
```

5 passengers older than 70 in the Titanic dataset.

---

### 6.3 Multiple Conditions with `&` and `|` [INTERMEDIATE]

```python
# Females who survived (AND condition)
df[(df['Sex'] == 'female') & (df['Survived'] == 1)]
```

**Critical syntax rule:** Each condition **must be wrapped in parentheses** because `&` has higher precedence than `==` in Python. Without parentheses, you get a `TypeError`.

| Operator | Meaning | Pandas Syntax |
|----------|---------|---------------|
| AND | Both must be true | `&` (not `and`) |
| OR | At least one true | `\|` (not `or`) |
| NOT | Invert | `~` (not `not`) |

**SQL equivalent:**

```sql
SELECT * FROM titanic WHERE Sex = 'female' AND Survived = 1;
```

---

### 6.4 Boolean Indexing + Column Selection [INTERMEDIATE]

```python
# Method 1: Chained (less preferred)
female_survivors_subset = df[(df['Sex'] == 'female') & (df['Survived'] == 1)][['Name', 'Age']]
female_survivors_subset.head()
```

```python
# Method 2: .loc (preferred — single step, safe for assignment)
df.loc[(df['Sex'] == 'female') & (df['Survived'] == 1), ['Name', 'Age']]
```

**Both produce the same output:**

```
                                                Name   Age
1     Cumings, Mrs. John Bradley (Florence Brigg...  38.0
2                              Heikkinen, Miss. Laina  26.0
3        Futrelle, Mrs. Jacques Heath (Lily May Peel)  35.0
...
```

**Why prefer `.loc`:** Chained indexing (`df[cond][cols]`) creates an intermediate copy. Assigning to it (e.g., `df[cond][cols] = value`) may not modify the original and raises `SettingWithCopyWarning`. `.loc` operates on the original in one step.

**SQL equivalent:**

```sql
SELECT Name, Age FROM titanic WHERE Sex = 'female' AND Survived = 1;
```

---

## Part 7 — Set Index & Reset Index

---

### 7.1 `.set_index()` [INTERMEDIATE]

```python
df_by_passenger_id = df.set_index('PassengerId')
df_by_passenger_id.head()
```

**Output:**

```
             Survived  Pclass                                               Name  ...
PassengerId
1                   0       3                            Braund, Mr. Owen Harris  ...
2                   1       1  Cumings, Mrs. John Bradley (Florence Briggs Th...  ...
3                   1       3                             Heikkinen, Miss. Laina  ...
4                   1       1       Futrelle, Mrs. Jacques Heath (Lily May Peel)  ...
5                   0       3                           Allen, Mr. William Henry  ...
```

Now `PassengerId` is the **row label** (index), not a regular column. Notice:
- The `PassengerId` column disappears from the data
- `df_by_passenger_id.loc[1]` returns the row where `PassengerId = 1`

```python
df_by_passenger_id.iloc[0]  # First row by position (still works)
df_by_passenger_id.loc[1]   # Row with PassengerId label = 1
```

**Why set a custom index?**
- Faster lookup by a meaningful key (e.g., `PassengerId`, `user_id`)
- Required for time-series analysis (DatetimeIndex)
- Used in joins/merges when joining on a non-default index

**SQL equivalent:**

```sql
-- Setting PassengerId as a primary key index for fast lookup
CREATE INDEX idx_passengerid ON titanic(PassengerId);
SELECT * FROM titanic WHERE PassengerId = 1;
```

---

### 7.2 `.reset_index()` [INTERMEDIATE]

```python
df_by_passenger_id = df_by_passenger_id.reset_index()
df_by_passenger_id.head(1)
```

**Output:**

```
   PassengerId  Survived  Pclass                     Name  ...
0            1         0       3  Braund, Mr. Owen Harris  ...
```

`reset_index()` moves the current index back to a regular column and resets to `RangeIndex`. The original `df` is unchanged — `set_index()` and `reset_index()` return **new DataFrames** by default (no `inplace` modification in Pandas 2.x best practice).

---

## Dataset Resources Used in Notebook

The notebook references several public datasets:

```python
# Titanic dataset (GitHub mirror)
titanic_url = "https://raw.githubusercontent.com/datasciencedojo/datasets/refs/heads/master/titanic.csv"

# Iris dataset (seaborn data)
iris_url = "https://raw.githubusercontent.com/mwaskom/seaborn-data/refs/heads/master/iris.csv"

# Kaggle (requires login for direct API access)
kaggle_link = "https://www.kaggle.com/datasets/yasserh/titanic-dataset/data"
```

---

## Assignment (from Notebook)

> **Assignment 1:** Download a CSV file using pure Python (no Pandas/NumPy).  
> - Create a `class` with a `download(URL, path_to_download="data")` method  
> - Use only standard libraries like `requests`  
> - Target URL: `https://raw.githubusercontent.com/datasciencedojo/datasets/refs/heads/master/titanic.csv`

**Starter code:**

```python
import requests
import os

class DatasetDownloader:
    def download(self, url: str, path_to_download: str = "data") -> str:
        os.makedirs(path_to_download, exist_ok=True)
        filename = url.split('/')[-1]
        filepath = os.path.join(path_to_download, filename)
        response = requests.get(url)
        response.raise_for_status()
        with open(filepath, 'w', encoding='utf-8') as f:
            f.write(response.text)
        return filepath

downloader = DatasetDownloader()
path = downloader.download(
    "https://raw.githubusercontent.com/datasciencedojo/datasets/refs/heads/master/titanic.csv"
)
print(f"Downloaded to: {path}")
```

---

## Before Pandas vs After Pandas

### Task: Find names and ages of female survivors over age 30

**Pure Python:**

```python
import csv

results = []
with open('titanic.csv', 'r') as f:
    reader = csv.DictReader(f)
    for row in reader:
        if row['Sex'] == 'female' and int(row['Survived']) == 1:
            age = float(row['Age']) if row['Age'] else None
            if age and age > 30:
                results.append({'Name': row['Name'], 'Age': age})

for r in results:
    print(r)
```

**Pandas:**

```python
df.loc[(df['Sex'] == 'female') & (df['Survived'] == 1) & (df['Age'] > 30), ['Name', 'Age']]
```

**Speedup:** Orders of magnitude faster on large datasets. Vectorized C-level operations vs Python-level loops.

---

## SettingWithCopyWarning / Copy-on-Write Guide

### What Is It?

In Pandas 1.x, chained indexing could silently fail to modify the original DataFrame:

```python
# UNSAFE in Pandas 1.x — may modify a copy, not the original
df[df['Age'] > 30]['Survived'] = 0  # SettingWithCopyWarning!
```

### Copy-on-Write in Pandas 2.x

Pandas 2.x introduced **Copy-on-Write (CoW)** as the default behavior. Any operation that modifies data creates a new copy — the original is never silently modified.

```python
# Safe pattern — always use .loc for conditional assignment
df.loc[df['Age'] > 30, 'Survived'] = 0  # ✅ Modifies original correctly
```

| Pattern | Pandas 1.x | Pandas 2.x (CoW) |
|---------|-----------|-----------------|
| `df[cond]['col'] = val` | May silently fail | Always creates copy (safe, but original unchanged) |
| `df.loc[cond, 'col'] = val` | Correct | Correct |

---

## Common Mistakes & Debugging

### Mistake 1: Wrong Bracket Syntax for Multi-Column Selection

```python
# WRONG
df['Name', 'Age']
# KeyError: ('Name', 'Age')
```

```python
# CORRECT
df[['Name', 'Age']]
```

**Why:** Single brackets with a tuple are interpreted as a single key lookup.

---

### Mistake 2: Missing Parentheses in Boolean Conditions

```python
# WRONG
df[df['Sex'] == 'female' & df['Survived'] == 1]
# TypeError: unsupported operand type(s) for &

# CORRECT
df[(df['Sex'] == 'female') & (df['Survived'] == 1)]
```

**Why:** `&` has higher operator precedence than `==`. Without parentheses, Python tries to evaluate `'female' & df['Survived']` first.

---

### Mistake 3: Using `and`/`or` Instead of `&`/`|`

```python
# WRONG
df[df['Sex'] == 'female' and df['Survived'] == 1]
# ValueError: The truth value of a Series is ambiguous

# CORRECT
df[(df['Sex'] == 'female') & (df['Survived'] == 1)]
```

**Why:** `and`/`or` work on single booleans. For element-wise operations on Series, use `&`/`|`.

---

### Mistake 4: `s2[0]` FutureWarning on Non-Integer Index

```python
s2 = pd.Series([10, 20, 30], index=['a', 'b', 'c'])
s2[0]  # FutureWarning in Pandas 2.x, KeyError in future

# CORRECT
s2.iloc[0]   # positional
s2.loc['a']  # label-based
```

---

### Mistake 5: `.loc` vs `.iloc` Slice Endpoint Confusion

```python
# .iloc: exclusive endpoint (Python-style)
df.iloc[0:3]   # rows at positions 0, 1, 2  (NOT 3)

# .loc: inclusive endpoint
df.loc[0:3]    # rows with labels 0, 1, 2, 3  (includes 3!)
```

---

## Edge Cases & Gotchas Catalog

| Edge Case | What Happens | Fix |
|-----------|-------------|-----|
| `df['col']` on missing column | `KeyError: 'col'` | Check `df.columns` first |
| `df[['col']]` vs `df['col']` | Former is DataFrame, latter is Series | Use `[[]]` when you need DataFrame shape |
| `loc` on integer index | `loc[1]` uses label, `iloc[1]` uses position — may differ after reset | Always be explicit |
| Boolean mask with `NaN` | `NaN > 70` → `False` (not included) | Use `.fillna()` or `.dropna()` before filtering |
| `set_index()` not inplace | Original `df` unchanged | Reassign: `df = df.set_index('col')` |
| Multiple conditions with `\|` | Lower precedence — always add `()` | `(cond1) \| (cond2)` |

---

## Axis Operations Reference

```
axis=0 → operates DOWN the rows (result has fewer rows)
axis=1 → operates ACROSS columns (result has fewer columns)

       Col_A  Col_B  Col_C
Row_0 [  10     20     30  ]  ← axis=1 operates across this
Row_1 [  40     50     60  ]
Row_2 [  70     80     90  ]
  ↑
axis=0 operates down this
```

**Mnemonic:** `axis=0` → **Down the rows** (collapses rows), `axis=1` → **Across the columns** (collapses columns)

---

## SQL ↔ Pandas Translation Guide

| SQL | Pandas |
|-----|--------|
| `SELECT * FROM titanic` | `df` |
| `SELECT Name, Age FROM titanic` | `df[['Name', 'Age']]` |
| `SELECT * FROM titanic LIMIT 5` | `df.head(5)` |
| `SELECT * FROM titanic WHERE Age > 70` | `df[df['Age'] > 70]` |
| `SELECT Name, Age FROM t WHERE Sex='female' AND Survived=1` | `df.loc[(df['Sex']=='female') & (df['Survived']==1), ['Name','Age']]` |
| `SELECT COUNT(*) FROM titanic` | `len(df)` or `df.shape[0]` |
| `DESCRIBE titanic` | `df.info()` |
| `SELECT ... FROM t ORDER BY PassengerId` | `df.sort_values('PassengerId')` |
| `ALTER TABLE t PRIMARY KEY (PassengerId)` | `df.set_index('PassengerId')` |
| `CREATE INDEX ON t(PassengerId)` | `df.set_index('PassengerId')` |

---

## Interoperability Guide

| From/To | Method | Notes |
|---------|--------|-------|
| List → Series | `pd.Series([1, 2, 3])` | Default RangeIndex |
| Dict → Series | `pd.Series({'a': 1})` | Keys become index |
| NumPy → Series | `pd.Series(arr)` | Zero-copy by default |
| Series → NumPy | `s.to_numpy()` or `s.values` | `.to_numpy()` preferred |
| Dict of lists → DataFrame | `pd.DataFrame({'a': [...], 'b': [...]})` | Most common constructor |
| List of dicts → DataFrame | `pd.DataFrame([{'a':1}, {'a':2}])` | JSON-style records |
| NumPy array → DataFrame | `pd.DataFrame(arr, columns=['A','B'])` | All cols same dtype |
| CSV → DataFrame | `pd.read_csv('file.csv')` | Most common IO |
| URL → DataFrame | `pd.read_csv(url)` | Direct HTTP fetch |
| DataFrame → NumPy | `df.to_numpy()` | All columns must be compatible |
| DataFrame → dict | `df.to_dict(orient='records')` | List of row dicts |
| DataFrame → CSV | `df.to_csv('file.csv', index=False)` | `index=False` usually preferred |

---

## Deprecation & Pandas 2.x Migration Notes

| Old (1.x) | New (2.x) | Notes |
|-----------|-----------|-------|
| `df.append(row)` | `pd.concat([df, new_row])` | `append()` **removed** in 2.0 |
| `df.applymap(func)` | `df.map(func)` | `applymap()` deprecated in 2.1+ |
| `s[0]` on non-int index | `s.iloc[0]` | `FutureWarning` → `KeyError` |
| Silent copy in indexing | Copy-on-Write default | Use `.loc` for assignment |
| `datetime64[ns]` always | Resolution-flexible | Can be `[s]`, `[ms]`, `[us]`, `[ns]` |
| Silent downcasting in `fillna` | Explicit `astype()` required | `FutureWarning` in 2.x |

---

## Best Practices & Idiomatic Pandas

- **Use `df.loc[row, col]`** instead of chained `df.loc[row]['col']` for assignment
- **Use `display()`** in Jupyter when showing multiple DataFrames in one cell
- **Prefer vectorized operations** over `.apply()` + lambda (10–100x faster)
- **Use `pd.read_csv(url)` directly** instead of downloading then reading
- **Always inspect with `.info()`** after loading a new dataset — see nulls and dtypes
- **Use `df[['col']]`** (double brackets) when you need a DataFrame, `df['col']` for a Series
- **Use `df.shape[0]`** for row count, `df.shape[1]` for column count
- **Always reset index** after filtering if you'll use positional logic later: `df.reset_index(drop=True)`

---

## When to Use / When NOT to Use

| Feature | Use When… | Avoid When… |
|---------|-----------|-------------|
| `df['col']` | Accessing a single column | Need a DataFrame with one column (use `df[['col']]`) |
| `.loc` | Labels are meaningful (names, IDs) | You need position-based speed of `.iloc` |
| `.iloc` | Position is what matters | Labels may shift (e.g., after filtering) |
| Boolean indexing | Filtering by condition | Working with very large datasets — consider `.query()` |
| `.set_index()` | Frequently looking up by a key | Your index is meaningless/numeric only |
| `pd.read_csv(url)` | Prototyping with public data | Production — download and cache locally |

---

## Big-O Complexity Annotations

| Operation | Complexity | Notes |
|-----------|-----------|-------|
| `df['col']` | O(1) | Dictionary-style column lookup |
| `df.iloc[i]` | O(n_cols) | One element per column |
| `df.loc[label]` | O(1) avg | Hash-based index lookup |
| Boolean filter | O(n_rows) | Vectorized comparison |
| `df.head(n)` | O(n) | Slice first n rows |
| `df.shape` | O(1) | Stored metadata |
| `df.info()` | O(n_rows × n_cols) | Scans for non-null counts |
| `df.describe()` | O(n_rows × n_numeric_cols) | Statistical computations |
| `df.set_index()` | O(n) | Build hash map |
| `df.reset_index()` | O(n) | Rebuild RangeIndex |

---

## Interview & Exam Corner

### Frequently Asked Questions

**Q1: What is the difference between `loc` and `iloc`?**  
`loc` is **label-based** — you access rows/columns by their names. `iloc` is **integer-position-based** — you access by numeric position (like list indexing). `loc` slice endpoints are **inclusive**; `iloc` endpoints are **exclusive** (Python-style).

**Q2: What does `df['col']` return vs `df[['col']]`?**  
`df['col']` returns a **Series** (1D). `df[['col']]` returns a **DataFrame** (2D, same data). The difference matters when you need a DataFrame shape for downstream operations.

**Q3: When would `loc[1]` and `iloc[1]` return different results?**  
When the index has been customized. Example: after `df = df.set_index('PassengerId')`, `df.loc[1]` returns the row where `PassengerId = 1`, while `df.iloc[1]` returns the second row regardless of its PassengerId.

**Q4: What is a boolean mask in Pandas?**  
A Series of `True`/`False` values, one per row, created by applying a condition to a column. When used inside `df[mask]`, only rows where the mask is `True` are returned.

**Q5: Why must you use `&` instead of `and` for multiple conditions?**  
`and`/`or` are Python logical operators for single booleans. A Pandas boolean Series contains many values, making truthiness ambiguous. `&`/`|` are element-wise bitwise operators that work correctly on arrays.

**Q6: What is `SettingWithCopyWarning`?**  
A warning that the assignment you made might have been applied to a copy of the DataFrame, not the original. It's triggered by chained indexing (`df[cond]['col'] = val`). Fix: use `df.loc[cond, 'col'] = val`.

**Q7: What does `.info()` tell you that `.describe()` doesn't?**  
`.info()` shows column **dtypes**, **non-null counts** (revealing missing data), **memory usage**, and includes **all columns** (even strings). `.describe()` shows **statistics** (mean, std, min, max, percentiles) but only for **numeric columns** by default.

**Q8: What is Copy-on-Write in Pandas 2.x?**  
CoW means that any operation that modifies a DataFrame creates a new copy — the original is never silently mutated. This eliminates the `SettingWithCopyWarning` class of bugs but requires explicit `.loc`-based assignment to modify data.

---

### Tricky Output Questions

**Q1: What does this return?**

```python
s = pd.Series([10, 20, 30], index=['a', 'b', 'c'])
print(s[['a', 'c']])
```

**Answer:**

```
a    10
c    30
dtype: int64
```

A Series with two elements — label-based selection with a list.

---

**Q2: What is the shape of the result?**

```python
df = pd.DataFrame({'A': [1, 2, 3], 'B': [4, 5, 6]})
result = df[['A']]
print(result.shape)
```

**Answer:** `(3, 1)` — double brackets return a DataFrame, not a Series.

---

**Q3: What does `df.loc[0:2]` return vs `df.iloc[0:2]`?**

**Answer:**  
- `df.loc[0:2]` → rows with labels 0, 1, and **2** (3 rows — inclusive)  
- `df.iloc[0:2]` → rows at positions 0 and **1** (2 rows — exclusive)

---

### One-Liner Challenges

**Challenge 1:** Get the names of passengers older than 70 as a list.  
**Solution:** `df[df['Age'] > 70]['Name'].tolist()`

**Challenge 2:** How many female survivors are there?  
**Solution:** `len(df[(df['Sex'] == 'female') & (df['Survived'] == 1)])`

**Challenge 3:** Get the first passenger's full row as a dict.  
**Solution:** `df.iloc[0].to_dict()`

---

### Explain the Difference

**`loc` vs `iloc`:**  
`loc` uses **labels** (row/column names); `iloc` uses **integer positions**. `loc` slice is inclusive; `iloc` slice is exclusive. When the index is default `RangeIndex`, they coincide — but diverge once the index is customized.

**`df['col']` vs `df.loc[:, 'col']`:**  
Both return the same Series. `df['col']` is shorthand. `df.loc[:, 'col']` is explicit and safe for assignment in all Pandas versions. The explicit form is preferred when combining with row selection.

---

## Practice Problems

### Problem 1 [BEGINNER]

**Task:** Load the Titanic CSV and display the `Name`, `Sex`, and `Survived` columns for the first 10 rows.

**Starter code:**

```python
import pandas as pd
df = pd.read_csv('titanic.csv')
# Your code here
```

**Expected output:** A DataFrame with 10 rows and 3 columns: `Name`, `Sex`, `Survived`.

**Solution:**

```python
df[['Name', 'Sex', 'Survived']].head(10)
```

---

### Problem 2 [INTERMEDIATE]

**Task:** Find the average age of passengers who survived vs those who didn't.

**Starter code:**

```python
import pandas as pd
df = pd.read_csv('titanic.csv')
# Your code here
```

**Solution:**

```python
df.groupby('Survived')['Age'].mean()
```

---

### Problem 3 [ADVANCED / Real-World]

**Task:** Using only `.loc`, select the `Name` and `Fare` of male passengers in first class (`Pclass == 1`) who did NOT survive, with a fare above 100.

**Solution:**

```python
mask = (df['Sex'] == 'male') & (df['Pclass'] == 1) & (df['Survived'] == 0) & (df['Fare'] > 100)
df.loc[mask, ['Name', 'Fare']]
```

---

## Visualization Quick-Reference

```python
import matplotlib.pyplot as plt

# Age distribution
df['Age'].plot(kind='hist', bins=30, title='Age Distribution')
plt.show()

# Survival count
df['Survived'].value_counts().plot(kind='bar')
plt.show()

# Age by survival (box plot)
df.boxplot(column='Age', by='Survived')
plt.show()

# Using seaborn
import seaborn as sns
sns.histplot(df['Age'].dropna(), kde=True)
```

---

## Mnemonics & Memory Aids

| Concept | Mnemonic |
|---------|----------|
| `loc` vs `iloc` | **L**oc = **L**abel, **i**loc = **i**nteger position |
| `loc` slice endpoint | **loc** is **loc**lusive (inclusive) |
| `iloc` slice endpoint | `iloc` follows Python slice (exclusive end) |
| `&` vs `and` | **&** = element-wise, **and** = single boolean |
| `axis=0` | **0** = down the rows (like the 0th dimension) |
| `axis=1` | **1** = across columns (1 → sideways) |
| Double brackets | `df[['col']]` = **DataFrame**, `df['col']` = **Series** |

---

## Quick-Reference Cheat Sheet

```python
# IMPORT
import pandas as pd
import numpy as np

# SERIES
pd.Series([1, 2, 3])                                 # from list
pd.Series([1,2,3], index=['a','b','c'], name='X')    # custom index
pd.Series({'a': 1, 'b': 2})                          # from dict
pd.Series(np.array([1,2,3]), index=['x','y','z'])    # from array

# SERIES ATTRIBUTES
s.index    s.values    s.dtype    s.name    s.shape

# SERIES INDEXING
s.iloc[0]          # position
s.loc['label']     # label
s[['a', 'b']]      # multi-label

# DATAFRAME CREATION
pd.DataFrame({'A': [1,2], 'B': [3,4]})            # dict of lists
pd.DataFrame([{'A':1,'B':2}, {'A':3,'B':4}])      # list of dicts
pd.DataFrame(np.array([[1,2],[3,4]]), columns=['A','B'])  # numpy

# READ CSV
pd.read_csv('file.csv')
pd.read_csv('https://...')

# INSPECTION
df.head()         df.tail()        df.head(10)
df.shape          df.columns       df.dtypes
df.info()         df.describe()    dir(df)

# COLUMN SELECTION
df['col']                        # Series
df[['col1', 'col2']]             # DataFrame

# ROW SELECTION
df.iloc[0]                       # by position
df.loc[0]                        # by label
df.loc[0, 'col']                 # cell by label
df.iloc[0, 3]                    # cell by position

# SLICING
df.loc[0:4, ['Name', 'Age']]     # inclusive row slice
df.iloc[0:3, 0:2]                # exclusive row/col slice
df.iloc[0:3, [3, 1]]             # non-adjacent columns by position

# BOOLEAN INDEXING
df[df['Age'] > 70]
df[(df['Sex'] == 'female') & (df['Survived'] == 1)]
df.loc[(df['Sex'] == 'female') & (df['Survived'] == 1), ['Name', 'Age']]

# INDEX MANAGEMENT
df.set_index('PassengerId')      # set column as index
df.reset_index()                 # reset to RangeIndex
```

---

## Summary (TL;DR)

- **Pandas** is Python's go-to library for tabular data — think Excel + SQL + NumPy in one
- **Series** = 1D labeled array; **DataFrame** = 2D table of Series sharing an index
- **Create** DataFrames from dicts, lists of dicts, NumPy arrays, or CSV files
- **Inspect** with `.head()`, `.tail()`, `.shape`, `.info()`, `.describe()`, `.columns`
- **Select columns** with `df['col']` (Series) or `df[['col1','col2']]` (DataFrame)
- **Select rows** with `.iloc[pos]` (position) or `.loc[label]` (label); **always prefer `df.loc[row, col]`** over chained indexing
- **Filter rows** with boolean masks: `df[df['col'] > val]`; combine with `&`/`|` (not `and`/`or`)
- **`loc` slice is inclusive; `iloc` slice is exclusive** — this is the #1 source of off-by-one errors
- **Pandas 2.x Copy-on-Write** means using `.loc` for assignments is not just good practice — it's required for correctness

---

## Further Reading & Official Docs

| Resource | Link |
|----------|------|
| Pandas Official Docs | https://pandas.pydata.org/docs/ |
| `pd.Series` API | https://pandas.pydata.org/docs/reference/api/pandas.Series.html |
| `pd.DataFrame` API | https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html |
| `pd.read_csv` API | https://pandas.pydata.org/docs/reference/api/pandas.read_csv.html |
| Pandas 2.0 Migration Guide | https://pandas.pydata.org/docs/whatsnew/v2.0.0.html |
| Copy-on-Write Guide | https://pandas.pydata.org/docs/user_guide/copy_on_write.html |
| Indexing User Guide | https://pandas.pydata.org/docs/user_guide/indexing.html |
| 10 Minutes to Pandas | https://pandas.pydata.org/docs/user_guide/10min.html |

---

## Flashcard-Style Recall

| Q | A |
|---|---|
| What does `pd.Series({'a': 1, 'b': 2})` use as the index? | Dict keys: `['a', 'b']` |
| What does `s.values` return? | NumPy ndarray |
| `df['col']` vs `df[['col']]`? | Series vs DataFrame |
| `.loc` slice endpoint? | **Inclusive** |
| `.iloc` slice endpoint? | **Exclusive** |
| How to combine boolean conditions? | `&` and `\|` with parentheses around each |
| How to select a safe single cell? | `df.loc[row, 'col']` or `df.iloc[r, c]` |
| What does `.shape` return? | `(n_rows, n_cols)` tuple |
| `df.info()` vs `df.describe()`? | Info = dtypes/nulls/memory; describe = stats |
| What does `set_index('col')` do? | Makes that column the row label (index) |
| How to undo `set_index`? | `.reset_index()` |
| How to view first N rows? | `.head(N)` |
| What is a boolean mask? | A Series of True/False values for filtering |
| Why `&` not `and`? | `and` is for single booleans; `&` is element-wise |
| `FutureWarning` on `s[0]`? | Use `s.iloc[0]` for position-based access |
