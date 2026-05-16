# Matplotlib — The Ultimate Structured Notes
### Based on: Online Retail II Dataset Visualization Course

---

## Prerequisites
- Python 3.8+ fundamentals (variables, loops, comprehensions, functions)
- NumPy basics — arrays, `np.linspace`, arithmetic on arrays
- Pandas basics — DataFrames, Series, `groupby`, `read_excel`, `dropna`, `head`
- Familiarity with Jupyter Notebooks or Google Colab is helpful but not required

---

## Environment Setup

### Package Imports Used in This Notebook

```python
import pandas as pd
import matplotlib.pyplot as plt
```

### Jupyter/Colab Rendering Note
In Jupyter/Google Colab, plots render **inline** automatically (no `plt.show()` required) due to the implicit `%matplotlib inline` backend. In standalone Python scripts, you **must** call `plt.show()` to display the plot.

```python
# Optional — force inline rendering in Jupyter
%matplotlib inline
```

> **Tip:** `%matplotlib notebook` gives interactive zoom/pan widgets; `%matplotlib widget` works in JupyterLab. Outside Jupyter, the default backend (TkAgg, Qt5Agg, etc.) opens a window.

---

## Why Data Visualization? — Instructor's Perspective

### The Learning Stack So Far

Before diving into Matplotlib, the instructor placed visualization in context of everything covered so far:

```
Python          → our programming language
NumPy           → core library for numerical computation
Pandas          → load, manipulate, and analyze data (uses NumPy underneath)
Matplotlib      → visualize the results of that analysis
```

Pandas does not give you analysis directly — you have to *use* Pandas to perform analysis. But once you have your numbers, how do you communicate them?

### The "240 Data Points" Problem

> *"If I talk about analysis of 2 years of data, and in these 2 years, for each month I consider 10 days of data per month — 12 months × 2 years × 10 = 240 data points. If I print all 240 rows, will you be able to easily conclude what the data does? No. If I go to row 100, I will forget what happened at row 50."*

This is the core motivation: **raw numbers are hard for humans to process at scale**.

### Humans Are Visual Beings

The instructor used a memorable analogy:

- **Scenario 1:** Remember 20 people's phone numbers → Very hard
- **Scenario 2:** Remember 20 people's faces → Much easier

> *"Numbers are hard for us. Numbers are easy for computers, but it is hard for us. Visualization is something that is very easy for us."*

Instead of staring at 240 rows of numbers, **plot them**. A single chart lets you see:
- The **pattern** as a whole
- What happened the previous month vs the next month
- The **trend** across time

**Definition (from the instructor):** Data visualization is the *graphical representation of information and data*. We use visual elements — charts, graphs, maps — to make it easier to see patterns, trends, and outliers in large datasets.

> A map is also a visualization. If someone asks you to remember coordinates from place X to place Y, you won't remember them after 5 days. But show a map — you'll remember it instantly.

### Communicating to Non-Technical Stakeholders

> *"Whatever we are working on, we are working on a task. A task, by default, is not technical. It is some kind of a problem statement. A problem statement is not technical, but to understand it and solve it, we use our technical knowledge."*

Most problem statements in industry come from **business** — people who don't care how many modules you built or what libraries you used. They care about the answer to their problem.

> *"If you say, 'Sir, I've created 5 modules to solve this solution' — does it even matter? No. They will say, 'We require a system like ChatGPT where we can solve customer queries.' That is the problem statement."*

The conclusion of any technical task should have:
- **Summary statistics**
- **Visualizations** — simple, easy-to-read ones that tell the business what was found and what they should do

> *"You should be very good at concluding things. You should be very good at providing a summary to non-technical people. I see many technical people who, when asked to present a solution, always talk in terms of the technical things — that the person they are presenting to doesn't even understand. Don't do that."*

**Key skill:** Think of it like storytelling. The chart is your story. It should be immediately readable — no prior knowledge of the chart type required.

---

## Principles of Good Visualization

> *"Data visualization will have coding, but we should use our logic and our mind here — whether this color is good, whether the way I'm presenting is good. There are a lot of things you need to consider."*

### 1. Clarity
The chart should be **clear about what it is telling**. If someone sees your chart and doesn't know what it is doing, the visualization is not good — even if it is technically correct.

> *"It should be easy to interpret."*

### 2. Proper Scale
Choose a scale that **makes sense for the data and the audience**.

**Example from the instructor:** Plotting human heights on the Y-axis in *kilometers* is technically valid but wrong. Every person's height becomes a tiny decimal (0.00170 km instead of 170 cm). The plot itself isn't wrong, but the scale is wrong.

> *"Scale is very important — what you want to show. Again, it is for other people, not for us. So it should have a proper scale."*

This is also why you should not mix columns with vastly different scales on the same Y-axis (e.g., Revenue in millions and Quantity in units — one line would appear nearly flat).

### 3. Simplicity / Minimalism
> *"The simpler, the better. It should follow minimalism — no unnecessary things."*

A common mistake: search for "best Python visualization library," find a fancy 3D animated chart, use it. The graph looks impressive, but someone needs to *learn* how to read it before they can extract any value.

> *"If someone has to learn visualization in order to read it, then it is not actually solving any problem."*

Simple colors, simple meaning — that is the main point.

### 4. Label Everything (Whenever You Can)
Label the X-axis, the Y-axis, what each value means (legend), colors — everything should be present.

> *"We should label what is X-axis, what is Y-axis, what each value means. That means legend, and colors and everything should be present."*

Never produce a chart without axis labels.

### 5. Use Colors Wisely
> *"It should not be like: this bar is pink, this one is blue, this one is green, this one is black, this one is white. It looks funny."*

You can use highlighted colors, but colors should not be **too distracting**. Keep it simple. Visualization is supposed to be simple.

### 6. Choose the Correct Chart for Your Data *(Very Important)*
> *"Choose the correct chart for your data. Very, very, very important."*

Different chart types communicate different things. Using the wrong chart type can completely mislead the reader. The section below covers each chart type and when to use it.

---

## Visual Introduction to Chart Types

> *"If you understand what each plot does, then it is very easy to do in the coding."*

The instructor introduced each chart type with a real-world whiteboard example before any code was written. These analogies are the foundation.

---

### Scatter Plot

**Use case:** Explore the *relationship* (correlation) between two numeric variables. Each observation becomes one dot in 2D space.

**Instructor's analogy — The Gym:**

> *"Let's suppose I have a gym, and I want to keep track of people who are trying to lose weight. I want to check their weight (W) and height (H)."*

```
Y-axis (Height): 100 ... 200 ... 300 ... 400 ... 500
X-axis (Weight):  50 ... 100 ... 150 ... 200 ... 250 ... 300

Person 1: Weight=100, Height=300 → plot the intersection → one dot
Person 2: Weight=120, Height=350 → another dot
Person 3: Weight=98,  Height=400 → another dot
```

Once you plot all gym members, you get a **scatter** of dots. If a pattern emerges (e.g., heavier people tend to be taller), the scatter reveals the relationship.

> *"A plot like this is called a scatter plot. Scatter plot is about scattering all the data points, and if you're able to find some kind of a pattern, then this is good."*

**When to use:** Two numeric variables, looking for correlation, clusters, or outliers.

---

### Line Chart

**Use case:** Show a *trend* over time. Data points are connected with a line.

**Instructor's analogy — Insurance Company:**

> *"I'm an insurance company. I want to check how much monthly revenue I am generating. January: $120K. February: $140K. March: $180K. Next month: a little less. Then the trend leads up again."*

```
Revenue
180K |        *
140K |   *       *  *
120K | *
     |_______________
       Jan Feb Mar Apr  → Month
```

When you **connect these dots**, you see the trend.

> *"Line chart is used to see trend — the trend over month, the accuracy over years, the change over years. If you get a problem statement that says 'I want to see the trend over months,' you use a line chart."*

**Scatter vs Line for the same data:**
> *"You could use scatter here too — it will look fine. But scatter doesn't connect the dots. The connecting line is what makes the trend visible."*

**When to use:** Time series, any data where the sequence matters, showing change over time.

---

### Bar Chart

**Use case:** *Compare* values across discrete categories.

**Instructor's analogy — Product Sales:**

> *"I want to see how much each product has been sold. I have Product A, B, C, D, E, F, G, H."*

If you used a **line chart** for this, you'd get dots connected by a line — but the connection is meaningless for products (Product A and Product B have no inherent sequence between them). You'd be implying a trend that doesn't exist.

If you used a **scatter plot**, you'd get dots — but dots alone don't make it easy to compare magnitude.

> *"Bar plot is better visualization. I just want to see which product is doing good — separated by their sales. I want to see comparison."*

```
Sales
  |      |---|
  |  |---|   |---|
  | -|   |   |   |---|
  |__|___|___|___|___|___
      A   B   C   D   E  → Product
```

> *"If I tell you 'give me the top 3 products' — E is first, then A and C. I'm able to compare them easily with a simple, beautiful graph."*

**When to use:** Categorical data, ranking, comparison across discrete groups. Sort by value (`sort_values(ascending=False)`) for immediate ranking clarity.

---

### Stacked Bar Chart

**Use case:** Compare multiple series across the same categories, stacked on top of each other.

**Instructor's extension — Two Categories:**

> *"Product A, B, C... is of category Food. Now I have another category — Body Care. Body care has its own products A, B, C... and its own sales."*

If you want to show **both** Food and Body Care sales together:
- Use two different colors — blue for Food, another for Body Care
- Stack them on the same bar

> *"I can merge two different plots. For food I use blue. For body care I use a different color on top of the same graph."*

**Disadvantage the instructor pointed out:**
> *"There is a possibility that one bar can be hidden behind the other — they overlap. But if you label correctly, you will see the text even if the actual bar is hidden behind another."*

**When to use:** Comparing totals across categories while also showing the part-to-whole breakdown within each category.

---

### Pie Chart

**Use case:** Show *proportions* of a whole. All slices must add up to 100%.

**Instructor's explanation:**

> *"If I have some categories — A, B, C — and their values are 70, 10, 20 — all adding up to 100. I want to see visually which one is bigger, which one is smaller."*

> *"Bar plot is not for this case. Bar plot is used for comparing — two categories can have the same sales (both 70). But pie chart is used when we have a total. When we add everything up, we get 100%. Each slice represents what percentage of the total that category makes up."*

**When to use:** Showing percentage distribution, part-to-whole relationships. All categories must sum to a meaningful total (usually 100%).

**When NOT to use:** More than 5-6 slices (becomes unreadable). Use a sorted bar chart instead.

---

### Box Plot (Whisker Plot)

**Use case:** Understand the *distribution* of a numerical column — specifically minimum, maximum, median, quartiles, and outliers — all in a single compact chart.

> *"This is also called a whisker plot. This is what we are going to use a lot as data scientists. It tells us distribution. We use it to understand the data of numerical columns."*

**Instructor's analogy — Product Category Prices:**

> *"I have 4 product categories: frozen dumplings (C1), frozen veggies (C2), ice cream (C3), frozen dessert (C4). I want to understand, for each category: What is the maximum price? What is the minimum price? What is the median? Do we have outliers?"*

**The Outlier Example:**
> *"There is one food item which is very fancy — gold leaf ice cream. Ice cream with gold leaf on top of it. The price of this is going to be the highest compared to its counterparts. Ice cream: $4. Frozen dessert: $3. Frozen veggies: $8. Gold leaf ice cream: $20. As compared to other items, $20 looks far away — that is an outlier."*

> *"Any data that is far from the generic group is called an outlier."*

**What a box plot shows in a single chart:**
| Element | Meaning |
|---------|---------|
| Bottom whisker | Minimum (excluding outliers) |
| Bottom of box | 25th percentile (Q1) |
| Line inside box | Median (50th percentile) |
| Top of box | 75th percentile (Q3) |
| Top whisker | Maximum (excluding outliers) |
| Dots beyond whiskers | Outliers |

**Why it is powerful:** Instead of plotting 5 separate charts (min, max, median, Q1, Q3), a box plot communicates all of it in a single compact visual — and you can put multiple categories side by side for comparison.

**Note from the instructor on scale:** Don't compare categories with very different price ranges on the same box plot. If one category has a maximum of 100 and another starts from 100K, the scale will make the first category appear completely flat.

---

## What Is Matplotlib?

From the notebook's own markdown cell:

