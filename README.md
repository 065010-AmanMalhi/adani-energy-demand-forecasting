Adani AI Labs – Hourly Energy Demand Forecasting
1. Problem Overview

The objective of this assessment is to forecast hourly electricity consumption using six years of historical transmission system operator data. The dataset represents a univariate time series with strong seasonal and autoregressive characteristics.

The goal is to build a robust forecasting model that accurately predicts future hourly demand while demonstrating structured preprocessing, meaningful feature engineering, appropriate model selection, benchmarking, and clear interpretation of results.

2. Dataset Description

Six years of hourly electricity consumption data

52,966 original observations

Variables:

Start time (UTC)

End time (UTC)

Electricity consumption (MWh)

The dataset reflects real-world grid operations and includes minor timestamp irregularities likely due to daylight saving transitions.

3. Data Preprocessing
3.1 Timestamp Handling

Converted timestamps to datetime format.

Sorted observations chronologically to preserve temporal integrity.

Validated time intervals between observations.

Minor irregular intervals (55/65 minutes) were retained as they represent operational realities.

3.2 Data Cleaning

Removed redundant End time UTC column.

Ensured no missing values remained after feature engineering.

Prevented data leakage during lag and rolling feature construction.

4. Feature Engineering

Electricity demand exhibits strong temporal dependence. Feature engineering focused on capturing seasonality, short-term momentum, and trend behavior.

4.1 Calendar Features

Hour of day

Day of week

Month

Quarter

Year

Weekend indicator

These capture structured seasonal and macro demand effects.

4.2 Lag Features (Autoregressive Memory)

lag_1 → Previous hour demand

lag_24 → Same hour previous day

lag_168 → Same hour previous week

Lag features capture short-term inertia and daily/weekly repetition patterns.

4.3 Rolling Mean Features

24-hour rolling mean

168-hour rolling mean

Rolling averages provide smoothed recent demand trends.

To avoid leakage, rolling computations were shifted to ensure only historical data was used.

5. Train-Test Strategy

A chronological split was used instead of random shuffling:

First 80% → Training set

Last 20% → Testing set

This simulates real-world forecasting, where future values must be predicted using past information only.

6. Model Selection and Justification

Several regression and forecasting approaches were evaluated conceptually:

Linear Regression

Simple and interpretable.

Assumes linear relationships.

Struggles to capture nonlinear interactions and peak demand behavior.

Decision Tree Regressor

Captures nonlinear patterns.

However, single trees are prone to overfitting and instability.

Random Forest Regressor

Handles nonlinearities well.

Reduces variance through bagging.

However, trees are built independently and do not sequentially correct errors.

Classical Time-Series Models (ARIMA/SARIMA)

Explicitly model autoregressive structure.

Strong for stationary series.

Less flexible when incorporating engineered lag and rolling features.

Struggle with multiple seasonalities and nonlinear effects.

Why XGBoost Was Selected

XGBoost (Extreme Gradient Boosting) was chosen because:

It builds trees sequentially, correcting previous prediction errors.

It effectively captures nonlinear relationships.

It handles complex interactions between lag and seasonal features.

It includes regularization to control overfitting.

It performs strongly on structured tabular datasets.

It does not require feature scaling.

Given the transformation of the time series into a supervised regression problem using lag features, gradient boosting provided the most effective balance between flexibility, accuracy, and generalization.

7. Baseline Benchmark

A naive benchmark model was implemented using:

Prediction = Demand at the same hour on the previous day (lag_24)

Baseline Performance:

MAE: 366.78

RMSE: 507.48

MAPE: 3.73%

This baseline captures daily seasonality but ignores short-term momentum and weekly patterns.

8. Final Model Performance (Tuned XGBoost)

MAE: 80.07

RMSE: 112.10

MAPE: 0.83%

The proposed model reduced forecasting error by approximately 78% compared to the naive baseline, demonstrating the value of incorporating autoregressive memory and rolling trend signals.

9. Model Insights
9.1 Dominance of Short-Term Memory

Feature importance analysis revealed:

lag_1 (previous hour) was the most influential predictor.

lag_24 (daily seasonality) was the second strongest.

This indicates that electricity demand is highly autocorrelated and strongly driven by immediate historical behavior.

9.2 Daily Seasonality Is Structurally Stable

The strong influence of lag_24 confirms that hourly demand patterns repeat consistently across days, making short-term forecasting highly reliable.

9.3 Calendar Features Have Secondary Impact

Once lag features were introduced, explicit calendar variables contributed less importance. This suggests that historical demand already encodes seasonal effects effectively.

10. Error Analysis

Prediction errors were centered around zero.

No significant systematic bias was observed.

Large errors were limited to extreme demand spikes.

A MAPE below 1% indicates highly accurate short-term forecasting.

The model demonstrates stable generalization and strong predictive consistency.

11. Implications for Energy Management and Sustainability

Accurate hourly demand forecasting has meaningful operational and environmental impact.

11.1 Grid Stability and Operational Efficiency

Improved generation scheduling

Reduced reserve margin requirements

Better load balancing

Lower operational uncertainty

11.2 Reduced Overproduction

Accurate forecasts reduce:

Excess generation

Energy waste

Inefficient ramp-ups

This improves cost efficiency and operational optimization.

11.3 Renewable Energy Integration

Reliable demand forecasts enable:

Better alignment of renewable generation with expected load

Optimized storage utilization

Reduced renewable curtailment

More efficient grid dispatch planning

11.4 Carbon Emission Reduction

Better forecasting reduces reliance on:

Emergency fossil-fuel ramp-ups

Inefficient peaking plants

This contributes directly to lower emissions and improved sustainability outcomes.

12. Future Improvements

Incorporate weather variables (temperature, humidity)

Add holiday indicators

Implement walk-forward validation

Explore probabilistic forecasting

Extend to multi-step forecasting scenarios
