---
title: "Time Series Forecasting on San Francisco Airport Data"
date: 2022-09-10
draft: false
description: "Detailed SARIMA modeling and forecasting of monthly passenger traffic at San Francisco International Airport (2005–2019), including stationarity analysis, transformation, model selection, diagnostics, and forecast evaluation."
tags: ["Time Series Analysis", "SARIMA", "R", "Forecasting", "Statistical Modeling", "Airport Analytics"]
categories: ["Data Science"]
featuredImage: "assets/img/Project_4.jpg"
math: true
---

{{< katex >}}

# Time Series Forecasting on San Francisco Airport Data


## Introduction
Airports often rely on accurate demand forecasting to guide staffing, infrastructure planning, and operational strategy. This project analyzes monthly passenger data from San Francisco International Airport (SFO) spanning July 2005 through December 2019. The data was obtained from [DataSF](https://data.sfgov.org/Transportation/Air-Traffic-Passenger-Statistics/rkru-6vcg) and includes counts of deplaned passengers.

Our goal is to build a robust seasonal forecasting model using the SARIMA framework, validate its assumptions through statistical diagnostics, and evaluate its forecast accuracy.

## Technologies Used
{{< badge >}}R{{< /badge >}}
{{< badge >}}SARIMA{{< /badge >}}
{{< badge >}}Time Series Decomposition{{< /badge >}}
{{< badge >}}Box-Cox Transformation{{< /badge >}}
{{< badge >}}Forecasting{{< /badge >}}

## Data Preprocessing
We begin by importing and aggregating the raw data into monthly totals, filtering for only "Deplaned" activity codes:

```r
library(dplyr)
data <- read.csv("Air_Traffic_Passenger_Statistics.csv")
data$Passenger.Count <- as.numeric(gsub(",", "", data$Passenger.Count))

monthly_data <- data %>%
  filter(Activity.Type.Code == "Deplaned") %>%
  group_by(Activity.Period) %>%
  summarize(Monthly_Passenger = sum(Passenger.Count))

monthly_ts <- ts(monthly_data$Monthly_Passenger, start=c(2005, 7), frequency=12)
```

A time series plot of the raw data reveals both a long-term upward trend and pronounced annual seasonality. Data after 2020 was excluded to avoid the confounding effects of the COVID-19 pandemic.

## Exploratory Analysis
We assess key distributional properties before modeling:

- **Histogram** of monthly passenger counts indicates positive skew.
- **ACF plot** shows strong autocorrelation at lags 12, 24, ..., suggesting yearly seasonality.
- **Trend**: Linear regression on the raw series confirms upward movement over time.

```r
hist(monthly_ts, col = "plum", main = "Monthly Passenger Count")
acf(monthly_ts, lag.max = 48)
```

## Data Transformation
Variance and non-stationarity necessitate transformation. First, we perform a Box-Cox transformation to identify a suitable variance-stabilizing transformation:

```r
library(MASS)
boxcox_res <- boxcox(monthly_ts ~ seq_along(monthly_ts))
lambda <- boxcox_res$x[which.max(boxcox_res$y)]  # ≈ 0 → log-transform
```

Log transformation is then applied, followed by seasonal differencing (lag 12) and first differencing (lag 1) to remove trend and seasonal components:

```r
log_ts <- log(monthly_ts)
diff_ts <- diff(diff(log_ts, lag=12), lag=1)
```

Decomposition confirms stationarity:

```r
dec <- decompose(log_ts)
plot(dec)
```

## Model Selection
We examine ACF and PACF of the transformed series to guide SARIMA parameter selection:

```r
acf(diff_ts, lag.max = 60)
pacf(diff_ts, lag.max = 60)
```

We iteratively fit multiple SARIMA models and compare their AICc scores. The best-performing model is:

```r
library(forecast)
model <- Arima(log_ts, order = c(0, 1, 5), seasonal = c(0, 1, 1))
summary(model)
```

This corresponds to SARIMA(0,1,5)(0,1,1)[12], with the model equation:

$$ \nabla_1\nabla_{12}\log(U_t) = (1 - 0.4999 B - 0.346 B^5)(1 - 0.9997 B^{12})Z_t $$

## Model Diagnostics
We validate the residuals to ensure model adequacy:

```r
checkresiduals(model)
Box.test(residuals(model), lag=12, type="Ljung-Box")
```

- Residuals approximate white noise (no autocorrelation)
- Normal Q-Q plot confirms normality assumption
- AICc = **-696.87**, the lowest among tested candidates

## Forecasting
With the validated model, we generate 12-month forecasts:

```r
forecast_vals <- forecast(model, h=12)
plot(forecast_vals, main = "12-Month Forecast of SFO Passenger Counts")
```

We also transform forecasts back to the original scale:

```r
orig_forecast <- exp(forecast_vals$mean)
```

Confidence intervals capture expected variation and align well with withheld data, suggesting high predictive performance.

## Results and Interpretation
Key findings:
- Forecast captures recurring seasonal dips and peaks (e.g., summer surges)
- Trend aligned with long-term historical growth
- Residual diagnostics and out-of-sample performance confirm robustness

## Business Applications
- **Airport Management**: Helps inform terminal staffing and gate planning
- **Airline Ops**: Anticipate demand surges for scheduling
- **Concessions & Retail**: Inventory decisions tied to forecasted volume

## Future Enhancements
- Add external regressors (e.g., GDP, fuel prices)
- Incorporate real-time updates with online learning models
- Explore hybrid models (e.g., SARIMA + Gradient Boosting)

{{< button href="https://github.com/Jinxiang2000/PSTAT-174-Time-Series/blob/main/Jinxiang%20Ma%20-%20174%20final%20project.pdf" target="_blank" >}}View Full Report (PDF){{< /button >}}

{{< button href="/projects" target="_self" >}}Back to Projects{{< /button >}}