> *Matplotlib is a powerful 2D plotting library in Python. It allows you to create publication-quality figures in a variety of formats (PNG, SVG, PDF, etc.). Commonly used module: `matplotlib.pyplot`, often imported as `plt`.*

### Why Matplotlib Exists

Before Matplotlib, Python had no native plotting. MATLAB dominated scientific visualization. Matplotlib was created to give Python the same plotting power — with a MATLAB-inspired API — while integrating natively with NumPy and Pandas.

### Real-World Analogy
Think of Matplotlib as **Microsoft Word for charts**. Just as Word gives you control over every margin, font, and spacing of a document, Matplotlib gives you control over every pixel, color, axis tick, and label of a chart. High control = high effort; but also high output quality.

### Why Use Matplotlib (from the notebook)?
- Core plotting engine **behind Seaborn and Pandas plots** — when you call `df.plot()`, Matplotlib is running underneath
- Works with **NumPy arrays and Pandas Series/DataFrames natively**
- **Low-level control** of every visual element — ideal for fine-tuning publication figures

### Where Matplotlib Fits in the Ecosystem

```
NumPy / Pandas (data)
       ↓
  Matplotlib (rendering engine)
       ↓                 ↑
  Seaborn           df.plot()
  (high-level)      (Pandas plotting)
```

---

## Key Terminology

| Term | Meaning |
|------|---------|
| **Figure** (`fig`) | The entire window or page — the top-level container for all visual elements |
| **Axes** (`ax`) | A single chart/plot *inside* a Figure — where data is actually drawn |
| **Axis** | The x-axis or y-axis number line on an Axes (NOT the same as "Axes"!) |
| **Artist** | Every visible object on a Figure: lines, text, patches, ticks, labels |
| **pyplot** | The `matplotlib.pyplot` module — provides a stateful, MATLAB-like interface |
| **OO API** | The Object-Oriented API — explicitly creates and controls `fig` and `ax` objects |
| **Backend** | The rendering engine that draws pixels to screen or file (Agg, TkAgg, Qt5Agg, inline) |
| **rcParams** | Global Matplotlib configuration dictionary controlling defaults (fontsize, DPI, style) |
| **DPI** | Dots Per Inch — resolution of a saved figure |
| **Marker** | The symbol drawn at each data point (circle, square, triangle, etc.) |
| **Colormap** | A mapping from numeric values to colors (viridis, ggplot, etc.) |
| **Spine** | The border lines around a plot (top, bottom, left, right) |
| **Legend** | A key showing what each line/bar/scatter group represents |

> **Mnemonic:** "Axes ≠ Axis" — *Axes* is the entire plot area (plural because it has both x and y); *Axis* is just one number line.

---

## The Figure/Axes Architecture

```
Figure (fig)  ← the entire window / page
├── Axes (axs[0])  ← one chart / plot area
│   ├── XAxis
│   │   ├── Major Ticks + Labels
│   │   └── Minor Ticks + Labels
│   ├── YAxis (same structure)
│   ├── Title
│   ├── Lines, Markers, Patches (data representations)
│   ├── Legend
│   ├── Text annotations
│   └── Spines (border lines: top, bottom, left, right)
├── Axes (axs[1])  ← second subplot
│   └── ... (same structure)
└── Suptitle (figure-level title, above all subplots)
```

- `fig` = the canvas/window (like a whiteboard)
- `ax` = one chart drawn on that canvas (like one diagram on the whiteboard)

---

## pyplot (Stateful) API vs Object-Oriented (Stateless) API

This is the **most critical concept** in Matplotlib. The notebook uses **both**.

| Feature | pyplot (Stateful) | OO (Stateless) |
|---------|-------------------|----------------|
| Syntax | `plt.plot(x, y)` | `ax.plot(x, y)` |
| Creates figure/axes | Implicitly (hidden) | Explicitly with `fig, ax = plt.subplots()` |
| Best for | Quick single plots, EDA | Multi-subplot, production, reusable code |
| State tracking | Maintains "current figure" globally | You hold the references yourself |
| Readability | Short | Verbose but explicit |
| MATLAB-like | Yes | No |
| Recommended for production | No | **Yes** |

> **Mnemonic:** "plt = playground, ax = architecture"

**Side-by-side comparison for the same plot:**

```python
# pyplot (stateful) — from notebook
plt.figure(figsize=(12, 6))
plt.plot(monthly_revenue.index, monthly_revenue.values, marker='o')
plt.title('Monthly Revenue Over Time')
plt.xlabel('Month')
plt.ylabel('Revenue')
plt.show()

# OO (stateless) — from notebook's subplot section
fig, ax = plt.subplots(figsize=(12, 6))
ax.plot(monthly_revenue.index, monthly_revenue.values, marker='o')
ax.set_title('Monthly Revenue Over Time')
ax.set_xlabel('Month')
ax.set_ylabel('Revenue')
plt.show()
```

**Rule of thumb:** For a single plot, either works. For **multiple subplots or complex layouts, always use the OO API**.

---

## Dataset Used in This Notebook

The notebook uses the **UCI Online Retail II dataset** — real transactional e-commerce data from a UK-based online retail company spanning 2009–2011.

```
Dataset URL: https://archive.ics.uci.edu/ml/machine-learning-databases/00502/online_retail_II.xlsx
Sheets: 'Year 2009-2010', 'Year 2010-2011'
```

### Columns:
- `Invoice` — Invoice number (prefixed with 'C' for cancellations)
- `StockCode` — Product code
- `Description` — Product name
- `Quantity` — Units purchased (negative for returns/cancellations)
- `InvoiceDate` — Date and time of transaction
- `Price` — Unit price in GBP
- `Customer ID` — Unique customer identifier (may be NaN)
- `Country` — Country of transaction
- `Revenue` *(engineered)* — `Quantity × Price`
- `Month` *(engineered)* — Year-month period string (e.g., `"2009-12"`)

---

## Cleaning & Feature Engineering [BEGINNER]

The notebook performs thorough data cleaning before any visualization. This is best practice — never visualize dirty data.

```python
import pandas as pd

# Load two years of data from separate Excel sheets
df_1 = pd.read_excel("/content/online_retail_II.xlsx", sheet_name='Year 2009-2010')
df_2 = pd.read_excel("/content/online_retail_II.xlsx", sheet_name='Year 2010-2011')

# Combine into a single DataFrame
df = pd.concat([df_1, df_2], ignore_index=True)

# --- CLEANING ---

# 1. Remove rows without a Customer ID
df = df.dropna(subset=['Customer ID'])

# 2. Remove cancellation invoices (Invoice starts with 'C')
df = df[~df['Invoice'].astype(str).str.startswith('C')]

# 3. Remove zero or negative quantities (returns, errors)
df = df[df['Quantity'] > 0]

# 4. Remove zero or negative prices
df = df[df['Price'] > 0]

# --- FEATURE ENGINEERING ---

# Revenue per transaction
df['Revenue'] = df['Quantity'] * df['Price']

# Parse InvoiceDate to proper datetime
df['InvoiceDate'] = pd.to_datetime(df['InvoiceDate'])

# Monthly period as string (e.g., '2009-12') for grouping & plotting
df['Month'] = df['InvoiceDate'].dt.to_period('M').astype(str)
```

**Why `.astype(str)` on Invoice before `.str.startswith('C')`?**
The Invoice column may contain mixed types (int, float, str). Casting to string first avoids TypeErrors.

**Why `ignore_index=True` in concat?**
Resets the index to 0, 1, 2, … for the combined DataFrame, avoiding duplicate index values from the two source DataFrames.

**Shape tracking from the notebook:**
- After concat: 824,364 rows
- After removing cancelled: 805,620 rows
- Cancelled transactions removed: `824364 - 805620 = 18,744`

---

## Aggregations Used for Plotting

```python
# Monthly total revenue — sorted by Month string (lexicographic order = chronological for YYYY-MM)
monthly_revenue = df.groupby('Month')['Revenue'].sum()

# Monthly total quantity
monthly_quantity = df.groupby('Month')['Quantity'].sum().sort_index()

# Top 10 countries by total revenue
country_revenue = df.groupby('Country')['Revenue'].sum().sort_values(ascending=False).head(10)

# Random sample for scatter plot (1000 rows, fixed seed for reproducibility)
sample_df = df.sample(1000, random_state=10000)
```

---

## `plt.figure()` — Creating the Canvas [BEGINNER]

### What It Does
`plt.figure()` creates a new empty **Figure** — the top-level container. Without calling it, `plt.plot()` will create one automatically (implicit behavior of the pyplot API).

**Real-world analogy:** `plt.figure()` is like pulling a blank sheet of paper from a stack before you start drawing. You can specify the paper size and background color.

### Syntax

```python
plt.figure(figsize=(width_inches, height_inches), facecolor='color', dpi=100)
```

| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| `figsize` | tuple `(w, h)` | Figure width and height **in inches** | `(6.4, 4.8)` |
| `facecolor` | str / tuple | Background color of the figure | `'white'` |
| `dpi` | int | Dots per inch for screen rendering | `100` |
| `tight_layout` | bool | Auto-adjust layout on creation | `False` |

### From the Notebook

```python
# Basic canvas — 12 inches wide, 6 inches tall
plt.figure(figsize=(12, 6))

# With white background explicitly set
plt.figure(figsize=(12, 6), facecolor='white')

# Smaller canvas for scatter plot
plt.figure(figsize=(8, 5))

# Bar chart canvas
plt.figure(figsize=(10, 6), facecolor='white')
```

**Why `figsize` matters:**
- Screen display: 12×6 inches × 100 DPI = 1200×600 pixels
- Saving: 12×6 inches × 300 DPI = 3600×1800 pixels — publication quality
- Default `(6.4, 4.8)` is often too small for dashboards or presentations

---

## Line Plot — `plt.plot()` [BEGINNER]

### What It Does
A line plot connects ordered data points with a continuous line. It is the most fundamental plot type for visualizing **trends, changes over time, and continuous relationships**.

**Real-world analogy:** Like a stock market ticker chart — it traces a continuous path showing how a value (stock price, revenue, temperature) changes over time. Each point is a measurement; the line shows the trend between measurements.

### Basic Syntax

```python
plt.plot(x, y, marker='o', color='blue', linestyle='-', linewidth=2, label='series name')
```

| Parameter | Type | Description | Default |
|-----------|------|-------------|---------|
| `x` | array-like | Horizontal data values | required |
| `y` | array-like | Vertical data values | required |
| `marker` | str | Symbol at each data point | `None` (no marker) |
| `color` | str / tuple | Line and marker color | auto-cycled |
| `linestyle` | str | Line style | `'-'` (solid) |
| `linewidth` | float | Line thickness in points | `1.5` |
| `label` | str | Legend label for this line | `None` |
| `alpha` | float `0–1` | Opacity/transparency | `1.0` |
| `markersize` | float | Size of markers | `6.0` |

---

### Example 1: Pandas Series Quick Plot [BEGINNER]

The notebook first shows the simplest possible plot — calling `.plot()` directly on a Pandas Series:

```python
data = pd.Series([100, 200, 150, 300], index=['Q1', 'Q2', 'Q3', 'Q4'])
data.plot()
```

**Visual Output Description:**
A simple line plot with 4 points connected by a line. X-axis shows Q1, Q2, Q3, Q4 (the index labels). Y-axis spans from ~100 to 300. The line rises from Q1 to Q2, dips at Q3, then rises sharply to Q4. Default blue line color, no markers, no title, no axis labels.

**Why this works:**
- `pd.Series.plot()` calls Matplotlib under the hood automatically
- The Series index becomes the X-axis; the values become the Y-axis
- No `plt.show()` needed in Jupyter (inline backend auto-renders)

**Key insight:** `df.plot()` and `series.plot()` are convenience wrappers around Matplotlib. They return an `ax` object you can further customize.

---

### Example 2: Full Line Plot with All Customizations [BEGINNER]

The notebook builds this plot step by step:

```python
import matplotlib.pyplot as plt

# 1. Create canvas: 12 inches wide, 6 inches tall
plt.figure(figsize=(12, 6))

# 2. Draw the line with circle markers
plt.plot(monthly_revenue.index, monthly_revenue.values, marker='o')

# 3. Title and axis labels
plt.title('Monthly Revenue Over Time')
plt.xlabel('Month')
plt.ylabel('Revenue')

# 4. Rotate tick labels to prevent overlap
plt.xticks(rotation=45)
plt.yticks(rotation=45)

# 5. Prevent label clipping at figure edges
plt.tight_layout()

# 6. Show grid lines
plt.grid(True)

# 7. Render the figure
plt.show()
```

