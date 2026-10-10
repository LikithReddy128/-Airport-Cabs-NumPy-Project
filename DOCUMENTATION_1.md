# Technical Documentation: Airport Cabs NumPy Project

**Project:** NYC Airport Taxi Analysis with NumPy
**Author:** Pakinati Likith Reddy
**Version:** 1.0
**Notebook:** `notebooks/airport_cabs_analysis.ipynb`
**Dataset:** `data/nyc_taxis.csv`

---

## 1. Purpose and Scope

This document explains the data, methodology, code logic, results and limitations of the project. For setup and GitHub instructions, see [`README.md`](README.md).

**Objective:** use NumPy only (no pandas) to load a taxi trip dataset, inspect its shape, and answer business questions through slicing, boolean masking and vectorised calculation.

**Out of scope:** visualisation, machine learning, and fare prediction.

---

## 2. Data Description

### 2.1 Source file
- Format: CSV, comma-delimited, with one header row
- Records: 89,560 rides (89,561 lines including the header)
- Fields: 15 numeric columns
- Period: January to June 2016
- All values are numeric, so `np.genfromtxt` loads the file as a single `float64` array

### 2.2 Data dictionary

| Idx | Column | Unit / Values | Observed range |
|---|---|---|---|
| 0 | pickup_year | year | 2016 only |
| 1 | pickup_month | 1–12 | 1 to 6 |
| 2 | pickup_day | day of month | 1 to 31 |
| 3 | pickup_dayofweek | 1–7 | 1 to 7 |
| 4 | pickup_time | hour | 0 to 5 |
| 5 | pickup_location_code | category code | 0 to 6 |
| 6 | dropoff_location_code | category code | 0 to 7 |
| 7 | trip_distance | miles | 0 to 182.9 |
| 8 | trip_length | seconds | 1 to 86,383 |
| 9 | fare_amount | USD | -52 to 400 |
| 10 | fees_amount | USD | -1.8 to 6.3 |
| 11 | tolls_amount | USD | -5.54 to 805.54 |
| 12 | tip_amount | USD | 0 to 100 |
| 13 | total_amount | USD | -58.34 to 834.84 |
| 14 | payment_type | 1 credit card, 2 cash, 3 no charge, 4 dispute | 1 to 4 |

### 2.3 Payment type distribution

| Code | Meaning | Rides |
|---|---|---|
| 1 | Credit card | 64,356 |
| 2 | Cash | 24,520 |
| 3 | No charge | 560 |
| 4 | Dispute | 124 |

### 2.4 Rides per month

| Month | Rides |
|---|---|
| 1 | 13,481 |
| 2 | 13,333 |
| 3 | 15,547 |
| 4 | 14,810 |
| 5 | 16,650 |
| 6 | 15,739 |

### 2.5 Summary statistics

| Metric | Value |
|---|---|
| Average trip distance | 12.67 miles |
| Average trip duration | 37.3 minutes |
| Average fare | $38.40 |
| Average tip | $5.81 |
| Average total amount | $48.97 |

---

## 3. Methodology

### 3.1 Loading

```python
taxi = np.genfromtxt('data/nyc_taxis.csv', delimiter=',', skip_header=True)
```

- `delimiter=','` splits fields on commas
- `skip_header=True` ignores the column-name row so the array stays numeric
- Result: `taxi.shape == (89560, 15)`

### 3.2 Understanding `.shape`

| Expression | Meaning | Value |
|---|---|---|
| `taxi.shape` | (rows, columns) | (89560, 15) |
| `taxi.shape[0]` | number of rides | 89,560 |
| `taxi.shape[1]` | number of fields | 15 |
| `taxi[:, 7].shape` | one column of data | (89560,) |

After filtering with a mask, `filtered.shape[0]` gives the **count of matching rides**. This is the main counting technique in the project.

### 3.3 Indexing patterns used

| Pattern | Meaning |
|---|---|
| `taxi[:, 7]` | all rows, column 7 |
| `taxi[:, -1]` | all rows, last column |
| `taxi[mask]` | rows where mask is True |
| `taxi[mask, 7]` | column 7 of the matching rows |

### 3.4 Boolean masking (how filtering works)

```python
mask = taxi[:, 1] == 2      # array of 89,560 True/False values
feb  = taxi[mask]           # keeps only rows where mask is True
count = feb.shape[0]        # 13,333
```

A mask can also be summed directly: `mask.sum()` counts the `True` values and gives the same answer without creating a filtered copy.

### 3.5 Vectorised speed calculation

```python
speed = taxi[:, 7] / (taxi[:, 8] / 3600)
```

- Column 7 is distance in miles
- Column 8 is duration in seconds; dividing by 3600 converts it to hours
- Miles divided by hours equals miles per hour
- NumPy applies the operation to all 89,560 rows at once, with no Python loop

---

## 4. Analysis Steps and Results

