# ECON-5200-Assignment-1
# 1. Fetch: Retrieved 5 economic time series from FRED API
fred = Fred(api_key='YOUR_API_KEY')
official_cpi = fred.get_series('CPIAUCSL')
tuition = fred.get_series('CUSR0000SEEB')
# ... etc

# 2. Normalize: Re-indexed all series to 2016=100 baseline
base_value = series.loc['2016-01-01']
indexed_series = (series / base_value) * 100

# 3. Construct: Built weighted Student Price Index
student_spi = (0.40 * tuition_idx + 0.30 * rent_idx + 
               0.15 * food_idx + 0.10 * streaming_idx + 
               0.05 * other_idx)

# 4. Compare: Visualized three-way comparison
plt.plot(national_cpi, label='National CPI', color='grey')
plt.plot(boston_cpi, label='Boston CPI', color='blue')
plt.plot(student_spi, label='Student SPI', color='red')
cost-of-living-analysis/
├── data/
│   ├── fred_data.csv
│   └── student_basket.json
├── src/
│   ├── data_collection.py
│   ├── index_construction.py
│   └── visualization.py
├── charts/
│   └── inflation_comparison.png
├── README.md
└── requirements.txt