**Visual Output Description:**
A line plot spanning ~24 months of data (2009-12 through 2011-12) on the X-axis. Revenue values on Y-axis ranging from roughly 400K to over 1.5M. Blue line with circular markers (default `tab:blue`). X-axis labels are month strings rotated 45°. Grid lines appear both horizontally and vertically. Title "Monthly Revenue Over Time" centered at top.

**Line-by-line breakdown:**
- `plt.figure(figsize=(12, 6))`: Creates a 12×6 inch canvas. Wide format suits time-series with many X-axis labels.
- `plt.plot(monthly_revenue.index, monthly_revenue.values, marker='o')`: Draws a line through each (month, revenue) pair. `marker='o'` places a circle at each data point — critical for identifying individual months.
- `plt.title(...)`: Adds a centered title above the axes. Tells the reader what the chart shows.
- `plt.xlabel(...)` / `plt.ylabel(...)`: Labels the axes. Never skip these — unlabeled axes are a data visualization mistake.
- `plt.xticks(rotation=45)`: Rotates X-axis labels 45 degrees. Essential when labels are long strings (like month names "2009-12") that would otherwise overlap.
- `plt.yticks(rotation=45)`: Rotates Y-axis labels. Less common but used here for visual consistency.
- `plt.tight_layout()`: Automatically adjusts subplot parameters so labels, titles, and tick labels are not clipped by the figure boundary. Always call this before `plt.show()` or `savefig()`.
- `plt.grid(True)`: Adds both horizontal and vertical grid lines, making it easier to read values from the chart.
- `plt.show()`: Flushes the current figure to the screen (required outside Jupyter).

---

### Example 3: Same Plot Without Grid [BEGINNER]

The notebook shows the identical plot but with `plt.grid()` commented out:

```python
plt.figure(figsize=(12, 6))
plt.plot(monthly_revenue.index, monthly_revenue.values, marker='o')
plt.title('Monthly Revenue Over Time')
plt.xlabel('Month')
plt.ylabel('Revenue')
plt.xticks(rotation=45)
plt.yticks(rotation=45)
plt.tight_layout()
# plt.grid(True)   ← commented out
plt.show()
```

**Visual Output Description:**
Identical to Example 2 but without grid lines. The plot area background is a plain white/off-white. This version is cleaner but harder to read precise Y-values.

