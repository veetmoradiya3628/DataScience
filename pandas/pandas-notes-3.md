# Pandas — String Operations, Regex, Apply, Categorical Data & DateTime

> **Source Notebook:** `28-02-2026.ipynb` | **Pandas Version:** 2.x | **Date:** 28 Feb 2026

---

## Prerequisites

- Python fundamentals (strings, slicing, functions, lambda, loops, `re` module basics)
- Pandas Series and DataFrame basics (see `22-02-2026-notes.md`)
- `pd.read_csv()`, `.head()`, `.loc[]`, `.apply()` (see prior notes)
- Understanding of NaN / missing data concepts
- Basic regex concepts are a plus (covered from scratch in Section 3)

---

## Topics Covered in This Notebook

| # | Topic | Key Methods / Concepts |
|---|-------|------------------------|
| 1 | String Operations — `.str` accessor | `.str.len()`, `.str.replace()`, `.str.split()`, `.str.strip()`, `.str.lstrip()`, `.str.rstrip()`, `.str.find()`, `.str.contains()`, `.str.pad()` |
| 2 | `apply()` with Custom Functions | `df['col'].apply(func)` — building `remove_substring` |
| 3 | Regex in Pandas + Python `re` module | `str.replace(regex=True)`, `re.fullmatch()`, raw strings |
| 4 | Categorical Data | `pd.Categorical()`, `.astype('category')`, `.cat.categories`, `.cat.codes`, ordered comparisons |
| 5 | DateTime Functionality | `pd.to_datetime()`, `.dt` accessor, `pd.date_range()`, `pd.Timedelta`, `pd.Timestamp`, `.between()` |

---

## Environment Setup

```python
import pandas as pd
import re

# Titanic dataset (local copy used throughout notebook)
df = pd.read_csv("titanic.csv")

# Alternative: load from GitHub URL
titanic_url = "https://raw.githubusercontent.com/datasciencedojo/datasets/refs/heads/master/titanic.csv"
df = pd.read_csv(titanic_url)
```

---

---

# Section 1 — String Operations via `.str` Accessor [BEGINNER → INTERMEDIATE]

## Concept Overview

The `.str` accessor is a **vectorized string interface** on a Pandas Series. Instead of writing a Python `for` loop to process each string, you call methods directly on the Series — Pandas applies them to every element internally, using a fast C-level loop.

**Real-world analogy:** Think of `.str` like Excel's text functions (LEN, REPLACE, FIND, TRIM…) — but applied to an entire column at once, not one cell at a time.

**Why it exists:** Pandas stores text columns as `object` or `string` dtype. Without `.str`, you'd need `.apply(lambda x: x.lower())` for every operation — slower and more verbose.

**Where it fits:** Used extensively in data cleaning, ETL pipelines, and feature engineering — anywhere you need to transform or inspect text data before modelling or reporting.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| `.str` accessor | A special namespace on a text Series that exposes string methods as vectorized operations |
| Vectorized | Applied element-by-element using internal C loops — no Python `for` loop needed |
| `NaN` propagation | String methods return `NaN` for any `NaN` input element — they don't crash |
| `regex=True/False` | Controls whether patterns in `.str.replace()` / `.str.contains()` are treated as regular expressions |
| `object` dtype | Legacy Pandas text dtype (stores Python `str` objects); still the default for most DataFrames |
| `string` dtype | Pandas 1.0+ nullable string type — use with `.astype('string')` for explicit handling |

---

## Quick Inspection: df.head()

```python
df = pd.read_csv("titanic.csv")
df.head()
```

**Output:**

```
   PassengerId  Survived  Pclass                                                 Name     Sex   Age  SibSp  Parch            Ticket     Fare Cabin Embarked
0            1         0       3                              Braund, Mr. Owen Harris    male  22.0      1      0         A/5 21171   7.2500   NaN        S
1            2         1       1  Cumings, Mrs. John Bradley (Florence Briggs Thayer)  female  38.0      1      0          PC 17599  71.2833   C85        C
2            3         1       3                               Heikkinen, Miss. Laina  female  26.0      0      0  STON/O2. 3101282   7.9250   NaN        S
3            4         1       1         Futrelle, Mrs. Jacques Heath (Lily May Peel)  female  35.0      1      0            113803  53.1000  C123        S
4            5         0       3                             Allen, Mr. William Henry    male  35.0      0      0            373450   8.0500   NaN        S
```

**Note:** The `Name` column follows the pattern `"LastName, Title. FirstName (MaidenName/AlternativeName)"`. Notice some names contain parenthetical content — this becomes the basis for several exercises in this notebook.

---

## 1.1 — `str.len()` — String Length [BEGINNER]

### What It Does

Returns the character count of each string as an integer Series.

### Syntax

```python
series.str.len()
```

### Example

```python
df['Name'].str.len()
```

**Output (first 5 rows):**

```
0    23
1    51
2    22
3    44
4    24
dtype: int64
```

**Line-by-line breakdown:**
- `df['Name']` — selects the `Name` column as a Series (dtype: `object`/`str`)
- `.str.len()` — computes the number of characters in each name string

**Why this matters:** Name length is itself a feature in NLP and exploratory analysis. More practically, you use `.str.len()` to verify max/min string sizes before inserting into a database column with a character limit.

**SQL equivalent:**
```sql
SELECT LEN(Name) FROM passengers;
```

**Performance:** O(n) — iterates through n string values. Faster than `.apply(lambda x: len(x))` for large Series because `.str.len()` avoids per-element Python dispatch overhead.

---

## 1.2 — `str.replace()` — Replace Substrings [BEGINNER]

### What It Does

Replaces occurrences of a pattern (literal string or regex) within each element of a string Series.

### Syntax

```python
series.str.replace(
    pat,            # string or compiled regex pattern to find
    repl,           # replacement string
    n=-1,           # max number of replacements per string (-1 = all)
    case=None,      # case-sensitive? (default True for strings)
    flags=0,        # regex flags (e.g., re.IGNORECASE)
    regex=True      # True: treat pat as regex; False: treat as literal string
)
```

> ⚠️ **Pandas 2.x note:** `regex=True` is the default. Always set `regex=False` when replacing literal strings that contain regex special characters (`.`, `(`, `)`, `*`, `+`, etc.) to avoid unexpected behaviour.

### Example 1: Literal String Replace [BEGINNER]

```python
df['Name'].str.replace("Mr.", "Sir", regex=False).head()
```

**Output:**

```
0                              Braund, Sir Owen Harris
1    Cumings, Mrs. John Bradley (Florence Briggs Th...
2                               Heikkinen, Miss. Laina
3         Futrelle, Mrs. Jacques Heath (Lily May Peel)
4                             Allen, Sir William Henry
Name: Name, dtype: object
```

**Why `regex=False` here?** The pattern `"Mr."` contains `.` which in regex means "any character". With `regex=True` (default), `"Mr."` would match `"Mr "`, `"MrX"`, `"Mr!"` etc. Setting `regex=False` treats it as a plain string — it only replaces the exact characters `M`, `r`, `.`.

**Gotcha:** Row 1 (`Cumings, Mrs.`) was **not** replaced — `"Mr."` and `"Mrs."` are different strings. This is correct behaviour.

---

## 1.3 — `str.split()` — Split Strings [BEGINNER]

### What It Does

Splits each string in a Series by a delimiter, returning a Series of lists.

### Syntax

```python
series.str.split(
    pat=None,       # delimiter (None = split on any whitespace)
    n=-1,           # max splits (-1 = all)
    expand=False    # True: return DataFrame instead of Series of lists
)
```

### Example 1: Direct Element Access [BEGINNER]

```python
df['Name'][0]
```

**Output:**
```
'Braund, Mr. Owen Harris'
```

This is plain Python index access (not vectorized). Returns a single string.

### Example 2: Vectorized Split + Element Access [INTERMEDIATE]

```python
df['Name'].str.split(",")[0]
```

**Output:**
```
['Braund', ' Mr. Owen Harris']
```

**Line-by-line:**
- `.str.split(",")` — splits every name at the comma, returning a Series of lists
- `[0]` — accesses the **first list** in the Series (row 0). The result is a Python list: `['Braund', ' Mr. Owen Harris']`

> **Key insight:** `.str.split()[0]` gives you the **first row's list**, NOT the first element of every name. To get the first token of every name, chain `.str[0]` or `.str[1]` on the split result:

```python
df['Name'].str.split().str[0]
```

**Output (first 5):**
```
0       Braund,
1      Cumings,
2    Heikkinen,
3     Futrelle,
4        Allen,
Name: Name, dtype: object
```

Here `.str.split()` (no arg) splits on whitespace. `.str[0]` accesses the first token of each resulting list — vectorized, applied to every row.

### Example 3: Print First Token of First Row [BEGINNER]

```python
print(df['Name'].str.split()[0][0])
```

**Output:**
```
Braund,
```

**Explanation:** `.str.split()[0]` → list for row 0 → `['Braund,', 'Mr.', 'Owen', 'Harris']`. `[0]` → `'Braund,'`. The trailing comma is included because we split on whitespace, not on comma.

**SQL equivalent for split:**
```sql
-- SQL Server
SELECT LEFT(Name, CHARINDEX(',', Name) - 1) AS LastName FROM passengers;
```

---

## 1.4 — Strip Whitespace: `str.strip()`, `str.lstrip()`, `str.rstrip()` [BEGINNER]

### What They Do

Remove leading/trailing whitespace (or specific characters) from each string.

| Method | Removes whitespace from |
|--------|------------------------|
| `.str.strip()` | Both ends |
| `.str.lstrip()` | Left (leading) only |
| `.str.rstrip()` | Right (trailing) only |

### Syntax

```python
series.str.strip(to_strip=None)   # None = strip whitespace
series.str.lstrip(to_strip=None)
series.str.rstrip(to_strip=None)
```

### Example [BEGINNER]

```python
df['Name'].str.strip()     # removes leading + trailing spaces
df['Name'].str.lstrip()    # removes leading spaces only
df['Name'].str.rstrip()    # removes trailing spaces only
```

**Output (first 3 rows — same for all three since Name column has no extra spaces):**

```
0                              Braund, Mr. Owen Harris
1    Cumings, Mrs. John Bradley (Florence Briggs Th...
2                               Heikkinen, Miss. Laina
Name: Name, dtype: object
```

