# ECON-5200-Assignment-1
# The Cost of Living Crisis: A Data-Driven Analysis

## The Problem
Official CPI is a national average. For students—especially in Boston—budgets are concentrated in tuition and rent. This project asks whether national CPI understates student cost-of-living inflation.

## Methodology
- Data source: FRED via `fredapi`
- Proxies (CPI subcomponents): Tuition, Rent, Food Away From Home, Cable & Streaming
- Normalize all series to **2016 = 100** to compare growth across different base years
- Construct a weighted **Student SPI** (Laspeyres-style weighted index)

## Key Findings
- Tuition and rent generally outpace official CPI.
- Student SPI exceeds national CPI, revealing an “inflation gap.”
- Boston CPI can diverge from national CPI, highlighting regional pressure.

## How to Run
1. Open the notebook in `notebooks/` (Colab or Jupyter).
2. Add your FRED API key.
3. Run cells top-to-bottom to reproduce charts.

## Repo Structure
```text
.
├── notebooks/
│   └── econ5200_cost_of_living.ipynb
├── figures/
└── README.md

---

## 2) `econ5200_cost_of_living.py`（核心代码脚本，可放 `src/` 或直接在 notebook 用）

```python
import pandas as pd
import matplotlib.pyplot as plt
from fredapi import Fred

# -----------------------------
# CONFIG
# -----------------------------
START_DATE = "2016-01-01"
BOSTON_SERIES_ID = "CUURA103SA0"  # Boston-Cambridge-Newton CPI-U: All Items (NSA)

SERIES = {
    "CPI": "CPIAUCSL",
    "Tuition": "CUSR0000SEEB",
    "Rent": "CUSR0000SEHA",
    "Streaming": "CUSR0000SERA02",
    "Food": "CUSR0000SEFV",
}

WEIGHTS = {
    "Tuition": 0.4,
    "Rent": 0.3,
    "Food": 0.2,
    "Streaming": 0.1,
}

# -----------------------------
# HELPERS
# -----------------------------
def normalize_to_2016(series: pd.Series, start_date: str = START_DATE) -> pd.Series:
    series = series.dropna().sort_index()
    base = series.loc[pd.Timestamp(start_date)]
    return (series / base) * 100

def fetch_series(fred: Fred, series_map: dict) -> pd.DataFrame:
    out = {}
    for name, code in series_map.items():
        s = fred.get_series(code)
        s.index = pd.to_datetime(s.index)
        out[name] = s
    return pd.DataFrame(out).sort_index()

def compute_student_spi(df_indexed: pd.DataFrame, weights: dict) -> pd.Series:
    spi = 0
    for col, w in weights.items():
        spi += df_indexed[col] * w
    return spi

# -----------------------------
# MAIN PIPELINE (call these in notebook)
# -----------------------------
def build_index_df(fred: Fred) -> pd.DataFrame:
    df_raw = fetch_series(fred, SERIES)

    # normalize each column to 2016=100
    df_indexed = pd.DataFrame(
        {c: normalize_to_2016(df_raw[c]) for c in df_raw.columns}
    )

    # trim to 2016+
    df_indexed = df_indexed.loc[START_DATE:].copy()

    # student SPI
    df_indexed["Student_SPI"] = compute_student_spi(df_indexed, WEIGHTS)

    return df_indexed

def add_boston_cpi(fred: Fred, df_indexed: pd.DataFrame) -> pd.DataFrame:
    boston = fred.get_series(BOSTON_SERIES_ID)
    boston.index = pd.to_datetime(boston.index)
    boston_idx = normalize_to_2016(boston, START_DATE)

    out = df_indexed.copy()
    out["Boston_CPI"] = boston_idx.reindex(out.index).ffill()
    return out

def plot_three(df: pd.DataFrame):
    plt.figure(figsize=(12, 6))
    plt.plot(df.index, df["CPI"], label="National CPI", color="grey")
    plt.plot(df.index, df["Boston_CPI"], label="Boston CPI", color="blue")
    plt.plot(df.index, df["Student_SPI"], label="Student SPI", color="red")
    plt.title("National CPI vs Boston CPI vs Student SPI (2016 = 100)")
    plt.xlabel("Year")
    plt.ylabel("Index")
    plt.legend()
    plt.grid(True)
    plt.show()