| Step | Question | Code | Result |
|---|---|---|---|
| 1 | Dataset size | `taxi.shape` | (89560, 15) |
| 2 | Average speed | `speed.mean()` | 32.24 mph |
| 3 | Median speed | `np.median(speed)` | 22.54 mph |
| 4 | February rides | `taxi[taxi[:,1]==2].shape[0]` | 13,333 |
| 5 | Distance over 50 miles | `taxi[taxi[:,7]>50].shape[0]` | 8 |
| 6 | Cash payments | `taxi[taxi[:,14]==2].shape[0]` | 24,520 |
| 7 | Tips over $50 | `taxi[taxi[:,12]>50].shape[0]` | 16 |
| 8 | Drop-off at code 2 | `taxi[taxi[:,6]==2].shape[0]` | 11,832 |

### Interpretation
- **Speed:** the median of 22.5 mph is a more realistic figure for NYC airport trips than the 32.2 mph mean, which is pulled upward by invalid records.
- **Payments:** about 72% of rides were paid by credit card (64,356 of 89,560) and about 27% by cash (24,520).
- **Monthly pattern:** May was the busiest month (16,650 rides) and February the quietest (13,333) out of the six months.
- **Extremes:** only 8 rides exceeded 50 miles and 16 had tips above $50, so these are rare events.

---

## 5. Data Quality Findings

| Issue | Evidence | Impact | Suggested handling |
|---|---|---|---|
| Implausible speeds | Max computed speed is 82,800 mph; 150 rides exceed 100 mph | Inflates the mean | Filter `speed < 100` or use the median |
| Extremely short trips | Minimum `trip_length` is 1 second | Causes the extreme speeds above | Drop trips under 60 seconds |
| Negative money values | Fare as low as -52, total as low as -58.34 | Likely refunds or corrections | Exclude or investigate |
| Extreme tolls | Maximum toll is $805.54 | Probable data-entry error | Cap or review |
| Undocumented location codes | CSV contains codes, not names | Cannot name airports or boroughs from this file alone | Obtain a lookup table before labelling |

Missing values: none detected (no NaN values in the speed calculation or column statistics).

---

## 6. Corrections to the Original Notebook

The original notebook (`airpotcab.ipynb`) contained three indexing mistakes found during verification. They are recorded here for transparency.

| # | Original code | What it actually measured | Corrected code | Correct result |
|---|---|---|---|---|
| 1 | `taxi[taxi[:, -3] > 50, -3]` | Tips over $50 (column 12) | `taxi[taxi[:, 7] > 50]` | 8 rides over 50 miles |
| 2 | `taxi[taxi[:, 6] == 2, 6]` | Drop-off location code 2 | `taxi[taxi[:, 14] == 2]` | 24,520 cash rides |
| 3 | Comment "2 (credit card)" | Wrong label | Payment type 2 is cash | Comment fixed |

**Lesson:** always check the column index map before slicing. Using named constants removes this risk:

```python
DISTANCE, DURATION, TIP, PAYMENT = 7, 8, 12, 14
taxi[taxi[:, DISTANCE] > 50]
```

---

## 7. Reproducibility

| Item | Detail |
|---|---|
| Python | 3.12 (3.9+ works) |
| Libraries | numpy, jupyter |
| Random elements | none; results are deterministic |
| Run order | execute notebook cells top to bottom |
| Path | use `../data/nyc_taxis.csv` when the notebook runs from `notebooks/` |

Verification script (run to confirm all numbers):

```python
import numpy as np
t = np.genfromtxt('data/nyc_taxis.csv', delimiter=',', skip_header=True)
assert t.shape == (89560, 15)
assert (t[:, 1] == 2).sum() == 13333
assert (t[:, 7] > 50).sum() == 8
assert (t[:, 14] == 2).sum() == 24520
assert (t[:, 12] > 50).sum() == 16
assert (t[:, 6] == 2).sum() == 11832
print("All checks passed")
```

---

## 8. Limitations

- Only six months of 2016 data are covered, so seasonal conclusions are limited
- `pickup_time` ranges from 0 to 5 in this file, suggesting a late-night/early-morning slice, so results may not represent all-day traffic
- The CSV does not state which airport each location code represents
- NumPy arrays hold one data type, so there are no column names; indexing by number is error-prone compared with pandas

---

## 9. Future Work

1. Plot speed, fare and payment distributions with Matplotlib
2. Build a cleaned dataset after removing outliers and compare statistics
3. Calculate tip percentage (`tip / fare`) and revenue per mile
4. Compare average fare by pickup and drop-off code using `np.unique` with `return_counts`
5. Repeat the analysis in pandas to compare readability and performance
6. Wrap the logic in functions with unit tests

---

## 10. Glossary

| Term | Meaning |
|---|---|
| ndarray | NumPy's N-dimensional array |
| shape | Tuple giving the size of each dimension |
| Boolean mask | Array of True/False values used to filter rows |
| Vectorisation | Applying an operation to a whole array without a Python loop |
| Outlier | A value far outside the normal range, often a data error |
| Median | Middle value; less affected by outliers than the mean |

---

## 11. Version History

| Version | Date | Change |
|---|---|---|
| 1.0 | 2026-10-10 | Initial documentation with verified results and corrections |