**Why use this?** Real-world CSV data often has invisible leading/trailing spaces — e.g., `" London"` instead of `"London"`. Comparisons, joins, and lookups will fail silently without stripping. **Always `.str.strip()` text columns after ingestion.**

**SQL equivalent:**
```sql
SELECT TRIM(Name) FROM passengers;       -- strip both ends
SELECT LTRIM(Name) FROM passengers;     -- left only
SELECT RTRIM(Name) FROM passengers;     -- right only
```

**To strip a specific character (not whitespace):**
```python
df['Name'].str.strip(',')   # removes commas from both ends
```

---

## 1.5 — `str.find()` — Find Substring Position [BEGINNER]

### What It Does

Returns the **index position** of the first occurrence of a substring in each string. Returns `-1` if not found.

### Syntax

```python
series.str.find(sub, start=0, end=None)
```

### Example [BEGINNER]

```python
df['Name'].str.find("Mr")
```

**Output (first 5):**

```
0     8
1     9
2    -1
3    10
4     7
dtype: int64
```

**Explanation:**
- Row 0: `"Braund, Mr. Owen Harris"` — `"Mr"` starts at index **8**
- Row 1: `"Cumings, Mrs. John Bradley..."` — `"Mr"` starts at index **9** (inside `"Mrs."`)
- Row 2: `"Heikkinen, Miss. Laina"` — `"Mr"` is **not found** → `-1`
- Row 3: `"Futrelle, Mrs. Jacques Heath..."` — `"Mr"` at index **10**
- Row 4: `"Allen, Mr. William Henry"` — `"Mr"` at index **7**

**Practical use:** `.str.find()` is used to locate substrings before slicing. It's the vectorized equivalent of Python's `str.find()`. When the result is `-1`, the substring is absent.

> **Important gotcha:** If you use `.str.find()` positions for slicing on rows where the pattern doesn't exist (returns -1), you'll get incorrect/unexpected slices. Always guard with a conditional check. See Section 2 for a real example of this bug.

---

## 1.6 — `str.contains()` — Check for Substring [BEGINNER]

### What It Does

Returns a boolean Series — `True` where the string contains the pattern.

### Syntax

```python
series.str.contains(
    pat,            # string or regex pattern
    case=True,      # case-sensitive (default True)
    flags=0,        # regex flags
    na=None,        # value to return for NaN elements
    regex=True      # treat pat as regex (default True)
)
```

### Example [BEGINNER]

```python
count = df['Name'].str.contains("Miss").sum()
print("Number of 'Miss':", count)
```

**Output:**
```
Number of 'Miss': 182
```

**Line-by-line:**
- `.str.contains("Miss")` — returns a boolean Series: `True` where `"Miss"` appears, `False` otherwise
- `.sum()` — counts `True` values (Python/NumPy treats `True` as 1, `False` as 0)

**Pattern:** `contains → sum` is the idiomatic way to count matching rows in Pandas.

**SQL equivalent:**
```sql
SELECT COUNT(*) FROM passengers WHERE Name LIKE '%Miss%';
```

**Case-insensitive search:**
```python
df['Name'].str.contains("miss", case=False)
```

**Boolean filtering with `contains`:**
```python
miss_passengers = df[df['Name'].str.contains("Miss")]
```

---

## 1.7 — `str.pad()` — Pad / Align Strings [BEGINNER]

### What It Does

Pads each string to a fixed width by inserting fill characters on the left, right, or both sides.

### Syntax

```python
series.str.pad(
    width,          # target total width
    side='left',    # 'left', 'right', or 'both'
    fillchar=' '    # character to fill with (default space)
)
```

### Example [BEGINNER]

(Uses the smaller `Name/Email` DataFrame created later in the notebook)

```python
data = {
    'Name': ['John Doe', 'Jane Smith', 'Emily Johnson', 'Robert Brown', 'Anna-Marie Lee'],
    'Email': ['john@example.com', 'jane.smith@work.org', 'emily_j@data.net', 'robertb@yahoo.com', 'anna.lee@company.co']
}
df_new = pd.DataFrame(data)

df_new['Name'].str.pad(width=20, side='left', fillchar='-')
```

**Output:**

```
0    ------------John Doe
1    ----------Jane Smith
2    -------Emily Johnson
3    --------Robert Brown
4    ------Anna-Marie Lee
dtype: object
```

**Why use this?** Useful for generating fixed-width text reports, aligning console output, or creating padded record IDs. The equivalent of Python's `str.ljust()`, `str.rjust()`, `str.center()`.

**Equivalent Python built-ins (non-vectorized):**
```python
"John Doe".rjust(20, '-')   # ------------John Doe
```

**Right-pad (align left, add dashes to right):**
```python
df_new['Name'].str.pad(width=20, side='right', fillchar='-')
```

---

## Full `.str` Accessor Reference Table

| Method | What It Does | Returns | Example |
|--------|-------------|---------|---------|
| `.str.len()` | Character count | int Series | `df['Name'].str.len()` |
| `.str.lower()` | Lowercase all | str Series | `df['Name'].str.lower()` |
| `.str.upper()` | Uppercase all | str Series | `df['Name'].str.upper()` |
| `.str.capitalize()` | First letter uppercase | str Series | `df['Name'].str.capitalize()` |
| `.str.strip()` | Remove leading+trailing whitespace | str Series | `df['col'].str.strip()` |
| `.str.lstrip()` | Remove leading whitespace | str Series | `df['col'].str.lstrip()` |
| `.str.rstrip()` | Remove trailing whitespace | str Series | `df['col'].str.rstrip()` |
| `.str.replace(pat, repl)` | Replace substring/pattern | str Series | `df['col'].str.replace("a", "b")` |
| `.str.split(pat)` | Split into list | Series of lists | `df['col'].str.split(",")` |
| `.str.find(sub)` | Position of first match (−1 if not found) | int Series | `df['col'].str.find("Mr")` |
| `.str.contains(pat)` | Boolean: is pattern present? | bool Series | `df['col'].str.contains("Miss")` |
| `.str.startswith(pat)` | Starts with pattern? | bool Series | `df['col'].str.startswith("Mr")` |
| `.str.endswith(pat)` | Ends with pattern? | bool Series | `df['col'].str.endswith("Jr")` |
| `.str.pad(width)` | Pad to fixed width | str Series | `df['col'].str.pad(20, fillchar='-')` |
| `.str.cat(sep)` | Concatenate strings | str | `df['col'].str.cat(sep=', ')` |
| `.str.extract(pat)` | Extract regex groups | DataFrame | `df['col'].str.extract(r'(\w+)')` |
| `.str.count(pat)` | Count pattern occurrences | int Series | `df['col'].str.count("l")` |
| `.str.slice(start, stop)` | Slice each string | str Series | `df['col'].str.slice(0, 5)` |
| `.str.get(i)` | Get character at index i | str Series | `df['col'].str.get(0)` |

---

---

# Section 2 — `apply()` with Custom Functions [INTERMEDIATE]

## Concept Overview

`Series.apply(func)` applies a **Python function element-by-element** across a Series. It's used when no built-in vectorized `.str` method can do what you need — e.g., multi-step string manipulation that requires custom logic.

**Real-world analogy:** `.apply()` is like writing a recipe for a single plate, then having Pandas cook that recipe for every row in the table, one at a time.

**Performance note:** `.apply()` with a Python function is significantly slower than vectorized `.str` methods because it involves per-element Python function calls. Only use it when no vectorized alternative exists.

---

## Building `remove_substring()` Step-by-Step

The instructor builds the function incrementally — first testing the logic on a single row, then wrapping it in a function, then applying it to the full column.

### Step 1: Access a Single Name Value [BEGINNER]

```python
df.loc[1]['Name']
# or equivalently:
df.loc[1, 'Name']
```

**Output (both):**
```
'Cumings, Mrs. John Bradley (Florence Briggs Thayer)'
```

**Difference between `df.loc[1]['Name']` and `df.loc[1, 'Name']`:**
- `df.loc[1]['Name']` — first selects row 1 as a **Series**, then accesses key `'Name'` (chained indexing — triggers a potential `SettingWithCopyWarning` on assignment)
- `df.loc[1, 'Name']` — single `.loc` call that directly retrieves the scalar — **preferred** form

### Step 2: Find Parenthesis Positions [BEGINNER]

```python
start = df.loc[1, 'Name'].find("(")   # → 27
end   = df.loc[1, 'Name'].find(")")   # → 50
```

**Output:** `start = 27`, `end = 50`

Standard Python `str.find()` (not vectorized — applied to a single scalar).

### Step 3: Slice to Remove Parenthetical Content [INTERMEDIATE]

```python
df.loc[1, 'Name'][0:start] + df.loc[1, 'Name'][end+1:]
```

**Output:**
```
'Cumings, Mrs. John Bradley '
```

**How it works:**
- `[0:start]` — everything before the opening `(`
- `[end+1:]` — everything after the closing `)`
- Concatenating the two parts removes the `(Florence Briggs Thayer)` portion

---

### Step 4: Wrap in a Reusable Function [INTERMEDIATE]

```python
def remove_substring(name):
    start = name.find("(")
    end = name.find(")")
    new_name = name[0:start] + name[end+1:]
    new_name = new_name.strip()
    return new_name
```

**Line-by-line:**
- `name.find("(")` — returns position of `(` in this specific name, or -1 if absent
- `name.find(")")` — returns position of `)` in this specific name, or -1 if absent
- `name[0:start] + name[end+1:]` — slices out the parenthetical content
- `.strip()` — removes any trailing space left by the removal

### Step 5: Apply to Entire Column [INTERMEDIATE]

```python
df["Name_trimmed"] = df["Name"].apply(remove_substring)
df["Name_trimmed"].head()
```

**Output:**

```
0      Braund, Mr. Owen HarriBraund, Mr. Owen Harris
1                         Cumings, Mrs. John Bradley
2        Heikkinen, Miss. LainHeikkinen, Miss. Laina
3                       Futrelle, Mrs. Jacques Heath
4    Allen, Mr. William HenrAllen, Mr. William Henry
Name: Name_trimmed, dtype: object
```

