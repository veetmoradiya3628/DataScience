# Seaborn — The Ultimate Structured Notes
### Based on: Online Retail II Dataset Visualization Course

---

## Prerequisites
- Python 3.8+ fundamentals (variables, loops, functions, list comprehensions)
- Pandas — DataFrames, Series, `groupby`, `value_counts`, `reset_index`, boolean filtering
- Matplotlib basics — `plt.figure()`, `plt.show()`, `plt.title()`, `plt.xlabel()`, `plt.ylabel()`
- NumPy basics — arrays, arithmetic
- Familiarity with Jupyter Notebooks is helpful (inline plot rendering)

---

## Environment Setup

### Installation

```python
# pip
pip install seaborn

# uv (modern fast installer)
uv add seaborn
```

### Standard Imports Used in This Notebook

```python
import seaborn as sns
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
```

### Dataset Used in This Notebook

The notebook uses a real-world **Online Retail II** e-commerce dataset, not a built-in Seaborn dataset. Key columns:

| Column | Type | Description |
|--------|------|-------------|
| `Invoice` | str | Invoice/transaction identifier |
| `Quantity` | int | Units ordered per line item |
| `Price` | float | Unit price |
| `Revenue` | float | Derived: `Quantity × Price` |
| `Country` | str | Customer country |
| `Month` | int/str | Month of transaction |

### Jupyter/Colab Rendering Note
Plots render inline automatically in Jupyter/Colab. In standalone scripts, always call `plt.show()`. The notebook uses `plt.tight_layout()` before `plt.show()` to prevent label clipping.

---

## Concept Overview

Seaborn is a **high-level statistical data visualization library** built on top of Matplotlib and tightly integrated with Pandas. It transforms raw data exploration from many lines of Matplotlib boilerplate into concise, expressive single-function calls.

**Why it exists:** Matplotlib is powerful but verbose. Drawing a histogram with a KDE overlay, a box plot with outlier labeling, or a scatter plot colored by a categorical variable each requires dozens of lines in raw Matplotlib. Seaborn collapses these into one call with intelligent defaults.

**Real-world analogy:** Matplotlib is like building furniture from raw lumber — full control, but you do all the work. Seaborn is like IKEA — pre-designed components that cover 90% of needs, and you can always reach back to the raw Matplotlib "wood" for custom finishing.

