# Crops Classification and Crop Yield Prediction of Texas Dataset — Math 4650 Final Project

## Project overview
This project provides a reproducible pipeline and analysis for addressing practical problems in crop agriculture: recommending suitable crops for a location and forecasting crop yields using soil and weather data.

What the repository contains:
- Raw and processed data used for analysis (soil surveys, GHCND weather aggregates, and merged yield-weather-soil tables).
- Jupyter notebooks that implement the end-to-end workflow: data collection and ETL, exploratory data analysis, feature engineering, classification (crop recommendation), and regression (yield prediction).
- Example model artifacts and visual outputs (plots and a local model checkpoint) produced by experiments.

How the project solves the problem:
- Data collection: fetches historical weather from NOAA and combines it with soil survey data and yield records to build location-level feature sets.
- Preprocessing & features: cleans and harmonizes identifiers, converts units, imputes missing values, and creates aggregated seasonal weather features and soil-derived predictors.
- Modeling: trains classification models to recommend crop categories and regression models to predict yield. Implemented algorithms include Random Forest, XGBoost, KNN, Lasso (polynomial features), and PyTorch neural networks.
- Evaluation: uses stratified cross-validation and metrics tailored to the task (G-Mean and confusion matrices for classification; MSE/RMSE and R² for regression) to ensure balanced performance and meaningful error estimates.

Intended users & impact:
- Agronomists, data scientists, and extension specialists can reproduce and adapt the notebooks to local data, enabling data-driven crop selection and yield forecasting that supports farm planning and risk management.

Limitations & next steps:
- Current notebooks are research-oriented and use local CSV artifacts; to productionize, add robust data versioning, configuration for API keys, and scripts to run experiments reproducibly.

## Data
The `data/` folder contains the datasets used for analysis and modeling:
- `texas_weather_2015_2025.csv` — weather observations 2015–2025
- `texas.csv` — soil data file
- `ghcnd-stations.txt` — station metadata

## Notebooks
Open the `notebooks/` folder to explore analyses and reproductions:
- `data_collection.ipynb`, `data_collection_for_texas.ipynb` — data ingestion & cleaning
- `crop_classification.ipynb` — modeling experiments
- `yield_prediction.ipynb` — yield prediction workflow

## Repository structure

- `data/` — raw and processed CSVs
- `Images/` — figures used in notebooks or presentations
- `notebooks/` — Jupyter notebooks for EDA and modeling
- `requirements.txt` — Python dependencies
- `Readme.md` — this file
- `memo.txt` — project notes

## Setup
Recommended: Python 3.8+ in a virtual environment.

Create & activate a venv, then install requirements:

```bash
python -m venv venv
# Windows
venv\Scripts\activate
pip install -r requirements.txt
```

## Usage
- Launch Jupyter Lab/Notebook and open the notebooks in `notebooks/` to run analyses and reproduce results.
- To run the yield prediction workflow, open `notebooks/yield_prediction.ipynb` and run cells top-to-bottom.

## Reproducing training
All training/experimentation steps are in the notebooks. To retrain models, run the relevant notebook cells that produce `best_model.pt`.

## Notes & next steps
- Continue EDA and feature engineering to improve model generalization.
- Add scripts to automate preprocessing and training (optional).

## Credits & Contact
Author: Math 4650 final project contributors.
Questions or contributions: open an issue or contact the project owner.

---
Updated: April 2026

**Resources & data collection**

This project uses real-world data sources and an API-driven ETL step to collect weather data. Key resources:

- NOAA GHCND (Global Historical Climatology Network - Daily) via the NOAA/Cdo Web API — used to fetch growing-season weather metrics by station. The data collection notebook (`notebooks/data_collection.ipynb`) includes the API calls and produces derived CSVs such as `noaa_weather_fixed.csv`.
- Soil survey / SSURGO-style soil property tables (provided as `data/texas.csv`).
- Merged datasets for analysis (`data/tx_merged_yield_weather_soil.csv`, `data/tx_ml_ready.csv`) are created by combining soil, weather, and yield records.

Security note: API tokens (NOAA or others) MUST NOT be committed. The data collection notebook currently references a `NOAA_TOKEN` variable; remove any hard-coded tokens and instead set an environment variable (example):

```bash
# Windows (PowerShell)
setx NOAA_TOKEN "your_noaa_token_here"

# macOS / Linux
export NOAA_TOKEN="your_noaa_token_here"
```

Do not commit `.env` files or notebooks with embedded secrets. The repo's `.gitignore` is configured to help prevent accidental commits of data, checkpoints, and environment files.

**Models, metrics, and outcomes (summary)**

All experiments and detailed numeric results are recorded in the notebooks. Below is a concise summary of the models, evaluation metrics, and where to find outcome visualizations and checkpoints.

