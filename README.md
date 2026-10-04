# Demand Forecasting with Uncertainty Quantification

This project forecasts retail demand and quantifies forecast uncertainty using machine learning.

## Project Overview

The goal is to predict daily product demand while also estimating uncertainty around the forecasts.

Three quantile forecasts are generated:

- **P10** — lower demand estimate
- **P50** — central demand forecast
- **P90** — upper demand estimate

The P10–P90 range forms an 80% prediction interval.

## Dataset

The project uses the **Store Item Demand Forecasting Challenge** dataset from Kaggle.

The dataset contains daily sales records for:

- 10 stores
- 50 items
- January 2013 to December 2017

The main columns are:

- `date` — date of the sale
- `store` — store identifier
- `item` — product identifier
- `sales` — number of units sold

The dataset can be obtained from the official Kaggle competition:
https://www.kaggle.com/competitions/demand-forecasting-kernels-only/data

## Methodology

### 1. Feature Engineering

Time-based features are created from the date:

- Year
- Month
- Day of week
- Day of month
- Week of year

Lag features:

- 1-day lag
- 7-day lag
- 14-day lag

Rolling statistics:

- 7-day rolling mean
- 14-day rolling mean
- 7-day rolling standard deviation

Only previous observations are used for lag and rolling features to avoid data leakage.

### 2. Forecasting Model

LightGBM gradient boosting regression is used with quantile loss.

Three separate models are trained:

- Quantile 0.10
- Quantile 0.50
- Quantile 0.90

A chronological split is used:

- **Training:** 2013–2016
- **Evaluation:** 2017

### 3. Evaluation

Point forecast accuracy is measured using:

- Mean Absolute Error (MAE)
- Root Mean Squared Error (RMSE)

Prediction interval calibration is evaluated using empirical coverage.

The P10–P90 interval has a nominal coverage of 80%.

## Results

The model achieved the following results on the 2017 evaluation period:

| Metric | Result |
|---|---:|
| P50 MAE | 6.22 |
| P50 RMSE | 8.10 |
| P10–P90 Coverage | 79.63% |
| Target Coverage | 80% |
| Average Interval Width | 19.87 |

For the highest-demand 10% of observations, interval coverage decreased to **75.96%**, indicating that uncertainty estimates are less reliable during demand spikes.

## Inventory Decision

Two inventory strategies are compared:

- **P50 strategy:** stock according to the central forecast
- **P90 strategy:** stock according to the upper forecast

Under an illustrative cost structure where leftover inventory costs 1 unit and a stockout costs 3 units:

| Strategy | Average Cost |
|---|---:|
| P50 | 12.78 |
| P90 | 11.64 |

The P90 strategy results in fewer stockouts and a lower average cost under these assumed costs.

## Limitations

The dataset does not contain explicit promotion information. Therefore, promotion-specific effects cannot be directly modeled.

High-demand observations are used as a proxy for demand spikes when analyzing uncertainty.

## Repository Contents

- `Demand_Forecasting_Uncertainty.ipynb` — complete project notebook

## Reproducibility

### 1. Install dependencies

Install the required Python packages:

```bash
pip install -r requirements.txt

### 2. Download the dataset

Download `train.csv` from the Store Item Demand Forecasting Challenge:

https://www.kaggle.com/competitions/demand-forecasting-kernels-only/data

For Google Colab, upload `train.csv` so that it is available at:

```text
/content/train.csv
### 3. Run the notebook

Open `Demand_Forecasting_Uncertainty.ipynb` in Google Colab or Jupyter Notebook.

Run the cells from top to bottom.
### 4. Re-run evaluation

The evaluation period is the year 2017, while 2013–2016 is used for training.

Running the notebook from top to bottom reproduces the reported evaluation metrics and plots.
