# Diabetes Progression: A Data-Science Walkthrough

A beginner-friendly, end-to-end project: **clean -> explore -> visualise -> model** a real public healthcare dataset using pandas, NumPy, Matplotlib and a lightweight scikit-learn model.

## Dataset
**Diabetes progression study** - 442 diabetes patients, 10 baseline measurements (age, sex, BMI, average blood pressure, six blood-serum tests) and a quantitative score of **disease progression one year after baseline** (range 25-346).

- Source: Efron, Hastie, Johnstone & Tibshirani (2004), *Least Angle Regression*, Annals of Statistics. Data page: <https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html>
- Loaded through `sklearn.datasets.load_diabetes(scaled=False)` (original units, no download needed).
- The `sex` variable is coded 1/2 and the source does not document which is which; the project keeps neutral labels.

## Project steps
1. **Load & audit** - types, missing values, duplicates, ranges.
2. **Clean** - readable column names, integer/category type conversion, plausibility range checks, missing-value strategy (median imputation, never impute the target), duplicate removal.
3. **Outliers** - IQR rule; 40 values flagged, kept and recorded in `n_outlier_flags` (a robustness check confirms this does not change model results).
4. **EDA** - summary statistics, group comparisons, 8 figures.
5. **Model** - regression: baseline vs linear vs ridge, 80/20 split, 5-fold cross-validation.

> The raw dataset turned out to be already very clean (0 missing values, 0 duplicates, 0 impossible values), so the cleaning work is mostly types, naming and validation. The code handles missing/impossible values in case you swap in a messier dataset.

## Key results
| Insight | Finding |
|---|---|
| 1 | BMI (r = +0.59) and triglycerides (+0.57) are the strongest single signals; sex is ~unrelated (+0.04) |
| 2 | Average progression: ~109 (healthy BMI) -> ~165 (overweight) -> ~213 (obese) |
| 3 | HDL "good" cholesterol is protective (r = -0.39); triglycerides raise risk |
| 4 | Total cholesterol and LDL overlap heavily (r = 0.90), making individual model weights unreliable |

**Model (test set, 89 patients):**

| Model | Test R2 | Test MAE | Test RMSE | CV R2 |
|---|---|---|---|---|
| Baseline (mean) | -0.01 | 64.0 | 73.2 | -0.03 |
| Linear regression | 0.45 | 42.8 | 53.9 | 0.48 |
| Ridge regression | 0.45 | 42.8 | 53.8 | 0.48 |

In plain language: the model explains about **45%** of the differences between patients and cuts the typical prediction error by about **one third** versus guessing the average (about 43 points off, on a scale spanning ~25-346). Useful but modest; it is not a clinical tool.

## Files
```
diabetes_progression_analysis.ipynb   # full notebook (code, charts, narrative)
data/diabetes_clean.csv               # cleaned dataset (442 rows x 15 columns)
data/diabetes_raw.csv                 # untouched raw copy
figures/*.png                         # all saved charts
requirements.txt
```

## Run it
```bash
pip install -r requirements.txt
jupyter notebook diabetes_progression_analysis.ipynb   # then Run All
```
Python 3.9+ . Fixed seed (`RANDOM_STATE = 42`) for reproducibility.

## Limitations
Small single-study sample; linear model only; associations, not causation. Numbers quoted come from a seed-42 run and may shift slightly across library versions.
