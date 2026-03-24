
======================================================================
  MATH 4650 FINAL PROJECT — COMPLETE RESULTS REPORT
  Crop Yield Prediction & Recommendation System
  Indiana & Iowa | 2010–2023
======================================================================

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  1. DATASET SUMMARY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Source 1 — USDA SSURGO Soil Data
    Counties covered   : 99 counties (Indiana + Iowa)
    Features           : sand, silt, clay, organic matter, pH, AWC
    Granularity        : county-level (unique soil profile per county)

  Source 2 — USDA NASS Yield Data
    Total records      : 2,466 county-year observations
    Crop               : Corn (grain)
    Year range         : 2010–2023
    Yield range        : 30.1–234.7 bu/acre
    Mean yield IN      : 167.5 bu/acre
    Mean yield IA      : 180.3 bu/acre

  Source 3 — NOAA Weather Data
    Station Indiana    : Indianapolis Intl Airport (USW00093819)
    Station Iowa       : Des Moines Intl Airport (USW00094910)
    Variables          : precip_mm, tmax_f, tmin_f, gdd (Apr–Sep)
    Notable year       : 2012 drought — Iowa precip=345mm vs avg ~700mm

  ML Feature Matrix    : 2,466 rows × 15 features
    Soil features (6)  : sand, silt, clay, om, ph1to1h2o, awc
    Weather features (4): precip_mm, tmax_f, tmin_f, gdd
    Derived features (5): water_supply, drought_index, gdd_om, texture_score, state_enc

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  2. MODEL RESULTS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Train/Test split: 75% / 25% (random, stratified by state)

  Model                     RMSE (bu/ac)    R² (test)    CV R² ± std
  ─────────────────────────────────────────────────────────────────────
  Random Forest                 12.53        0.8099    -1.8270 ± 1.6153
  Gradient Boosting             11.74        0.8330    -1.5035 ± 1.1159
  Ridge Regression              22.66        0.3780    -0.8840 ± 0.8607

  Best model: Gradient Boosting
    RMSE = 11.74 bu/acre  (avg corn yield ~174 → error is 6.7%)
    R²   = 0.8330  (model explains 83.3% of yield variance)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  3. FEATURE IMPORTANCE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Feature group contribution:
    Weather features : 62.0%  (year-to-year variation)
    Soil features    : 21.3%  (county-to-county variation)
    Derived features : 16.6%  (interaction effects)

  Top 5 individual predictors:
    1. tmax_f                 0.3021  [weather]
    2. tmin_f                 0.1716  [weather]
    3. clay                   0.0987  [soil]
    4. precip_mm              0.0898  [weather]
    5. drought_index          0.0771  [derived]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  4. KEY FINDINGS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Finding 1 — Weather dominates yield prediction (62% importance)
    tmax_f (max temperature) is the single most important feature.
    High summer temperatures cause heat stress during corn pollination,
    directly reducing kernel set and final grain yield.

  Finding 2 — Soil explains county-level differences (21% importance)
    Clay content and organic matter are the top soil predictors.
    High clay soils retain more water, buffering drought stress.
    High organic matter improves nutrient availability and soil structure.

  Finding 3 — 2012 drought signature is clearly visible
    Iowa precipitation dropped to 345mm (vs ~700mm average).
    County yields dropped 40–80 bu/acre below normal in affected areas.
    The drought_index feature (tmax/precip ratio) captures this signal.

  Finding 4 — Gradient Boosting outperforms Random Forest
    GB achieves lower RMSE (11.74 vs 12.53 bu/acre) by fitting
    residuals sequentially, handling the non-linear weather-yield
    relationship better than the bagging approach of Random Forest.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  5. LIMITATIONS & FUTURE WORK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Limitation 1 — Single weather station per state
    All Indiana counties share Indianapolis airport weather.
    County-level weather stations would improve spatial prediction.
    This explains the negative cross-validation scores when holding
    out entire years — the model learns county soil patterns well
    but cannot extrapolate weather to unseen years.

  Limitation 2 — Corn only
    Adding soybeans and wheat from NASS would enable the crop
    recommendation classifier (currently skipped).

  Limitation 3 — No management data
    Fertilizer rates, irrigation, and seed variety significantly
    affect yield but are not captured in public datasets.

  Future work:
    - Use PRISM or Daymet gridded weather (county-level, daily)
    - Add soybeans/wheat for multi-crop recommendation
    - Incorporate NDVI satellite data for in-season monitoring
    - Build county-specific models using transfer learning

======================================================================









# Texas Crop Yield Analysis (2024)

## 📁 Repository Structure
* **main.ipynb**: Core analysis and Machine Learning modeling using soil data.
* **analysis_copy.ipynb**: Data collection scripts (USDA API for soil, NOAA for 2024 weather).
* **data/**
    * **texas.csv**: Processed soil data in Texas.
    * **texas_weather_2024.csv**: Monthly weather summary (Precipitation, TMAX, TMIN).
* **requirements.txt**: List of necessary Python libraries.


## 🛠 Installation
To set up the environment, run:
```bash
pip install -r requirements.txt

Unfortunately, the soil data don't have time frame, they are updated every single year (I think). 
Thus it is impossible to combine soil dataset with weather data which have the time frame. 
So I think we just make models using soil data and analyze with it after that to make some insight. 