**Seaborn vs Matplotlib — Business Perspective [Instructor's Note]:**
Seaborn produces better-looking visualizations, but in real-world business reports and presentations, **the only thing that matters is the factual information the chart communicates**. Whether it looks pretty is a bonus, not a requirement. Matplotlib alone can produce every chart in this notebook — Seaborn just makes many of them faster and more polished. Use Seaborn when aesthetics and statistical features (like automatic CI bars, KDE overlays) save you time. Use Matplotlib when you need precise, custom control.

> *"When you talk about a business report, do you think you require the best visualization, or just a visualization which speaks the fact?"* — Instructor

**Where it fits in the ecosystem:**
```
Raw Data (Pandas DataFrame)
        ↓
Seaborn (high-level API, statistical plots, themes)
        ↓
Matplotlib (Figure, Axes objects — always accessible)
        ↓
Screen / File output
```

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **Figure-level function** | Creates its own `Figure` + `FacetGrid`; supports `col`/`row` faceting; returns a grid object, NOT an `Axes` |
| **Axes-level function** | Draws on a single `Axes`; embeddable in any Matplotlib layout; returns an `Axes` object |
| **Semantic mapping** | Encoding a data variable as a visual property — `hue` → color, `style` → shape, `size` → marker size |
| **Faceting** | Splitting a dataset into sub-panels by a categorical variable (`col=` or `row=`) |
| **Tidy data (long-form)** | Each row = one observation, each column = one variable; Seaborn's expected format |
| **KDE** | Kernel Density Estimate — a smooth continuous approximation of a distribution |
| **Confidence Interval (CI)** | Range of values within which the true parameter lies at a given probability |
| **Palette** | Named or custom set of colors used to encode data visually |
| **`hue`** | Parameter that colors plot elements by a categorical or continuous variable |
| **`estimator`** | Statistical function applied to aggregate data (default: `mean`) |

---

## Figure-level vs Axes-level Functions [CRITICAL CONCEPT]

This is Seaborn's most important architectural distinction.

```
┌─────────────────────────────────────────────────────────────────┐
│  FIGURE-LEVEL FUNCTIONS                                         │
│  (relplot, displot, catplot, lmplot, pairplot, jointplot)       │
│                                                                 │
│  • Always create their own Figure + FacetGrid                   │
│  • Return a FacetGrid / PairGrid / JointGrid object             │
│  • Support col= and row= for multi-panel faceting               │
│  • Cannot be placed inside an existing subplot                  │
│  • Size controlled by: height=, aspect=                         │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│  AXES-LEVEL FUNCTIONS                                           │
│  (histplot, kdeplot, boxplot, barplot, scatterplot, etc.)       │
│                                                                 │
│  • Draw on a single existing Axes                               │
│  • Return an Axes object (full Matplotlib access)               │
│  • Cannot do col=/row= faceting on their own                    │
│  • Can be placed inside plt.subplots() layouts                  │
│  • Size controlled by: plt.figure(figsize=...)                  │
└─────────────────────────────────────────────────────────────────┘
```

| Feature | Figure-level | Axes-level |
|---------|-------------|------------|
| Creates own Figure? | YES (always) | NO |
| Returns | `FacetGrid` / grid object | `Axes` |
| `col`/`row` faceting | YES | NO |
| Place in subplot | NO | YES |
| Size control | `height=`, `aspect=` | `figsize` on `plt.figure()` |
| Examples | `relplot`, `displot`, `catplot` | `scatterplot`, `histplot`, `boxplot` |

**Figure-level ↔ Axes-level mapping:**

| Figure-level | Axes-level equivalent | Plot kind |
|---|---|---|
| `sns.relplot(kind='scatter')` | `sns.scatterplot()` | Scatter |
| `sns.relplot(kind='line')` | `sns.lineplot()` | Line |
| `sns.displot(kind='hist')` | `sns.histplot()` | Histogram |
| `sns.displot(kind='kde')` | `sns.kdeplot()` | KDE |
| `sns.catplot(kind='box')` | `sns.boxplot()` | Box |
| `sns.catplot(kind='bar')` | `sns.barplot()` | Bar (with CI) |
| `sns.catplot(kind='count')` | `sns.countplot()` | Count |
| `sns.catplot(kind='strip')` | `sns.stripplot()` | Strip |
| `sns.catplot(kind='violin')` | `sns.violinplot()` | Violin |
| `sns.lmplot()` | `sns.regplot()` | Regression |
| `sns.pairplot()` | `sns.PairGrid()` + manual | Pairwise |
| `sns.jointplot()` | (no direct equivalent) | Joint distribution |

**Mnemonic:** "Figure-level = Faceting-capable; Axes-level = Attachable to any layout"

---

## The Statistical Visualization Advantage

Seaborn automates statistical computation that would require manual work in Matplotlib:

| Task | Raw Matplotlib | Seaborn |
|------|---------------|---------|
| Histogram + KDE overlay | Compute bins, fit KDE manually, two separate plots | `sns.histplot(..., kde=True)` |
| Box plot with outliers | Sort data, compute quartiles, draw manually | `sns.boxplot(data=df, x='col', y='val')` |
| Bar chart + confidence intervals | Bootstrap manually, draw error bars | `sns.barplot()` — automatic |
| Color by category | Map each category to a color, create legend | `hue='category'` — automatic |
| Correlation heatmap | Build matrix, map to colors, annotate | `sns.heatmap(corr, annot=True)` |

---

## Detailed Explanation with Examples

---

## Understanding "Distribution" in Data Science [INSTRUCTOR CONCEPT]

Before working with histograms and KDE plots, it is important to understand what **distribution** means in this context.

> *"Distribution is how my data is distributed. Is it evenly distributed? This is NOT distribution as in a distribution channel or sales distribution — it's about how your data is spread across values."* — Instructor

In data science, when we ask **"how is this data distributed?"**, we are asking:
- Where do most values cluster?
- Is the spread wide or narrow?
- Are there extreme values (outliers)?
- Is it evenly spread (uniform), or does it peak in one area (normal/skewed)?

**Example:** *"In each invoice, how many units does a customer usually buy? Some invoices may have 1 unit, some have 100. Distribution shows me how often each quantity range occurs across all invoices."*

We will learn more about distribution types (normal, skewed, uniform) in the Statistics module. For now, think of a distribution plot as a **frequency map** of your data.

---

### 1. `sns.histplot()` — Histogram [BEGINNER]

#### 1a. Basic Histogram (no KDE)

```python
import seaborn as sns
import matplotlib.pyplot as plt

# Filter: keep only quantities between 1 and 99
df_quantity = df[(df['Quantity'] > 0) & (df['Quantity'] < 100)]

plt.figure(figsize=(12, 6))
sns.histplot(df_quantity['Quantity'], bins=10, kde=False, color='skyblue')
plt.title('Distribution of Revenue')
plt.xlabel('Quantity bins')
plt.ylabel('Count')
plt.tight_layout()
plt.show()
```

**Visual Output Description:**
A bar chart with 10 equally-spaced bins spanning the range 1–99. The bars are sky blue. The x-axis shows the quantity range, y-axis shows the count of observations per bin. The distribution is right-skewed — most transactions involve low quantities (1–20), with counts dropping steeply as quantity increases.

**What are "bins" and why do we need them? [Instructor Concept]:**
If you plotted every individual quantity value (0 through 99), you would have up to 100 separate bars — the chart would be too dense to read. Instead, we group values into **bins** (equal-width ranges):

```
Bin 1:  0–10   →  count of invoices with quantity  0 to 10
Bin 2: 11–20   →  count of invoices with quantity 11 to 20
Bin 3: 21–30   →  ...
  ...
Bin 10: 91–100 →  count of invoices with quantity 91 to 100
```

With `bins=10`, all possible quantity values are grouped into 10 bars. Each bar height = number of rows that fall in that range. **More bins → finer detail but noisier. Fewer bins → smoother but may hide structure.**

**Line-by-line breakdown:**
- `df[(df['Quantity'] > 0) & (df['Quantity'] < 100)]` — boolean filter to remove returns (negative quantities) and bulk outliers
- `plt.figure(figsize=(12, 6))` — creates a 12×6 inch canvas before Seaborn draws on it
- `sns.histplot(df_quantity['Quantity'], ...)` — passes a Pandas Series directly (no `data=` + column name here)
- `bins=10` — divides the range into 10 equal-width bins
- `kde=False` — only draw the histogram bars, no smooth curve
- `color='skyblue'` — sets the bar fill color (any Matplotlib color string works)
- `plt.tight_layout()` — adjusts subplot parameters to prevent label clipping

**Why this works:** `sns.histplot()` counts observations in each bin and draws a bar. This is the direct replacement for the deprecated `sns.distplot()`.

---

#### 1b. Histogram with KDE Overlay [BEGINNER]

```python
plt.figure(figsize=(12, 6))
sns.histplot(df_quantity['Quantity'], bins=10, kde=True, color='skyblue')
plt.title('Distribution of Revenue')
plt.xlabel('Quantity bins')
plt.ylabel('Count')
plt.tight_layout()
plt.show()
```

**Visual Output Description:**
Same sky-blue histogram as above, but with a smooth bell-curved line (the KDE) overlaid on top. The KDE curve follows the shape of the bars — peaking in the low-quantity range and tapering toward 99. The y-axis still shows count, and the KDE curve is scaled to match.

**What `kde=True` does internally:**
1. Computes the histogram (as before)
2. Fits a **Kernel Density Estimate** — places a small Gaussian "kernel" at every data point and sums them to create a smooth continuous curve
3. Scales the KDE curve to the histogram's count axis automatically

**Why KDE matters:** The histogram depends heavily on the `bins` parameter — change bins and the shape changes. The KDE gives a bins-independent smooth view of the data's probability density. Together they validate each other.

**Real-world insight:** If the histogram bars and the KDE curve disagree in shape, your bin count may be hiding structure (too few bins) or showing noise (too many bins).

---

### 2. `sns.kdeplot()` — Kernel Density Estimate [INTERMEDIATE]

```python
# sns.kdeplot() — KDE (smooth version of histogram)
plt.figure(figsize=(12, 8))
sns.kdeplot(df_quantity['Quantity'], fill=True, color='green')
plt.title('Revenue KDE Curve')
plt.xlabel('Revenue')
plt.tight_layout()
plt.show()
```

**Visual Output Description:**
A smooth green bell-shaped curve with the area under the curve filled in green (semi-transparent shading). The x-axis shows quantity values from ~1 to 99. The y-axis shows probability density (not counts). The curve peaks in the low-quantity range and has a long right tail, confirming the right-skewed distribution.

**Line-by-line breakdown:**
- `sns.kdeplot(df_quantity['Quantity'], ...)` — takes the raw Series and computes the KDE
- `fill=True` — shades the area under the curve (replaces deprecated `shade=True`)
- `color='green'` — sets both the line and fill color

**`histplot(kde=True)` vs `kdeplot()` — when to use each:**

| `histplot(kde=True)` | `kdeplot()` |
|---------------------|-------------|
| Shows both bars AND curve | Shows ONLY the curve |
| Y-axis = count (or density if `stat='density'`) | Y-axis = probability density |
| Better for showing raw data distribution | Better for comparing distributions across groups with `hue=` |
| Easier to see sample size via bar heights | Cleaner for overlay comparisons |

**Key parameters for `kdeplot()`:**

| Parameter | Meaning | Example |
|-----------|---------|---------|
| `fill=True` | Shade area under curve | `fill=True` |
| `bw_adjust` | Bandwidth multiplier (smoothness) | `bw_adjust=0.5` (rougher), `bw_adjust=2` (smoother) |
| `hue=` | Multiple KDE curves by category | `hue='Country'` |
| `x=`, `y=` | Use both for 2D KDE (contour plot) | `x='Quantity', y='Revenue'` |
| `cut=` | Range cutoff beyond data range | `cut=0` (don't extend past data) |
| `cumulative=True` | Cumulative KDE (like ECDF) | `cumulative=True` |

**What KDE computes internally:**
$$\hat{f}(x) = \frac{1}{nh} \sum_{i=1}^{n} K\left(\frac{x - x_i}{h}\right)$$
Where $K$ is the kernel (usually Gaussian), $h$ is the bandwidth, and $n$ is sample size. A smaller $h$ → spikier curve; larger $h$ → smoother curve.

**Real-world analogy:** KDE is like applying a blur filter to a histogram. Each data point "spreads" its influence to neighboring values, creating a smooth probability landscape instead of blocky bars.

---

### 3. `sns.boxplot()` — Distribution + Outlier Visualization [BEGINNER → INTERMEDIATE]

**What a box plot shows:**
```
         ┌──────────────┐
  ───────┤     IQR      ├───────
  min*   Q1    Median   Q3   max*   ● outliers
         └──────────────┘

* Whiskers extend to last point within 1.5×IQR from Q1/Q3
  Points beyond whiskers are plotted as individual dots (outliers)
```

| Part | Meaning |
|------|---------|
| Box bottom (Q1) | 25th percentile |
| Box middle line | Median (50th percentile) |
| Box top (Q3) | 75th percentile |
| Box height | IQR = Q3 − Q1 |
| Whiskers | Extend to last data point within 1.5 × IQR |
| Dots beyond whiskers | Outliers |

**Real-world analogy:** A box plot is a statistical "body scan" — it shows the middle mass of your data (the box), how spread out values are (box height), and which patients are abnormally extreme (the outlier dots).

#### 3a. Boxplot by Country [INTERMEDIATE]

```python
df_sample = df[['Quantity', 'Price', 'Revenue', 'Country', 'Month']].copy()

plt.figure(figsize=(14, 4))
sns.boxplot(data=df_sample, x='Country', y='Revenue')
plt.xticks(rotation=90)
plt.title('Boxplot of Revenue by Country')
plt.tight_layout()
plt.show()
```

**Visual Output Description:**
A row of box plots — one per country — arranged along the x-axis. Countries are labeled on the x-axis (rotated 90° for readability). Revenue is on the y-axis. Most countries show compact boxes with low median revenue, but a few countries (e.g., United Kingdom) have tall boxes indicating wide revenue spread, plus many outlier dots extending far above the upper whisker.

**Line-by-line breakdown:**
- `data=df_sample` — passes the full DataFrame; Seaborn handles grouping internally
- `x='Country'` — creates one box per unique country value
- `y='Revenue'` — the continuous variable being distributed within each box
- `plt.xticks(rotation=90)` — rotates x-axis labels to prevent overlap (Matplotlib method on top of Seaborn's Axes)

**EDA insight:** Countries with tall boxes have inconsistent revenue per transaction. Countries with many high outliers have occasional large bulk orders.

---

#### 3b. Boxplot by Month [BEGINNER]

```python
plt.figure(figsize=(14, 4))
sns.boxplot(data=df_sample, x='Month', y='Revenue')
plt.xticks(rotation=90)
plt.title('Boxplot of Revenue by Country')
plt.tight_layout()
plt.show()
```

**Visual Output Description:**
Twelve boxes — one per month — showing revenue distribution month by month. The box heights (IQR) vary by month, revealing which months have more variable transaction sizes. November and December typically show taller boxes and higher medians due to holiday shopping.

---

#### 3c. Boxplot of Full DataFrame (All Numeric Columns) [BEGINNER]

```python
plt.figure(figsize=(14, 4))
sns.boxplot(data=df_sample)
plt.xticks(rotation=90)
plt.title('Boxplot of Revenue by Country')
plt.tight_layout()
plt.show()
```

**Visual Output Description:**
One box per numeric column in `df_sample` — Quantity, Price, Revenue plotted side-by-side. The columns have very different scales (Quantity might be 1–100, Revenue 0–10,000+), so this view is primarily useful for a quick sanity check on each column's spread and outlier presence.

**Why this works:** When `x=` and `y=` are omitted, Seaborn defaults to one box per numeric column in the DataFrame. Categorical columns are automatically skipped.

**Important:** When columns have very different scales, each box uses its own y-axis range implicitly — but all share the same axis here, so interpretation is limited. Use `plt.yscale('log')` or normalize first for cross-column comparison.

---

#### 3d. Filtered Boxplot for Cleaner Analysis [INTERMEDIATE]

```python
df_revenue_sample = df[(df['Revenue'] > 1000) & (df['Revenue'] < 2000)]

plt.figure(figsize=(18, 10))
sns.boxplot(data=df_revenue_sample, x='Month', y='Revenue')
plt.xticks(rotation=90)
plt.title('Boxplot of Revenue by Country')
plt.tight_layout()
plt.show()
```

**Visual Output Description:**
Same monthly box plot structure, but now restricted to the $1,000–$2,000 revenue band. The boxes are much more compact and comparable — outliers are minimal because the extreme values were filtered out. This reveals subtle month-to-month differences in the mid-range revenue distribution that were invisible when full-range data was used.

**Key `boxplot()` parameters:**

| Parameter | Meaning | Example |
|-----------|---------|---------|
| `data=` | DataFrame | `data=df` |
| `x=` | Categorical grouping variable | `x='Country'` |
| `y=` | Continuous variable | `y='Revenue'` |
| `hue=` | Secondary grouping by color | `hue='Month'` |
| `palette=` | Color palette | `palette='viridis'` |
| `orient=` | `'v'` (vertical) or `'h'` (horizontal) | `orient='h'` |
| `whis=` | Whisker length (default: 1.5) | `whis=2.0` |
| `showfliers=` | Show/hide outlier dots | `showfliers=False` |
| `order=` | Explicit category order | `order=['Jan','Feb',...]` |
| `ax=` | Matplotlib Axes to draw on | `ax=ax1` |

---

### 4. `sns.barplot()` — Central Tendency with Confidence Intervals [INTERMEDIATE]

**Important distinction:** `sns.barplot()` is NOT a simple frequency bar chart. It:
1. Computes the **mean** (or another estimator) of `y` for each `x` category
2. Draws a bar to that height
3. Adds a **confidence interval** (bootstrapped CI, 95% by default) as an error bar

For simple frequency counts, use `sns.countplot()` instead.

**Real-world analogy:** `barplot()` is like a scientific summary table turned into a chart — each bar is the average value for that group, and the error bars show how confident you are in that average.

#### 4a. Horizontal Bar Plot — Top Countries by Revenue [INTERMEDIATE]

```python
# Aggregate: total revenue per country, top 10
top_countries = df.groupby('Country')['Revenue'].sum().sort_values(ascending=False).head(10)

plt.figure(figsize=(10, 6))
sns.barplot(x=top_countries.values, y=top_countries.index, palette='viridis')
plt.title('Top 10 Countries by Total Revenue')
plt.xlabel('Total Revenue')
plt.ylabel('Country')
plt.tight_layout()
plt.show()
```

**Visual Output Description:**
A horizontal bar chart with 10 bars. Countries are on the y-axis, ordered from highest to lowest revenue (top = highest). Each bar length represents total revenue. Colors transition through the 'viridis' colormap (purple → blue → green → yellow), with the longest bar being the darkest/first color. The United Kingdom bar is visually dominant — far longer than all others given the dataset is UK-based.

**Line-by-line breakdown:**
- `df.groupby('Country')['Revenue'].sum()` — aggregates raw transaction rows into country-level totals
- `.sort_values(ascending=False).head(10)` — rank and take top 10
- `x=top_countries.values` — passing a NumPy array directly (not a column name) because data is already aggregated
- `y=top_countries.index` — the index holds country names
- `palette='viridis'` — applies a sequential colormap across bars (color encodes rank here)

**Note:** Since `top_countries` is already pre-aggregated (one value per country), Seaborn's automatic mean calculation has only one value per category — so the result equals the raw values with no error bars visible.

---

#### 4b. Vertical Bar Plot with `hue` [INTERMEDIATE]

```python
plt.figure(figsize=(10, 6))
sns.barplot(
    x=top_countries.index,
    y=top_countries.values,
    palette='viridis',
    hue=top_countries.index
)
plt.title('Top 10 Countries by Total Revenue')
plt.xlabel('Country')
plt.ylabel('Total revenue')
plt.tight_layout()
plt.show()
```

**Visual Output Description:**
The same data transposed — countries on the x-axis, revenue on the y-axis. A legend appears on the right side listing all 10 countries (since `hue=top_countries.index` maps each country to a unique color). The legend is redundant here (x-axis already labels the countries), which is why the next example adds `legend=False`.

**Why use `hue=` on the same variable as `x=`?**
This is a pattern used to apply per-bar coloring from a palette when you don't want one uniform color but also don't have a secondary grouping variable. Each unique value of `hue` gets its own color from the palette.

---

#### 4c. Horizontal Bar Plot — Spectral Palette, Legend Hidden [INTERMEDIATE]

```python
plt.figure(figsize=(10, 6))
sns.barplot(
    x=top_countries.values,
    y=top_countries.index,
    hue=top_countries.index,
    legend=False,
    palette='Spectral'
)
plt.title('Top 10 Countries by Total Revenue')
plt.xlabel('Total Revenue')
plt.ylabel('Country')
plt.tight_layout()
plt.show()
```

**Visual Output Description:**
Same horizontal bar layout, but using the 'Spectral' diverging palette (red → orange → yellow → green → blue) which creates a rainbow effect across the 10 bars. `legend=False` removes the redundant legend, giving more plot space.

**Palette comparison:**

| Palette | Type | Best For |
|---------|------|----------|
| `'viridis'` | Sequential | Ordered/ranked data |
| `'Spectral'` | Diverging | Contrast between extremes |
| `'deep'` | Qualitative | Distinct unordered categories |
| `'colorblind'` | Qualitative | Accessibility-first charts |
| `'coolwarm'` | Diverging | Data with natural center (e.g., correlation) |
| `'crest'` | Sequential | Modern perceptually uniform |

**Key `barplot()` parameters:**

| Parameter | Meaning | Example |
|-----------|---------|---------|
| `x=`, `y=` | Variables (column names or arrays) | `x='Country', y='Revenue'` |
| `hue=` | Secondary color grouping | `hue='Category'` |
| `palette=` | Color palette | `palette='viridis'` |
| `estimator=` | Aggregation function (default: `mean`) | `estimator='median'` |
| `errorbar=` | Error bar style (replaced `ci=`) | `errorbar='sd'` or `errorbar=('ci', 95)` |
| `order=` | Explicit x-axis category order | `order=['UK', 'Germany']` |
| `hue_order=` | Explicit hue order | `hue_order=['A', 'B']` |
| `orient=` | `'v'` or `'h'` | `orient='h'` |
| `legend=` | Show/hide legend | `legend=False` |
| `ax=` | Matplotlib Axes | `ax=ax1` |

---

#### 4d. Grouped Bar Plot with `hue` (Monthly Revenue by Country) [INTERMEDIATE]

> **Line chart vs Bar chart — when to use which? [Instructor's Note]:**
> - **Bar chart** → use when you want to **compare values** across categories: *"how much revenue did each month generate?"*
> - **Line chart** → use when you want to see a **trend** — whether values go up or down over time: *"is revenue rising or falling through the year?"*
> - Both can display the same data, but the choice communicates your intent. A line chart's primary signal is **direction of change**. A bar chart's primary signal is **magnitude comparison**. If your question is "this month vs that month", bar is clearer. If your question is "is revenue growing?", line is clearer.
> - **Important:** Only compare variables that share the same scale on the same plot. For example, do not plot revenue (in £) and quantity (units) together on one axis — the ranges are incompatible and the comparison is misleading.

```python
# Data preparation
top_countries = df['Country'].value_counts().head(5).index.tolist()
df_top = df[df['Country'].isin(top_countries)].copy()
grouped = df_top.groupby(['Country', 'Month'])['Revenue'].sum().reset_index()

# Plot
plt.figure(figsize=(14, 6))
sns.barplot(data=grouped, x='Month', y='Revenue', hue='Country')
plt.title('Monthly Revenue by Country (Grouped)')
plt.ylabel('Revenue')
plt.xlabel('Month')
plt.xticks(rotation=45)
plt.tight_layout()
plt.legend(title='Country')
plt.show()
```

**Visual Output Description:**
A grouped bar chart with months on the x-axis. For each month, 5 bars are drawn side-by-side — one per country — each a distinct color from Seaborn's default palette. The United Kingdom's bars tower over other countries in most months. A legend in the top-right maps colors to country names. The grouped structure makes month-over-month trends within each country visible, as well as which country dominates in each month.

**Line-by-line breakdown:**
- `df['Country'].value_counts().head(5).index.tolist()` — gets the 5 most-transacted countries
- `df[df['Country'].isin(top_countries)]` — filters to only those countries
- `.groupby(['Country', 'Month'])['Revenue'].sum().reset_index()` — creates a tidy DataFrame with one row per (country, month) pair
- `hue='Country'` — this is the key parameter: Seaborn automatically creates separate bars for each country and assigns colors
- `plt.legend(title='Country')` — adds a title to the auto-generated legend (Matplotlib method)

**Why `reset_index()`?** After `groupby().sum()`, the result has a MultiIndex. `reset_index()` converts it to a flat DataFrame with regular integer index — required for Seaborn's `data=` parameter to work correctly with column names.

---

### 5. `sns.countplot()` — Frequency Counts per Category [BEGINNER]

```python
plt.figure(figsize=(14, 6))
sns.countplot(
    data=df_sample,
    x='Country',
    order=df_sample['Country'].value_counts().index
)
plt.xticks(rotation=90)
plt.title('Number of Transactions by Country')
plt.xlabel('Country')
plt.ylabel('Number of Transactions')
plt.tight_layout()
plt.show()
```

**Visual Output Description:**
A vertical bar chart where each bar represents one country. The bars are ordered from left to right by descending transaction count (highest volume country first). The United Kingdom bar is dramatically taller than all others. The y-axis shows raw count of rows (transactions). No error bars — this is a pure frequency count, not a statistical estimate.

**Line-by-line breakdown:**
- `sns.countplot(data=df_sample, x='Country', ...)` — counts rows per unique `Country` value automatically
- `order=df_sample['Country'].value_counts().index` — sorts bars by frequency descending; without `order=`, bars appear in arbitrary (alphabetical or insertion) order

**`barplot()` vs `countplot()` — Critical Difference:**

| Feature | `barplot()` | `countplot()` |
|---------|-------------|---------------|
| What it measures | Mean (or custom `estimator`) of a numeric column | Row count per category |
| Needs `y=` column | YES (the numeric value to aggregate) | NO (counts rows automatically) |
| Error bars | YES (confidence interval) | NO |
| Use case | "Average revenue per country" | "How many rows per country" |
| Equivalent to | `groupby(x)[y].mean()` | `value_counts()` |

**Real-world analogy:** `countplot()` is a frequency histogram for categorical variables — exactly what you'd get from `df['Country'].value_counts().plot(kind='bar')`, but with Seaborn's styling and ordering control.

**Key `countplot()` parameters:**

| Parameter | Meaning | Example |
|-----------|---------|---------|
| `x=` or `y=` | Categorical variable (use `y=` for horizontal) | `x='Country'` |
| `hue=` | Color grouping | `hue='Month'` |
| `order=` | Bar display order | `order=df['Country'].value_counts().index` |
| `palette=` | Color palette | `palette='deep'` |
| `dodge=` | Separate hue groups | `dodge=True` |
| `stat=` | `'count'` (default) or `'percent'`, `'probability'` | `stat='percent'` |

---

### 6. `sns.scatterplot()` — Relationship Between Two Continuous Variables [BEGINNER → INTERMEDIATE]

**Why sample data before scatter plotting? [Instructor's Note]:**
The dataset has ~800,000 rows. Plotting all of them as individual dots would:
1. Take a very long time to render
2. Create a massive solid-colored blob where overlapping points are indistinguishable
3. Hide the actual pattern you want to see

The solution is to use a **random sample** of 1000 rows:
```python
df_sample = df.sample(1000, random_state=10)
```
`random_state` (also called **seed**) ensures reproducibility — anyone using the same `random_state` value on the same dataset gets the exact same 1000 rows. This lets you and a teammate reproduce identical results. Without it, every run picks different rows. See `matplotlib_notes.md` for a full explanation of `df.sample()` and `random_state`.

#### 6a. Basic Scatter Plot with `hue` [BEGINNER]

```python
plt.figure(figsize=(20, 12))
sns.scatterplot(data=df_sample, x='Quantity', y='Revenue', hue='Country', alpha=0.6)
plt.title("Quantity vs Revenue (by Country)")
plt.xlabel("Quantity")
plt.ylabel("Revenue")
plt.show()
```

**Visual Output Description:**
A dense scatter plot with Quantity on the x-axis and Revenue on the y-axis. Each dot represents one transaction. Dots are colored by country — each country gets a distinct color from Seaborn's default qualitative palette. A legend maps colors to country names. `alpha=0.6` makes overlapping points semi-transparent, revealing point density. The pattern shows a positive correlation — higher quantity generally means higher revenue — but with significant spread due to varying unit prices.

**Line-by-line breakdown:**
- `hue='Country'` — the key parameter that automatically assigns a unique color to each country
- `alpha=0.6` — transparency passed through to Matplotlib's scatter artist; helps visualize overlapping points

**EDA insight:** The positive trend confirms `Revenue = Quantity × Price`, but the width of the cloud reveals price variation. A wide cloud = inconsistent unit prices across transactions.

---

#### 6b. Filtered Scatter Plot — Removing Outliers [INTERMEDIATE]

```python
# Filter out extreme outliers for cleaner visualization
df_filtered = df_sample[
    (df_sample['Quantity'] > 0) &
    (df_sample['Quantity'] < 5000) &
    (df_sample['Revenue'] < 10000) &
    (df_sample['Revenue'] > 0)
]

# Keep only top 5 countries
top_countries = df_filtered['Country'].value_counts().head(5).index
df_filtered = df_filtered[df_filtered['Country'].isin(top_countries)]

plt.figure(figsize=(10, 6))
sns.scatterplot(data=df_filtered, x='Quantity', y='Revenue', hue='Country', alpha=0.8)
plt.title("Quantity vs Revenue (Filtered Top Countries)")
plt.xlabel("Quantity")
plt.ylabel("Revenue")
plt.tight_layout()
plt.show()
```

**Visual Output Description:**
A cleaner scatter plot with 5 distinct color groups — one per top country. Quantity runs 0–5000 on x-axis, Revenue 0–10,000 on y-axis. The positive linear relationship is now clear. Most transactions cluster in the low-quantity/low-revenue corner. The United Kingdom (likely largest group) has the widest spread. Other countries cluster at lower quantities.

---

#### 6c. Scatter Plot with Explicit Axis Limits [INTERMEDIATE]

```python
plt.figure(figsize=(10, 6))
sns.scatterplot(data=df_filtered, x='Quantity', y='Revenue', hue='Country', alpha=0.8)
plt.title("Quantity vs Revenue (Filtered Top Countries)")
plt.xlabel("Quantity")
plt.ylabel("Revenue")
plt.xlim(0, 5000)
plt.ylim(0, 10000)
plt.tight_layout()
plt.show()
```

**What changes vs 6b:**
`plt.xlim()` and `plt.ylim()` are pure Matplotlib calls that set explicit axis bounds. They do not filter the data — they just control what range is visible. Use these to "zoom in" on a region of interest without dropping data.

**Key `scatterplot()` parameters:**

| Parameter | Meaning | Example |
|-----------|---------|---------|
| `data=` | DataFrame | `data=df` |
| `x=`, `y=` | Column names or arrays | `x='Quantity', y='Revenue'` |
| `hue=` | Color by variable | `hue='Country'` |
| `style=` | Marker shape by variable | `style='Category'` |
| `size=` | Marker size by variable | `size='Price'` |
| `alpha=` | Point transparency (0–1) | `alpha=0.6` |
| `palette=` | Color palette | `palette='deep'` |
| `s=` | Fixed marker size (pixels) | `s=20` |
| `marker=` | Fixed marker shape | `marker='o'` |
| `legend=` | `True` / `False` / `'auto'` | `legend=False` |
| `ax=` | Matplotlib Axes | `ax=ax1` |

**Semantic mapping layers — building up a scatterplot:**
```python
# Layer 1: Basic
sns.scatterplot(data=df, x='Quantity', y='Revenue')

# Layer 2: + hue (color by category)
sns.scatterplot(data=df, x='Quantity', y='Revenue', hue='Country')

# Layer 3: + style (shape by category)
sns.scatterplot(data=df, x='Quantity', y='Revenue', hue='Country', style='Country')

# Layer 4: + size (marker size by continuous variable)
sns.scatterplot(data=df, x='Quantity', y='Revenue', hue='Country', style='Country', size='Price')
```

---

### 7. `sns.pairplot()` — Pairwise Relationships Grid [INTERMEDIATE]

```python
sns.pairplot(df_sample[['Quantity', 'Price', 'Revenue']], diag_kind='kde')
plt.suptitle("Pairwise Relationships", y=1.02)
plt.show()
```

**Visual Output Description:**
A 3×3 grid of plots. The diagonal shows KDE curves for each variable individually (Quantity, Price, Revenue). Off-diagonal plots show scatter plots for each pair:
- Row 1/Col 2: Quantity vs Price scatter
- Row 1/Col 3: Quantity vs Revenue scatter (positive linear trend)
- Row 2/Col 3: Price vs Revenue scatter
- Mirrored lower triangle shows the same pairs with axes transposed

The KDE diagonals show right-skewed distributions for all three variables.

**Line-by-line breakdown:**
- `df_sample[['Quantity', 'Price', 'Revenue']]` — selects only numeric columns for pairplot (avoids plotting categorical columns)
- `diag_kind='kde'` — draws KDE curves on the diagonal instead of the default histogram; options: `'hist'`, `'kde'`, `'auto'`
- `plt.suptitle("...", y=1.02)` — adds a super-title above the grid; `y=1.02` shifts it slightly above the top row to prevent overlap

**What pairplot reveals:**
- `Quantity` vs `Revenue`: strong positive correlation (expected since Revenue = Quantity × Price)
- `Price` vs `Quantity`: likely weak/negative correlation (expensive items bought in smaller quantities)
- Diagonal KDE shapes: confirm right-skewed distributions

**`pairplot()` is a convenience wrapper for `PairGrid()`:**
```python
# pairplot() equivalent:
g = sns.PairGrid(df_sample[['Quantity', 'Price', 'Revenue']])
g.map_diag(sns.kdeplot)
g.map_offdiag(sns.scatterplot)
```

**Key `pairplot()` parameters:**

| Parameter | Meaning | Example |
|-----------|---------|---------|
| `data=` | DataFrame | `data=df` |
| `hue=` | Color by category | `hue='Country'` |
| `vars=` | Subset of columns to use | `vars=['Quantity', 'Revenue']` |
| `kind=` | Off-diagonal plot type | `kind='scatter'` / `'reg'` / `'kde'` / `'hist'` |
| `diag_kind=` | Diagonal plot type | `diag_kind='kde'` / `'hist'` / `'auto'` |
| `corner=True` | Show only lower triangle | `corner=True` |
| `plot_kws=` | Keyword args passed to off-diag plot | `plot_kws={'alpha': 0.5}` |
| `diag_kws=` | Keyword args passed to diag plot | `diag_kws={'fill': True}` |

**PairGrid layout structure (ASCII):**
```
         Quantity        Price         Revenue
         ┌─────────────┬─────────────┬─────────────┐
Quantity │  KDE diag   │  scatter    │  scatter    │
         ├─────────────┼─────────────┼─────────────┤
Price    │  scatter    │  KDE diag   │  scatter    │
         ├─────────────┼─────────────┼─────────────┤
Revenue  │  scatter    │  scatter    │  KDE diag   │
         └─────────────┴─────────────┴─────────────┘
```

---

### 8. `sns.heatmap()` — Color-Encoded Matrix [INTERMEDIATE]

#### 8a. Correlation Matrix Heatmap — `coolwarm` palette

```python
# Step 1: Compute correlation matrix
corr_matrix = df_sample[['Quantity', 'Price', 'Revenue']].corr()

print(corr_matrix)
#           Quantity     Price   Revenue
# Quantity  1.000000 -0.050000  0.720000
# Price    -0.050000  1.000000  0.400000
# Revenue   0.720000  0.400000  1.000000

# Step 2: Plot
plt.figure(figsize=(6, 4))
sns.heatmap(corr_matrix, annot=True, cmap='coolwarm', fmt='.2f', linewidths=0.5)
plt.title("Correlation Matrix")
plt.tight_layout()
plt.show()
```

**Visual Output Description:**
A 3×3 grid of colored cells. The diagonal (self-correlation) is always 1.00 and appears as the darkest red/warm color. Off-diagonal cells show pairwise correlations as numbers (`fmt='.2f'` → 2 decimal places). The color encodes correlation strength: warm colors (red) = strong positive correlation, cool colors (blue) = negative correlation, white/neutral = near zero. Thin white lines between cells (`linewidths=0.5`) improve readability. The Quantity-Revenue cell shows a high positive value (e.g., 0.72), confirming the scatter plot's linear trend.

**Line-by-line breakdown:**
- `df_sample[['Quantity', 'Price', 'Revenue']].corr()` — Pandas method that computes Pearson correlation for all column pairs; returns a square DataFrame
- `annot=True` — prints the numeric value inside each cell
- `cmap='coolwarm'` — diverging colormap centered at 0; red = positive, blue = negative
- `fmt='.2f'` — Python format string for the annotation numbers; `.2f` = 2 decimal places
- `linewidths=0.5` — thin white borders between cells for visual separation

---

#### 8b. Heatmap — `crest` palette [INTERMEDIATE]

```python
plt.figure(figsize=(6, 4))
sns.heatmap(corr_matrix, annot=True, cmap='crest', fmt='.2f', linewidths=0.5)
plt.title("Correlation Matrix")
plt.tight_layout()
plt.show()
```

**Visual Output Description:**
Same 3×3 correlation matrix, but using Seaborn's `'crest'` sequential palette (light teal → dark teal). The sequential palette works less intuitively for correlations (which are bipolar, -1 to +1) — high positive correlations are dark teal and low/negative values are nearly white. Useful when all correlations are positive and you want to emphasize magnitude.

**`coolwarm` vs `crest` for correlation matrices:**

| Palette | Type | Best for Correlation? | Why |
|---------|------|----------------------|-----|
| `coolwarm` | Diverging | ✅ YES | Maps -1 (blue) → 0 (white) → +1 (red) naturally |
| `crest` | Sequential | ⚠️ OK if all positive | Can't distinguish negative from near-zero visually |
| `RdBu_r` | Diverging | ✅ YES | Classic red-white-blue diverging |
| `vlag` | Diverging | ✅ YES | Seaborn-native diverging |

**Key `heatmap()` parameters:**

| Parameter | Meaning | Example |
|-----------|---------|---------|
| `data=` | 2D array or DataFrame | `data=corr_matrix` |
| `annot=` | Print values in cells | `annot=True` |
| `cmap=` | Colormap | `cmap='coolwarm'` |
| `fmt=` | Value format string | `fmt='.2f'` |
| `linewidths=` | Width of cell borders | `linewidths=0.5` |
| `linecolor=` | Color of cell borders | `linecolor='white'` |
| `vmin=`, `vmax=` | Color scale range | `vmin=-1, vmax=1` |
| `center=` | Center of diverging colormap | `center=0` |
| `square=True` | Force square cells | `square=True` |
| `mask=` | Boolean mask to hide cells | `mask=np.triu(...)` (upper triangle) |
| `xticklabels=`, `yticklabels=` | Axis label control | `xticklabels=True` |
| `cbar=` | Show colorbar | `cbar=True` |
| `cbar_kws=` | Colorbar customization | `cbar_kws={'shrink': 0.5}` |

**Masking the upper triangle (common pattern):**
```python
import numpy as np

mask = np.triu(np.ones_like(corr_matrix, dtype=bool))
sns.heatmap(corr_matrix, annot=True, cmap='coolwarm', mask=mask, fmt='.2f')
```
This hides the upper triangle (redundant in a symmetric matrix), creating a cleaner lower-triangle-only view.

---

## Semantic Mappings Reference

Seaborn's most powerful differentiator: encoding data variables into visual channels automatically.

```
DataFrame column  ──→  Visual property
──────────────────────────────────────
hue='Country'     ──→  Color (qualitative or sequential)
style='Category'  ──→  Marker shape / line dash pattern
size='Price'      ──→  Marker diameter / line width
col='Month'       ──→  Subplot column (figure-level only)
row='Year'        ──→  Subplot row (figure-level only)
```

| Parameter | Visual Encoding | Categorical Variable | Continuous Variable |
|-----------|----------------|---------------------|---------------------|
| `hue=` | Color | Distinct colors per category | Color gradient |
| `style=` | Shape/dash | Different markers per category | Not applicable |
| `size=` | Size | Distinct sizes per category | Proportional sizing |
| `col=` | Subplot column | Separate panel per category | Not applicable |
| `row=` | Subplot row | Separate panel per category | Not applicable |

**`hue` with categorical vs continuous:**
```python
# Categorical hue — distinct colors, qualitative palette
sns.scatterplot(data=df, x='Quantity', y='Revenue', hue='Country')

# Continuous hue — color gradient, sequential palette
sns.scatterplot(data=df, x='Quantity', y='Revenue', hue='Price')
```

---

## Themes & Palettes Reference

### Seaborn Styles

```python
# Apply style
sns.set_style('whitegrid')   # White background + horizontal grid lines
sns.set_style('darkgrid')    # Dark background + grid (default)
sns.set_style('white')       # Plain white, no grid
sns.set_style('dark')        # Plain dark background
sns.set_style('ticks')       # White with tick marks

# All-in-one control
sns.set_theme(style='whitegrid', context='notebook', palette='deep')
```

### Seaborn Contexts (scale for output medium)

| Context | Use Case | Effect |
|---------|----------|--------|
| `'paper'` | Journal figures | Smallest text and lines |
| `'notebook'` | Default Jupyter | Medium scale |
| `'talk'` | Presentations | Larger text |
| `'poster'` | Conference posters | Largest text |

```python
sns.set_context('talk')
```

### Palette Types

| Type | Examples | Use Case |
|------|---------|----------|
| Qualitative | `'deep'`, `'muted'`, `'pastel'`, `'bright'`, `'dark'`, `'colorblind'` | Distinct unordered categories |
| Sequential | `'Blues'`, `'Greens'`, `'rocket'`, `'mako'`, `'flare'`, `'crest'` | Ordered / ranked data |
| Diverging | `'coolwarm'`, `'vlag'`, `'icefire'`, `'RdBu'` | Data with meaningful center (0 or mean) |

```python
# View a palette
sns.color_palette('viridis', 10)  # Returns list of RGB tuples, auto-displays in Jupyter

# Apply palette globally
sns.set_palette('colorblind')

# Per-plot palette
sns.barplot(..., palette='Spectral')
```

---

## Data Format Requirements

Seaborn expects **tidy (long-form) data:**
- Each row = one observation
- Each column = one variable

```
WIDE-FORM (not ideal for Seaborn):
Country   Jan    Feb    Mar
UK       1000   1200   900
Germany   200    300   250

LONG-FORM (Seaborn-ready):
Country   Month   Revenue
UK        Jan     1000
UK        Feb     1200
UK        Mar      900
Germany   Jan      200
...
```

**Convert wide → long with `pd.melt()`:**
```python
df_long = pd.melt(
    df_wide,
    id_vars=['Country'],           # Columns to keep as-is
    value_vars=['Jan', 'Feb', 'Mar'],  # Columns to unpivot
    var_name='Month',              # Name for the new "variable" column
    value_name='Revenue'           # Name for the new "value" column
)
```

---

## Customizing Seaborn Plots with Matplotlib

Every Seaborn plot returns Matplotlib objects — all Matplotlib customization methods work:

```python
# Axes-level: returns ax
ax = sns.histplot(data=df, x='Quantity', bins=10)
ax.set_title('Custom Title')          # Matplotlib
ax.set_xlabel('Quantity Sold')        # Matplotlib
ax.axvline(x=50, color='red', linestyle='--', label='Threshold')  # Overlay
plt.tight_layout()

# Figure-level: returns FacetGrid (has .fig and .axes attributes)
g = sns.displot(data=df, x='Quantity', col='Country')
g.fig.suptitle('By Country', y=1.02)     # Overall title
g.axes[0, 0].set_title('UK Panel')        # Individual panel
```

**Common post-plot Matplotlib calls used in this notebook:**
```python
plt.xticks(rotation=90)       # Rotate x-axis labels
plt.xlim(0, 5000)             # Set x-axis limits
plt.ylim(0, 10000)            # Set y-axis limits
plt.tight_layout()            # Prevent label clipping
plt.legend(title='Country')   # Customize legend
plt.suptitle('Title', y=1.02) # Super-title above grid
```

---

## Plot Type Selection Guide

| Question About Your Data | Recommended Plot | Seaborn Function |
|--------------------------|-----------------|------------------|
| How is this single variable distributed? | Histogram + KDE | `histplot(kde=True)` |
| What's the smooth probability shape? | KDE plot | `kdeplot()` |
| What are median, spread, and outliers? | Box plot | `boxplot()` |
| How do two continuous vars relate? | Scatter plot | `scatterplot()` |
| What's the average per category? | Bar plot (with CI) | `barplot()` |
| How many rows per category? | Count plot | `countplot()` |
| How do all numeric vars relate to each other? | Pair plot | `pairplot()` |
| What's the correlation structure? | Heatmap | `heatmap()` |
| How does distribution compare across groups? | Violin or Box | `violinplot()` / `boxplot()` |
| Is there a linear trend? | Regression plot | `regplot()` / `lmplot()` |

---

## Common Mistakes & Debugging

### Mistake 1: Using `barplot()` when you want `countplot()`
**Wrong intent:** "I want to count how many transactions per country"
```python
# Wrong — needs a y= column but you just want counts
sns.barplot(data=df, x='Country')   # TypeError: missing y
```
**Correct:**
```python
sns.countplot(data=df, x='Country')  # Counts rows automatically
```

---

### Mistake 2: Forgetting `order=` in `countplot()`
**Wrong:** Bars appear in arbitrary order
```python
sns.countplot(data=df, x='Country')  # Alphabetical or insertion order
```
**Correct:** Sort by frequency
```python
sns.countplot(
    data=df, x='Country',
    order=df['Country'].value_counts().index  # Descending count order
)
```

---

### Mistake 3: Passing pre-aggregated data without understanding CI behavior
**Situation:** You already called `.groupby().sum()` before passing to `barplot()`.
```python
top_countries = df.groupby('Country')['Revenue'].sum()

# This works but has no error bars (each category has one value)
sns.barplot(x=top_countries.index, y=top_countries.values)
```
**Why:** `barplot()` expects raw (unaggregated) data and computes mean + CI itself. If you pre-aggregate to one value per category, the CI calculation has nothing to bootstrap — no error bars appear. This is fine if you WANT a simple bar chart of totals.

---

### Mistake 4: Figure-level function inside `plt.subplots()`
**Wrong:**
```python
fig, axes = plt.subplots(1, 2)
sns.displot(data=df, x='Quantity', ax=axes[0])  # ERROR: displot ignores ax=
```
**Error:** `displot` creates its own figure and ignores the `ax=` parameter.
**Correct:** Use the axes-level equivalent
```python
fig, axes = plt.subplots(1, 2)
sns.histplot(data=df, x='Quantity', ax=axes[0])  # Works correctly
```

---

### Mistake 5: Deprecated `sns.distplot()` usage
```python
sns.distplot(df['Quantity'])  # FutureWarning → removed in 0.14
```
**Correct modern equivalents:**
```python
# Just histogram:
sns.histplot(df['Quantity'])

# Histogram + KDE overlay:
sns.histplot(df['Quantity'], kde=True)

# Just KDE:
sns.kdeplot(df['Quantity'], fill=True)
```

---

### Mistake 6: Non-tidy data causing wrong grouping
**Wrong data format:**
```python
# Wide-form: Seaborn doesn't know how to group months
df_wide = df_top.pivot(index='Country', columns='Month', values='Revenue')
sns.barplot(data=df_wide, ...)  # Confusing results
```
**Correct:**
```python
# Long-form: one row per (Country, Month) pair
grouped = df_top.groupby(['Country', 'Month'])['Revenue'].sum().reset_index()
sns.barplot(data=grouped, x='Month', y='Revenue', hue='Country')
```

---

### Mistake 7: Forgetting `reset_index()` after `groupby()`
```python
grouped = df_top.groupby(['Country', 'Month'])['Revenue'].sum()
# grouped has MultiIndex — Seaborn can't use column names directly
sns.barplot(data=grouped, x='Month', y='Revenue')  # KeyError: 'Month'
```
**Correct:**
```python
grouped = df_top.groupby(['Country', 'Month'])['Revenue'].sum().reset_index()
# Now 'Country', 'Month', 'Revenue' are regular columns
```

---

## Edge Cases & Gotchas Catalog

| Edge Case | What Happens | Fix |
|-----------|-------------|-----|
| `pairplot()` with many columns | Slow + unreadable grid | Use `vars=['col1', 'col2', 'col3']` to limit columns |
| `boxplot()` with extreme outliers | Box compressed, outliers dominate | Filter data first or use `plt.ylim()` to zoom |
| Many categories on x-axis without rotation | Labels overlap, unreadable | Add `plt.xticks(rotation=90)` |
| `heatmap()` with un-centered diverging palette | Misleading color encoding | Add `vmin=-1, vmax=1, center=0` for correlation matrices |
| `histplot(kde=True)` on very small samples | KDE curve is unreliable | Use `histplot(kde=False)` for n < 30 |
| `barplot()` with one value per category | CI = 0, no error bars | Expected behavior for pre-aggregated data |
| `pairplot()` with categorical columns | Error or meaningless plots | Select only numeric columns first |
| `scatterplot()` with millions of points | Very slow rendering | Sample data or use `rasterized=True` |
| `countplot()` without `order=` | Arbitrary bar ordering | Always set `order=df['col'].value_counts().index` |

---

## Before Seaborn vs After Seaborn

**Task:** Histogram with KDE overlay

**Raw Matplotlib (manual):**
```python
import numpy as np
from scipy.stats import gaussian_kde

data = df['Quantity'].dropna().values
counts, bins, _ = plt.hist(data, bins=10, color='skyblue', density=True)
kde = gaussian_kde(data)
x_range = np.linspace(data.min(), data.max(), 300)
plt.plot(x_range, kde(x_range), color='navy')
plt.show()
```
~8 lines, requires `scipy`, manual KDE scaling.

**Seaborn (one line):**
```python
sns.histplot(df['Quantity'], bins=10, kde=True, color='skyblue')
```
**Benefit:** 1 line vs 8 lines, no scipy import, automatic KDE scaling.

---

**Task:** Grouped bar chart (Revenue by Month, colored by Country)

**Raw Matplotlib:**
```python
countries = grouped['Country'].unique()
months = sorted(grouped['Month'].unique())
x = np.arange(len(months))
width = 0.8 / len(countries)
for i, country in enumerate(countries):
    vals = [grouped[(grouped['Country']==country) & (grouped['Month']==m)]['Revenue'].values[0]
            for m in months]
    plt.bar(x + i*width, vals, width, label=country)
plt.xticks(x + width*len(countries)/2, months)
plt.legend()
plt.show()
```
~10 lines, manual positioning math.

**Seaborn:**
```python
sns.barplot(data=grouped, x='Month', y='Revenue', hue='Country')
```
**Benefit:** 1 line vs 10 lines, automatic dodge positioning, automatic legend.

---

## Performance Notes

| Operation | Complexity | Notes |
|-----------|-----------|-------|
| `scatterplot()` N points | O(N) | Fast even for large N |
| `pairplot()` P vars, N rows | O(P² × N) | Slow for P > 10 |
| `histplot()` N points, B bins | O(N + B) | Very fast |
| `kdeplot()` N points | O(N × K) | K = evaluation points; slow for N > 100K |
| `boxplot()` N points | O(N log N) | Sort for quartiles |
| `heatmap()` M×N matrix | O(M × N) | Fast for small matrices |
| `barplot()` with CI | O(N × n_boot) | Default 1000 bootstrap iterations |

---

## How It Works Internally

Every Seaborn plot creates standard Matplotlib `Figure` and `Axes` objects:

```python
ax = sns.histplot(data=df, x='Quantity')
# ax IS a matplotlib.axes.Axes object
# All matplotlib methods work:
ax.set_facecolor('#f8f8f8')
ax.spines['top'].set_visible(False)
```

For `barplot()` with CI:
1. Group data by x-axis categories
2. Compute `estimator` (default: `mean`) for each group
3. Run `n_boot=1000` bootstrap iterations per group
4. Draw CI bar at the `ci=95` (or `errorbar=('ci', 95)`) percentile range

For `histplot(kde=True)`:
1. Compute histogram bins + counts
2. Fit Gaussian KDE using `scipy.stats.gaussian_kde`
3. Scale KDE curve to match histogram's y-axis units
4. Render both on same Axes

---

## Comparison Table

| Feature | Seaborn | Matplotlib | Plotly |
|---------|---------|-----------|--------|
| Statistical plots (auto CI, KDE, regression) | Built-in | Manual | Limited |
| Syntax simplicity | High | Low | Medium |
| Interactivity | No | No | Yes |
| Faceting (`col=`/`row=`) | Built-in | `GridSpec` (manual) | Built-in |
| Pandas integration | Native (tidy data) | Partial | Partial |
| Customization depth | Via Matplotlib | Full | Via Plotly API |
| Publication defaults | Excellent | Manual tuning needed | Decent |
| Learning curve | Low | Medium | Low |
| Correlation/matrix plots | `heatmap()` built-in | Manual | Manual |

---

## Seaborn ↔ ggplot2 (R) Translation Guide

| ggplot2 (R) | Seaborn (Python) |
|------------|-----------------|
| `ggplot(df, aes(x, y)) + geom_point()` | `sns.scatterplot(data=df, x='x', y='y')` |
| `+ geom_histogram()` | `sns.histplot(data=df, x='x')` |
| `+ geom_density()` | `sns.kdeplot(data=df, x='x')` |
| `+ geom_boxplot()` | `sns.boxplot(data=df, x='cat', y='val')` |
| `+ geom_bar(stat='count')` | `sns.countplot(data=df, x='cat')` |
| `+ geom_bar(stat='identity')` | `sns.barplot(data=df, x='cat', y='val')` |
| `aes(color=country)` | `hue='Country'` |
| `+ facet_wrap(~Month)` | `col='Month'` (figure-level) |
| `+ geom_smooth()` | `sns.regplot()` or `sns.lmplot()` |
| `+ theme_minimal()` | `sns.set_style('whitegrid')` |
| `+ scale_color_viridis()` | `palette='viridis'` |
| `+ theme(axis.text.x=element_text(angle=90))` | `plt.xticks(rotation=90)` |
| `+ labs(title='', x='', y='')` | `plt.title()`, `plt.xlabel()`, `plt.ylabel()` |
| `+ coord_flip()` | Swap `x=` and `y=` in Seaborn call |

---

## Deprecation & Migration Notes

| Old / Deprecated | New / Replacement | Version | Notes |
|-----------------|-------------------|---------|-------|
| `sns.distplot()` | `sns.histplot()` + `sns.kdeplot()` | Removed 0.14 | Split into two cleaner functions |
| `sns.factorplot()` | `sns.catplot()` | Renamed 0.9 | Same functionality |
| `sns.lvplot()` | `sns.boxenplot()` | Renamed 0.9 | Same functionality |
| `sns.tsplot()` | `sns.lineplot()` | Removed 0.11 | Better statistical API |
| `ci=` parameter | `errorbar=` parameter | Deprecated 0.12 | More flexible error bar control |
| `shade=True` in kdeplot | `fill=True` | Deprecated 0.12 | Clearer naming |
| `sns.palplot()` | `sns.color_palette()` display | Deprecated | Auto-displays in Jupyter |
| `size=` in figure-level | `height=` | Renamed 0.9 | Avoids conflict with `size` semantic |

---

## Best Practices & Publication-Quality Figures

```python
# 1. Set theme at start of notebook
sns.set_theme(style='whitegrid', context='notebook', palette='colorblind')

# 2. Use tight_layout() before every plt.show()
plt.tight_layout()

# 3. Rotate labels for many categories
plt.xticks(rotation=90)

# 4. Filter extreme outliers before plotting distributions
df_clean = df[(df['Revenue'] > 0) & (df['Revenue'] < df['Revenue'].quantile(0.99))]

# 5. Use order= for countplot to avoid arbitrary ordering
sns.countplot(data=df, x='Country', order=df['Country'].value_counts().index)

# 6. Use legend=False when hue encodes the same info as x=
sns.barplot(x=top.index, y=top.values, hue=top.index, legend=False)

# 7. Save with high DPI for publication
plt.savefig('figure.png', dpi=300, bbox_inches='tight')

# 8. Use colorblind-friendly palettes for public/published charts
sns.set_palette('colorblind')
```

---

## When to Use / When NOT to Use

| Use Seaborn When… | Avoid Seaborn When… |
|-------------------|---------------------|
| Exploring statistical properties of data | You need interactive/zoomable charts (use Plotly) |
| Working with tidy Pandas DataFrames | Complex custom layouts with many subplots |
| Publication-quality figures quickly | Real-time streaming data visualization |
| EDA workflows | Non-tabular data (images, graphs, networks) |
| Comparing distributions across groups | Pie charts (Seaborn doesn't have one) |
| Correlation / matrix analysis | Full Matplotlib control is needed without overhead |

---

## EDA Workflow Template with Seaborn

```python
import seaborn as sns
import matplotlib.pyplot as plt
import pandas as pd

sns.set_theme(style='whitegrid', context='notebook')

# ── STEP 1: Distribution of each numeric variable ──
for col in ['Quantity', 'Price', 'Revenue']:
    plt.figure(figsize=(10, 4))
    sns.histplot(df[col], kde=True, bins=30)
    plt.title(f'Distribution of {col}')
    plt.tight_layout()
    plt.show()

# ── STEP 2: Relationships between numeric variables ──
sns.pairplot(df[['Quantity', 'Price', 'Revenue']], diag_kind='kde')
plt.suptitle('Pairwise Relationships', y=1.02)
plt.show()

# ── STEP 3: Correlation structure ──
corr = df[['Quantity', 'Price', 'Revenue']].corr()
plt.figure(figsize=(6, 4))
sns.heatmap(corr, annot=True, cmap='coolwarm', fmt='.2f', vmin=-1, vmax=1, center=0)
plt.title('Correlation Matrix')
plt.tight_layout()
plt.show()

# ── STEP 4: Category distributions ──
plt.figure(figsize=(14, 5))
sns.countplot(data=df, x='Country', order=df['Country'].value_counts().index)
plt.xticks(rotation=90)
plt.title('Transactions by Country')
plt.tight_layout()
plt.show()

# ── STEP 5: Category comparisons (numeric vs category) ──
plt.figure(figsize=(14, 5))
sns.boxplot(data=df, x='Month', y='Revenue')
plt.title('Revenue Distribution by Month')
plt.tight_layout()
plt.show()

# ── STEP 6: Grouped analysis ──
grouped = df.groupby(['Country', 'Month'])['Revenue'].sum().reset_index()
top5 = df['Country'].value_counts().head(5).index
plt.figure(figsize=(14, 6))
sns.barplot(data=grouped[grouped['Country'].isin(top5)], x='Month', y='Revenue', hue='Country')
plt.title('Monthly Revenue by Top 5 Countries')
plt.tight_layout()
plt.show()
```

---

## Interview & Exam Corner

### Frequently Asked Questions

**Q1: What is the difference between `sns.histplot()` and `sns.kdeplot()`?**
`histplot()` divides data into discrete bins and draws a bar for each bin (count or density). `kdeplot()` fits a continuous smooth curve to the data using kernel density estimation. `histplot(kde=True)` combines both. KDE is preferred when comparing multiple distributions on the same axes with `hue=`; histogram is better for showing raw sample distribution.

**Q2: What's the difference between `barplot()` and `countplot()`?**
`barplot()` shows the mean (or custom estimator) of a continuous `y` variable per category, with bootstrapped confidence intervals. `countplot()` shows the frequency (row count) per category. `barplot()` requires a `y=` column; `countplot()` does not.

**Q3: What is the difference between figure-level and axes-level functions?**
Figure-level functions (e.g., `relplot`, `displot`, `catplot`) create their own `Figure` and return a `FacetGrid`. They support `col=`/`row=` faceting but cannot be placed in an existing subplot. Axes-level functions (e.g., `histplot`, `boxplot`, `scatterplot`) draw on a single `Axes`, return that `Axes`, and can be embedded in any Matplotlib layout.

**Q4: How do you control the order of bars in `countplot()`?**
Use the `order=` parameter: `order=df['Country'].value_counts().index` for descending frequency order.

**Q5: What does `kde=True` in `histplot()` do?**
It overlays a Kernel Density Estimate (KDE) — a smooth continuous probability curve — on top of the histogram bars. The KDE provides a distribution shape independent of bin count.

**Q6: Why do we need `reset_index()` after `groupby()`?**
`groupby().sum()` returns a DataFrame (or Series) with the grouping columns as the index. Seaborn's `data=` parameter expects regular column-based access. `reset_index()` converts the index back to regular columns so Seaborn can reference them by name.

**Q7: What does `alpha=` do in `scatterplot()`?**
`alpha=` controls transparency of markers (0 = invisible, 1 = fully opaque). Setting `alpha=0.6` makes overlapping points semi-transparent, allowing you to perceive point density in dense scatter plots.

**Q8: How do you fix overlapping x-axis labels?**
Call `plt.xticks(rotation=90)` after the Seaborn plot call. This rotates labels 90°. Alternatively, `rotation=45` with `ha='right'` for angled labels.

**Q9: What's the correct palette for a correlation matrix heatmap?**
A diverging palette like `'coolwarm'` or `'RdBu_r'`, combined with `vmin=-1, vmax=1, center=0` to ensure 0 maps to neutral white/gray and the color scale is symmetric.

**Q10: What is tidy data and why does Seaborn require it?**
Tidy (long-form) data has one observation per row and one variable per column. Seaborn uses column names for `x=`, `y=`, `hue=` parameters — it needs each variable to be its own column. Wide-form data (multiple observations in one row) must be melted with `pd.melt()` first.

---

### Tricky Output Questions

**Q: What happens when you call `sns.boxplot(data=df_sample)` without specifying `x=` and `y=`?**
Seaborn draws one box per numeric column in the DataFrame. Categorical columns are skipped. All boxes share the same y-axis, so columns with very different scales make the plot hard to interpret.

**Q: Will `sns.barplot(x=top_countries.values, y=top_countries.index, palette='viridis')` show confidence interval error bars?**
No. Because the data is already pre-aggregated (one value per category), Seaborn cannot compute a confidence interval. No error bars appear. This is expected behavior, not a bug.

**Q: If you call `sns.pairplot(df_sample)` where `df_sample` includes `Country` and `Month` columns, what happens?**
Seaborn will attempt to plot categorical columns, likely producing errors or meaningless scatter plots. Always pre-select numeric columns: `sns.pairplot(df_sample[['Quantity', 'Price', 'Revenue']])`.

---

### One-Liner Challenges

```python
# 1. Histogram with KDE of Quantity (1–99 only), skyblue, 10 bins
sns.histplot(df[df['Quantity'].between(1, 99)]['Quantity'], bins=10, kde=True, color='skyblue')

# 2. Correlation heatmap for numeric columns with annotations
sns.heatmap(df[['Quantity', 'Price', 'Revenue']].corr(), annot=True, cmap='coolwarm', fmt='.2f')

# 3. Countplot of countries sorted by frequency
sns.countplot(data=df, x='Country', order=df['Country'].value_counts().index)
```

---

### Explain the Difference

**`histplot()` vs `kdeplot()`:**
- `histplot()`: discrete bins, shows raw count/density, depends on `bins=` choice
- `kdeplot()`: continuous smooth curve, bins-independent, shows probability density

**`boxplot()` vs `violinplot()`:**
- `boxplot()`: shows quartiles + outliers, compact, fast
- `violinplot()`: shows KDE of full distribution shape + embedded box, more informative, slower

**`barplot()` vs `countplot()`:**
- `barplot()`: mean of a continuous column per category + CI error bars
- `countplot()`: row count per category, no error bars

**`scatterplot()` vs `relplot(kind='scatter')`:**
- `scatterplot()`: axes-level, returns Axes, no faceting
- `relplot(kind='scatter')`: figure-level, returns FacetGrid, supports `col=`/`row=`

---

## Practice Problems

### Problem 1 (Beginner): Distribution Histogram
**Task:** Plot a histogram of `Price` values (between $0 and $50) with 20 bins and a KDE overlay. Use a green color.

**Starter code:**
```python
df_price = df[(df['Price'] > 0) & (df['Price'] < 50)]
```

**Solution:**
```python
plt.figure(figsize=(10, 5))
sns.histplot(df_price['Price'], bins=20, kde=True, color='green')
plt.title('Distribution of Unit Price ($0–$50)')
plt.xlabel('Price ($)')
plt.ylabel('Count')
plt.tight_layout()
plt.show()
```

---

### Problem 2 (Intermediate): Grouped Analysis
**Task:** Plot the average `Quantity` per `Month` for the top 3 countries (by transaction count). Use a grouped bar chart with country as `hue`.

**Solution:**
```python
top3 = df['Country'].value_counts().head(3).index.tolist()
df_top3 = df[df['Country'].isin(top3)]
grouped = df_top3.groupby(['Country', 'Month'])['Quantity'].mean().reset_index()

plt.figure(figsize=(12, 6))
sns.barplot(data=grouped, x='Month', y='Quantity', hue='Country')
plt.title('Average Quantity per Month — Top 3 Countries')
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

---

### Problem 3 (Advanced): EDA Summary Dashboard
**Task:** Create a 2×2 subplot grid showing:
- Top-left: Revenue histogram (filtered 0–5000)
- Top-right: Boxplot of Revenue by Month
- Bottom-left: Countplot of top 10 countries
- Bottom-right: Correlation heatmap

**Solution:**
```python
fig, axes = plt.subplots(2, 2, figsize=(16, 12))
sns.set_theme(style='whitegrid')

# Top-left: Revenue histogram
df_rev = df[(df['Revenue'] > 0) & (df['Revenue'] < 5000)]
sns.histplot(df_rev['Revenue'], bins=30, kde=True, ax=axes[0, 0], color='steelblue')
axes[0, 0].set_title('Revenue Distribution')

# Top-right: Boxplot by Month
sns.boxplot(data=df_rev, x='Month', y='Revenue', ax=axes[0, 1])
axes[0, 1].set_title('Revenue by Month')
axes[0, 1].tick_params(axis='x', rotation=45)

# Bottom-left: Countplot top 10 countries
sns.countplot(
    data=df, x='Country', ax=axes[1, 0],
    order=df['Country'].value_counts().head(10).index
)
axes[1, 0].set_title('Transactions by Country (Top 10)')
axes[1, 0].tick_params(axis='x', rotation=90)

# Bottom-right: Heatmap
corr = df[['Quantity', 'Price', 'Revenue']].corr()
sns.heatmap(corr, annot=True, cmap='coolwarm', fmt='.2f', ax=axes[1, 1], vmin=-1, vmax=1, center=0)
axes[1, 1].set_title('Correlation Matrix')

plt.suptitle('E-Commerce EDA Dashboard', fontsize=16, y=1.02)
plt.tight_layout()
plt.show()
```

---

## Mnemonics & Memory Aids

- **"Figure-level = Faceting-capable; Axes-level = Attachable"** — figure-level functions create their own figure and support `col=`/`row=`; axes-level attach to an existing Axes
- **"hue = color, style = shape, size = magnitude"** — the three semantic mapping parameters of relational plots
- **"dis-t-plot is dis-continued"** — `distplot` is deprecated; use `histplot` instead
- **"barplot = mean + CI; countplot = counts only"** — barplot needs a `y=` column; countplot just counts rows
- **"reset_index() before Seaborn"** — after any `groupby()`, call `reset_index()` so Seaborn can access column names
- **"coolwarm for correlation"** — diverging palettes center at 0; perfect for correlation matrices where 0 = no correlation
- **"order= to order countplot"** — without `order=`, bars appear in arbitrary sequence

---

## Functions Covered — Quick Reference

| Function | Type | Returns | Purpose |
|----------|------|---------|---------|
| `sns.histplot()` | Axes-level | `Axes` | Histogram (+ optional KDE) |
| `sns.kdeplot()` | Axes-level | `Axes` | Kernel Density Estimate |
| `sns.boxplot()` | Axes-level | `Axes` | Box plot: quartiles + outliers |
| `sns.barplot()` | Axes-level | `Axes` | Mean per category + CI error bars |
| `sns.countplot()` | Axes-level | `Axes` | Row count per category |
| `sns.scatterplot()` | Axes-level | `Axes` | Scatter plot, supports hue/style/size |
| `sns.pairplot()` | Figure-level | `PairGrid` | Grid of pairwise scatter/KDE plots |
| `sns.heatmap()` | Axes-level | `Axes` | Color-encoded matrix |

---

## Summary (TL;DR)

- **Seaborn** is a high-level statistical visualization library wrapping Matplotlib, designed for Pandas DataFrames
- **Axes-level functions** (`histplot`, `boxplot`, `scatterplot`, etc.) return a Matplotlib `Axes` and work inside `plt.subplots()` layouts
- **Figure-level functions** (`pairplot`, `displot`, etc.) create their own figure and return grid objects; they support `col=`/`row=` faceting
- **`hue=`** is Seaborn's superpower — it automatically splits data by a variable and assigns colors, creating grouped visualizations in one parameter
- **Data must be tidy (long-form)** — one row per observation; use `pd.melt()` to convert wide data; always call `reset_index()` after `groupby()`
- **Every Seaborn plot is a Matplotlib plot** — use `plt.title()`, `plt.xticks()`, `plt.xlim()`, etc. after any Seaborn call
- **`barplot()` ≠ `countplot()`** — `barplot()` computes mean + confidence interval; `countplot()` counts rows
- **`distplot()` is deprecated** — use `histplot(kde=True)` instead

---

## Further Reading & Official Docs

- [Seaborn Official Documentation](https://seaborn.pydata.org/)
- [Seaborn Tutorial: Statistical Data Visualization](https://seaborn.pydata.org/tutorial.html)
- [API Reference — All Functions](https://seaborn.pydata.org/api.html)
- [Example Gallery](https://seaborn.pydata.org/examples/index.html)
- [Figure-level vs Axes-level functions guide](https://seaborn.pydata.org/tutorial/function_overview.html)
- [Color palettes tutorial](https://seaborn.pydata.org/tutorial/color_palettes.html)
- [Matplotlib documentation](https://matplotlib.org/stable/contents.html) (for post-Seaborn customization)
- [Pandas melt() docs](https://pandas.pydata.org/docs/reference/api/pandas.melt.html) (for wide→long conversion)

---

## Flashcard-Style Recall

**Q:** What does `kde=True` in `sns.histplot()` add?
**A:** A smooth Kernel Density Estimate curve overlaid on the histogram bars.

**Q:** What does `fill=True` do in `sns.kdeplot()`?
**A:** Shades the area under the KDE curve with semi-transparent color.

**Q:** What is the y-axis of a box plot's box?
**A:** Q1 (bottom) to Q3 (top) = IQR. Middle line = median. Whiskers extend to last point within 1.5×IQR.

**Q:** What parameter controls bar order in `countplot()`?
**A:** `order=df['col'].value_counts().index`

**Q:** What does `hue=` do in `sns.scatterplot()`?
**A:** Colors each point by the specified variable — unique colors for categorical, gradient for continuous.

**Q:** What is the modern replacement for `sns.distplot()`?
**A:** `sns.histplot()` for histogram, `sns.kdeplot()` for smooth curve, `sns.histplot(kde=True)` for both.

**Q:** How do you access the underlying Matplotlib Figure from a `pairplot()` result?
**A:** `g = sns.pairplot(...)` → `g.fig` for the Figure, `g.axes` for the Axes grid.

**Q:** What does `annot=True` do in `sns.heatmap()`?
**A:** Prints the numeric value of each cell inside the colored rectangle.

**Q:** Why do we filter data before certain Seaborn plots?
**A:** Extreme outliers compress box plots and skew histograms, hiding the distribution of typical values.

**Q:** What does `plt.tight_layout()` do?
**A:** Adjusts subplot padding automatically to prevent axis labels and titles from overlapping.
