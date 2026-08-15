# Binaaz — Real Estate Market EDA

Exploratory data analysis and data cleaning project on real estate listings: deep data cleaning of 100K+ listings, a per-column null-handling strategy, outlier removal with IQR and Isolation Forest, 4 new engineered features, and correlation/categorical visualizations (Matplotlib, Seaborn, Plotly).

---

## Dataset

**Original data (Kaggle):**
Not stored in the repo due to file size.
👉 [Kaggle — Binaa.az Sale Project](https://www.kaggle.com/datasets/sehriyarmemmedli/binaaz-sale-project)

**Cleaned dataset (this repo):**
- `data/binaaz_cleaned.zip` — after full cleaning, null-handling, outlier removal, and feature engineering (64,454 rows, 36 columns)

---

## Project Structure

```
📦 Binaaz-Real-Estate-Analysis
 ├── 📓 Binaaz_Analysis_.ipynb          ← Full pipeline: cleaning, EDA, outliers, features, viz
 ├── 📊 data/
 │   └── binaaz_cleaned_data.zip
 └── README.md
```

---

## Workflow

### 1. Data Loading & Initial Look
- Loaded from Kaggle (`house_sale.csv`), initial shape **(100775, 51)**
- Identified `_x`/`_y` suffixed duplicate columns from a prior table merge
- Found 6 duplicate columns via pairwise `.equals()` comparison and dropped them
- Confirmed `price` == `total_price` (data leakage risk) and dropped `total_price`
- Removed re-scraped duplicate listings, keeping the latest version per `estate_rel_url`
- Fixed dtypes: dates, numeric fields extracted from unit-suffixed strings (`Sahə`, `unit_price`, `Torpaq sahəsi`)

### 2. Null Value Handling
Each column was handled with a reasoned, column-specific strategy rather than blanket imputation or row-dropping:
- Dropped `Binanın növü` (99.8% missing)
- `products_label` → filled with `'Private'` (missing implies no agency)
- `vip`, `featured`, `İpoteka` → filled with 0/1 and cast to `category`
- `unit_price` → derived from `price / Sahə (m²)` where missing
- `owner_name`, `shop_name`, `description` → filled with `'Unknown'` / `'Fiziki şəxs'` / `'No description'`
- `Mərtəbə`, `Otaq sayı`, `Torpaq sahəsi` → left as-is where missingness is structural (e.g. land listings have no floor/room count), not random

### 3. Outlier Detection & Removal
- IQR was chosen first since the data is heavily right-skewed and median-based methods are less sensitive to extreme values than z-score
- Flagged outliers were inspected before removal (`nlargest`, threshold checks) to confirm they were genuine high-value listings, not data errors
- Final removal used **Isolation Forest** (contamination=0.01) across 5 numeric columns jointly, since it captures multivariate anomalies that single-column IQR bounds miss

### 4. Feature Engineering
- `Current_floor` / `Total_floor` — split from the `Mərtəbə` (floor) string
- `Area_per_room` — `Sahə (m²) / Otaq sayı`
- `district` — extracted from the `extra_info` text field via regex

### 5. Visualization
- Log-scale distribution histograms for all numeric columns
- Correlation heatmap (Seaborn)
- Categorical breakdown (count plots) for low-cardinality columns
- Interactive Plotly views: price by district (box plot), views over time (line chart), listing locations (map)

---

## Setup

```bash
git clone https://github.com/your-username/Binaaz-Real-Estate-Analysis.git
cd Binaaz-Real-Estate-Analysis
```

Download the original dataset from Kaggle and place it in the root folder as `house_sale.csv`, then run:

```
Binaaz_EDA.ipynb
```

---

## Technologies Used

![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=flat&logo=pandas&logoColor=white)
![NumPy](https://img.shields.io/badge/NumPy-013243?style=flat&logo=numpy&logoColor=white)
![Scikit-learn](https://img.shields.io/badge/Scikit--learn-F7931E?style=flat&logo=scikit-learn&logoColor=white)
![Matplotlib](https://img.shields.io/badge/Matplotlib-11557C?style=flat&logo=python&logoColor=white)
![Seaborn](https://img.shields.io/badge/Seaborn-4C72B0?style=flat&logo=python&logoColor=white)
![Plotly](https://img.shields.io/badge/Plotly-3F4F75?style=flat&logo=plotly&logoColor=white)