> ⚠️ **BUG ALERT — This is a critical teaching moment:**  
> Rows 0, 2, and 4 produce **corrupted output**! `"Braund, Mr. Owen HarriBraund, Mr. Owen Harris"` — the name is duplicated and truncated.
>
> **Why?** Python's `str.find()` returns **-1** when the substring is not found. When `start = -1`:
> - `name[0:-1]` → removes the **last character** of the string (not an empty slice!)
> - `name[(-1)+1:]` = `name[0:]` → the entire string again
> - Result: a mangled concatenation of two versions of the name
>
> **Fix with guard:**
> ```python
> def remove_substring_safe(name):
>     start = name.find("(")
>     end = name.find(")")
>     if start == -1:          # no parentheses found — return original
>         return name.strip()
>     new_name = name[0:start] + name[end+1:]
>     return new_name.strip()
> ```
>
> **Better fix — use regex (Section 3):** The entire `remove_substring` approach is replaced by a single vectorized regex call, which handles all edge cases automatically.

### Verification on the Fixed Row [BEGINNER]

```python
df.loc[1, 'Name']          # original: 'Cumings, Mrs. John Bradley (Florence Briggs Thayer)'
df.loc[1, 'Name_trimmed']  # trimmed:  'Cumings, Mrs. John Bradley'
```

---

## When to Use `.apply()` vs Vectorized `.str` Methods

| Criterion | Use `.apply()` | Use `.str` methods |
|-----------|---------------|-------------------|
| Operation type | Complex multi-step custom logic | Simple single-operation string transforms |
| Performance | Slower (Python loop per element) | Faster (C-level loop) |
| NaN handling | Depends on your function | Auto-propagates NaN |
| Readability | Explicit and debuggable | Concise and idiomatic |
| Example | `remove_substring`, ML feature extraction | `.str.lower()`, `.str.contains()` |

---

---

# Section 3 — Regex in Pandas + Python `re` Module [INTERMEDIATE]

## Concept Overview

A **regular expression** (regex) is a pattern language for matching, searching, and replacing text. Pandas exposes regex through the `.str` accessor. Python has a built-in `re` module for standalone regex operations.

**Real-world analogy:** Regex is like a search warrant with flexible criteria — instead of searching for an exact address, you describe a _pattern_ of addresses (e.g., "any street number between 100 and 999 on Oak Street"). The regex engine finds everything that fits the description.

---

## Regex Basics Needed for This Notebook

| Pattern | Meaning |
|---------|---------|
| `.` | Any single character |
| `*` | Zero or more of the preceding |
| `+` | One or more of the preceding |
| `\(` | Literal `(` (escaped because `(` has special meaning in regex) |
| `\)` | Literal `)` |
| `.*` | Any sequence of characters (any length, including empty) |
| `^` | Start of string |
| `$` | End of string |
| `[a-z]` | Any lowercase letter a–z |
| `{2,}` | 2 or more of the preceding |

---

## 3.1 — Regex Replace in Pandas [INTERMEDIATE]

### The Problem (from Section 2 bug)

The manual `remove_substring()` function fails on names without parentheses. The regex approach handles this elegantly in a single vectorized call:

```python
# r"\(.*\)" means: match a literal ( then any characters then literal )
df['Name'].str.replace(r"\(.*\)", "", regex=True).head()
```

**Output:**

```
0          Braund, Mr. Owen Harris
1      Cumings, Mrs. John Bradley 
2           Heikkinen, Miss. Laina
3    Futrelle, Mrs. Jacques Heath 
4         Allen, Mr. William Henry
Name: Name, dtype: object
```

**This is correct!** Rows 0, 2, 4 (no parentheses) are returned unchanged. Rows 1, 3 have the parenthetical content cleanly removed.

### Regex Pattern Breakdown (from notebook markdown):

