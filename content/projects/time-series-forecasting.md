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




## Introduction
Airports often rely on accurate demand forecasting to guide staffing, infrastructure planning, and operational strategy. This project analyzes monthly passenger data from San Francisco International Airport (SFO) spanning July 2005 through December 2019. The data was obtained from [DataSF](https://data.sfgov.org/Transportation/Air-Traffic-Passenger-Statistics/rkru-6vcg) and includes counts of deplaned passengers.

Our goal is to build a robust seasonal forecasting model using the SARIMA framework, validate its assumptions through statistical diagnostics, and evaluate its forecast accuracy.

## Technologies Used
{{< badge >}}R{{< /badge >}}
{{< badge >}}SARIMA{{< /badge >}}
{{< badge >}}Time Series Decomposition{{< /badge >}}
{{< badge >}}Box-Cox Transformation{{< /badge >}}
{{< badge >}}Forecasting{{< /badge >}}

In this time series forecasting project, I built a SARIMA model to predict monthly passenger volumes at San Francisco International Airport (SFO). The dataset spanned from 2005 to 2019. The primary challenge was the presence of both a long-term upward trend and strong yearly seasonality. I performed the following steps:

- **Data Cleaning & Aggregation**: Combined monthly totals, filtered for "Deplaned" passengers, and excluded post-2019 data due to COVID-induced anomalies.
- **Variance Stabilization**: Performed a **Box-Cox transformation**, where `lambda` was approximately 0, so I applied a **log transformation**.
- **Trend and Seasonality Removal**: Applied `nabla_1` (first difference) and `nabla_12` (seasonal difference at lag 12) to ensure stationarity.
- **Model Identification**: Used **ACF and PACF plots** to suggest possible AR and MA terms. Selected candidate models and compared them using **AICc**.
- **Model Selection**: Chose **SARIMA(0,1,5)(0,1,1)[12]**, which was both statistically significant and parsimonious.
- **Model Diagnostics**: Verified invertibility (roots > 1), normality of residuals (Q-Q plot), and no autocorrelation (Ljung-Box test p > 0.05).
- **Forecasting**: Predicted the next 12 months and back-transformed to original scale using `exp()`. Visualized the forecast and confirmed most test points fell within the 95% confidence interval.

This end-to-end process strengthened my understanding of model selection, stationarity, diagnostics, and forecasting—all of which are highly transferable to real-world time series tasks.

---

## Box-Cox Transformation & Stationarity

A common issue with real-world time series is **non-constant variance** (heteroskedasticity), where fluctuations become stronger over time. This violates the assumption of **homoskedasticity** required by ARIMA/SARIMA models. The **Box-Cox transformation** helps stabilize variance.

Box-Cox formula:

$$
y^{(\lambda)} = 
  \begin{cases}
    \frac{y^{\lambda} - 1}{\lambda}, & \text{if } \lambda \ne 0 \\\\
    \log(y), & \text{if } \lambda = 0
  \end{cases}
$$

If the estimated `lambda` is close to 0, a log transformation is typically used:

```r
log_ts <- log(ts_data)
```

Next, we address **non-stationarity**, which means the series has a changing mean or autocorrelation over time. Stationarity is crucial because SARIMA models assume that relationships between observations are stable over time. We achieve this by **differencing**:

- First difference `nabla_1` removes linear trends:
  $$
  \nabla_1 y_t = y_t - y_{t-1}
  $$

- Seasonal difference `nabla_12` removes yearly cyclicality (period = 12):
  $$
  \nabla_{12} y_t = y_t - y_{t-12}
  $$

Combined transformation:

$$
\nabla_1 \nabla_{12} \log(U_t)
$$

This transformation flattens trend and removes repeated seasonal effects, helping us meet the stationarity assumption.

---

## Parameter Selection via ACF/PACF
We analyze autocorrelation and partial autocorrelation plots to determine the lag structure of our model:

- **ACF (Autocorrelation Function)** shows how current values relate to past values. Significant spikes suggest an MA component.
- **PACF (Partial Autocorrelation Function)** isolates the direct correlation between y_t and y_{t-k}, after removing the effects of all intermediate lags. Significant spikes suggest an AR component.

In our case:
- ACF spikes at lag 1, 5 → q in {1,5}
- PACF spikes at lag 1, 2, 4 → p in {1,2,4}
- Seasonal pattern → P = 1, Q = 1, seasonal period = 12

> **Parsimony Principle**: Always prefer simpler models unless complexity yields clear gains in performance. AICc helps balance fit and complexity.

---

## Model Estimation & Diagnostics
We estimate candidate SARIMA models and choose the one with the lowest AICc:

```r
model <- Arima(log_ts, order = c(0,1,5), seasonal = list(order = c(0,1,1), period = 12))
AICc(model)  # Best model has AICc = -696.87
```

Final model equation:

$$
\nabla_{1} \nabla_{12} \log(U_t) = (1 - 0.4999 B - 0.346 B^5)(1 - 0.9997 B^{12}) Z_t
$$

### Why Check Model Roots?
- A model is **invertible** (for MA terms) or **stationary** (for AR terms) **only if all roots of the characteristic equation lie outside the unit circle**.
- This ensures that the model doesn’t produce explosive or cyclical behavior that doesn't die out.

Check root modulus:

```r
roots <- polyroot(c(1, -model$coef[1:5]))
Mod(roots) > 1  # All TRUE => Valid model
```

### Why Check Residuals?
Once a model is fitted, its **residuals should resemble white noise**:
- No autocorrelation (checked by ACF/PACF plots of residuals)
- Normally distributed (checked by histogram + Q-Q plot)
- Statistically independent (Ljung-Box test)

Residual hypothesis test:

$$
H_0: \text{Residuals are white noise} \\\\
H_1: \text{Residuals are autocorrelated → model misspecified}
$$

```r
Box.test(residuals(model), lag = 12, type = "Ljung-Box")  # p > 0.05 ⇒ OK
```

We also check residual structure using the Yule-Walker method:

```r
ar(residuals(model), aic = TRUE)  # Order = 0 ⇒ residuals are white noise
```

---

## Forecasting Pipeline

| Step                                | Description                                              | Method / Tool                                     | Output / Insight                                     |
|-------------------------------------|----------------------------------------------------------|---------------------------------------------------|--------------------------------------------------------|
| **Model Selection**                 | Compare AICc and p-values                                | `Arima()` + `AICc()`                              | SARIMA(0,1,5)(0,1,1)[12]                             |
| **Forecast Generation**            | Predict next 12 months                                   | `forecast()`                                      | Future passenger count (log scale)                  |
| **Back-Transformation**            | Convert to original scale                                | `exp()`                                           | Forecast in passenger numbers                        |
| **Interval Check**                 | Validate 95% CI coverage                                 | Plot with `ggplot2` / `autoplot()`                | Forecast band captures test data                     |
| **Residual Checks**                | Confirm stationarity and no autocorrelation              | `checkresiduals()`, `Box.test()`                  | Model passes diagnostics                             |
| **Model Summary**                  | Communicate model coefficients & diagnostics             | `summary(model)`                                  | Significant coefficients, small residual variance     |

---

## Conclusion
SARIMA(0,1,5)(0,1,1)[12] proved to be a highly effective model for forecasting SFO passenger volume. It captured seasonal behavior, passed all diagnostic tests, and produced forecasts with tight confidence intervals. These modeling steps can be easily adapted to similar use cases in manufacturing, logistics, or energy consumption forecasting.

**GitHub Repository**: [PSTAT-174-Time-Series](https://github.com/Jinxiang2000/PSTAT-174-Time-Series) 

{{< button href="/projects" target="_self" >}}Back to Projects{{< /button >}}




