[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/065010-AmanMalhi/adani-energy-demand-forecasting/blob/main/code/energy_forecasting.ipynb)
> Interactive Plotly charts may not fully render in GitHub preview.  
> Please open the notebook in Google Colab using the badge above.

Adani AI Labs – Hourly Energy Demand Forecasting

 1. Problem Overview

The objective of this assessment is to forecast hourly electricity consumption using six years of historical transmission system operator data.

The dataset represents a univariate time series with strong seasonal and autoregressive behavior.

The goal was to build a data-driven forecasting model that:

Accurately predicts future hourly demand

Demonstrates structured preprocessing

Applies meaningful feature engineering

Uses proper time-aware evaluation

Provides business-relevant insights

 2. Dataset Description

6 years of hourly electricity consumption

52,966 original observations

Variables:

Start time (UTC)

End time (UTC)

Electricity consumption (MWh)

The dataset reflects real-world grid operations and includes minor timestamp irregularities likely due to daylight saving transitions.

 3. Data Preprocessing

🔹 Timestamp Handling

Converted timestamps to datetime format

Sorted data chronologically

Validated time intervals

Retained minor 55/65-minute gaps (real operational behavior)

🔹 Data Cleaning

Removed redundant End time UTC

Prevented data leakage during feature creation

Ensured complete integrity before modeling

 4. Feature Engineering

Electricity demand exhibits strong temporal memory. Feature engineering focused on capturing:

🔹 Calendar Features

Hour of day

Day of week

Month

Quarter

Year

Weekend indicator

🔹 Lag Features (Autoregressive Memory)

lag_1 → Previous hour

lag_24 → Same hour previous day

lag_168 → Same hour previous week

🔹 Rolling Mean Features

24-hour rolling mean

168-hour rolling mean

Rolling calculations were shifted to ensure no data leakage.

 5. Train-Test Strategy

A chronological split was used instead of random shuffling:

First 80% → Training

Last 20% → Testing

This ensures realistic forecasting conditions where future values are predicted strictly from past data.

6. Model Selection and Justification

Several models were evaluated conceptually:

🔹 Linear Regression

Simple and interpretable

Assumes linearity

Underfits nonlinear seasonal patterns

🔹 Decision Tree

Captures nonlinearities

Prone to instability

🔹 Random Forest

Handles nonlinear relationships

Trees built independently

Does not sequentially correct errors

🔹 ARIMA 

Strong autoregressive modeling

Less flexible with engineered features

Limited with multiple nonlinear seasonal effects

✅ Why XGBoost Was Selected

XGBoost (Extreme Gradient Boosting) was chosen because:

Builds trees sequentially, correcting errors

Captures nonlinear interactions

Handles complex lag-based features effectively

Includes built-in regularization

Performs exceptionally well on structured tabular data

Requires no feature scaling

Given the lag-based transformation of the time series into a supervised regression problem, gradient boosting provided the optimal balance of flexibility and generalization.

 7. Baseline Benchmark

A naive benchmark was implemented:

Prediction = Demand at same hour previous day (lag_24)

Baseline Performance

MAE: 366.78

RMSE: 507.48

MAPE: 3.73%

This captures daily seasonality but ignores short-term momentum.

 8. Final Model Performance (Tuned XGBoost)

MAE: 80.07

RMSE: 112.10

MAPE: 0.83%

The model reduced forecasting error by approximately 78% compared to the naive baseline, demonstrating the importance of incorporating autoregressive memory and rolling trend features.

 9. Model Insights
🔹 Short-Term Inertia Dominates

lag_1 was the most influential predictor.
Hourly demand is highly autocorrelated.

🔹 Strong Daily Seasonality

lag_24 was the second strongest feature.
Demand patterns repeat consistently across days.

🔹 Calendar Variables Are Secondary

Explicit calendar features had lower importance once lag memory was introduced.

This confirms that historical demand encodes seasonality more effectively than categorical labels alone.

📉 10. Error Analysis

Errors centered around zero

No systematic bias observed

Large errors limited to extreme demand spikes

MAPE < 1% → Highly accurate short-term forecasting

 11. Implications for Energy Management & Sustainability

Accurate hourly forecasting enables:

🔹 Grid Stability

Improved generation scheduling

Better reserve management

Reduced operational uncertainty

🔹 Cost Optimization

Reduced overproduction

Lower reliance on expensive peaking plants

🔹 Renewable Integration

Better alignment of renewable generation with load

Improved storage utilization

Reduced renewable curtailment

🔹 Sustainability Impact

Lower fossil fuel ramp-ups

Reduced emissions

Improved decarbonization efficiency

Reliable forecasting directly contributes to efficient and sustainable grid operations.

 12. Future Improvements

🔹 Incorporate weather variables

🔹 Add holiday indicators

🔹 Implement walk-forward validation

🔹 Explore probabilistic forecasting

🔹 Extend to multi-step forecasting
