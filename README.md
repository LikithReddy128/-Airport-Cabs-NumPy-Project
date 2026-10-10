Airport Cabs NumPy Project

> Exploratory analysis of **89,560 NYC airport taxi trips** using only **NumPy** — array shapes, slicing, boolean masking and vectorised arithmetic.

![Python](https://github.com/LikithReddy128/-Airport-Cabs-NumPy-Project/blob/main/airpotcab.ipynb)
![Data set](https://github.com/LikithReddy128/-Airport-Cabs-NumPy-Project/blob/main/nyc_taxis.csv)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange)
![Status](https://img.shields.io/badge/status-complete-brightgreen)

**Author:** Pakinati Likith Reddy · B.Tech EEE (2026) · Aspiring Data Analyst
**Full technical write-up:** see [`DOCUMENTATION.md`](DOCUMENTATION.md)

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Dataset](#2-dataset)
3. [Questions Answered & Results](#3-questions-answered--results)
4. [NumPy Concepts Used](#4-numpy-concepts-used)
5. [Repository Structure](#5-repository-structure)
6. [Getting Started](#6-getting-started)
7. [How to Create This Repository on GitHub (Step by Step)](#7-how-to-create-this-repository-on-github-step-by-step)
8. [Code Walkthrough](#8-code-walkthrough)
9. [Important Notes & Corrections](#9-important-notes--corrections)
10. [Requirements, .gitignore and License](#10-requirements-gitignore-and-license)
11. [Future Improvements](#11-future-improvements)
12. [Contact](#12-contact)

---

## 1. Project Overview

Airport trips are among the highest-value rides for a taxi driver. This project loads a CSV of NYC airport taxi rides into a NumPy `ndarray` and answers practical business questions without using pandas:

- How fast do these cabs travel on average?
- How many rides happened in February?
- How many extreme rides exist (very long distance, very high tip)?
- How are rides split by payment type and drop-off location?

**Skills demonstrated:** `np.genfromtxt`, `.shape`, 2D indexing, boolean masks, vectorised math, aggregation (`mean`, `median`), data-quality checking.

---

## 2. Dataset

| Property | Value |
|---|---|
| File | `data/nyc_taxis.csv` |
| Rows (rides) | **89,560** |
| Columns | **15** |
| Year | 2016 |
| Months covered | January – June (1–6) |
| Loaded array shape | `(89560, 15)` |

### Column index map (important for NumPy indexing)

| Index | Column | Description |
|---|---|---|
| 0 | `pickup_year` | Year of pickup (2016) |
| 1 | `pickup_month` | Month, 1–6 |
| 2 | `pickup_day` | Day of month |
| 3 | `pickup_dayofweek` | Day of week code (1–7) |
| 4 | `pickup_time` | Hour of pickup (0–5 in this file) |
| 5 | `pickup_location_code` | Encoded pickup location |
| 6 | `dropoff_location_code` | Encoded drop-off location |
| 7 | `trip_distance` | Distance in **miles** |
| 8 | `trip_length` | Duration in **seconds** |
| 9 | `fare_amount` | Fare in USD |
| 10 | `fees_amount` | Fees in USD |
| 11 | `tolls_amount` | Tolls in USD |
| 12 | `tip_amount` | Tip in USD |
| 13 | `total_amount` | Total charged in USD |
| 14 | `payment_type` | 1 = Credit card, 2 = Cash, 3 = No charge, 4 = Dispute |

---

## 3. Questions Answered & Results

| # | Question | NumPy approach | Result |
|---|---|---|---|
| 1 | What is the average speed of a trip (mph)? | `taxi[:,7] / (taxi[:,8] / 3600)` then `.mean()` | **32.24 mph** (raw mean) |
| 2 | How many rides were in February? | `taxi[taxi[:,1] == 2].shape[0]` | **13,333** |
| 3 | How many rides had a distance over 50 miles? | `taxi[taxi[:,7] > 50].shape[0]` | **8** |
| 4 | How many rides were paid by cash (payment type 2)? | `taxi[taxi[:,14] == 2].shape[0]` | **24,520** |
| 5 | How many rides had a tip over $50? | `taxi[taxi[:,12] > 50].shape[0]` | **16** |
| 6 | How many rides dropped off at location code 2? | `taxi[taxi[:,6] == 2].shape[0]` | **11,832** |

Extra insight: the **median** speed is **22.54 mph**, noticeably lower than the 32.24 mph mean because a few bad records (for example a 1-second trip) inflate the mean. See [Section 9](#9-important-notes--corrections).

---

## 4. NumPy Concepts Used

| Concept | Example in this project |
|---|---|
| Loading data | `np.genfromtxt('data/nyc_taxis.csv', delimiter=',', skip_header=True)` |
| Array shape | `taxi.shape` → `(89560, 15)` |
| Column slicing | `taxi[:, 7]` selects all rows of column 7 |
| Negative index | `taxi[:, -1]` is the last column (`payment_type`) |
| Boolean mask | `taxi[:, 1] == 2` returns `True`/`False` per row |
| Mask filtering | `taxi[mask]` keeps only matching rows |
| Counting with shape | `filtered.shape[0]` is the number of matches |
| Vectorised arithmetic | `distance / (seconds / 3600)` computed for all rows at once |
| Aggregation | `.mean()`, `np.median()`, `.max()`, `.min()` |

> **`.shape` tip:** for a 2D array, `shape[0]` = number of rows (rides) and `shape[1]` = number of columns (fields).

---

## 5. Repository Structure

```
airport-cabs-numpy-analysis/
│
├── README.md                     <- project overview (this file)
├── DOCUMENTATION.md              <- detailed technical documentation
├── requirements.txt              <- dependencies
├── .gitignore                    <- files Git should ignore
├── LICENSE                       <- MIT license
│
├── data/
│   └── nyc_taxis.csv             <- dataset (89,560 rows x 15 columns)
│
└── notebooks/
    └── airport_cabs_analysis.ipynb   <- main analysis notebook
```

---

## 6. Getting Started

### Prerequisites
- Python 3.9 or newer (project built on 3.12)
- Git
- Jupyter Notebook or JupyterLab (or VS Code with the Jupyter extension)

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/<your-username>/airport-cabs-numpy-analysis.git
cd airport-cabs-numpy-analysis

# 2. (Recommended) create a virtual environment
python -m venv venv
source venv/bin/activate        # macOS / Linux
venv\Scripts\activate           # Windows

# 3. Install dependencies
pip install -r requirements.txt

# 4. Launch the notebook
jupyter notebook notebooks/airport_cabs_analysis.ipynb
```

> The notebook loads the data with a relative path. If you run it from inside `notebooks/`, use `../data/nyc_taxis.csv`.

---

## 7. How to Create This Repository on GitHub (Step by Step)

### Step 1: Prepare your local project folder

```bash
mkdir airport-cabs-numpy-analysis
cd airport-cabs-numpy-analysis
mkdir data notebooks
```

Copy your files in:
- `nyc_taxis.csv` into `data/`
- `airpotcab.ipynb` into `notebooks/` and rename it to `airport_cabs_analysis.ipynb` (fixes the spelling)
- `README.md` and `DOCUMENTATION.md` into the project root

### Step 2: Create the supporting files

```bash
# requirements.txt
printf "numpy>=1.26\njupyter\n" > requirements.txt

# .gitignore
printf "venv/\n__pycache__/\n.ipynb_checkpoints/\n.DS_Store\n" > .gitignore
```

Add a `LICENSE` file (MIT is standard for portfolios). On GitHub you can also add it from the web UI: **Add file → Create new file → name it `LICENSE` → Choose a license template**.

### Step 3: Create the repository on GitHub

1. Sign in at [github.com](https://github.com).
2. Click **+** (top right) → **New repository**.
3. **Repository name:** `airport-cabs-numpy-analysis`
4. **Description:** `Exploratory analysis of NYC airport taxi trips using NumPy`
5. Choose **Public** (recruiters can see it).
6. **Do NOT** tick "Add a README" or ".gitignore" (you already have them locally).
7. Click **Create repository**.

### Step 4: Initialise Git and make the first commit

```bash
git init
git config user.name  "Your Name"
git config user.email "your-email@example.com"

git add .
git status                      # review what will be committed
git commit -m "Initial commit: add NYC airport taxi NumPy analysis"
```

### Step 5: Connect to GitHub and push

```bash
git branch -M main
git remote add origin https://github.com/<your-username>/airport-cabs-numpy-analysis.git
git push -u origin main
```

When prompted for a password, use a **Personal Access Token** (GitHub → Settings → Developer settings → Personal access tokens), because GitHub no longer accepts account passwords for Git operations.

### Step 6: Make realistic commits afterwards

Real repositories grow in small steps. Instead of one giant commit, commit as you improve:

```bash
git add notebooks/airport_cabs_analysis.ipynb
git commit -m "Fix payment type and distance columns in analysis"

git add README.md DOCUMENTATION.md
git commit -m "Update docs with corrected results"

git push
```

**Good commit message style:** start with a verb, keep it under about 70 characters (`Add`, `Fix`, `Update`, `Refactor`).

### Step 7: Polish the repository page
- Add **Topics** (right sidebar → gear icon): `numpy`, `python`, `data-analysis`, `jupyter-notebook`, `nyc-taxi`.
- Pin the repository to your profile (Profile → **Customize your pins**).
- Add the repo link to your resume under Projects.

---

## 8. Code Walkthrough

```python
import numpy as np

# Load the CSV; skip the header row
taxi = np.genfromtxt('../data/nyc_taxis.csv', delimiter=',', skip_header=True)
print(taxi.shape)                     # (89560, 15)

# Q1: average speed in mph  (miles / hours)
speed = taxi[:, 7] / (taxi[:, 8] / 3600)
print(speed.mean())                   # 32.24...
print(np.median(speed))               # 22.54... (more robust)

# Q2: rides in February (pickup_month == 2)
feb = taxi[taxi[:, 1] == 2]
print(feb.shape[0])                   # 13333

# Q3: rides with distance over 50 miles
long_trips = taxi[taxi[:, 7] > 50]
print(long_trips.shape[0])            # 8

# Q4: cash rides (payment_type == 2)
cash = taxi[taxi[:, 14] == 2]
print(cash.shape[0])                  # 24520

# Q5: tips over $50
big_tips = taxi[taxi[:, 12] > 50]
print(big_tips.shape[0])              # 16

# Q6: rides dropped off at location code 2
drop2 = taxi[taxi[:, 6] == 2]
print(drop2.shape[0])                 # 11832
```

---

## 9. Important Notes & Corrections

While reviewing the original notebook (`airpotcab.ipynb`), I verified every output against the CSV and found issues you should fix before publishing, so the repository is accurate.

| Original cell | Problem | Fix |
|---|---|---|
| `taxi[taxi[:, -3] > 50, -3]` with comment "distance greater than 50 miles" | Index `-3` is **`tip_amount`** (column 12), not distance. The result `16` is the count of **tips over $50**. | Use column `7` for distance → **8** rides over 50 miles |
| `taxi[taxi[:, 6] == 2, 6]` with comment "payment type 2 (credit card)" | Column `6` is **`dropoff_location_code`**. The result `11832` is rides dropped at location 2. Payment type is column `14`. | Use `taxi[:, 14] == 2` → **24,520** rides |
| Comment "payment type 2 (credit card)" | In this dataset type **1 = credit card, 2 = cash** | Fix the comment |
| `rides_feb = taxi[taxi[:, 1] == 2, 1]` | Works (13,333) but selects only the month column | Use `taxi[taxi[:, 1] == 2]` to keep full rows |
| Mean speed `32.24` | Inflated by outliers (some trips last 1 second; max "speed" is 82,800 mph) | Report the median (22.54 mph) or filter unrealistic speeds |

Reporting both the original output and the corrected one is good practice: it shows data-validation skills, which analysts are hired for.

> Note: I could only see the notebook and CSV you uploaded. No images with chart/question notes came through, so the questions in Section 3 are taken from the notebook's cells. If your notes list other questions, send the image again and I will add them.

---

## 10. Requirements, .gitignore and License

**requirements.txt**
```
numpy>=1.26
jupyter
```

**.gitignore**
```
venv/
__pycache__/
.ipynb_checkpoints/
.DS_Store
```

**License:** MIT. Create a `LICENSE` file with the standard MIT text and your name and year (2026).

---

## 11. Future Improvements

- Add `matplotlib` charts: speed distribution, rides per month, payment type split
- Filter outliers (for example, speed over 100 mph or trip length under 60 seconds) and compare results
- Compute average fare, tip percentage and revenue per mile by drop-off location
- Re-implement the analysis in pandas and compare with NumPy
- Add a `src/` module with reusable functions and unit tests

---

## 12. Contact

**Pakinati Likith Reddy**
Entry-level Data Analyst | Python · SQL · Power BI · Excel
GitHub: `https://github.com/<your-username>` · LinkedIn: `<your-linkedin-url>`

If you found this project useful, please give it a star.
