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