- **Classification (notebooks/crop_classification.ipynb)**
	- Models implemented: Random Forest (baseline), Imbalanced Random Forest using SMOTE + under-sampling, XGBoost (XGBClassifier), K-Nearest Neighbors (KNN), and a PyTorch neural network classifier.
	- Evaluation metrics: Accuracy, geometric mean (G-Mean), confusion matrix; Stratified K-Fold cross-validation (G-Mean reported per fold). Confusion matrices and CV plots are generated in the notebook (look for saved images such as `cv_gmean_results.png`).
	- Outcomes: per-run numeric scores (accuracy, G-Mean) and confusion matrices are produced in `notebooks/crop_classification.ipynb`. Re-run the notebook to reproduce exact numbers for your environment and dataset subset.

- **Yield prediction (notebooks/yield_prediction.ipynb)**
	- Models implemented: Polynomial Lasso regression (PolynomialFeatures + Lasso), XGBoost regressor (`XGBRegressor`), and a PyTorch neural network regressor.
	- Evaluation metrics: Mean Squared Error (MSE), Root Mean Squared Error (RMSE), and R² (coefficient of determination). K-fold CV metrics and RMSE plots are produced in the notebook.
	- Outcomes: trained neural regressor saves `best_model.pt` via early stopping; numeric results (RMSE, R²) are printed in the notebook. Re-run `notebooks/yield_prediction.ipynb` to reproduce the reported metrics locally.

Where outputs are saved

- Plots produced by notebooks are expected to be saved to the working directory (the notebooks currently call `plt.savefig('cv_gmean_results.png', ...)` in several places). To keep the repo clean, move or save final figures into `Images/outputs/` before committing.
- Model checkpoints (e.g., `best_model.pt`) are saved locally by the notebooks but are excluded by `.gitignore`. If you want to share a checkpoint, upload it to external storage and add a small README pointer.

Reproducing results

1. Set your `NOAA_TOKEN` in the environment and ensure the `data/` folder contains the required soil and yield CSVs (or run the data collection notebook to fetch derived weather CSVs).
2. Open the relevant notebook (`notebooks/crop_classification.ipynb` or `notebooks/yield_prediction.ipynb`) in Jupyter and run cells top-to-bottom.
3. Inspect printed metrics, saved plots in the notebook directory, and exported artifacts.

## Datasets referenced by the notebooks
The primary datasets used by the notebooks are:

- `data/texas.csv` — soil survey / soil property table (used for soil features)
- `data/texas_weather_2015_2025.csv` — weather observations (monthly/growing-season aggregates)
- `data/texas_weather_2025.csv` — 2025 weather subset (used selectively)
- `data/tx_merged_yield_weather_soil.csv` — merged dataset combining yield, weather, and soil (analysis-ready)
- `data/tx_ml_ready.csv` — ML-ready processed dataset (if present)
- `data/ghcnd-stations.txt` — station metadata used for data collection/ETL

Note: Some notebooks produce intermediate files (for example `noaa_weather_fixed.csv` from the data collection notebook). Treat generated CSVs as derived artifacts — keep them locally but do not commit large raw datasets to the repo.


Recommended workflow to keep the repo small and clean:

1. Place final figures you want to keep in `Images/outputs/` (create this folder). Only files inside `Images/outputs/` are intended to be committed.
2. Keep raw/large datasets inside `data/` on your machine but do not add them to git. Use external storage (Google Drive, S3, or dataset managers) for sharing large files.
3. Save model checkpoints locally (e.g. `best_model.pt`) but avoid committing them to the repository. If you must share a checkpoint, upload it to external storage and include a small README pointer.

## .gitignore (created)
A `.gitignore` file has been added to this repository to help avoid accidental commits of large or unwanted files. It ignores common large artifacts (data, environments, model files) and keeps only `Images/outputs/` for committed figures.


**Evaluation Results (summary)**

Below are the evaluation metrics produced by the notebooks and where to find their outputs. Use these as the canonical set of metrics when reporting model performance.

- Classification (`notebooks/crop_classification.ipynb`):
	- Primary metrics: **Accuracy**, **Geometric Mean (G-Mean)** (weighted where applicable).
	- Secondary outputs: **Confusion Matrix** visualizations and per-fold Stratified K-Fold scores (mean ± std).
	- How to reproduce: run `notebooks/crop_classification.ipynb` top-to-bottom; the notebook prints per-run accuracy and G-Mean and runs cross-validation that reports fold G-Mean values and averages.

- Yield prediction (`notebooks/yield_prediction.ipynb`):
	- Primary metrics: **Mean Squared Error (MSE)**, **Root Mean Squared Error (RMSE)**, **R²**.
	- Saved outputs: CV RMSE plots (produced by the CV cells) and model checkpoint `best_model.pt` (saved locally by early stopping).
	- How to reproduce: run `notebooks/yield_prediction.ipynb` top-to-bottom; the notebook prints CV metrics (neg-MSE per fold → RMSE) and final test RMSE and R² for the regressor.

Notes on interpreting G-Mean and RMSE:

- G-Mean is useful for imbalanced multi-class classification because it balances recall across classes; we use it alongside accuracy to capture class-wise performance.
- RMSE is in the same units as the target (after any unit conversions applied in the notebook); the yield notebook applies `log1p` during training and transforms predictions back using `expm1` before reporting RMSE on the original scale.

