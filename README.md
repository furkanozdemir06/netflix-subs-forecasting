# Netflix Subscriptions Forecasting

A time series project that analyzes Netflix's quarterly subscriber counts, forecasts future growth with ARIMA and SARIMA models, and tests whether those models actually beat a simple baseline.

## Overview

The notebook explores 42 quarters of Netflix subscriber data, visualizes quarterly and year-over-year growth, checks the autocorrelation structure of the series, fits an ARIMA(1, 1, 1) model, and forecasts the next 5 quarters. A backtest on the last 6 quarters then compares ARIMA, SARIMA, and a naive baseline, so the forecast can be judged on evidence instead of taken at face value.

## Dataset

The dataset (`Netflix-Subscriptions.csv`) contains quarterly subscriber counts from Q2 2013 to Q3 2023.

| Column | Description |
|--------|-------------|
| `Time Period` | First day of the quarter (`dd/mm/yyyy`) |
| `Subscribers` | Total subscribers at that quarter |

The data has 42 observations and no missing values. Subscribers grew from about 34.2 million (April 2013) to about 238.4 million (July 2023).

## Workflow

1. **Exploratory data analysis**
   - Checked shape, data types, missing values, and summary statistics.
   - Converted `Time Period` to a datetime type.
   - Plotted recent subscriber growth as a line chart built directly from the DataFrame.
2. **Growth analysis (Plotly)**
   - Quarterly growth rate bar chart, with positive and negative growth colored differently.
   - Year-over-year growth rate bar chart (each quarter compared with the same quarter one year earlier).
3. **Time series diagnostics**
   - Differenced the series and plotted ACF and PACF to guide the choice of ARIMA orders.
4. **Modeling**
   - Fitted `ARIMA(1, 1, 1)` with `statsmodels` on the full series and forecasted the next 5 quarters.
5. **Model evaluation**
   - Held out the last 6 quarters as a test set and trained on the first 36.
   - Compared ARIMA(1, 1, 1), SARIMA(1, 1, 1)(1, 1, 1, 4), and a naive baseline ("next quarter = last quarter") using MAE, RMSE, and MAPE.
   - Refit ARIMA on all data and plotted the 5-quarter forecast with a 95% confidence interval.

## Results

### Backtest (last 6 quarters)

| Model | MAE (M) | RMSE (M) | MAPE (%) |
|-------|---------|----------|----------|
| ARIMA(1, 1, 1) | 12.671 | 13.155 | 5.539 |
| SARIMA(1, 1, 1)(1, 1, 1, 4) | 12.997 | 13.523 | 5.673 |
| **Naive baseline** | **6.457** | **8.850** | **2.762** |

The naive baseline beat both models by a wide margin, and adding seasonality did not help: SARIMA had a lower AIC on the training set (1026.46 vs. 1151.75) but a higher test error. The ARIMA AR coefficient is about 0.9997, which makes the model keep extrapolating the earlier, faster growth. Subscriber growth in the test window was slower than that trend, so the forecasts overshoot.

### 5-quarter forecast (ARIMA, full data)

| Quarter | Forecast (millions) |
|---------|---------------------|
| Oct 2023 | 243.32 |
| Jan 2024 | 248.25 |
| Apr 2024 | 253.18 |
| Jul 2024 | 258.11 |
| Oct 2024 | 263.03 |

The model projects subscribers to pass 250 million in the April 2024 quarter. Given the backtest, this should be read as an optimistic estimate rather than a reliable prediction.

## Tech Stack

- Python
- pandas, NumPy
- statsmodels (ARIMA, SARIMAX, ACF/PACF)
- scikit-learn (error metrics)
- Plotly
- matplotlib, seaborn
