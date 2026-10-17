# Delhivery – Feature Engineering & Trip Analysis

End-to-end case study on Delhivery's logistics data: cleaning segment-level records, merging them into trip-level features, testing planned (OSRM) vs actual performance, and turning the findings into business recommendations.

## Problem
Delhivery's pipeline emits one row per **segment** of a trip. To support delivery-time forecasting, the data must be cleaned, aggregated to trip level, feature-engineered and made model-ready.

## Dataset
- 144,867 segment rows × 24 columns → **14,817 unique trips**
- Trips created 12 Sep – 3 Oct 2018
- Source: Delhivery dataset from the Scaler DSML course (file not included due to size)

## Approach
| Step | What I did |
|---|---|
| 1. Cleaning | Datetime conversion, category dtypes, recovered missing centre names via centre-id mapping, labelled the remaining ~0.2% as `Unknown` instead of dropping |
| 2. Aggregation | Cumulative fields → `last` per leg; `segment_*` fields → `sum`; legs → trips by `sum` |
| 3. Feature engineering | City / place / code / state split from names, time features (hour, weekday, day), `od_time_diff`, state and city corridors |
| 4. Hypothesis tests | Paired Wilcoxon (primary) and paired t-test on 5 metric pairs, plus mean gap and correlation |
| 5. Outliers | 1.5×IQR flags 8–11% of trips; **winsorised** (not deleted) to keep all 14,817 trips |
| 6. Encoding & scaling | One-hot (route type, data split, weekday), MinMaxScaler |
| 7. Insights | Corridor volume, route-type mix, time-of-day demand, slowest states vs OSRM |

## Key findings
- **Real trips take ~2.2× the OSRM estimate** (+121% on average, r ≈ 0.96), so OSRM is a good predictor but needs a correction factor.
- **60% of trips are Carting** (short, ~40 km, 1.4 legs); **40% are FTL** (~350 km, 2.4 legs).
- Traffic is mostly **intra-state**: Maharashtra, Karnataka, Haryana lead. Busiest city corridor: Bengaluru → Bengaluru (~1,376 trips).
- **Slowest vs plan** (states with 100+ trips): Bihar (~2.9×), West Bengal (~2.7×), Assam (~2.6×). Short Mumbai–Bhiwandi runs are 3–3.8× OSRM.
- Demand peaks **8 pm – midnight**; Wednesday busiest, Sunday quietest.
- Distance, OSRM time and actual time are highly correlated (r > 0.9), so use regularisation / tree models or keep one per family.

## Recommendations
1. Quote ETAs with a real-world factor (~2× typical, higher for remote states).
2. Add capacity in Maharashtra, Karnataka, Haryana, especially Carting in city corridors.
3. Staff for the evening rush and Wednesdays.
4. Investigate the slowest routes to find where time is lost.
5. Fix data quality: one name per city/centre, fill missing centre names.
6. Track FTL on-time performance separately.

## Tech stack
Python · Pandas · NumPy · Matplotlib · Seaborn · SciPy · scikit-learn

## Run it
```bash
git clone https://github.com/Shetty-Shwetha/delhivery-feature-engineering.git
cd delhivery-feature-engineering
pip install -r requirements.txt
# place delhivery_data.csv in the project root, then:
jupyter notebook delhivery_case_study.ipynb
```

## Author
**Shwetha Shetty** – Business Analyst (NBFC / financial services) moving into data science.
[GitHub](https://github.com/Shetty-Shwetha)