**Design principle:** Grids are helpful when readers need to read exact values. Remove them for aesthetic/presentation figures where only trends matter (following Tufte's "maximize data-ink ratio" principle).

---

## `plt.grid()` — Grid Lines [BEGINNER]

```python
plt.grid(True)          # Both horizontal and vertical grid lines
plt.grid(False)         # No grid
plt.grid(axis='x')      # Only vertical grid lines (used in bar chart)
plt.grid(axis='y')      # Only horizontal grid lines
plt.grid(True, linestyle='--', alpha=0.7, color='gray')  # Custom style
```

| Parameter | Description |
|-----------|-------------|
| `b` / first arg | `True` to show, `False` to hide |
| `axis` | `'both'` (default), `'x'`, `'y'` |
| `linestyle` | `'-'`, `'--'`, `':'`, `'-.'` |
| `alpha` | Transparency of grid lines |
| `color` | Grid line color |
| `which` | `'major'` (default), `'minor'`, `'both'` |

> From the notebook's bar chart: `plt.grid(axis='x')` — shows only vertical grid lines, appropriate for a horizontal bar chart where you compare values along Y.

---

## `plt.style.use()` — Style Sheets [BEGINNER]

### What It Does
Style sheets change the **global visual theme** of all subsequent plots — colors, backgrounds, font sizes, grid styles, and more. Think of it as switching between PowerPoint themes.

**Real-world analogy:** Like choosing a CSS theme for a website. One line of code changes the entire look and feel without modifying individual plot calls.

### Syntax

```python
plt.style.use('style_name')
```

### Styles Used in the Notebook

```python
# Classic — MATLAB-like white background, default pre-2.0 Matplotlib look
plt.style.use('classic')

# ggplot — R's ggplot2-inspired grey background with colored lines
plt.style.use('ggplot')
```

### Listing All Available Styles

```python
print(plt.style.available)
# Output (subset): ['Solarize_Light2', '_classic_test_patch', '_mpl-gallery',
# '_mpl-gallery-nogrid', 'bmh', 'classic', 'dark_background', 'fast',
# 'fivethirtyeight', 'ggplot', 'grayscale', 'seaborn-v0_8', 'seaborn-v0_8-bright',
# 'seaborn-v0_8-colorblind', 'seaborn-v0_8-dark', ... ]
```

### Popular Styles Reference

| Style | Visual Character | Best For |
|-------|-----------------|----------|
| `'ggplot'` | Grey background, colored lines, R-like | Data analysis presentations |
| `'classic'` | White background, MATLAB-like | Traditional scientific figures |
| `'fivethirtyeight'` | Grey bg, bold colors, FiveThirtyEight aesthetic | Journalism, web dashboards |
| `'dark_background'` | Black background, bright colors | Dark mode presentations |
| `'bmh'` | Bayesian Methods for Hackers style | Statistical reports |
| `'seaborn-v0_8'` | Clean, muted Seaborn palette | Statistical visualization |
| `'grayscale'` | Greyscale only | Print-only figures |

### Temporary Style (Context Manager)

```python
# Apply style ONLY inside the with block — doesn't affect other plots
with plt.style.context('ggplot'):
    plt.plot(monthly_revenue.index, monthly_revenue.values)
    plt.show()
```

> **Important:** `plt.style.use()` is **persistent** — it affects all subsequent plots in the session. Use `plt.style.context()` for temporary changes.

> **Deprecation note:** In Matplotlib 3.6+, style names like `'seaborn'` were renamed to `'seaborn-v0_8'`. Use the new names to avoid deprecation warnings.

---

## Markers [BEGINNER]

Markers are symbols drawn at each data point on a line (or scatter) plot. The notebook includes this reference table as a comment:

| Marker Symbol | String | Description |
|--------------|--------|-------------|
| `'o'` | Circle | ○ — most common, used in notebook |
| `'.'` | Point | · — tiny dot |
| `','` | Pixel | 1-pixel dot — smallest |
| `'x'` | X | × — cross mark |
| `'+'` | Plus | + — cross |
| `'s'` | Square | □ — used in quantity subplot |
| `'D'` | Diamond | ◇ |
| `'^'` | Triangle up | △ |
| `'v'` | Triangle down | ▽ |
| `'*'` | Star | ☆ |
| `'p'` | Pentagon | ⬠ |
| `'h'` | Hexagon | ⬡ |

### Usage in the Notebook

```python
# Revenue line: circle markers
plt.plot(monthly_revenue.index, monthly_revenue.values, marker='o')

# Quantity line: square markers (differentiates the two lines visually)
axs[1].plot(monthly_quantity.index, monthly_quantity.values, marker='s', color='green')
```

> **Design tip:** When plotting multiple lines, use **both** different colors AND different markers to distinguish them (colorblind accessibility).

---

## `plt.xticks()` and `plt.yticks()` — Tick Configuration [BEGINNER]

### What It Does
Controls the **position and appearance of tick labels** on the axes.

```python
plt.xticks(rotation=45)          # Rotate X-axis labels 45°
plt.yticks(rotation=45)          # Rotate Y-axis labels 45°
plt.xticks(rotation=90)          # Full 90° rotation for very long labels
plt.xticks(ticks=[0, 1, 2], labels=['A', 'B', 'C'])  # Custom positions and labels
```

| Parameter | Description |
|-----------|-------------|
| `ticks` | List of tick positions |
| `labels` | List of tick label strings |
| `rotation` | Rotation angle in degrees |
| `fontsize` | Label font size |
| `ha` | Horizontal alignment (`'right'`, `'center'`, `'left'`) |

**Why rotation matters:** Month strings like `"2009-12"` are 7 characters wide. At default 0° rotation, they overlap on a 12-inch wide figure with 24+ months. 45° rotation makes them readable without requiring a huge figure.

> **OO API equivalent:** `ax.set_xticklabels(ax.get_xticklabels(), rotation=45)` or `ax.tick_params(axis='x', rotation=45)`

---

## `plt.tight_layout()` — Layout Adjustment [BEGINNER]

### What It Does
Automatically adjusts the subplot parameters so **labels, titles, and tick labels don't get clipped** by the figure boundary.

```python
plt.tight_layout()
plt.tight_layout(pad=1.5)    # Extra padding around figure edges
plt.tight_layout(rect=[0, 0, 1, 0.95])  # Leave room for suptitle
```

**Real-world analogy:** Like the "Fit to Page" button in Word — it automatically adjusts margins so nothing is cut off when printing.

**When to use:** Always call it before `plt.show()` or `plt.savefig()`. It prevents the common "X-axis labels cut off" problem.

**OO API alternative:** Pass `constrained_layout=True` when creating the figure:

```python
fig, ax = plt.subplots(constrained_layout=True)
```

`constrained_layout=True` is **more robust** than `tight_layout()` for complex subplot grids.

---

## `plt.title()`, `plt.xlabel()`, `plt.ylabel()` — Labels [BEGINNER]

### From the Notebook

```python
plt.title('Monthly Revenue Over Time')
plt.xlabel('Month')
plt.ylabel('Revenue')
```

### Full Syntax

```python
plt.title('Title Text', fontsize=14, fontweight='bold', color='navy', pad=10)
plt.xlabel('X Label', fontsize=12, labelpad=5)
plt.ylabel('Y Label', fontsize=12, labelpad=5)
```

| Parameter | Description |
|-----------|-------------|
| First arg | The text string |
| `fontsize` | Font size in points |
| `fontweight` | `'bold'`, `'normal'`, `'light'` |
| `color` | Text color |
| `pad` | Extra space between label and axis (in points) |
| `loc` | Title alignment: `'left'`, `'center'` (default), `'right'` |

### OO API Equivalents

| pyplot | OO API |
|--------|--------|
| `plt.title('...')` | `ax.set_title('...')` |
| `plt.xlabel('...')` | `ax.set_xlabel('...')` |
| `plt.ylabel('...')` | `ax.set_ylabel('...')` |

---

## `plt.text()` — Adding Data Labels on Points [INTERMEDIATE]

### What It Does
Places arbitrary text at a specific (x, y) location on the current axes. Used in the notebook to add **revenue values directly above each data point** on the line chart.

**Real-world analogy:** Like adding callout text boxes in PowerPoint that point to specific chart data — but positioned precisely in data coordinates.

### Syntax

```python
plt.text(x, y, s, ha='center', va='bottom', fontsize=8, color='black', fontdict=None)
```

| Parameter | Description |
|-----------|-------------|
| `x`, `y` | Position in **data coordinates** |
| `s` | The text string |
| `ha` | Horizontal alignment: `'center'`, `'left'`, `'right'` |
| `va` | Vertical alignment: `'bottom'`, `'top'`, `'center'` |
| `fontsize` | Font size |
| `fontdict` | Dictionary of font properties (see next section) |
| `color` | Text color |
| `rotation` | Text rotation in degrees |

### From the Notebook — Simple Data Labels [INTERMEDIATE]

```python
plt.figure(figsize=(12, 6))
plt.style.use('classic')
plt.plot(monthly_revenue.index, monthly_revenue.values, marker='o')

# Iterate over each data point and add a label above it
for i, value in enumerate(monthly_revenue.values):
    plt.text(
        monthly_revenue.index[i],   # X position: the month string
        value + 10000,              # Y position: slightly ABOVE the data point
        f'{int(value)}',            # Label text: revenue as integer string
        ha='center',                # Center the text horizontally over the point
        fontsize=8                  # Small font so labels don't crowd each other
    )

plt.title('Monthly Revenue Over Time')
plt.xlabel('Month')
plt.ylabel('Revenue')
plt.xticks(rotation=45)
plt.yticks(rotation=45)
plt.tight_layout()
plt.grid(True)
plt.show()
```

**Visual Output Description:**
Same line plot as before, but now each circle marker has an integer revenue value printed just above it (offset by 10,000 units on the Y-axis). Labels are centered horizontally over their respective data points. The `value + 10000` offset prevents the text from overlapping the circle marker.

**Line-by-line breakdown:**
- `enumerate(monthly_revenue.values)`: Returns `(index, value)` pairs — `i` is the integer position (0, 1, 2, …), `value` is the revenue amount.
- `monthly_revenue.index[i]`: Gets the month string at position `i` — used as the X coordinate for the text.
- `value + 10000`: Offsets Y position upward by 10,000 units so text appears above the marker, not on top of it.
- `f'{int(value)}'`: Formats the float revenue as an integer string (no decimal places) for clean display.
- `ha='center'`: Centers the text string horizontally over the data point.

**When to use data labels:**
- When exact values matter (executive dashboards, financial reports)
- When readers can't easily read values from the Y-axis
- On bar charts (very common), line charts (less common — use sparingly on busy charts)

**When NOT to use:**
- Dense data (many points) — labels will overlap and become unreadable
- When a tooltip-based interactive chart (Plotly) would be more appropriate

---

## `fontdict` — Font Properties Dictionary [INTERMEDIATE]

### What It Does
A dictionary that bundles font styling properties for text labels, passed to `plt.text()` or any other text function via the `fontdict` parameter.

**Real-world analogy:** Like a CSS class for text — define the style once, reuse it across many text calls.

### From the Notebook

```python
# Define font style once
font = {
    'family': 'serif',       # Font family: 'serif', 'sans-serif', 'monospace'
    'color': 'black',        # Text color
    'weight': 'bold',        # Font weight: 'bold', 'normal', 'light'
    'size': 10               # Font size in points
}

plt.figure(figsize=(12, 6), facecolor='white')
plt.style.use('classic')
plt.plot(monthly_revenue.index, monthly_revenue.values, marker='o', color='green')

for i, value in enumerate(monthly_revenue.values):
    plt.text(
        monthly_revenue.index[i],
        value + 10000,
        f'{int(value)}',
        ha='center',
        fontdict=font          # Apply the font dictionary
    )

plt.title('Monthly Revenue Over Time')
plt.xlabel('Month')
plt.ylabel('Revenue')
plt.xticks(rotation=45)
plt.yticks(rotation=45)
plt.tight_layout()
plt.grid(True)
plt.show()
```

**Visual Output Description:**
Same line as Examples 2–3 but now the line is **green** (specified with `color='green'`). Data labels above each point are **bold serif font** in black, size 10 — slightly more prominent than the simple `fontsize=8` labels. The figure background is explicitly white (`facecolor='white'`).

**fontdict keys:**

| Key | Values | Description |
|-----|--------|-------------|
| `'family'` | `'serif'`, `'sans-serif'`, `'monospace'` | Font family |
| `'color'` | Any color string | Text color |
| `'weight'` | `'bold'`, `'normal'`, `'light'`, `'heavy'` | Font weight |
| `'size'` | Integer or string (`'small'`, `'large'`) | Font size |
| `'style'` | `'normal'`, `'italic'`, `'oblique'` | Font style |
| `'stretch'` | `'condensed'`, `'normal'`, `'expanded'` | Character spacing |

---

## Multiple Lines on One Plot [BEGINNER]

### What It Does
Multiple `plt.plot()` calls on the same figure draw multiple lines, automatically cycling through the color palette.

**Real-world analogy:** Like overlaying two time-series on the same stock chart — "Actual Revenue" and "Projected Revenue" on the same axes.

### From the Notebook

```python
font = {
    'family': 'serif',
    'color': 'black',
    'weight': 'bold',
    'size': 10
}

plt.figure(figsize=(12, 6), facecolor='white')
plt.style.use('classic')

# First line: actual monthly revenue (green)
plt.plot(monthly_revenue.index, monthly_revenue.values, marker='o', color='green')

for i, value in enumerate(monthly_revenue.values):
    plt.text(monthly_revenue.index[i], value + 10000, f'{int(value)}', ha='center', fontdict=font)

# Second line: hypothetical "premium" revenue (default blue, offset +1,000,000)
plt.plot(monthly_revenue.index, monthly_revenue.values + 1000000, marker='o')

for i, value in enumerate(monthly_revenue.values):
    plt.text(monthly_revenue.index[i], (value + 1000000) + 10000,
             f'{int(value + 1000000)}', ha='center', fontdict=font)

plt.title('Monthly Revenue Over Time')
plt.xlabel('Month')
plt.ylabel('Revenue')
plt.xticks(rotation=45)
plt.yticks(rotation=45)
plt.tight_layout()
plt.grid(True)
plt.show()
```

**Visual Output Description:**
Two parallel line curves on the same plot. The lower green line shows actual monthly revenue values. The upper blue line (default color) shows the same data shifted upward by 1,000,000 — used here as a demonstration of plotting a second series. Both lines have circle markers. Each point on both lines has a bold serif data label. The Y-axis now spans from roughly 400K to over 2.5M to accommodate both lines.

**Key mechanism:** Each `plt.plot()` call adds a new `Line2D` artist to the **current axes**. Matplotlib automatically assigns the next color from its color cycle (`tab:blue`, `tab:orange`, `tab:green`, `tab:red`, …).

**Best practice:** Always add a `label=` parameter and call `plt.legend()` when plotting multiple lines so readers can tell them apart.

---

## Bar Chart — `plt.bar()` [BEGINNER]

### What It Does
A bar chart displays categorical data as rectangular bars whose **height is proportional to the value**. Best for **comparing values across categories**.

**Real-world analogy:** Like a leaderboard — each bar represents a competitor (country), and the height shows their score (revenue).

### Syntax

```python
plt.bar(x, height, width=0.8, color='skyblue', edgecolor='black', alpha=1.0, label='name')
plt.barh(y, width, ...)   # Horizontal bars
```

| Parameter | Description | Default |
|-----------|-------------|---------|
| `x` | Categories or positions | required |
| `height` | Bar heights (values) | required |
| `width` | Bar width (fraction of spacing) | `0.8` |
| `color` | Bar fill color | auto-cycled |
| `edgecolor` | Bar border color | `None` |
| `alpha` | Transparency | `1.0` |
| `label` | Legend label | `None` |
| `align` | Bar alignment: `'center'`, `'edge'` | `'center'` |

### From the Notebook [BEGINNER]

```python
# Group by country and sum revenue, take top 10
country_revenue = df.groupby('Country')['Revenue'].sum().sort_values(ascending=False).head(10)

plt.figure(figsize=(10, 6), facecolor='white')
plt.style.use('ggplot')

plt.bar(country_revenue.index, country_revenue.values, color='skyblue')

plt.title("Top 10 Countries by Revenue")
plt.xlabel("Country")
plt.ylabel("Total Revenue")
plt.xticks(rotation=45)
plt.tight_layout()
plt.grid(axis='x')   # Only vertical grid lines for a vertical bar chart

plt.show()
```

**Visual Output Description:**
10 vertical bars with a grey ggplot background. Countries on X-axis (rotated 45°), Total Revenue on Y-axis. The United Kingdom bar dwarfs all others (it's a UK-based retailer), reaching several million in revenue. Remaining 9 countries have comparatively small bars. Bars are sky blue. Vertical grid lines (`axis='x'`) appear between the bars.

**Line-by-line breakdown:**
- `country_revenue = df.groupby('Country')['Revenue'].sum().sort_values(ascending=False).head(10)`: Groups by Country, sums Revenue, sorts descending, keeps top 10.
- `plt.bar(country_revenue.index, country_revenue.values, color='skyblue')`: Draws one bar per country. Index = category labels (X), values = bar heights (Y).
- `plt.grid(axis='x')`: Shows only vertical grid lines. For vertical bar charts, horizontal grids (`axis='y'`) are more useful for reading values — this is a stylistic choice.

**When to use bar charts:**
- Comparing discrete categories (countries, products, teams)
- Showing ranking (sorted bars = immediate visual rank)

**When NOT to use:**
- Continuous data → use line plot
- Too many categories (>15) → use horizontal bar or treemap
- Time series → use line plot (bar charts hide the trend)

**Pro tip:** Sort bars by value (`sort_values(ascending=False)`) before plotting to make rankings immediately obvious.

---

## Scatter Plot — `plt.scatter()` [BEGINNER]

### What It Does
A scatter plot shows **individual data points** as dots in a 2D space, one dimension on each axis. Best for **exploring relationships and correlations** between two numeric variables.

**Real-world analogy:** Like plotting GPS coordinates — each dot is one observation in a two-dimensional space. Clusters reveal patterns; outliers appear as isolated dots.

### Syntax

```python
plt.scatter(x, y, s=50, c='purple', alpha=0.5, marker='o', cmap='viridis',
            edgecolors='black', linewidths=0.5, label='series')
```

| Parameter | Description | Default |
|-----------|-------------|---------|
| `x`, `y` | Horizontal and vertical data | required |
| `s` | Marker size in points² | `20` |
| `c` | Color (string, or array for colormapping) | auto |
| `alpha` | Transparency (0=transparent, 1=opaque) | `1.0` |
| `marker` | Marker shape | `'o'` |
| `cmap` | Colormap (when `c` is an array of values) | `'viridis'` |
| `edgecolors` | Marker border color | `None` |
| `label` | Legend label | `None` |

### From the Notebook [BEGINNER]

```python
# Use a random 1000-row sample for performance and clarity
sample_df = df.sample(1000, random_state=10000)

plt.figure(figsize=(8, 5))
plt.scatter(sample_df['Quantity'], sample_df['Revenue'], alpha=0.5, color='purple')
plt.xlabel("Quantity")
plt.ylabel("Revenue")
plt.title("Quantity vs Revenue")
plt.grid(True)
plt.tight_layout()
plt.show()
```

**Visual Output Description:**
1,000 purple dots scattered across the plot area. X-axis is "Quantity" ranging from 1 to a few hundred; Y-axis is "Revenue" ranging from near 0 to several thousand. Most dots cluster near the origin (small quantities, small revenue). A positive linear trend is visible — higher quantities tend to generate higher revenue, as expected (Revenue = Quantity × Price). Some outliers appear in the upper right (bulk purchases of high-price items). `alpha=0.5` makes overlapping dots partially transparent, revealing density.

**Line-by-line breakdown:**
- `df.sample(1000, random_state=10000)`: Randomly samples 1,000 rows. `random_state` ensures reproducible results. Using a sample avoids overplotting 800K+ dots.
- `plt.scatter(sample_df['Quantity'], sample_df['Revenue'], alpha=0.5, color='purple')`: Each row becomes one dot. `alpha=0.5` — 50% transparency — helps visualize overplotting (where many dots overlap, the area appears darker purple).
- `plt.grid(True)`: Reference grid helps estimate coordinates.

**`plt.plot()` vs `plt.scatter()` for this data:**

| Feature | `plt.plot()` | `plt.scatter()` |
|---------|-------------|-----------------|
| Connects points | Yes (line) | No |
| Per-point size variation | No | Yes (`s=` array) |
| Per-point color variation | No | Yes (`c=` array + `cmap`) |
| Performance (large N) | Faster | Slower |
| Use case | Time series, trends | Relationships, distributions |

**Why sample?**
Plotting all 800K+ rows as scatter dots would:
1. Be extremely slow to render
2. Result in solid blobs of color (complete overplotting)
3. Waste memory

For large datasets, alternatives include: `plt.hexbin()`, `datashader`, or `alpha=0.01` with all points.

---

## Subplots — `plt.subplots()` [INTERMEDIATE]

### What It Does
`plt.subplots()` creates a **Figure with a grid of Axes** simultaneously. This is the standard OO API entry point and is the **recommended way** to create any plot in production code.

**Real-world analogy:** Like creating a dashboard with multiple chart panels — each panel is an `Axes`, and the full dashboard is the `Figure`. Instead of creating each panel manually, `subplots()` creates them all at once in a grid.

### Syntax

```python
fig, axs = plt.subplots(nrows=1, ncols=1, figsize=(w, h), sharex=False, sharey=False,
                        constrained_layout=False)
```

| Parameter | Description | Default |
|-----------|-------------|---------|
| `nrows` | Number of subplot rows | `1` |
| `ncols` | Number of subplot columns | `1` |
| `figsize` | Figure size in inches `(width, height)` | `(6.4, 4.8)` |
| `sharex` | Share X-axis across all subplots: `True`, `False`, `'col'`, `'row'` | `False` |
| `sharey` | Share Y-axis across all subplots | `False` |
| `constrained_layout` | Auto-adjust layout (preferred over `tight_layout`) | `False` |

**Return values:**
- `fig`: The `Figure` object
- `axs`: A single `Axes` (if 1×1) or a NumPy array of `Axes` objects

### Accessing Subplots

```python
fig, axs = plt.subplots(2, 1)
axs[0]   # Top subplot
axs[1]   # Bottom subplot

fig, axs = plt.subplots(2, 2)
axs[0, 0]   # Top-left
axs[0, 1]   # Top-right
axs[1, 0]   # Bottom-left
axs[1, 1]   # Bottom-right
```

---

### Example 1: Two Stacked Subplots — Revenue and Quantity [INTERMEDIATE]

```python
monthly_quantity = df.groupby('Month')['Quantity'].sum().sort_index()

fig, axs = plt.subplots(nrows=2, ncols=1, figsize=(10, 8), sharex=True)

# --- Top subplot: Monthly Revenue ---
axs[0].plot(monthly_revenue.index, monthly_revenue.values,
            marker='o', color='blue', label='Revenue-label')
axs[0].set_title("Monthly Revenue")
axs[0].set_xlabel("Month")
axs[0].set_ylabel("Revenue")
axs[0].legend()
axs[0].grid(True)

# --- Bottom subplot: Monthly Quantity ---
axs[1].plot(monthly_quantity.index, monthly_quantity.values,
            marker='s', color='green', label='Quantity-label')
axs[1].set_title("Monthly Quantity")
axs[1].set_xlabel("Month")
axs[1].set_ylabel("Quantity")
axs[1].grid(True)

plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

**Visual Output Description:**
Two vertically stacked line plots sharing the same X-axis (months). Top plot: blue line with circle markers — Monthly Revenue — spanning ~24 months. Bottom plot: green line with square markers — Monthly Quantity. Both plots have grid lines. The top subplot has a legend showing "Revenue-label". X-axis tick labels appear only once at the bottom (because `sharex=True` hides the redundant labels on the top plot).

**Key parameter — `sharex=True`:**
Ensures both subplots share the same X-axis. When you zoom/pan one, the other moves together. It also automatically **hides redundant X-axis tick labels** on interior subplots (only the bottom subplot shows labels). This is essential for vertically stacked time-series comparisons.

**OO API method names (vs pyplot):**

| pyplot | OO (ax.method) |
|--------|----------------|
| `plt.title()` | `ax.set_title()` |
| `plt.xlabel()` | `ax.set_xlabel()` |
| `plt.ylabel()` | `ax.set_ylabel()` |
| `plt.legend()` | `ax.legend()` |
| `plt.grid()` | `ax.grid()` |
| `plt.plot()` | `ax.plot()` |

> **Why `set_` prefix in OO API?** Convention: setter methods use `set_`, getter methods use `get_`. This is consistent Python OO design. `ax.set_title('foo')` sets the title; `ax.get_title()` retrieves it.

---

### Example 2: Two Lines in One Subplot + Another Subplot [INTERMEDIATE]

```python
fig, axs = plt.subplots(nrows=2, ncols=1, figsize=(10, 8), sharex=True)

# --- Top subplot: BOTH Revenue and Quantity together ---
axs[0].plot(monthly_revenue.index, monthly_revenue.values,
            marker='o', color='blue', label='Revenue')
axs[0].plot(monthly_quantity.index, monthly_quantity.values,
            marker='s', color='green', label='Quantity')
axs[0].set_title("Monthly Revenue")
axs[0].set_xlabel("Month")
axs[0].set_ylabel("Revenue")
axs[0].legend()
axs[0].grid(True)

# --- Bottom subplot: Quantity only ---
axs[1].plot(monthly_quantity.index, monthly_quantity.values,
            marker='s', color='green', label='Quantity-label')
axs[1].set_title("Monthly Quantity")
axs[1].set_xlabel("Month")
axs[1].set_ylabel("Quantity")
axs[1].grid(True)

plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

**Visual Output Description:**
Top subplot now shows **two lines** — blue Revenue and green Quantity — on the same axes. The legend in the top subplot labels both. Bottom subplot shows only the Quantity line (same as before). This demonstrates how multiple `ax.plot()` calls add successive lines to the same Axes.

**Important note on scale:** Revenue (in GBP, millions range) and Quantity (unit counts, thousands range) have **very different scales**. Plotting them on the same Y-axis makes the Quantity line appear nearly flat at the bottom. For production: use `ax.twinx()` to add a secondary Y-axis.

---

## `ax.legend()` — Legends [BEGINNER]

### What It Does
Displays a box identifying each plotted series by color/marker and label. The legend reads the `label=` parameter from each `ax.plot()` / `plt.plot()` call.

### Syntax

```python
ax.legend()                                    # Auto-generate from label= kwargs
ax.legend(loc='upper right')                   # Specify position
ax.legend(loc='best')                          # Matplotlib chooses least-overlapping position
ax.legend(ncol=2)                              # Two-column legend
ax.legend(bbox_to_anchor=(1.05, 1), loc='upper left')   # Outside the plot area
ax.legend(frameon=False)                       # No border box
ax.legend(fancybox=True, shadow=True)          # Rounded corners with shadow
```

| `loc` value | Position |
|-------------|----------|
| `'best'` | Auto-placed (default) |
| `'upper right'` | Top-right corner |
| `'upper left'` | Top-left corner |
| `'lower right'` | Bottom-right |
| `'lower left'` | Bottom-left |
| `'center'` | Centered |

### From the Notebook

```python
# In first subplot — legend auto-reads 'Revenue-label' from label= kwarg
axs[0].legend()

# In second subplot — legend in separate plot
axs[0].plot(..., label='Revenue')
axs[0].plot(..., label='Quantity')
axs[0].legend()   # Shows both labels
```

**Important:** If you don't set `label=` in your `plot()` call, `ax.legend()` will show "Line 1", "Line 2", etc. Always set meaningful labels.

---

## Saving Figures — `plt.savefig()` [BEGINNER]

### What It Does
Saves the current figure to a file. Supports multiple formats. This is the standard way to export charts for reports, papers, presentations, and dashboards.

**Real-world analogy:** Like File → Export in Excel or Photoshop — you specify the filename, format, and quality settings.

### Syntax

```python
plt.savefig(fname, dpi=None, bbox_inches=None, facecolor='auto', transparent=False, format=None)
```

| Parameter | Description | Recommended Value |
|-----------|-------------|------------------|
| `fname` | File path and name (extension determines format) | `'plot.png'`, `'plot.pdf'` |
| `dpi` | Resolution in dots per inch | `300` for print, `150` for web |
| `bbox_inches` | Crop figure: `'tight'` removes extra whitespace | `'tight'` (always use) |
| `facecolor` | Background color of saved figure | `'white'` |
| `transparent` | Save with transparent background (PNG only) | `False` |
| `format` | Override format detection from filename | `'png'`, `'pdf'`, `'svg'` |

### From the Notebook

```python
plt.figure(figsize=(8, 4))
plt.plot(monthly_revenue.index, monthly_revenue.values, marker='o')
plt.title("Monthly Revenue")
plt.xlabel("Month")
plt.ylabel("Revenue")
plt.tight_layout()

# Save as PNG at 300 DPI (print quality)
plt.savefig("monthly_revenue_plot.png", dpi=300)

# Close the figure to free memory
plt.close()
```

**Visual Output:** Creates `monthly_revenue_plot.png` in the current working directory. 8 inches × 4 inches × 300 DPI = 2400 × 1200 pixel PNG file. High enough resolution for print or large-format presentation slides.

**Line-by-line breakdown:**
- `plt.savefig("monthly_revenue_plot.png", dpi=300)`: Saves to the current directory. The `.png` extension sets the format automatically. `dpi=300` is the standard for print-quality figures.
- `plt.close()`: **Closes and destroys the figure from memory**. Critical when saving figures in loops — without it, Matplotlib accumulates figures and you'll get a memory warning / crash on large batches.

### File Format Guide

| Format | Extension | Scalable? | Best For |
|--------|-----------|-----------|----------|
| PNG | `.png` | No (raster) | Web, presentations (300 DPI) |
| PDF | `.pdf` | Yes (vector) | Publications, printing |
| SVG | `.svg` | Yes (vector) | Web (editable in browsers) |
| EPS | `.eps` | Yes (vector) | LaTeX documents |
| JPEG | `.jpg` | No (raster) | Not recommended (lossy, poor for plots) |

**Best practice:**
- Use `PDF` or `SVG` for academic publications (vector = infinitely scalable)
- Use `PNG` at 300 DPI for PowerPoint, Google Slides
- Always add `bbox_inches='tight'` to avoid label clipping in the saved file

```python
# Production-quality save with all best practices
plt.savefig("monthly_revenue_plot.pdf", bbox_inches='tight', dpi=300)
```

---

## `plt.close()` — Memory Management [BEGINNER]

```python
plt.close()       # Close the current (most recently created) figure
plt.close(fig)    # Close a specific figure object
plt.close('all')  # Close ALL open figures — use this in loops
```

**Why it matters:** Each Matplotlib figure consumes memory. In scripts or notebooks that generate many plots (e.g., one figure per month, per product, per country), failing to close figures causes:
- Memory warnings: *"More than 20 figures have been opened"*
- Memory leaks → eventual crash or slowdown

**Pattern for batch figure generation:**

```python
for country in country_list:
    fig, ax = plt.subplots(figsize=(8, 4))
    ax.plot(...)
    ax.set_title(country)
    plt.savefig(f"{country}_revenue.png", dpi=150, bbox_inches='tight')
    plt.close(fig)   # ← CRITICAL: free memory after each save
```

---

## `plt.show()` — Display [BEGINNER]

```python
plt.show()   # Render and display the current figure
```

- **In Jupyter/Colab:** Usually not needed — inline backend renders automatically. However, calling it explicitly ensures the figure is finalized and avoids double-rendering issues.
- **In scripts (.py files):** **Required** — without it, the plot window never appears and the script exits.
- **After `plt.show()`:** The current figure is "consumed" — subsequent `plt.plot()` calls start a new figure.

**Double-rendering issue in Jupyter:** If you call both `plt.show()` AND don't use `plt.close()`, Jupyter may render the figure twice (once when the cell returns the figure object, once when `plt.show()` is called). Fix: call `plt.show()` explicitly and/or suppress with `;` at the end of the last line.

---

## Quick Reference: pyplot vs OO API

| Task | pyplot (stateful) | OO (stateless) |
|------|-------------------|----------------|
| Create figure | `plt.figure(figsize=...)` | `fig, ax = plt.subplots(figsize=...)` |
| Line plot | `plt.plot(x, y)` | `ax.plot(x, y)` |
| Bar chart | `plt.bar(x, h)` | `ax.bar(x, h)` |
| Scatter | `plt.scatter(x, y)` | `ax.scatter(x, y)` |
| Title | `plt.title('...')` | `ax.set_title('...')` |
| X label | `plt.xlabel('...')` | `ax.set_xlabel('...')` |
| Y label | `plt.ylabel('...')` | `ax.set_ylabel('...')` |
| Legend | `plt.legend()` | `ax.legend()` |
| Grid | `plt.grid(True)` | `ax.grid(True)` |
| X ticks | `plt.xticks(rotation=45)` | `ax.tick_params(axis='x', rotation=45)` |
| Save | `plt.savefig('file.png')` | `fig.savefig('file.png')` |
| Show | `plt.show()` | `plt.show()` |
| Close | `plt.close()` | `plt.close(fig)` |
| Layout | `plt.tight_layout()` | `fig.tight_layout()` |

---

## Plot Type Selection Guide

| Data & Goal | Recommended Plot | Notebook Function |
|-------------|-----------------|------------------|
| Trend over time | Line plot | `plt.plot()` |
| Compare categories | Bar chart | `plt.bar()` |
| Correlation between two numerics | Scatter plot | `plt.scatter()` |
| Multiple metrics side by side | Subplots | `plt.subplots()` |
| Quick DataFrame overview | Pandas plot | `df.plot()` / `series.plot()` |

---

## Customization Reference

### Colors Used in the Notebook

| Code | Color |
|------|-------|
| `color='skyblue'` | Light blue — used for bar chart |
| `color='purple'` | Purple — used for scatter plot |
| `color='green'` | Green — used for first line in multi-line plot |
| `color='blue'` | Blue — used for Revenue subplot |
| `facecolor='white'` | White figure background |

### Color Specification Methods

| Method | Example | Use Case |
|--------|---------|----------|
| Named color | `'skyblue'`, `'green'`, `'purple'` | Quick styling |
| Hex | `'#FF5733'` | Precise brand colors |
| RGB tuple | `(0.1, 0.2, 0.5)` | Computed colors |
| RGBA tuple | `(0.1, 0.2, 0.5, 0.8)` | With transparency |
| Tableau prefix | `'tab:blue'`, `'tab:orange'` | Default color cycle |

### Line Styles

| Style | Shorthand | Example |
|-------|-----------|---------|
| Solid | `'-'` | `plt.plot(x, y, linestyle='-')` |
| Dashed | `'--'` | `plt.plot(x, y, linestyle='--')` |
| Dash-dot | `'-.'` | `plt.plot(x, y, linestyle='-.')` |
| Dotted | `':'` | `plt.plot(x, y, linestyle=':')` |

### Format String Shorthand
Combine `[color][marker][linestyle]` in one string:

```python
plt.plot(x, y, 'go--')   # Green, circle marker, dashed line
plt.plot(x, y, 'bs-')    # Blue, square marker, solid line
plt.plot(x, y, 'r^:')    # Red, triangle-up marker, dotted line
```

Color letters: `b`(blue), `g`(green), `r`(red), `c`(cyan), `m`(magenta), `y`(yellow), `k`(black), `w`(white)

---

## Styles & Themes Deep Dive

### `plt.style.use('classic')` — Used in the Notebook

- White background (MATLAB-like)
- Default Matplotlib pre-v2.0 look
- Black axis lines and ticks
- No background color on axes

### `plt.style.use('ggplot')` — Used in the Notebook

- Grey background on the axes area
- Colored grid lines
- Softer, warmer color palette
- Resembles R's `ggplot2` aesthetic
- Used in the notebook for the bar chart

### Comparison of the Two Styles Used

| Aspect | `'classic'` | `'ggplot'` |
|--------|-------------|------------|
| Axes background | White | Grey |
| Grid | Off by default | On by default |
| Colors | Old defaults (MATLAB-like) | Warm, richer palette |
| Look | Traditional, minimal | Modern, journalistic |
| Best for | Academic papers | Business dashboards |

---

## Before Matplotlib vs After Matplotlib

**Task:** Communicate monthly revenue trends to stakeholders

**Before Matplotlib:** Export data to Excel → Insert Chart → Manually format colors → Copy-paste screenshot into PowerPoint. Fragile, non-reproducible, can't be automated.

**After Matplotlib:**

```python
plt.figure(figsize=(12, 6))
plt.plot(monthly_revenue.index, monthly_revenue.values, marker='o', color='green')
plt.title('Monthly Revenue Over Time')
plt.xlabel('Month')
plt.ylabel('Revenue (GBP)')
plt.xticks(rotation=45)
plt.tight_layout()
plt.grid(True)
plt.savefig('monthly_revenue.png', dpi=300, bbox_inches='tight')
```

**Benefit:** Reproducible, automatable, version-controllable, publication-quality. Run the same script on next month's data and get an updated chart instantly.

---

## How Matplotlib Works Internally

### Rendering Pipeline

```
Python code (plt.plot, ax.bar, ...)
         ↓
    Artist Tree (Figure → Axes → Lines, Patches, Text)
         ↓
    draw() call
         ↓
    Backend renderer (Agg, TkAgg, Qt5Agg, inline)
         ↓
    Output (screen window / PNG file / SVG file / PDF file)
```

### Backend System

| Backend | Type | Use Case |
|---------|------|----------|
| `Agg` | Non-interactive (file) | Server-side rendering, `savefig()` only |
| `TkAgg` | Interactive (GUI) | Desktop scripts, popup windows |
| `Qt5Agg` | Interactive (GUI) | Desktop, Qt applications |
| `inline` | Non-interactive | Jupyter Notebooks |
| `widget` | Interactive | JupyterLab |
| `WebAgg` | Interactive (browser) | Web-based Matplotlib |

The backend is set automatically but can be overridden:

```python
import matplotlib
matplotlib.use('Agg')   # Must be called BEFORE importing pyplot
import matplotlib.pyplot as plt
```

### Coordinate Systems

| System | Origin | Range | Use Case |
|--------|--------|-------|----------|
| **Data coordinates** | Data scale | Data values | `ax.plot(x, y)` — your data units |
| **Axes coordinates** | Bottom-left of axes | 0 to 1 | `ax.text(0.5, 0.5, ...)` centered in axes |
| **Figure coordinates** | Bottom-left of figure | 0 to 1 | `fig.text(0.5, 0.98, ...)` centered in figure |
| **Display coordinates** | Bottom-left of screen | Pixels | Rarely used directly |

In `plt.text()`, the default is **data coordinates** — `value + 10000` in the notebook means 10,000 units above the data point on the Y-axis in data (revenue) scale.

---

## Common Mistakes & Debugging

### Mistake 1: Calling `plt.style.use()` After `plt.figure()`

**Wrong:**
```python
plt.figure(figsize=(12, 6))
plt.style.use('classic')   # ← Too late! Figure already created with default style
plt.plot(...)
```

**Correct:**
```python
plt.style.use('classic')   # ← Must come BEFORE creating the figure
plt.figure(figsize=(12, 6))
plt.plot(...)
```

**Why it fails:** `plt.figure()` locks in the style at creation time. Style changes after figure creation may partially apply or not apply at all.

---

### Mistake 2: Forgetting `label=` Before Calling `legend()`

**Wrong:**
```python
ax.plot(monthly_revenue.index, monthly_revenue.values, marker='o', color='blue')
ax.legend()   # Shows "Line 1" — not useful
```

**Correct:**
```python
ax.plot(monthly_revenue.index, monthly_revenue.values, marker='o', color='blue', label='Revenue')
ax.legend()   # Shows "Revenue" ✓
```

---

### Mistake 3: X-axis Labels Clipped After Rotation

**Problem:** After `plt.xticks(rotation=45)`, the bottom part of the labels disappears.

**Wrong (no layout adjustment):**
```python
plt.xticks(rotation=45)
plt.show()   # Labels clipped at bottom
```

**Correct:**
```python
plt.xticks(rotation=45)
plt.tight_layout()   # ← Adjusts margins to fit rotated labels
plt.show()
```

---

### Mistake 4: Memory Leak in Loops (Forgetting `plt.close()`)

**Wrong:**
```python
for country in countries:
    plt.figure()
    plt.plot(...)
    plt.savefig(f"{country}.png")
    # Forgot plt.close() — figure stays in memory!
```

**Correct:**
```python
for country in countries:
    fig, ax = plt.subplots()
    ax.plot(...)
    fig.savefig(f"{country}.png", bbox_inches='tight')
    plt.close(fig)   # ← Free memory immediately
```

**Error you'll see:** `RuntimeWarning: More than 20 figures have been opened. Figures created through the pyplot interface (plt.figure, plt.subplots, ...) are retained until explicitly closed and may consume too much memory.`

---

### Mistake 5: Plotting on Wrong Scale (Revenue + Quantity on Same Y-axis)

**Problem (from notebook's Example 2 subplots):** The notebook plots both Revenue and Quantity on the same Y-axis in `axs[0]`. Revenue is in millions (GBP); Quantity is in thousands (units). Result: Quantity line appears nearly flat.

**Solution for dual scales:** Use `ax.twinx()` to create a second Y-axis:

```python
fig, ax1 = plt.subplots(figsize=(12, 6))
ax2 = ax1.twinx()   # Create second Y-axis sharing the same X-axis

ax1.plot(monthly_revenue.index, monthly_revenue.values, color='blue', label='Revenue')
ax2.plot(monthly_quantity.index, monthly_quantity.values, color='green', label='Quantity')

ax1.set_ylabel('Revenue (GBP)', color='blue')
ax2.set_ylabel('Quantity (units)', color='green')
plt.tight_layout()
plt.show()
```

---

### Mistake 6: `plt.grid(axis='x')` on a Vertical Bar Chart

**Note from the notebook:** The notebook uses `plt.grid(axis='x')` for the bar chart (vertical bars). This draws **vertical** grid lines (along the X-axis), which aren't particularly useful for reading bar heights.

**For reading bar values, use:**
```python
plt.grid(axis='y')   # Horizontal grid lines — helps read bar heights
```

---

## Blank Plot Debugging Guide

**Symptom:** The plot window opens but is empty, or no chart appears.

| Cause | Symptom | Fix |
|-------|---------|-----|
| `plt.show()` not called | No window in scripts | Add `plt.show()` |
| `plt.close()` called before `plt.show()` | Empty or no window | Move `plt.close()` after `plt.show()` / `savefig()` |
| Wrong variable passed | Empty plot | Print your data before plotting (`print(monthly_revenue)`) |
| Filtered DataFrame is empty | Empty axes | Check `df.shape` and filter conditions |
| Style applied after figure creation | Partial styling | Apply `plt.style.use()` before `plt.figure()` |

---

## Edge Cases & Gotchas Catalog

| Edge Case | What Happens | Fix |
|-----------|-------------|-----|
| Plotting 800K+ scatter points | Extremely slow, solid blob | Use `df.sample(1000)` or `plt.hexbin()` |
| Long X-axis labels (month strings) | Labels overlap | `plt.xticks(rotation=45)` + `tight_layout()` |
| Revenue and Quantity on same Y | One line appears flat | Use `ax.twinx()` for dual Y-axis |
| No `plt.close()` in loops | Memory warning, slow down | `plt.close(fig)` after each `savefig()` |
| `plt.style.use()` after `plt.figure()` | Style doesn't apply | Call style BEFORE figure creation |
| Missing `label=` before `legend()` | Shows "Line 1", "Line 2" | Add `label=` to every `plot()` call |
| `bbox_inches='tight'` omitted in `savefig` | Labels clipped in saved file | Always use `bbox_inches='tight'` |
| Saving JPEG for charts | Lossy compression, blurry text | Use PNG or PDF instead |
| `df.groupby('Month').sum()` month order | Lexicographic sort ≠ chronological | Use `pd.Period` or sort index explicitly |

---

## Best Practices & Publication-Quality Figures

1. **Use the OO API** (`fig, ax = plt.subplots()`) for all production code — explicit references prevent state bugs
2. **Set `figsize` explicitly** — never rely on the 6.4×4.8 default for presentations or publications
3. **Always call `plt.tight_layout()`** or use `constrained_layout=True` to prevent label clipping
4. **Use `bbox_inches='tight'`** in `savefig()` for clean exports
5. **Save as PDF or SVG** for academic publications; PNG at 300 DPI for presentations
6. **Always label axes** — no chart should have unlabeled axes
7. **Use `plt.close(fig)`** after `savefig()` in any loop or batch process
8. **Apply `plt.style.use()`** before creating the figure
9. **Sample large datasets** before scatter plots (`df.sample(1000)`)
10. **Use `alpha`** on scatter plots to reveal density through transparency
11. **Use `color=` explicitly** when plotting multiple series to ensure colorblind-accessible combinations
12. **Set `random_state`** when sampling (`df.sample(n, random_state=42)`) for reproducibility
13. **Sort your data** before bar charts (`sort_values(ascending=False)`) for immediate ranking readability

---

## When to Use Matplotlib / When NOT To

| Use Matplotlib When… | Avoid Matplotlib When… |
|----------------------|------------------------|
| Full control over every visual element is needed | Quick interactive EDA (use Plotly or Bokeh) |
| Publication-quality static figures needed | Web-based interactive dashboards |
| Fine-tuning labels, fonts, spacing | Real-time data streaming |
| Seaborn's defaults aren't quite right | JavaScript-heavy web apps |
| Saving to PDF/SVG for LaTeX/Word | Mobile-first charts |
| Complex multi-subplot layouts | Shareable interactive notebooks |

---

## Matplotlib ↔ MATLAB Translation Guide

| MATLAB | Matplotlib |
|--------|-----------|
| `figure` | `fig = plt.figure()` or `fig, ax = plt.subplots()` |
| `plot(x, y)` | `plt.plot(x, y)` or `ax.plot(x, y)` |
| `bar(x, y)` | `plt.bar(x, y)` |
| `scatter(x, y)` | `plt.scatter(x, y)` |
| `title('text')` | `plt.title('text')` or `ax.set_title('text')` |
| `xlabel('X')` | `plt.xlabel('X')` or `ax.set_xlabel('X')` |
| `ylabel('Y')` | `plt.ylabel('Y')` or `ax.set_ylabel('Y')` |
| `grid on` | `plt.grid(True)` |
| `subplot(2,1,1)` | `fig, axs = plt.subplots(2,1); axs[0].plot(...)` |
| `hold on` | Default — subsequent `plot()` calls add to same axes |
| `legend('a', 'b')` | `ax.legend(['a', 'b'])` or use `label=` kwargs |
| `saveas(fig, 'f.png')` | `plt.savefig('f.png', dpi=300)` |
| `close all` | `plt.close('all')` |
| `set(gca, 'XTickLabel', ...)` | `ax.set_xticklabels(...)` |
| `text(x, y, 'str')` | `plt.text(x, y, 'str')` |

---

## Interoperability Guide

| From / To | Method | Notes |
|-----------|--------|-------|
| **Pandas → Matplotlib** | `df.plot()` / `series.plot()` returns `ax` | Customize returned `ax` with Matplotlib methods |
| **Seaborn → Matplotlib** | All Seaborn functions return `ax` | Use `ax.set_title()` etc. to further customize |
| **Matplotlib → PDF** | `plt.savefig('file.pdf')` | Vector format — ideal for publications |
| **Matplotlib → SVG** | `plt.savefig('file.svg')` | Editable in Inkscape, Illustrator |
| **Matplotlib → Jupyter** | `%matplotlib inline` | Inline rendering |
| **Matplotlib → PIL/Pillow** | `fig.canvas.draw(); PIL.Image.frombytes(...)` | Convert figure to image object |
| **NumPy → Matplotlib** | `plt.plot(np_array)` | Direct support |

---

## Deprecation & Matplotlib 3.x Migration Notes

| Old / Deprecated | New / Preferred | Notes |
|-----------------|-----------------|-------|
| `ax.set_axis_bgcolor()` | `ax.set_facecolor()` | Removed in 3.0 |
| `plt.hold(True)` / `plt.hold(False)` | Removed entirely | Plots always "hold" in 3.x |
| `mpl.cm.get_cmap('viridis')` | `mpl.colormaps['viridis']` | `get_cmap` deprecated in 3.7 |
| `plt.subplot(111)` (legacy) | `fig, ax = plt.subplots()` | OO API preferred |
| `from mpl_toolkits.axes_grid` | `from mpl_toolkits.axes_grid1` | Module reorganized |
| `'seaborn'` style name | `'seaborn-v0_8'` | Renamed in 3.6 |
| Direct string `loc` integers for legend | Use string names | Some numeric values changed |

---

## Pandas Integration — `series.plot()` and `df.plot()` [BEGINNER]

From the notebook's first plotting cell:

```python
data = pd.Series([100, 200, 150, 300], index=['Q1', 'Q2', 'Q3', 'Q4'])
data.plot()
```

**What it returns:** An `Axes` object that you can further customize with Matplotlib methods.

### Common `df.plot()` / `series.plot()` Parameters

| Parameter | Description |
|-----------|-------------|
| `kind` | `'line'`, `'bar'`, `'barh'`, `'hist'`, `'scatter'`, `'pie'`, `'box'` |
| `figsize` | `(width, height)` in inches |
| `title` | Plot title |
| `xlabel`, `ylabel` | Axis labels |
| `color` | Color or list of colors |
| `legend` | `True`/`False` |
| `grid` | `True`/`False` |
| `rot` | Tick label rotation |

```python
# Get the ax object for further customization
ax = data.plot(figsize=(10, 4), title='Quarterly Data')
ax.set_ylabel('Revenue')
ax.grid(True)
plt.show()
```

---

## Performance Notes

| Scenario | Issue | Solution |
|----------|-------|----------|
| Scatter with 800K+ points | Extremely slow render, overplotting | `df.sample(1000)` or `plt.hexbin()` |
| Saving high DPI | Large file size, slow | Use `dpi=150` for web, `dpi=300` for print only |
| Many figures in loops | Memory leak | `plt.close(fig)` after each save |
| Rendering time-series with 1M+ rows | Slow | Downsample or use `rasterized=True` |

```python
# Rasterize a dense line plot (renders as bitmap, not vector, inside PDF)
ax.plot(x, y, rasterized=True)
fig.savefig('output.pdf', dpi=300)
```

---

## Interview & Exam Corner

### Frequently Asked Questions

**Q1: What is the difference between `Figure` and `Axes` in Matplotlib?**
`Figure` is the entire window or page — the top-level container. `Axes` is a single plot area within the Figure where data is drawn. One Figure can contain multiple Axes (e.g., in subplots). Note: "Axes" ≠ "Axis" — Axis is just the x-axis or y-axis number line.

**Q2: What is the difference between `plt.plot()` and `ax.plot()`?**
`plt.plot()` uses the stateful pyplot API — it acts on the "current axes" maintained internally by Matplotlib. `ax.plot()` uses the OO API — you explicitly reference the `ax` object. Both draw a line, but `ax.plot()` is preferred for any multi-subplot or production code.

**Q3: Why do we call `plt.tight_layout()` before `plt.show()`?**
`tight_layout()` automatically adjusts subplot parameters (margins, spacing) so labels and tick labels don't get clipped by the figure boundary. It's especially important when X-axis labels are long or rotated.

**Q4: What does `alpha=0.5` do in `plt.scatter()`?**
Sets the transparency of each marker to 50%. This is crucial for scatter plots with many overlapping points — semi-transparent markers stack visually, showing density through darker areas, rather than appearing as a solid opaque blob.

**Q5: Why use `df.sample(1000, random_state=10000)` before plotting?**
The full dataset has 800K+ rows. Plotting all of them in a scatter plot would be extremely slow to render and result in complete overplotting. Sampling 1,000 rows gives a statistically representative subset. `random_state` ensures the same 1,000 rows are sampled every run (reproducibility).

**Q6: What does `sharex=True` do in `plt.subplots()`?**
It links the X-axes of all subplots so they share the same range and tick positions. When you zoom one plot, all others zoom together. It also hides redundant X-axis tick labels on all but the bottom subplot, making the layout cleaner.

**Q7: What is the difference between `plt.savefig()` and `plt.show()`?**
`plt.savefig()` writes the figure to a file (PNG, PDF, SVG, etc.). `plt.show()` renders the figure to the screen. You can call `savefig()` without `show()` (useful in scripts/batch jobs). If you call `show()` first, the figure may be reset before `savefig()` can capture it — so always call `savefig()` BEFORE `show()`.

**Q8: Why use `plt.close()` after `plt.savefig()`?**
After saving, the figure still exists in memory. In a script or loop, many saved figures accumulate and eventually cause a `RuntimeWarning` or memory exhaustion. `plt.close()` explicitly destroys the figure and frees its memory.

**Q9: What is `fontdict` in `plt.text()`?**
A dictionary of font properties (`family`, `color`, `weight`, `size`, `style`) that can be defined once and reused across multiple `text()` calls, instead of repeating individual kwargs for every annotation.

**Q10: How do you add a legend to a multi-line plot?**
Add `label='name'` to each `plt.plot()` or `ax.plot()` call, then call `plt.legend()` or `ax.legend()` once. Matplotlib automatically matches labels to their plotted lines.

---

### Tricky Output Questions

**Q1: What does this code print/show?**
```python
data = pd.Series([100, 200, 150, 300], index=['Q1', 'Q2', 'Q3', 'Q4'])
data.plot()
```
**Answer:** A line plot with Q1–Q4 on the X-axis and values 100, 200, 150, 300 on the Y-axis. Line rises Q1→Q2, dips Q2→Q3, rises sharply Q3→Q4. Default blue line, no markers, no title, no axis labels. This works because `pd.Series.plot()` calls Matplotlib under the hood.

**Q2: What is the effect of `plt.grid(axis='x')` on a vertical bar chart?**
**Answer:** It draws **vertical** grid lines (parallel to the X-axis direction), appearing between the bars. For reading bar heights, `plt.grid(axis='y')` (horizontal lines) would be more useful.

**Q3: What happens if you call `plt.style.use('ggplot')` after `plt.figure(figsize=(10, 6))`?**
**Answer:** The style may not apply correctly to the already-created figure. `plt.style.use()` must be called **before** `plt.figure()`. The figure is created with the current style at the time of creation.

---

### One-Liner Challenges

**Q1: Create a 12×6 figure, plot `monthly_revenue` as a green line with circle markers, and show it.**
```python
plt.figure(figsize=(12,6)); plt.plot(monthly_revenue.index, monthly_revenue.values, marker='o', color='green'); plt.show()
```

**Q2: Save the current plot as a 300 DPI PNG with tight bounding box.**
```python
plt.savefig('plot.png', dpi=300, bbox_inches='tight')
```

**Q3: Print all available Matplotlib style names.**
```python
print(plt.style.available)
```

---

### Explain the Difference

**1. `plt.plot()` vs `plt.scatter()`**
Both plot data points but `plt.plot()` connects them with a line and is optimized for sequential (time-series) data. `plt.scatter()` draws isolated points and supports per-point size (`s=`) and color (`c=`) variation — useful for correlation analysis. `scatter()` is slower for large N because each point is a separate artist.

**2. `plt.subplots(nrows=2, ncols=1)` vs `plt.subplot(2, 1, 1)`**
`plt.subplots(nrows=2, ncols=1)` (OO API) creates the entire figure and all axes at once, returning `fig` and `axs` array. `plt.subplot(2, 1, 1)` (pyplot API) creates/selects one subplot at a time, implicitly modifying the "current axes". The `subplots()` approach is preferred because you hold explicit references to all axes from the start.

**3. `plt.tight_layout()` vs `constrained_layout=True`**
`plt.tight_layout()` is a post-creation call that adjusts spacing. `constrained_layout=True` is a parameter passed to `plt.subplots()` that manages layout continuously as the figure changes. `constrained_layout` is more robust for complex multi-subplot grids and handles colorbars better.

---

## Practice Problems

### Problem 1 — Beginner: Quarterly Revenue Bar Chart

**Task:** Plot quarterly revenue totals from the dataset as a bar chart with custom colors, grid, and labels.

**Starter code:**
```python
import pandas as pd
import matplotlib.pyplot as plt

# Sample data (use your df if available)
quarterly = pd.Series(
    [1_200_000, 1_500_000, 1_350_000, 1_800_000],
    index=['Q1-2010', 'Q2-2010', 'Q3-2010', 'Q4-2010']
)

# Your code here:
```

**Solution:**
```python
plt.figure(figsize=(8, 5), facecolor='white')
plt.style.use('ggplot')

plt.bar(quarterly.index, quarterly.values, color='steelblue')

plt.title("Quarterly Revenue 2010")
plt.xlabel("Quarter")
plt.ylabel("Revenue (GBP)")
plt.xticks(rotation=0)
plt.grid(axis='y')
plt.tight_layout()
plt.show()
```

**Expected Output:** 4 steel-blue vertical bars, heights proportional to revenue values. Q4-2010 is the tallest bar. Horizontal grid lines help read values. Clean ggplot grey background.

---

### Problem 2 — Intermediate: Multi-Subplot Revenue + Country Breakdown

**Task:** Create a 2-row subplot. Top: Monthly Revenue line chart. Bottom: Top 5 countries bar chart. Share no axis (different X categories).

**Solution:**
```python
fig, axs = plt.subplots(nrows=2, ncols=1, figsize=(12, 10))

# Top: Monthly Revenue
axs[0].plot(monthly_revenue.index, monthly_revenue.values, marker='o', color='blue')
axs[0].set_title("Monthly Revenue Over Time")
axs[0].set_xlabel("Month")
axs[0].set_ylabel("Revenue (GBP)")
axs[0].tick_params(axis='x', rotation=45)
axs[0].grid(True)

# Bottom: Top 5 Countries
top5 = df.groupby('Country')['Revenue'].sum().sort_values(ascending=False).head(5)
axs[1].bar(top5.index, top5.values, color='coral')
axs[1].set_title("Top 5 Countries by Revenue")
axs[1].set_xlabel("Country")
axs[1].set_ylabel("Total Revenue (GBP)")
axs[1].tick_params(axis='x', rotation=30)
axs[1].grid(axis='y')

plt.tight_layout()
plt.savefig("revenue_dashboard.png", dpi=300, bbox_inches='tight')
plt.close()
```

---

### Problem 3 — Advanced: Annotated Scatter Plot with Labels on Outliers

**Task:** Create a scatter plot of Quantity vs Revenue from the 1,000-row sample. Identify the top 5 highest-revenue points and annotate them with their revenue value.

**Solution:**
```python
sample_df = df.sample(1000, random_state=10000)
top5_points = sample_df.nlargest(5, 'Revenue')

fig, ax = plt.subplots(figsize=(10, 6))

ax.scatter(sample_df['Quantity'], sample_df['Revenue'],
           alpha=0.4, color='purple', s=30, label='All transactions')

ax.scatter(top5_points['Quantity'], top5_points['Revenue'],
           color='red', s=100, zorder=5, label='Top 5 by Revenue')

for _, row in top5_points.iterrows():
    ax.annotate(
        f"£{int(row['Revenue']):,}",
        xy=(row['Quantity'], row['Revenue']),
        xytext=(10, 5),
        textcoords='offset points',
        fontsize=8,
        color='darkred'
    )

ax.set_title("Quantity vs Revenue — Top Outliers Annotated")
ax.set_xlabel("Quantity")
ax.set_ylabel("Revenue (GBP)")
ax.legend()
ax.grid(True, alpha=0.3)
plt.tight_layout()
plt.savefig("scatter_annotated.png", dpi=300, bbox_inches='tight')
plt.close()
```

---

## Data Visualization Design Principles

### Tufte's Data-Ink Ratio
*"Above all else, show the data."* — Edward Tufte

Maximize the proportion of ink devoted to data. Remove: unnecessary grid lines, background colors, 3D effects, decorative borders (chart junk). Keep: data lines, axis labels, titles.

**Application from the notebook:**
- `plt.grid(axis='x')` on bar chart (could argue only `axis='y'` needed)
- `plt.style.use('classic')` vs `'ggplot'` — classic has less chart junk

### Colorblind Accessibility
~8% of men have red-green color blindness. The notebook uses green and blue for two lines — this pair is distinguishable. Avoid red/green combinations.

**Colorblind-safe palette:**
```python
# Use Tableau's colorblind palette
colors = ['#006BA4', '#FF800E', '#ABABAB', '#595959', '#5F9ED1']
```

Or use `plt.style.use('seaborn-v0_8-colorblind')`.

### Chart Selection Framework

| Data Type | Recommended | Avoid |
|-----------|-------------|-------|
| Change over time | Line chart | Pie chart |
| Ranking | Sorted bar chart | Unsorted bar |
| Correlation | Scatter plot | Bar chart |
| Part-of-whole | Stacked bar | Pie (when >5 categories) |
| Distribution | Histogram | Bar chart |
| Geographic | Map | Bar chart |

---

## Mnemonics & Memory Aids

- **"plt = playground, ax = architecture"** — pyplot for quick explorations, OO API for real code
- **"Axes ≠ Axis"** — Axes is the whole plot box; Axis is just one number line (x or y)
- **"Figure = Frame, Axes = Art"** — the Figure is the picture frame; you paint on the Axes canvas inside it
- **"Tight before Show"** — always call `tight_layout()` before `show()` or `savefig()`
- **"Style before Figure"** — always call `plt.style.use()` before `plt.figure()`
- **"Save before Show, Close after Save"** — the order: `savefig()` → `show()` → `close()`
- **"label before legend"** — always set `label=` in `plot()` before calling `legend()`
- **"Sample before Scatter"** — for large DataFrames, sample before `plt.scatter()` to avoid overplotting

---

## Conceptual Diagrams (ASCII)

### Figure / Axes / Axis Hierarchy
```
┌─────────────────────────────────────────────────────────┐
│                        Figure (fig)                      │
│  ┌───────────────────────────────────────────────────┐  │
│  │                   Axes (ax)                        │  │
│  │                                                   │  │
│  │    YAxis │  ●───●───●───●───●  ← Line artists    │  │
│  │  (Axis)  │ /           \      ← Markers           │  │
│  │          │●             ●                         │  │
│  │          └─────────────────── XAxis (Axis)        │  │
│  │         Ticks  Tick Labels  Grid lines             │  │
│  └───────────────────────────────────────────────────┘  │
│                   Title (ax.set_title)                   │
└─────────────────────────────────────────────────────────┘
```

### Subplot Grid (2 rows × 1 col from notebook)
```
plt.subplots(nrows=2, ncols=1, figsize=(10, 8), sharex=True)

┌────────────────────────────────────┐
│          Figure (10×8 inches)      │
│  ┌──────────────────────────────┐  │
│  │  axs[0] — Monthly Revenue    │  │
│  │  ●─●─●─●─●─●─●─●─●─●─●─●  │  │
│  └──────────────────────────────┘  │
│  ┌──────────────────────────────┐  │
│  │  axs[1] — Monthly Quantity   │  │
│  │  ■─■─■─■─■─■─■─■─■─■─■─■  │  │
│  └──────────────────────────────┘  │
│      Shared X-axis tick labels      │
└────────────────────────────────────┘
```

### Rendering Pipeline
```
plt.plot() / ax.bar() / plt.scatter()
         ↓
   Artist objects added to Axes
         ↓
   plt.show() or plt.savefig()
         ↓
   fig.canvas.draw() — render all artists
         ↓
   Backend (Agg / TkAgg / inline)
         ↓
   Output: Screen window / PNG / PDF / SVG
```

---

## Quick-Reference Cheat Sheet

```python
# === SETUP ===
import matplotlib.pyplot as plt
import pandas as pd

plt.style.use('ggplot')           # Set style BEFORE figure creation

# === FIGURE ===
plt.figure(figsize=(12, 6))       # Create figure (pyplot)
plt.figure(figsize=(12, 6), facecolor='white')  # With white background
fig, ax = plt.subplots(figsize=(12, 6))         # OO API (preferred)
fig, axs = plt.subplots(2, 1, figsize=(10, 8), sharex=True)  # 2-row subplot

# === PLOTS ===
plt.plot(x, y, marker='o')                    # Line plot
plt.plot(x, y, marker='o', color='green')     # Green line
plt.bar(x, y, color='skyblue')               # Bar chart
plt.scatter(x, y, alpha=0.5, color='purple') # Scatter plot
series.plot()                                  # Pandas quick plot

# === OO PLOTS ===
ax.plot(x, y, marker='o', color='blue', label='Revenue')
ax.plot(x, y, marker='s', color='green', label='Quantity')
ax.bar(x, y, color='skyblue')
ax.scatter(x, y, alpha=0.5)

# === LABELS & TITLES ===
plt.title('Title')             ax.set_title('Title')
plt.xlabel('X Label')          ax.set_xlabel('X Label')
plt.ylabel('Y Label')          ax.set_ylabel('Y Label')

# === TICKS ===
plt.xticks(rotation=45)        ax.tick_params(axis='x', rotation=45)
plt.yticks(rotation=45)        ax.tick_params(axis='y', rotation=45)

# === LEGEND ===
plt.legend()                   ax.legend()
ax.legend(loc='upper right')   ax.legend(loc='best')

# === GRID ===
plt.grid(True)                 ax.grid(True)
plt.grid(axis='x')             ax.grid(axis='y')

# === TEXT ON PLOT ===
plt.text(x, y+offset, f'{int(value)}', ha='center', fontsize=8)
plt.text(x, y+offset, f'{int(value)}', ha='center', fontdict=font)

# fontdict definition:
font = {'family': 'serif', 'color': 'black', 'weight': 'bold', 'size': 10}

# === LAYOUT ===
plt.tight_layout()             fig.tight_layout()

# === STYLES ===
plt.style.use('classic')
plt.style.use('ggplot')
print(plt.style.available)     # List all styles

# === SAVE & CLOSE ===
plt.savefig('plot.png', dpi=300)
plt.savefig('plot.pdf', bbox_inches='tight', dpi=300)
plt.close()                    plt.close(fig)
plt.close('all')               # Close all open figures

# === SHOW ===
plt.show()
```

---

## Summary (TL;DR)

- **Matplotlib** is Python's core 2D plotting library, powering Seaborn, Pandas `.plot()`, and most scientific visualization
- Use **`plt.figure(figsize=...)`** to control canvas size before any plotting
- **`plt.plot()`** for line plots, **`plt.bar()`** for bar charts, **`plt.scatter()`** for scatter plots — all take X, Y data and styling kwargs
- **`plt.style.use('ggplot')`** / `'classic'` transforms the entire visual theme — must be called BEFORE creating the figure
- **`plt.text(x, y, 'label')`** adds data labels at precise positions in data coordinates; use `fontdict=` for reusable font styles
- **`fig, axs = plt.subplots(nrows=2, ncols=1, sharex=True)`** is the standard OO API for multi-panel dashboards — access plots via `axs[0]`, `axs[1]`
- Always call **`plt.tight_layout()`** before `show()` / `savefig()` to prevent label clipping
- **`plt.savefig('file.png', dpi=300)`** exports at print quality; always follow with **`plt.close()`** to free memory
- The **OO API** (`fig, ax = plt.subplots()` + `ax.set_title()`) is preferred over the pyplot API for any production or multi-subplot code

---

## Further Reading & Official Docs

- [Matplotlib Official Documentation](https://matplotlib.org/stable/index.html)
- [Matplotlib Tutorials — User Guide](https://matplotlib.org/stable/tutorials/index.html)
- [Matplotlib Gallery — Examples by Plot Type](https://matplotlib.org/stable/gallery/index.html)
- [pyplot API Reference](https://matplotlib.org/stable/api/pyplot_summary.html)
- [Axes API Reference](https://matplotlib.org/stable/api/axes_api.html)
- [Figure API Reference](https://matplotlib.org/stable/api/figure_api.html)
- [Style Sheets Reference](https://matplotlib.org/stable/gallery/style_sheets/style_sheets_reference.html)
- [Anatomy of a Figure](https://matplotlib.org/stable/gallery/showcase/anatomy.html)
- [UCI Online Retail II Dataset](https://archive.ics.uci.edu/ml/machine-learning-databases/00502/online_retail_II.xlsx)
- [Seaborn Documentation](https://seaborn.pydata.org/) — high-level Matplotlib wrapper
- [Plotly Python](https://plotly.com/python/) — interactive alternative
- [Pandas Visualization Guide](https://pandas.pydata.org/docs/user_guide/visualization.html)

---

## Flashcard-Style Recall

**Q:** What does `plt.figure(figsize=(12, 6))` do?
**A:** Creates a new Figure (canvas) 12 inches wide and 6 inches tall.

**Q:** What is the difference between `plt.plot()` and `ax.plot()`?
**A:** Same result, different API: `plt.plot()` is stateful (operates on implicit current axes); `ax.plot()` is explicit OO API (operates on the `ax` object you hold).

**Q:** How do you add labels to data points on a line chart?
**A:** Loop with `enumerate`, call `plt.text(x_pos, y_pos + offset, label_string, ha='center')`.

**Q:** What does `alpha=0.5` do in `plt.scatter()`?
**A:** Makes each marker 50% transparent — reveals density through overlapping in dense areas.

**Q:** How do you create two stacked subplots sharing the same X-axis?
**A:** `fig, axs = plt.subplots(nrows=2, ncols=1, sharex=True)`

**Q:** What is `plt.tight_layout()` for?
**A:** Automatically adjusts margins so labels, titles, and tick labels aren't clipped.

**Q:** How do you save a figure at 300 DPI?
**A:** `plt.savefig('file.png', dpi=300, bbox_inches='tight')`

**Q:** Why call `plt.close()` after `savefig()`?
**A:** Frees the figure from memory. Without it, figures accumulate and cause `RuntimeWarning` or memory exhaustion in loops.

**Q:** What is `fontdict` in `plt.text()`?
**A:** A dictionary of font properties (`family`, `color`, `weight`, `size`) reused across multiple `text()` calls.

**Q:** What does `sharex=True` do in `plt.subplots()`?
**A:** Links X-axes of all subplots so they share the same range and hides redundant tick labels on non-bottom subplots.

**Q:** What must you do BEFORE calling `plt.style.use()`?
**A:** Nothing — but you must call it BEFORE `plt.figure()` / `plt.subplots()` for the style to apply correctly.

**Q:** How do you list all available Matplotlib styles?
**A:** `print(plt.style.available)`

**Q:** What is the recommended format for saving plots for academic publications?
**A:** PDF or SVG (vector formats, infinitely scalable, small file size).

**Q:** When is `plt.show()` required?
**A:** In standalone Python scripts. In Jupyter, inline backend renders without it, but calling it explicitly ensures clean rendering.

**Q:** What is `facecolor='white'` in `plt.figure()`?
**A:** Sets the figure background color to white — useful when the style's default background differs (e.g., `'classic'` uses grey by default in some versions).
