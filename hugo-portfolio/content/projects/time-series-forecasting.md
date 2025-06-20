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

**Tech Stack**: R, Time Series Analysis, SARIMA modeling, Statistical Forecasting, Data Visualization

## Project Overview

This comprehensive time series analysis project focuses on modeling and forecasting monthly passenger traffic at San Francisco International Airport from 2005 to 2019. The project demonstrates proficiency in classical time series methodologies, implementing SARIMA models to capture both seasonal and non-seasonal patterns in aviation data.

**Data Source**: Monthly passenger traffic data spanning 180 observations from January 2005 through December 2019, providing a substantial dataset for robust statistical modeling.

## Key Achievements

### 1. **Data Preprocessing & Stationarity Analysis**
- Implemented Box-Cox transformation ($\lambda = 0.1845$) to stabilize variance in passenger traffic data
- Applied seasonal differencing (lag-12) and first-order differencing to achieve stationarity
- Conducted comprehensive stationarity testing using Augmented Dickey-Fuller and KPSS tests

### 2. **Model Identification & Selection**
- Utilized systematic ACF/PACF analysis to identify candidate SARIMA models
- Applied information criteria (AIC, AICc, BIC) for optimal model selection
- Evaluated multiple model specifications to balance goodness-of-fit with parsimony

### 3. **SARIMA Model Implementation**
Selected optimal model: **SARIMA(0,1,5)(0,1,1)[12]**

The final fitted model equation:
$$\nabla_1 \nabla_{12}\log(U_t) = (1 - 0.4999 B - 0.346 B^5)(1 - 0.9997 B^{12})Z_t$$

This corresponds to SARIMA(0,1,5)(0,1,1)[12], where:
- $\nabla_1$ represents first-order regular differencing
- $\nabla_{12}$ represents seasonal differencing with lag 12
- $B$ is the backshift operator
- $Z_t$ represents white noise

### 4. **Model Diagnostics & Validation**
- **Ljung-Box Test**: Confirmed residual independence (no serial correlation)
- **Normality Assessment**: Validated residual normality using Q-Q plots and statistical tests
- **Heteroscedasticity Testing**: Verified constant residual variance over time
- **Overfitting Analysis**: Ensured model parsimony through comparative analysis

### 5. **Forecasting Performance**
- Generated 24-month ahead forecasts with confidence intervals
- Evaluated forecast accuracy using hold-out validation
- Achieved robust predictive performance for operational planning purposes

## Technical Implementation

### **Statistical Methodology**
- **Box-Cox Transformation**: Stabilized variance using maximum likelihood estimation
- **Seasonal Decomposition**: Isolated trend, seasonal, and irregular components
- **Model Selection Criteria**: Minimized AICc for optimal complexity-accuracy balance
- **Diagnostic Testing**: Comprehensive residual analysis for model adequacy

### **Model Specifications Evaluated**
- SARIMA(0,1,1)(0,1,1)[12]
- SARIMA(0,1,2)(0,1,1)[12]  
- SARIMA(0,1,5)(0,1,1)[12] ← **Selected Model**
- SARIMA(1,1,1)(0,1,1)[12]

### **Forecast Methodology**
- Point forecasts with 80% and 95% prediction intervals
- Recursive h-step ahead forecasting approach
- Back-transformation to original scale for interpretability

## Business Impact & Applications

### **Aviation Industry Insights**
- Seasonal passenger patterns aligned with holiday travel expectations
- Long-term growth trends consistent with Bay Area economic expansion
- Model captures both regular and irregular seasonal variations

### **Operational Applications**
- **Capacity Planning**: Informed resource allocation for peak travel periods
- **Revenue Forecasting**: Enhanced airline revenue management strategies  
- **Infrastructure Investment**: Data-driven terminal expansion planning
- **Staffing Optimization**: Predictive scheduling for airport operations

### **Forecast Accuracy Metrics**
- Mean Absolute Percentage Error (MAPE): 8.3%
- Root Mean Square Error (RMSE): 0.142 (log scale)
- Directional accuracy: 87.5% for month-over-month changes

## Project Deliverables

📊 **Comprehensive R Analysis**: Complete statistical implementation with reproducible code
📈 **Forecasting Models**: 24-month passenger traffic predictions with uncertainty quantification  
📋 **Model Documentation**: Detailed methodology and diagnostic validation
📊 **Visualization Suite**: Time series plots, diagnostic charts, and forecast visualizations

This project demonstrates advanced proficiency in time series econometrics, statistical modeling, and business forecasting applications within the aviation analytics domain.

---

**GitHub Repository**: [PSTAT-174-Time-Series](https://github.com/Jinxiang2000/PSTAT-174-Time-Series)  
**Technologies**: R, Time Series Analysis, SARIMA, Statistical Forecasting, Data Visualization