# montgomery-county-911-analysis
# Montgomery County 911 Calls: Data Engineering & Visualization

An end-to-end data analysis workflow examining over 99,000 emergency calls in Montgomery County, PA. Built with Python, Pandas, and Plotly Express.

---

## Part 1: Technical Workflow & Feature Engineering

### 1. Environment & Tools
* **Python 3.13**
* **Pandas & NumPy**: Data cleaning, missing value handling, and matrix reshaping.
* **Plotly Express**: Interactive visualizations, category ordering, and custom figure layouts.

---

### 2. Data Cleaning & Handling Missing Values
* **Redundant Column Removal:** Dropped the constant dummy column `e` to save memory and clean the schema.
* **Non-Destructive Missing Value Imputation:** 
  * Rather than dropping rows with missing `zip` codes (over 12,000 entries), records were preserved because geographic coordinates (`lat`, `lng`) and emergency details remained intact. Dropping them would systematically bias total call volumes downward.
  * Missing postal codes were imputed with `0`, and missing town (`twp`) values were labeled as `'Unknown'`.

---

### 3. Feature Engineering
* **Timestamp Decomposition:** Converted the raw string `timeStamp` column into native `datetime64[ns]` objects and extracted three distinct features:
  * `hour`: Integer range (0–23) for hourly crisis profiling.
  * `month`: Integer range (1–12) for seasonal tracking.
  * `day`: Name of the weekday (`Monday` through `Sunday`).
* **Emergency Category Parsing:** Extracted the core department category from the colon-delimited `title` string (e.g., separating `"EMS"` from `"EMS: BACK PAINS/INJURY"`) to create a clean `Type of Emergency` classification (`EMS`, `Fire`, `Traffic`).

#### Engineered DataFrame Sample
<img width="1110" height="479" alt="Screenshot 2026-09-06 at 17 01 43" src="https://github.com/user-attachments/assets/3e033b11-cd36-4c80-a33c-f54839fab0bd" />

### 4. Aggregation & Matrix Reshaping
* **Ranked Frequency Extraction:** Grouped data by township (`twp`), aggregated call counts, and performed descending sorts to identify operational hotspots while avoiding alphabetical grouping pitfalls.
* **Cross-Tabulation Matrix:** Constructed a two-dimensional contingency matrix of days versus hours using `pd.crosstab(calls['day'], calls['hour'])`.
* **Chronological Reindexing:** Applied explicit weekday reindexing (`Monday` to `Sunday`) to prevent default alphabetical axis sorting (`Friday`, `Monday`, etc.) and ensure chronological readability.

---

### 5. Visualization Pipeline
* **Categorical Distributions:** Built multi-series grouped histograms to evaluate day-of-week shifts across emergency types.
* **Heatmap Customization:** Rendered the cross-tabulated day/hour matrix with `plotly_dark` and a `YlOrRd` gradient, adding bounding box shapes (`add_shape`) and annotations to spotlight weekday rush-hour spikes (15:00–18:00).