| Part | Meaning |
|------|---------|
| `r` | Raw string literal — tells Python **not to interpret** `\` as escape (so `\(` reaches the regex engine as `\(`, not as a special char) |
| `\(` | Escaped `(` — matches a literal opening parenthesis |
| `.*` | `.` matches **any character**, `*` means **zero or more** |
| `\)` | Escaped `)` — matches a literal closing parenthesis |

**Why raw strings?** In a normal Python string, `\n` means newline. In a raw string (`r"..."`) `\n` is literally backslash + n. Since regex patterns use `\` for escaping, always write regex patterns as raw strings to avoid confusion.

**Windows path caveat (mentioned in notebook comment):** On Windows, backslash in paths (`C:\Users\...`) must be written as `C:\\Users\\...` or `r"C:\Users\..."`. The same raw-string rule applies.

---

## 3.2 — Python `re` Module: Email Validation [INTERMEDIATE]

The notebook demonstrates a standalone regex use case — validating email addresses using `re.fullmatch()`.

```python
import re

def is_valid_email(email):
    # Pattern breakdown:
    # ^[a-zA-Z0-9._%+-]+   : One or more valid username characters
    # @                     : Mandatory @ symbol
    # [a-zA-Z0-9.-]+        : Domain name (letters, digits, dots, hyphens)
    # \.[a-zA-Z]{2,}$       : Dot followed by TLD (min 2 letters), at end of string
    pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'

    if re.fullmatch(pattern, email):
        return True
    return False

emails = ["user@example.com", "invalid-email", "name.surname@sub.domain.org", "hello@ghail@abc.com"]
for e in emails:
    print(f"{e}: {'Valid' if is_valid_email(e) else 'Invalid'}")
```

**Output:**

```
user@example.com: Valid
invalid-email: Invalid
name.surname@sub.domain.org: Valid
hello@ghail@abc.com: Invalid
```

**Why `re.fullmatch()` and not `re.match()` or `re.search()`?**

| Function | What it does |
|----------|-------------|
| `re.match(pattern, s)` | Matches only at the **start** of the string (does NOT require matching the full string) |
| `re.search(pattern, s)` | Finds **first match anywhere** in the string |
| `re.fullmatch(pattern, s)` | The entire string must match the pattern — **required for validation** |

`"hello@ghail@abc.com"` fails because `[a-zA-Z0-9._%+-]+` can't span the `@` sign, so two `@` signs are caught by the pattern's structure.

### Applying Email Validation to a DataFrame [INTERMEDIATE]

```python
data = {
    'Name': ['John Doe', 'Jane Smith', 'Emily Johnson', 'Robert Brown', 'Anna-Marie Lee'],
    'Email': ['john@example.com', 'jane.smith@work.org', 'emily_j@data.net', 'robertb@yahoo.com', 'anna.lee@company.co']
}
df_new = pd.DataFrame(data)

df_new['is_valid_email'] = df_new['Email'].apply(is_valid_email)
```

**Why use `.apply()` here?** There's no vectorized built-in for custom regex validation logic. This is a legitimate use case for `.apply()`.

---

---

# Section 4 — Categorical Data in Pandas [INTERMEDIATE]

## Concept Overview

Categorical data is **textual (or integer) data with a finite, known set of values** — called categories or levels. Examples: gender (`male/female`), grade (`A/B/C/D/F`), port of embarkation (`C/Q/S`), size (`S/M/L/XL`).

**Real-world analogy:** Think of a categorical column as a dropdown menu in a form — the user can only pick from a predefined list of options. A `string` column is a free-text box with no constraints.

**Why it matters:**
1. **Memory:** A `category` column stores each unique value once and uses integer codes for the rest — massive memory savings for low-cardinality columns
2. **Performance:** Operations on category columns are faster (integer comparisons vs string comparisons)
3. **Ordered categories:** You can define a custom ordering (`G1 < G2 < G3`) and use comparison operators (`>`, `<`, `>=`) meaningfully

**SQL equivalent:** `ENUM` type or `CHECK CONSTRAINT` limiting values to a fixed set.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| Cardinality | Number of unique values in a column |
| Categories | The set of allowed unique values in a categorical Series |
| Ordered | Whether the categories have a meaningful sequence (e.g., Low < Medium < High) |
| Codes | Integer encodings that represent each category internally (0, 1, 2, ...) |
| `CategoricalDtype` | The Pandas dtype for categorical columns |

---

## 4.1 — Creating Categorical Data with `pd.Categorical()` [INTERMEDIATE]

### Syntax

```python
pd.Categorical(
    values,             # array-like of values to categorize
    categories=None,    # list defining the order and set of allowed categories
    ordered=False       # True: categories have a meaningful rank order
)
```

### Example: Class Groups [INTERMEDIATE]

```python
class_data = {
    "Name":   ["A", "B", "C", "D"],
    "Groups": ["G1", "G2", "G3", "G4"]
}
class_df = pd.DataFrame(class_data)
```

**Output:**

```
  Name Groups
0    A     G1
1    B     G2
2    C     G3
3    D     G4
```

**`class_df.info()` — before categorical:**

```
<class 'pandas.DataFrame'>
RangeIndex: 4 entries, 0 to 3
Data columns (total 2 columns):
 #   Column  Non-Null Count  Dtype
---  ------  --------------  -----
 0   Name    4 non-null      str
 1   Groups  4 non-null      str
dtypes: str(2)
memory usage: 196.0 bytes
```

Both columns are plain `str` (object-backed). No ordering is possible — alphabetical comparison is meaningless for group codes.

```python
class_df['Group_cat'] = pd.Categorical(
    class_df['Groups'],
    categories=['G3', 'G1', 'G2', 'G4'],  # Custom order: G3 is lowest, G4 is highest
    ordered=True
)
```

**`class_df.info()` — after categorical:**

```
<class 'pandas.DataFrame'>
RangeIndex: 4 entries, 0 to 3
Data columns (total 3 columns):
 #   Column     Non-Null Count  Dtype
---  ------     --------------  -----
 0   Name       4 non-null      str
 1   Groups     4 non-null      str
 2   Group_cat  4 non-null      category
dtypes: category(1), str(2)
memory usage: 404.0 bytes
```

`Group_cat` now shows `category` dtype. Memory usage slightly increased here (tiny DataFrame) but for large DataFrames the savings are dramatic.

---

### Inspecting Category Order [BEGINNER]

```python
class_df['Group_cat'].cat.categories
```

**Output:**

```
Index(['G3', 'G1', 'G2', 'G4'], dtype='str')
```

The order reflects what was passed to `categories=` — **not alphabetical**. This custom order means:
- `'G3'` = rank 0 (lowest)
- `'G1'` = rank 1
- `'G2'` = rank 2
- `'G4'` = rank 3 (highest)

---

### Ordered Comparison [INTERMEDIATE]

```python
class_df['Group_cat'] >= "G4"
```

**Output:**

```
0    False
1    False
2    False
3     True
dtype: bool
```

**Why this works:** With `ordered=True`, Pandas maps categories to their rank integers internally and performs integer comparisons:
- `G1` (rank 1) >= `G4` (rank 3)? → `False`
- `G2` (rank 2) >= `G4` (rank 3)? → `False`
- `G3` (rank 0) >= `G4` (rank 3)? → `False`
- `G4` (rank 3) >= `G4` (rank 3)? → `True`

**This would FAIL on a plain `str` column** — string `"G1" >= "G4"` is alphabetical comparison (would return `False` because `"G"` + `"1"` < `"G"` + `"4"` lexicographically), which accidentally works here but is semantically wrong and non-configurable.

```python
class_df
```

**Output:**

```
  Name Groups Group_cat
0    A     G1        G1
1    B     G2        G2
2    C     G3        G3
3    D     G4        G4
```

---

## 4.2 — Auto-Categorical: `.astype('category')` [BEGINNER]

When you don't need a custom order, use `.astype('category')` for quick conversion. Categories are inferred automatically (alphabetical order by default).

### Titanic `Embarked` Column Example

```python
df = pd.read_csv("titanic.csv")

df.info()
```

**Output:**

```
<class 'pandas.DataFrame'>
RangeIndex: 891 entries, 0 to 890
Data columns (total 12 columns):
 #   Column       Non-Null Count  Dtype
---  ------       --------------  -----
 0   PassengerId  891 non-null    int64
 1   Survived     891 non-null    int64
 2   Pclass       891 non-null    int64
 3   Name         891 non-null    str
 4   Sex          891 non-null    str
 5   Age          714 non-null    float64
 6   SibSp        891 non-null    int64
 7   Parch        891 non-null    int64
 8   Ticket       891 non-null    str
 9   Fare         891 non-null    float64
 10  Cabin        204 non-null    str
 11  Embarked     889 non-null    str
dtypes: float64(2), int64(5), str(5)
memory usage: 83.7 KB
```

**Key observations:** 891 passengers, 12 columns. `Age` has 177 missing values, `Cabin` has 687 missing, `Embarked` has 2 missing — these 2 NaNs are what we'll fill next.

```python
df['Embarked'].unique()
```

**Output (before fillna):**

```
<StringArray>
['S', 'C', 'Q', nan]
Length: 4, dtype: str
```

> **Pandas 2.x note:** In Pandas 2.x, `.unique()` returns a `StringArray` for string columns (not a plain NumPy array as in 1.x).

The `Embarked` column represents the port of embarkation:
- `C` = Cherbourg
- `Q` = Queenstown
- `S` = Southampton
- `NaN` = not provided

```python
# Fill missing values before categorizing
df['Embarked'] = df['Embarked'].fillna("N")  # N = Not provided

df['Embarked'].unique()
```

**Output:**

```
<StringArray>
['S', 'C', 'Q', 'N']
Length: 4, dtype: str
```

```python
df['Embarked_auto_cat'] = df['Embarked'].astype('category')
df['Embarked_auto_cat'].cat.categories
```

**Output:**

```
Index(['C', 'N', 'Q', 'S'], dtype='str')
```

**Note:** Auto-categorical assigns alphabetical order — `C < N < Q < S`. This may not be meaningful for the data. Use `pd.Categorical()` when order matters.

---

## 4.3 — Custom-Ordered Categorical on Titanic Embarked [INTERMEDIATE]

```python
df['Embarked_cust_cat'] = pd.Categorical(
    df['Embarked'],
    categories=['N', 'S', 'C', 'Q'],   # N=0, S=1, C=2, Q=3 (ascending rank)
    ordered=True
)

df['Embarked_cust_cat'].cat.categories
```

**Output:**

```
Index(['N', 'S', 'C', 'Q'], dtype='str')
```

Imagine you've assigned port sizes: N (no port) < S (smallest) < C < Q (largest). Now you can filter:

```python
df['Embarked_cust_cat'] > 'S'
```

**Output (first 10 rows):**

```
0    False
1     True
2    False
3    False
4    False
5     True
6    False
7    False
8    False
9     True
dtype: bool
```

`True` for passengers who embarked at `C` or `Q` (higher rank than `S`).

---

## 4.4 — `.cat.codes` — Underlying Integer Codes [INTERMEDIATE]

```python
df['Embarked_cust_cat'].head()
```

**Output:**

```
0    S
1    C
2    S
3    S
4    S
Categories (4, str): ['N' < 'S' < 'C' < 'Q']
Name: Embarked_cust_cat, dtype: category
```

The `Categories` line shows the ordered sequence. Internally, Pandas stores these as integers:

```python
df['Embarked_cust_cat'].cat.codes.head()
```

**Output:**

```
0    1
1    2
2    1
3    1
4    1
dtype: int8
```

**Code mapping:**
- `N` → 0
- `S` → 1
- `C` → 2
- `Q` → 3

**Why `.cat.codes` matters:**
- ML models require numeric input — `.cat.codes` gives a quick integer encoding
- Memory: `int8` uses 1 byte per value vs. 5+ bytes for the string `"Southampton"`
- Fast group lookups and sorting use integer codes internally

---

## 4.5 — Encoding Categorical Columns for ML [INTERMEDIATE]

The notebook shows three equivalent ways to encode `Sex` (binary: `male`/`female`) into 0/1:

```python
df['Sex'].unique()
# → ['male', 'female']

# Method 1: auto-codes from categorical
df['Sex_encoded'] = df['Sex'].astype('category').cat.codes

# Method 2: replace labels with numbers
df['Sex_encoded'] = df['Sex'].replace(['female', 'male'], [0, 1])

# Method 3: apply lambda
df['Sex_encoded'] = df['Sex'].apply(lambda sex: 1 if sex == 'male' else 0)
```

| Method | Pros | Cons |
|--------|------|------|
| `.cat.codes` | Auto, memory-efficient, works for multi-class | Code assignment is alphabetical — verify mapping |
| `.replace()` | Explicit, readable | Verbose for many categories |
| `.apply(lambda)` | Fully flexible | Slowest (Python loop) |

**Best practice for many categories:** Use `pd.get_dummies()` (one-hot encoding) or `sklearn.preprocessing.LabelEncoder` / `OrdinalEncoder` for production ML pipelines.

---

## Memory Savings: `category` vs `str`

```python
import numpy as np

n = 1_000_000
s_str = pd.Series(np.random.choice(['C', 'Q', 'S', 'N'], n))
s_cat = s_str.astype('category')

print(s_str.memory_usage(deep=True))   # ~65 MB
print(s_cat.memory_usage(deep=True))   # ~1 MB (60x reduction!)
```

For a 4-category column with 1 million rows: stores **1 integer code** per row instead of a full Python string object. The category labels are stored only once.

---

## When to Use Categorical Dtype

| Use When… | Avoid When… |
|-----------|-------------|
| Column has low cardinality (< 50 unique values) | Column has high cardinality (many unique values) |
| You need ordered comparisons (`<`, `>`) | Column values change frequently |
| Sorting and GroupBy speed matters | Column is used in string joins/merges |
| Memory is a constraint | You need to frequently add new unseen categories |

---

---

# Section 5 — DateTime Functionality in Pandas [INTERMEDIATE]

## Concept Overview

Dates and times are fundamental to time-series analysis, event tracking, and reporting. Pandas has a rich ecosystem for datetime handling:

- `pd.to_datetime()` — converts strings/numbers to Pandas datetime objects
- `.dt` accessor — like `.str` but for datetime Series; exposes date components and operations
- `pd.Timestamp` — a single point in time (Pandas' equivalent of Python's `datetime.datetime`)
- `pd.Timedelta` — a duration (difference between two Timestamps)
- `pd.date_range()` — generates a sequence of dates at a given frequency

**Real-world analogy:** Think of `pd.to_datetime()` as teaching Pandas to read a calendar — once Pandas understands `"2025-01-01"` as a date (not just a string), it can extract the month, calculate days between events, filter by date range, etc.

---

## Key Terminology

| Term | Meaning |
|------|---------|
| `datetime64[us]` | Pandas 2.x datetime dtype with microsecond resolution |
| `Timestamp` | A single point in time — Pandas equivalent of `datetime.datetime` |
| `Timedelta` | A duration — difference between two timestamps |
| `.dt` accessor | Namespace on a datetime Series exposing components (year, month, day, etc.) |
| Epoch | Unix time origin: January 1, 1970 00:00:00 UTC |
| `NaT` | Not a Time — the datetime equivalent of `NaN` |

---

## 5.1 — `pd.to_datetime()` — Convert Strings to Datetime [BEGINNER]

### Setup: The Events DataFrame

```python
df = pd.DataFrame({
    'event': ['Concert', 'Conference', 'Wedding'],
    'date': ['2025-01-01', '2025-03-15', '2025-07-20']
})
```

**Output (`df` — raw, before conversion):**

```
        event        date
0     Concert  2025-01-01
1  Conference  2025-03-15
2     Wedding  2025-07-20
```

**`df.info()` — before conversion:**

```
<class 'pandas.DataFrame'>
RangeIndex: 3 entries, 0 to 2
Data columns (total 2 columns):
 #   Column  Non-Null Count  Dtype
---  ------  --------------  -----
 0   event   3 non-null      str
 1   date    3 non-null      str
dtypes: str(2)
memory usage: 180.0 bytes
```

The `date` column is stored as plain strings — Pandas cannot do any date math or comparisons on it yet.

```python
df['date_pandas'] = pd.to_datetime(df['date'])
```

**`df.info()` — after conversion:**

```
<class 'pandas.DataFrame'>
RangeIndex: 3 entries, 0 to 2
Data columns (total 3 columns):
 #   Column       Non-Null Count  Dtype
---  ------       --------------  -----
 0   event        3 non-null      str
 1   date         3 non-null      str
 2   date_pandas  3 non-null      datetime64[us]
dtypes: datetime64[us](1), str(2)
memory usage: 204.0 bytes
```

`date_pandas` is now `datetime64[us]` — a true datetime dtype.

> **Pandas 2.x change:** In Pandas 1.x, `pd.to_datetime()` always produced `datetime64[ns]` (nanosecond resolution). In Pandas 2.x, the resolution is flexible — it can be `[s]`, `[ms]`, `[us]`, or `[ns]` depending on input. ISO format strings like `'2025-01-01'` infer `datetime64[us]` (microsecond). Be aware of this if you compare timestamps across DataFrames with different resolutions.

```python
df.head()
```

**Output:**

```
        event        date date_pandas
0     Concert  2025-01-01  2025-01-01
1  Conference  2025-03-15  2025-03-15
2     Wedding  2025-07-20  2025-07-20
```

### Syntax with All Key Parameters

```python
pd.to_datetime(
    arg,                    # string, list, Series, or DataFrame
    errors='raise',         # 'raise', 'coerce' (NaT on error), or 'ignore'
    format=None,            # e.g., '%d/%m/%Y' — explicit format for speed + safety
    utc=False,              # localize to UTC
    unit=None,              # 's', 'ms', 'us', 'ns' for numeric epoch timestamps
    infer_datetime_format=False  # DEPRECATED in 2.x — use format= instead
)
```

**`errors='coerce'` pattern (important for dirty data):**
```python
pd.to_datetime(['2025-01-01', 'not-a-date', '2025-07-20'], errors='coerce')
# → DatetimeIndex(['2025-01-01', 'NaT', '2025-07-20'], dtype='datetime64[ns]', freq=None)
```

---

## 5.2 — `.dt` Accessor — Extracting Date Components [BEGINNER]

Once a column is `datetime64`, the `.dt` accessor unlocks all date/time components:

### Year, Month, Day

```python
df['date_pandas'].dt.year
```
**Output:**
```
0    2025
1    2025
2    2025
dtype: int32
```

```python
df['date_pandas'].dt.month
```
**Output:**
```
0    1
1    3
2    7
dtype: int32
```

```python
df['date_pandas'].dt.day
```
**Output:**
```
0     1
1    15
2    20
dtype: int32
```

### Weekday

```python
df['date_pandas'].dt.weekday
```
**Output:**
```
0    2
1    5
2    6
dtype: int32
```

**Weekday encoding:** `0 = Monday`, `1 = Tuesday`, ..., `5 = Saturday`, `6 = Sunday`
- Concert (Jan 1 2025) → Wednesday (2) ✓
- Conference (Mar 15 2025) → Saturday (5) ✓
- Wedding (Jul 20 2025) → Sunday (6) ✓

### Day Name (Human-Readable)

```python
df['date_pandas'].dt.day_name()
```
**Output:**
```
0    Wednesday
1     Saturday
2       Sunday
dtype: object
```

---

### Full `.dt` Accessor Reference

| Attribute / Method | Returns | Description |
|--------------------|---------|-------------|
| `.dt.year` | int | Year (e.g., 2025) |
| `.dt.month` | int | Month (1–12) |
| `.dt.day` | int | Day of month (1–31) |
| `.dt.hour` | int | Hour (0–23) |
| `.dt.minute` | int | Minute (0–59) |
| `.dt.second` | int | Second (0–59) |
| `.dt.weekday` | int | Day of week (0=Mon, 6=Sun) |
| `.dt.dayofweek` | int | Same as weekday |
| `.dt.dayofyear` | int | Day of year (1–366) |
| `.dt.week` | int | ISO week number |
| `.dt.quarter` | int | Quarter (1–4) |
| `.dt.days_in_month` | int | Days in the month |
| `.dt.is_month_start` | bool | True if first day of month |
| `.dt.is_month_end` | bool | True if last day of month |
| `.dt.is_year_start` | bool | True if Jan 1 |
| `.dt.is_year_end` | bool | True if Dec 31 |
| `.dt.date` | date object | Date portion only |
| `.dt.time` | time object | Time portion only |
| `.dt.day_name()` | str | Full day name (`"Monday"`, ...) |
| `.dt.month_name()` | str | Full month name (`"January"`, ...) |
| `.dt.tz_localize(tz)` | datetime with tz | Assign timezone |
| `.dt.tz_convert(tz)` | datetime in new tz | Convert timezone |

**SQL equivalents:**
```sql
EXTRACT(YEAR FROM date_col)    -- dt.year
EXTRACT(MONTH FROM date_col)   -- dt.month
DAYOFWEEK(date_col)            -- dt.weekday (SQL uses 1=Sunday in some dialects)
DAYNAME(date_col)              -- dt.day_name()
```

---

## 5.3 — Date Filtering [BEGINNER]

Pandas allows comparing datetime columns with date strings directly using standard comparison operators:

### Filter: Greater Than a Date

```python
df[df['date_pandas'] > '2025-04-01']
```

**Output:**

```
     event        date date_pandas
2  Wedding  2025-07-20  2025-07-20
```

**How it works:** Pandas automatically parses `'2025-04-01'` as a `Timestamp` and compares element-wise. Only the `Wedding` (July 2025) is after April 2025.

**SQL equivalent:**
```sql
SELECT * FROM events WHERE date_col > '2025-04-01';
```

---

### Filter: Between Two Dates — `.between()`

```python
df[df['date_pandas'].between('2025-01-01', '2025-06-01')]
```

**Output:**

```
        event        date date_pandas
0     Concert  2025-01-01  2025-01-01
1  Conference  2025-03-15  2025-03-15
```

**`.between()` is inclusive on both ends** — equivalent to `(date >= start) & (date <= end)`.

**SQL equivalent:**
```sql
SELECT * FROM events WHERE date_col BETWEEN '2025-01-01' AND '2025-06-01';
```

---

## 5.4 — `pd.date_range()` — Generate Date Sequences [BEGINNER]

### What It Does

Creates a `DatetimeIndex` of evenly spaced dates — used to create time series, fill gaps in data, or generate calendar-based ranges.

### Syntax

```python
pd.date_range(
    start=None,     # start date (string, Timestamp)
    end=None,       # end date (string, Timestamp)
    periods=None,   # number of periods (alternative to end=)
    freq='D',       # frequency string
    tz=None,        # timezone
    normalize=False,
    inclusive='both'  # 'both', 'left', 'right', 'neither'
)
```

### Frequency Strings (notebook-covered)

| Freq String | Meaning | Example Range |
|-------------|---------|--------------|
| `'D'` | Daily | Every calendar day |
| `'ME'` | Month End | Last day of each month |
| `'MS'` | Month Start | First day of each month |
| `'W'` | Weekly | Every Sunday (default) |
| `'h'` | Hourly | Every hour |
| `'min'` | Minutely | Every minute |
| `'s'` | Secondly | Every second |

> **Pandas 2.x change:** Old frequency aliases `'M'` (month end), `'H'` (hourly) are deprecated. Use `'ME'`, `'h'` etc. (lowercase/explicit).

### Example 1: Daily Range [BEGINNER]

```python
pd.date_range(start='2026-02-01', end='2026-02-10', freq='D')
```

**Output:**

```
DatetimeIndex(['2026-02-01', '2026-02-02', '2026-02-03', '2026-02-04',
               '2026-02-05', '2026-02-06', '2026-02-07', '2026-02-08',
               '2026-02-09', '2026-02-10'],
              dtype='datetime64[us]', freq='D')
```

### Example 2: Month-End Frequency [INTERMEDIATE]

```python
pd.date_range(start='2024-01-01', end='2024-03-10', freq='ME')
```

**Output:**

```
DatetimeIndex(['2024-01-31', '2024-02-29'], dtype='datetime64[us]', freq='ME')
```

**Note:** `'ME'` gives the **last day** of each month. February 2024 is `29` because 2024 is a leap year.

### Example 3: Month-Start Frequency [INTERMEDIATE]

```python
pd.date_range(start='2024-01-01', end='2024-03-10', freq='MS')
```

**Output:**

```
DatetimeIndex(['2024-01-01', '2024-02-01', '2024-03-01'], dtype='datetime64[us]', freq='MS')
```

**`'MS'` vs `'ME'` mnemonic:** MS = **M**onth **S**tart (day 1), ME = **M**onth **E**nd (last day).

---

## 5.5 — Date Arithmetic with `pd.Timedelta` and `pd.Timestamp` [INTERMEDIATE]

### `pd.Timedelta` — Representing a Duration

```python
pd.Timedelta(days=7)
```

**Output:**
```
Timedelta('7 days 00:00:00')
```

### Adding a Duration to a Date Column

```python
df['next_week'] = df['date_pandas'] + pd.Timedelta(days=7)
```

This shifts every date forward by exactly 7 days — vectorized, applied to all rows.

**`df.head()` — after adding `next_week`:**

```
        event        date date_pandas  next_week
0     Concert  2025-01-01  2025-01-01 2025-01-08
1  Conference  2025-03-15  2025-03-15 2025-03-22
2     Wedding  2025-07-20  2025-07-20 2025-07-27
```

### `pd.Timestamp.today()` — Current Date/Time

```python
pd.Timestamp.today()
```

**Output (example — will vary by run date):**
```
Timestamp('2026-05-10 14:23:11.503219')
```

### Calculating Time Differences

```python
df['days_to_event'] = df['date_pandas'] - pd.Timestamp.today()
df.head()
```

**Output (as of May 10, 2026):**

```
        event        date date_pandas  next_week  days_to_event
0     Concert  2025-01-01  2025-01-01 2025-01-08     -494 days
1  Conference  2025-03-15  2025-03-15 2025-03-22     -421 days
2     Wedding  2025-07-20  2025-07-20 2025-07-27     -294 days
```

**Negative timedelta** = event was in the past. Positive = event is in the future.

`df['days_to_event']` is dtype `timedelta64[us]` — a duration type. To get just the integer number of days:

```python
df['days_to_event'].dt.days
# → -494, -421, -294
```

### `pd.Timedelta` Constructor Options

```python
pd.Timedelta(days=7)                    # 7 days
pd.Timedelta(hours=12)                  # 12 hours
pd.Timedelta(weeks=2, days=3)           # 17 days
pd.Timedelta('1 day 6 hours')           # string form
pd.Timedelta('7D')                      # shorthand string
```

### `pd.DateOffset` — Calendar-Aware Arithmetic

For calendar-aware operations (e.g., "add 1 month" accounting for month lengths):

```python
from pandas.tseries.offsets import MonthEnd, BDay

# Add 1 month (calendar-aware)
df['date_pandas'] + pd.DateOffset(months=1)

# Add 5 business days
df['date_pandas'] + 5 * BDay()
```

`pd.Timedelta` is **fixed duration** (always exactly N days/hours). `pd.DateOffset` is **calendar-aware** (accounts for months, years, business days).

---

## 5.6 — Datetime Ranges as Index (Time Series Pattern) [INTERMEDIATE]

```python
# Create a time-series DataFrame from a date range
dates = pd.date_range(start='2024-01-01', periods=5, freq='D')
ts_df = pd.DataFrame({
    'sales': [100, 150, 130, 200, 175]
}, index=dates)

print(ts_df)
```

**Output:**
```
            sales
2024-01-01    100
2024-01-02    150
2024-01-03    130
2024-01-04    200
2024-01-05    175
```

With a `DatetimeIndex`, you can slice with date strings directly:

```python
ts_df['2024-01-02':'2024-01-04']
```

**Output:**
```
            sales
2024-01-02    150
2024-01-03    130
2024-01-04    200
```

---

---

# Common Mistakes & Debugging [ALL LEVELS]

## Mistake 1: `remove_substring` Bug — `str.find()` Returns -1 [INTERMEDIATE]

**Wrong (from notebook):**
```python
def remove_substring(name):
    start = name.find("(")
    end = name.find(")")
    new_name = name[0:start] + name[end+1:]
    new_name = new_name.strip()
    return new_name

df["Name_trimmed"] = df["Name"].apply(remove_substring)
df["Name_trimmed"].head()
```

**Buggy Output:**
```
0      Braund, Mr. Owen HarriBraund, Mr. Owen Harris   ← corrupted!
1                         Cumings, Mrs. John Bradley   ← correct
2        Heikkinen, Miss. LainHeikkinen, Miss. Laina   ← corrupted!
```

**Why it fails:** `"Braund, Mr. Owen Harris".find("(")` returns `-1`. Then:
- `name[0:-1]` = `"Braund, Mr. Owen Harri"` (drops last char!)
- `name[-1+1:]` = `name[0:]` = full string
- Result: truncated prefix + full string = mangled output

**Correct (guard against -1):**
```python
def remove_substring_safe(name):
    start = name.find("(")
    if start == -1:
        return name.strip()     # no parentheses — return as-is
    end = name.find(")")
    return (name[0:start] + name[end+1:]).strip()
```

**Best fix — use regex:**
```python
df['Name'].str.replace(r"\(.*\)", "", regex=True).str.strip()
```

---

## Mistake 2: `str.replace()` Without `regex=False` on Literal Patterns [BEGINNER]

**Wrong:**
```python
df['Name'].str.replace("Mr.", "Sir")  # default: regex=True
```

**Why it fails:** `"Mr."` as a regex means `"Mr"` followed by any character (`.` = wildcard). Would match `"MrX"`, `"Mr!"`, `"Mr "` — possibly too broadly.

**Correct:**
```python
df['Name'].str.replace("Mr.", "Sir", regex=False)  # literal string match
```

---

## Mistake 3: Chained Indexing `df.loc[1]['Name']` vs `df.loc[1, 'Name']` [INTERMEDIATE]

**Acceptable for reading:**
```python
df.loc[1]['Name']   # works for reading — but chained indexing
```

**Wrong for writing (SettingWithCopyWarning):**
```python
df.loc[1]['Name'] = "New Name"   # may not modify df — operates on a copy!
```

**Correct for writing:**
```python
df.loc[1, 'Name'] = "New Name"   # single .loc call — guaranteed to modify df
```

---

## Mistake 4: Comparing Unordered Categoricals [INTERMEDIATE]

**Wrong:**
```python
s = pd.Categorical(['G1', 'G2', 'G3'], ordered=False)
pd.Series(s) > 'G2'    # TypeError: Unordered Categoricals can only compare equality or inequality
```

**Error:** `TypeError: Unordered Categoricals can only compare equality or inequality`

**Correct:** Set `ordered=True` when creating the Categorical.

---

## Mistake 5: `pd.to_datetime()` on Ambiguous Dates [INTERMEDIATE]

**Wrong (ambiguous date format):**
```python
pd.to_datetime("01-02-2025")  # Is this Jan 2 or Feb 1?
```

**Correct (explicit format):**
```python
pd.to_datetime("01-02-2025", format="%d-%m-%Y")  # Day-Month-Year → Feb 1
pd.to_datetime("01-02-2025", format="%m-%d-%Y")  # Month-Day-Year → Jan 2
```

**With `errors='coerce'` for dirty data:**
```python
pd.to_datetime(["2025-01-01", "invalid-date"], errors='coerce')
# → DatetimeIndex(['2025-01-01', 'NaT'], dtype='datetime64[ns]', freq=None)
```

---

## Mistake 6: Old Frequency Aliases in Pandas 2.x [INTERMEDIATE]

**Wrong (Pandas 1.x aliases):**
```python
pd.date_range('2024-01-01', periods=3, freq='M')   # FutureWarning → error in 2.x
pd.date_range('2024-01-01', periods=3, freq='H')   # FutureWarning → error in 2.x
```

**Correct (Pandas 2.x aliases):**
```python
pd.date_range('2024-01-01', periods=3, freq='ME')  # Month End
pd.date_range('2024-01-01', periods=3, freq='h')   # Hourly
```

---

---

# Edge Cases & Gotchas Catalog

| Edge Case | What Happens | Example |
|-----------|-------------|---------|
| `.str` on non-string column | `AttributeError` or `NaN` output | `df['Age'].str.len()` → error |
| `NaN` input to `.str` methods | Returns `NaN` (propagated) | `pd.Series([None, "hello"]).str.len()` → `[NaN, 5.0]` |
| `str.find()` when not found | Returns `-1` (causes bugs in slicing) | `"hello".find("z")` → -1 |
| `pd.Categorical` with out-of-vocabulary values | Becomes `NaN` in the categorical | Assigning `"G5"` to a col with categories `['G1','G2','G3']` |
| Datetime resolution mismatch (2.x) | `TypeError` when comparing `[us]` vs `[ns]` | Merge two DFs with different datetime resolutions |
| `str.split().str[0]` on NaN rows | Returns `NaN` | Rows with `NaN` names produce `NaN` tokens |
| `.cat.codes` returns `-1` for NaN | `-1` is the code for missing values | Use `.fillna()` before categorizing |
| Timezone-naive vs timezone-aware comparison | `TypeError` | Cannot compare `datetime64[ns]` with timezone-aware `Timestamp` |
| `freq='M'` in Pandas 2.x | `FutureWarning` / `ValueError` | Use `'ME'` instead |

---

---

# Best Practices & Idiomatic Pandas

- **Always use `.str.strip()` after reading text CSVs** — invisible whitespace silently breaks joins and lookups
- **Set `regex=False` explicitly** when replacing literal strings in `.str.replace()` — prevents accidental regex interpretation
- **Prefer `.loc[row, col]` over `.loc[row][col]`** — avoids chained indexing and `SettingWithCopyWarning`
- **Use `pd.Categorical()` with `ordered=True`** for any column where rank/ordering matters — enables comparison operators
- **Convert to `category` dtype early** for low-cardinality string columns — saves memory and speeds up `groupby`/`sort`
- **Always pass `errors='coerce'` to `pd.to_datetime()`** in production pipelines — handles dirty date strings gracefully as `NaT`
- **Use `format=` explicitly** with `pd.to_datetime()` for non-ISO date strings — avoids ambiguity and is faster
- **Guard against `-1` from `.find()`** before using the result as a slice index
- **Use regex for pattern-based string removal** (e.g., `r"\(.*\)"`) instead of manual `.find()` + slicing — more robust, cleaner, vectorized
- **Use `.dt.days` to extract integer from `Timedelta` Series** — `timedelta64` objects are not directly integers

---

---

# SQL ↔ Pandas Translation Guide

| SQL | Pandas |
|-----|--------|
| `SELECT LEN(name) FROM t` | `df['name'].str.len()` |
| `SELECT REPLACE(name, 'Mr.', 'Sir') FROM t` | `df['name'].str.replace("Mr.", "Sir", regex=False)` |
| `SELECT TRIM(name) FROM t` | `df['name'].str.strip()` |
| `SELECT LTRIM(name) FROM t` | `df['name'].str.lstrip()` |
| `SELECT CHARINDEX('Mr', name) FROM t` | `df['name'].str.find("Mr")` |
| `SELECT * FROM t WHERE name LIKE '%Miss%'` | `df[df['name'].str.contains("Miss")]` |
| `SELECT COUNT(*) WHERE name LIKE '%Miss%'` | `df['name'].str.contains("Miss").sum()` |
| `CAST(col AS DATE)` | `pd.to_datetime(df['col'])` |
| `EXTRACT(YEAR FROM date_col)` | `df['date_col'].dt.year` |
| `EXTRACT(MONTH FROM date_col)` | `df['date_col'].dt.month` |
| `DAYNAME(date_col)` | `df['date_col'].dt.day_name()` |
| `WHERE date_col BETWEEN d1 AND d2` | `df[df['date_col'].between(d1, d2)]` |
| `WHERE date_col > '2025-04-01'` | `df[df['date_col'] > '2025-04-01']` |
| `DATEADD(day, 7, date_col)` | `df['date_col'] + pd.Timedelta(days=7)` |
| `DATEDIFF(day, date_col, GETDATE())` | `pd.Timestamp.today() - df['date_col']` |
| `GENERATE_SERIES(d1, d2, '1 day')` | `pd.date_range(start=d1, end=d2, freq='D')` |
| `ENUM('C', 'Q', 'S')` column | `df['col'].astype('category')` or `pd.Categorical(...)` |

---

---

# Deprecation & Pandas 2.x Migration Notes

| Old (1.x) | New (2.x) | Notes |
|-----------|-----------|-------|
| `freq='M'` in `date_range` | `freq='ME'` | Month-end alias changed |
| `freq='H'` in `date_range` | `freq='h'` | Hourly alias lowercased |
| `freq='T'` | `freq='min'` | Minute alias changed |
| `freq='S'` | `freq='s'` | Second alias lowercased |
| `datetime64[ns]` always | `datetime64[us]`, `[ms]`, `[s]` possible | Resolution flexible in 2.x |
| `df.applymap()` | `df.map()` | `applymap` deprecated in 2.1+ |
| `infer_datetime_format=True` in `pd.to_datetime()` | Removed — use `format=` explicitly | Deprecated in 2.0, removed 2.x |
| `.unique()` returns ndarray | Returns `StringArray` for string columns | Type check code may break |
| `df.append()` | `pd.concat([df, new_rows])` | `append()` removed in 2.0 |
| Copy-on-Write opt-in | CoW default in 2.0 | `SettingWithCopyWarning` behaviour changes |

---

---

# Interoperability Guide

| From / To | Method | Notes |
|-----------|--------|-------|
| `datetime64` → Python `datetime` | `ts.to_pydatetime()` | For single Timestamp |
| Python `datetime` → Pandas | `pd.Timestamp(dt_obj)` | Wraps Python datetime |
| String → Pandas datetime | `pd.to_datetime("2025-01-01")` | Most common pattern |
| Pandas datetime → Unix epoch | `df['date'].astype('int64') // 1e9` | Seconds since epoch |
| Unix epoch → Pandas datetime | `pd.to_datetime(epoch_series, unit='s')` | `unit='ms'`, `'us'`, `'ns'` also valid |
| Category → string | `df['cat_col'].astype('str')` | Converts back to plain string |
| Category → int codes | `df['cat_col'].cat.codes` | Returns `int8` Series |
| Pandas → NumPy | `df.to_numpy()` | Category → object array |
| Pandas → dict | `df.to_dict()` | Various `orient=` options |

---

---

# Interview & Exam Corner

## Frequently Asked Questions

**Q1: What is the `.str` accessor in Pandas? Why use it instead of `.apply()`?**
> The `.str` accessor provides vectorized string methods on a text Series. It's faster than `.apply()` because it uses C-level loops internally. `.apply()` requires one Python function call per element.

**Q2: What does `regex=False` do in `str.replace()`? When is it important?**
> It tells Pandas to treat the pattern as a literal string, not a regular expression. It's critical when the pattern contains regex special characters like `.`, `(`, `)`, `*`, `+` — e.g., `"Mr."` would match `"MrX"` with `regex=True` (`.` = any char), but only `"Mr."` with `regex=False`.

**Q3: What's the difference between `pd.Categorical()` and `.astype('category')`?**
> `.astype('category')` auto-detects categories (unordered, alphabetical). `pd.Categorical()` lets you specify a custom category list and set `ordered=True` to enable `<`, `>`, `<=`, `>=` comparisons.

**Q4: What does `.cat.codes` return?**
> An integer Series where each category is mapped to a sequential integer (0, 1, 2, ...). `-1` represents `NaN`. Useful for ML encoding and memory inspection.

**Q5: What is `pd.Timedelta` and when would you use it?**
> `pd.Timedelta` represents a fixed duration (days, hours, minutes, etc.). Use it to add/subtract a fixed duration from a datetime column. Use `pd.DateOffset` instead for calendar-aware operations (e.g., "add 1 month").

**Q6: What does `pd.to_datetime()` with `errors='coerce'` do?**
> Instead of raising an exception on an invalid date string, it silently converts unparseable values to `NaT` (Not a Time — the datetime equivalent of NaN). Essential for production data pipelines with dirty input.

**Q7: How do you count rows where a string column contains a specific substring?**
> `df['col'].str.contains("pattern").sum()` — `.contains()` returns a boolean Series, `.sum()` counts `True` values.

**Q8: What's the difference between `str.find()` and `str.contains()`?**
> `.str.find()` returns the integer position of the first match (-1 if not found). `.str.contains()` returns a boolean (True/False). Use `find` when you need the position; use `contains` for filtering.

**Q9: Why does `datetime64[us]` appear instead of `datetime64[ns]` in Pandas 2.x?**
> Pandas 2.x introduced resolution-flexible datetime types. ISO format strings (`"2025-01-01"`) infer microsecond (`[us]`) resolution by default. `[ns]` is only used when data requires nanosecond precision. This is a breaking change from 1.x.

---

## Tricky Output Questions

**Q1: What does this print?**
```python
import pandas as pd
s = pd.Series(["hello world", "foo bar", None])
print(s.str.split().str[0])
```
> ```
> 0    hello
> 1      foo
> 2     None
> dtype: object
> ```
> `None` (NaN) propagates — `.str.split()` on `None` returns `NaN`, and `.str[0]` on `NaN` returns `NaN` (displayed as `None`).

**Q2: What does this output?**
```python
import pandas as pd
cats = pd.Categorical(['B', 'A', 'C'], categories=['C', 'A', 'B'], ordered=True)
s = pd.Series(cats)
print(s > 'A')
```
> ```
> 0     True
> 1    False
> 2    False
> dtype: bool
> ```
> Category order is `C=0 < A=1 < B=2`. `B` (rank 2) > `A` (rank 1) → True. `A` (rank 1) > `A` (rank 1) → False. `C` (rank 0) > `A` (rank 1) → False.

**Q3: What's wrong with this?**
```python
pd.date_range('2024-01-01', '2024-03-01', freq='M')
```
> In Pandas 2.x, `'M'` is deprecated. Use `'ME'` (month end) or `'MS'` (month start). Raises `FutureWarning` → `ValueError` in later 2.x versions.

---

## One-Liner Challenges

**1. Count how many Titanic passenger names are longer than 30 characters:**
```python
(df['Name'].str.len() > 30).sum()
```

**2. Get all unique first words (last names) from the Name column:**
```python
df['Name'].str.split(",").str[0].unique()
```

**3. Extract all events in Q1 2025 (Jan–Mar) from an events DataFrame:**
```python
df[df['date_pandas'].dt.quarter == 1]
```

---

## Explain the Difference

**`str.replace(".", "-")` vs `str.replace(".", "-", regex=False)`**
> Without `regex=False`, the default `regex=True` treats `"."` as a regex wildcard matching ANY character — every character in the string gets replaced! With `regex=False`, only literal `.` characters are replaced.

**`pd.Timedelta` vs `pd.DateOffset`**
> `pd.Timedelta` is a fixed physical duration (7 days = exactly 604,800 seconds, always). `pd.DateOffset` is calendar-aware: `+ pd.DateOffset(months=1)` adds 1 month correctly whether that month has 28, 29, 30, or 31 days. Use `Timedelta` for precise durations, `DateOffset` for business/calendar logic.

---

---

# Practice Problems

## Problem 1 — String Cleaning [BEGINNER]

**Task:** Given the Titanic `Name` column, create a new column `Last_Name` containing only the last name (the part before the comma).

**Starter code:**
```python
df = pd.read_csv("titanic.csv")
# Your code here
```

**Expected output (first 5):**
```
0       Braund
1      Cumings
2    Heikkinen
3     Futrelle
4        Allen
Name: Last_Name, dtype: object
```

**Solution:**
```python
df['Last_Name'] = df['Name'].str.split(',').str[0]
```

---

## Problem 2 — Categorical Encoding for ML [INTERMEDIATE]

**Task:** Using the Titanic dataset:
1. Fill `NaN` values in `Embarked` with `"Unknown"`
2. Create an ordered Categorical column `Embarked_ord` with order: `Unknown < S < C < Q`
3. Filter all passengers where `Embarked_ord > 'S'` (i.e., embarked at C or Q)
4. Count how many such passengers survived

**Starter code:**
```python
df = pd.read_csv("titanic.csv")
# Your code here
```

**Solution:**
```python
df['Embarked'] = df['Embarked'].fillna("Unknown")

df['Embarked_ord'] = pd.Categorical(
    df['Embarked'],
    categories=['Unknown', 'S', 'C', 'Q'],
    ordered=True
)

filtered = df[df['Embarked_ord'] > 'S']
survivors = filtered['Survived'].sum()
print(f"Survivors who embarked at C or Q: {survivors}")
```

---

## Problem 3 — Date-Based Feature Engineering [ADVANCED]

**Task:** Create a DataFrame of 10 future events (use `pd.date_range()` starting from today, weekly). Add the following columns:
1. `day_of_week` — name of the weekday
2. `is_weekend` — boolean True if Saturday or Sunday
3. `days_from_now` — integer number of days from today
4. `quarter` — which quarter (Q1–Q4) the event falls in

**Starter code:**
```python
import pandas as pd
today = pd.Timestamp.today().normalize()
# Your code here
```

**Solution:**
```python
today = pd.Timestamp.today().normalize()

df = pd.DataFrame({
    'event_date': pd.date_range(start=today, periods=10, freq='W')
})

df['day_of_week']  = df['event_date'].dt.day_name()
df['is_weekend']   = df['event_date'].dt.weekday >= 5
df['days_from_now'] = (df['event_date'] - today).dt.days
df['quarter']      = df['event_date'].dt.quarter

print(df.to_string())
```

---

---

# Visualization Quick-Reference

```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("titanic.csv")

# Name length distribution
df['Name'].str.len().plot(kind='hist', bins=30, title='Name Length Distribution')
plt.xlabel('Name Length (characters)')
plt.show()

# Embarkation port counts (categorical)
df['Embarked'].value_counts().plot(kind='bar', title='Passengers by Port')
plt.ylabel('Count')
plt.show()

# Events over time (datetime index)
ts = pd.DataFrame({
    'bookings': [10, 25, 15, 30, 20]
}, index=pd.date_range('2025-01-01', periods=5, freq='ME'))

ts.plot(kind='line', marker='o', title='Monthly Bookings')
plt.ylabel('Bookings')
plt.show()
```

---

---

# Mnemonics & Memory Aids

- **`.str` = String Transformer** — if you want to do something to every string in a column, look for it under `.str`
- **`regex=False` = Exact Match** — when you mean a literal string, kill the regex
- **`str.find()` returns -1 = Not Found** — always guard with `if start == -1` before slicing
- **`.cat.codes` = Category to Integer** — category → its rank number in the categories list
- **`ordered=True` = Comparable** — only ordered categoricals support `<`, `>`, `<=`, `>=`
- **`.dt` for dates, `.str` for strings, `.cat` for categories** — three accessor namespaces to know
- **`pd.to_datetime` = "teach Pandas about time"** — once converted, all `.dt` operations become available
- **`Timedelta` = fixed clock duration, DateOffset = calendar duration** — `Timedelta(days=30)` is always 30 days; `DateOffset(months=1)` is the next calendar month
- **`ME` = Month End, `MS` = Month Start** — memorize the Pandas 2.x freq aliases
- **`NaT` = NaN for dates** — Not a Time is to datetime what NaN is to numbers

---

---

# Conceptual Diagrams (ASCII)

## `.str` Accessor — How Vectorization Works

```
Series: ['Braund, Mr. Owen Harris', 'Cumings, Mrs. John Bradley', 'Heikkinen, Miss. Laina']
           ↓                          ↓                               ↓
        str.len()                  str.len()                       str.len()
           ↓                          ↓                               ↓
          23                         26                              22
           ↓                          ↓                               ↓
Result: [23, 26, 22]      ← returned as a new int Series
```

No Python `for` loop — Pandas applies the operation across all elements internally.

---

## `pd.Categorical()` — Category Encoding

```
Raw string column:               After pd.Categorical(ordered=True):
┌───────────┐                    ┌───────────┬──────┐
│  Groups   │                    │ Group_cat │ code │
├───────────┤                    ├───────────┼──────┤
│    G1     │  → rank lookup →   │    G1     │  1   │
│    G2     │                    │    G2     │  2   │
│    G3     │                    │    G3     │  0   │  ← lowest
│    G4     │                    │    G4     │  3   │  ← highest
└───────────┘                    └───────────┴──────┘
                                  Categories: ['G3' < 'G1' < 'G2' < 'G4']
```

Comparisons (`>`, `>=`) use the integer codes, not alphabetical order.

---

## DateTime Column Lifecycle

```
Raw string column          After pd.to_datetime()        After .dt accessor
──────────────────         ──────────────────────         ─────────────────────
'2025-01-01'  (str)   →   2025-01-01 (datetime64[us]) → .dt.year   = 2025
'2025-03-15'  (str)   →   2025-03-15 (datetime64[us]) → .dt.month  = 3
'2025-07-20'  (str)   →   2025-07-20 (datetime64[us]) → .dt.day    = 20
                                                        → .dt.weekday = 6 (Sunday)
                                                        → filtering, arithmetic...
```

---

## `pd.date_range()` — Frequency Diagram

```
freq='D'  (Daily):    Jan 1 ─── Jan 2 ─── Jan 3 ─── Jan 4 ─── ...
freq='MS' (Mon.Start): Jan 1 ─────────── Feb 1 ─────────── Mar 1 ─── ...
freq='ME' (Mon.End):   Jan 31 ──────── Feb 28/29 ─────── Mar 31 ─── ...
freq='W'  (Weekly):   Jan 5 (Sun) ─── Jan 12 ─── Jan 19 ─── ...
```

---

---

# Quick-Reference Cheat Sheet

## String Operations (`.str` accessor)

```python
# Length
df['col'].str.len()

# Case
df['col'].str.lower()
df['col'].str.upper()
df['col'].str.capitalize()

# Replace
df['col'].str.replace("old", "new", regex=False)   # literal
df['col'].str.replace(r"\(.*\)", "", regex=True)   # regex

# Split
df['col'].str.split(",")           # returns Series of lists
df['col'].str.split(",").str[0]    # first element of each list
df['col'].str.split(",", expand=True)  # returns DataFrame of columns

# Strip whitespace
df['col'].str.strip()
df['col'].str.lstrip()
df['col'].str.rstrip()

# Find / Search
df['col'].str.find("pattern")             # position (-1 if not found)
df['col'].str.contains("pattern")         # boolean Series
df['col'].str.contains("pattern").sum()   # count matches
df['col'].str.startswith("prefix")        # boolean Series
df['col'].str.endswith("suffix")          # boolean Series

# Pad / align
df['col'].str.pad(width=20, side='left', fillchar='-')
```

## Apply

```python
# Custom function on Series
df['col'].apply(my_function)

# Lambda (simple)
df['col'].apply(lambda x: x.strip() if pd.notna(x) else x)
```

## Regex (Python `re` module)

```python
import re

re.fullmatch(pattern, string)  # full match (for validation)
re.match(pattern, string)      # match at start
re.search(pattern, string)     # find anywhere
re.sub(pattern, repl, string)  # replace (single string, not vectorized)
```

## Categorical

```python
# Auto (alphabetical, unordered)
df['col'].astype('category')

# Custom ordered
pd.Categorical(df['col'], categories=['low','mid','high'], ordered=True)

# Inspect
df['cat_col'].cat.categories          # Index of categories
df['cat_col'].cat.codes               # Integer codes Series

# Comparison (only with ordered=True)
df['cat_col'] > 'mid'                 # boolean Series
```

## DateTime

```python
# Convert
pd.to_datetime(df['col'])                        # infer format
pd.to_datetime(df['col'], format='%d/%m/%Y')     # explicit format
pd.to_datetime(df['col'], errors='coerce')       # NaT on error

# Extract components (.dt accessor)
df['date_col'].dt.year
df['date_col'].dt.month
df['date_col'].dt.day
df['date_col'].dt.weekday        # 0=Mon, 6=Sun
df['date_col'].dt.day_name()     # 'Monday', 'Tuesday', ...
df['date_col'].dt.quarter
df['date_col'].dt.is_month_end

# Filter
df[df['date_col'] > '2025-01-01']
df[df['date_col'].between('2025-01-01', '2025-06-01')]

# Generate ranges
pd.date_range(start='2025-01-01', end='2025-03-01', freq='D')   # daily
pd.date_range(start='2025-01-01', periods=12, freq='ME')         # 12 month-ends
pd.date_range(start='2025-01-01', periods=12, freq='MS')         # 12 month-starts

# Arithmetic
df['date_col'] + pd.Timedelta(days=7)          # add 7 days
df['date_col'] - pd.Timestamp.today()          # days since today (Timedelta Series)
(df['date2'] - df['date1']).dt.days            # integer days between dates

# Special values
pd.Timestamp.today()      # current date+time
pd.Timestamp('2025-01-01')   # specific timestamp
pd.NaT                    # missing datetime value
```

---

---

# Summary (TL;DR)

- **`.str` accessor** is the vectorized string toolkit in Pandas — faster than `.apply()` for text transformations; supports `len`, `replace`, `split`, `strip`, `find`, `contains`, `pad`, and many more
- **`apply()` with a custom function** is used when no built-in `.str` method exists — but always guard against edge cases (e.g., `str.find()` returning -1 when the pattern is absent)
- **Regex in Pandas** uses `str.replace(regex=True)` — always write patterns as raw strings (`r"..."`) and prefer regex over manual slicing for robustness
- **`pd.Categorical()`** with `ordered=True` enables rank-based comparisons (`<`, `>`) on text columns — critical for ordinal data (grades, tiers, port sizes)
- **`.astype('category')`** is the quick auto-version of categorical — assigns categories alphabetically, unordered
- **`.cat.categories`** shows the category order; **`.cat.codes`** gives the underlying integer encodings (useful for ML)
- **`pd.to_datetime()`** converts string columns to `datetime64` — always use `errors='coerce'` in production and specify `format=` for ambiguous dates
- **The `.dt` accessor** unlocks date components (year, month, day, weekday, day_name) and supports date filtering and arithmetic
- **`pd.date_range()`** generates evenly-spaced date sequences — use `'D'`, `'ME'`, `'MS'`, `'W'`, `'h'` for common frequencies (Pandas 2.x aliases)
- **`pd.Timedelta`** adds/subtracts fixed durations from datetime columns; **`pd.DateOffset`** for calendar-aware arithmetic

---

---

# Further Reading & Official Docs

- [Pandas String Methods — Official Docs](https://pandas.pydata.org/docs/user_guide/text.html)
- [Pandas Categorical Data — Official Docs](https://pandas.pydata.org/docs/user_guide/categorical.html)
- [Pandas DateTime — Official Docs](https://pandas.pydata.org/docs/user_guide/timeseries.html)
- [Pandas 2.0 Migration Guide](https://pandas.pydata.org/docs/whatsnew/v2.0.0.html)
- [Python `re` module — Official Docs](https://docs.python.org/3/library/re.html)
- [Regex101 — Interactive Regex Tester](https://regex101.com/) (great for building and debugging patterns)
- [Pandas Datetime Frequency Aliases (2.x)](https://pandas.pydata.org/docs/user_guide/timeseries.html#timeseries-offset-aliases)
- [Pandas vs SQL Cheatsheet](https://pandas.pydata.org/docs/getting_started/comparison/comparison_with_sql.html)

---

---

# Flashcard-Style Recall

**Q:** How do you get the character count of every string in a Series?
**A:** `series.str.len()`

**Q:** How do you replace `"Mr."` (literal dot) without accidentally using `.` as a regex wildcard?
**A:** `series.str.replace("Mr.", "Sir", regex=False)`

**Q:** What does `str.find()` return when the pattern is not found?
**A:** `-1`. Always check for `-1` before using the result as a slice index.

**Q:** What's the difference between `str.find()` and `str.contains()`?
**A:** `find()` returns the integer position; `contains()` returns a boolean True/False.

**Q:** How do you count rows containing `"Miss"` in a name column?
**A:** `df['Name'].str.contains("Miss").sum()`

**Q:** How do you split a string column and get the first token of every row?
**A:** `df['col'].str.split().str[0]`

**Q:** What does `pd.Categorical(values, categories=[...], ordered=True)` enable?
**A:** Rank-based comparisons using `<`, `>`, `<=`, `>=` on the categorical column.

**Q:** What does `.cat.codes` return?
**A:** An integer Series where each category value is mapped to its rank index (0, 1, 2, ...). NaN becomes -1.

**Q:** What is the Pandas 2.x replacement for `freq='M'` and `freq='H'` in `date_range`?
**A:** `freq='ME'` (month end) and `freq='h'` (hourly).

**Q:** What dtype does `pd.to_datetime()` produce in Pandas 2.x?
**A:** `datetime64[us]` (microsecond) for ISO format strings — not `datetime64[ns]` as in 1.x.

**Q:** How do you calculate the number of days between a date column and today?
**A:** `(df['date_col'] - pd.Timestamp.today()).dt.days`

**Q:** What's the raw string prefix `r` used for in regex patterns?
**A:** It prevents Python from interpreting backslashes as escape sequences — so `\(` reaches the regex engine as a literal escaped parenthesis, not as a Python escape code.

**Q:** What does `errors='coerce'` do in `pd.to_datetime()`?
**A:** Converts unparseable date strings to `NaT` instead of raising an exception.

**Q:** Which is faster — `.str.lower()` or `.apply(lambda x: x.lower())`?
**A:** `.str.lower()` is significantly faster — it uses C-level vectorized operations. `.apply()` calls the Python function once per element.
