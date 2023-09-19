# Covid-19 Case Study of Regione Lombardia

> Authors: Valeria Insogna, Roberta Pascale, Anna Pederzani, Thomas Verardo

## Introduction

- **Objective**: Modeling the trend of the number of hospitalized people in Lombardia during the Covid-19 spreading outbreak and provide forward predictions given the models built.
- **Input dataset**: Covid-19 data from the official website of Protezione Civile, Covid-19 Rt data from the Istituto Superiore di Sanità, color of the regions from Ondata.it.
- **Variable of interest**: `totale_ospedalizzati`.
- **Time reference**: 1st October 2020 - 1st February 2021.
- **Area of interest**: Lombardia.

## Exploratory Data Analysis

- Data: 124 observations of 30 variables.
  - 3 variables totally missing: `note`, `note_test`, `note_casi`.
  - 9 variables missing more than 50% of data listed above.
  - 11 variables with no missing data such as `ricoverati_con_sintomi`, `terapia_intensiva`, etc.
- Cumulative variables have been converted to per-day metrics like `dimessi_guariti_per_day`, `deceduti_per_day`, etc.
- Added extra covariates:
  - color of the region
  - `rt_positive`
  - dummy variable indicating if the peak has already been reached.

## Model Building

### Linear Model
- Adopting a forward selection approach, with the main assumptions such as linearity, homoscedasticity, and independence of the observations.
- Evaluation metrics include: `R2`, `F-test`, checking for multicollinearity (VIF), and more.

### Generalized Linear Models (GLM)
- The GLM allows flexibility in modeling the distribution of the response variable.
  - Assumptions and advantages listed above.
  
### Specific GLM and GAM Models
- GLM Poisson
- GLM QuasiPoisson
- GLM Gamma
- GAM Poisson
- GAM Gamma


Details on the models and their statistics are as follows:

## Model Selection

### GLM Models

| Model Details | Deviance Explained | AIC |
| --- | --- | --- |
| `totale_ospedalizzati ~ mean_nuovi_positivi + log(mean_dimessi_guariti_per_day)*I(rt^2) + rt + deceduti_per_day, family = poisson` | 0.969 | 7715.7 |
| `totale_ospedalizzati ~ mean_nuovi_positivi+ sqrt(mean_dimessi_guariti_per_day)*I(rt^2) + rt, quasipoisson` | 0.948 | NA |
| `totale_ospedalizzati ~ mean_nuovi_positivi + log(mean_dimessi_guariti_per_day) * I(rt^2) + rt, family = Gamma(link = "log")` | 0.943 | 1966.4 |

### GAM Models

| Model Details | Adjusted R2 | AIC |
| --- | --- | --- |
| `totale_ospedalizzati ~ s(mean_nuovi_positivi) + s(mean_dimessi_guariti_per_day), family = poisson(link = log)` | 0.992 | 2708.01 |
| `totale_ospedalizzati ~ s(mean_nuovi_positivi) + s(mean_dimessi_guariti_per_day) +I(rt^2), family = Gamma(link = log)` | 0.982 | 1746.353 |

## Data Prediction

### GLM Models

| Model Details | Deviance Explained | AIC | RMSE |
| --- | --- | --- | --- |
| `totale_ospedalizzati ~ mean_nuovi_positivi + log(mean_dimessi_guariti_per_day)*I(rt^2) + rt + deceduti_per_day, family = poisson` | 0.969 | 7715.7 | 658.8 |
| `totale_ospedalizzati ~ mean_nuovi_positivi+ sqrt(mean_dimessi_guariti_per_day)*I(rt^2) + rt, family = quasipoisson` | 0.948 | NA | 819.2 |
| `totale_ospedalizzati ~ mean_nuovi_positivi + log(mean_dimessi_guariti_per_day) * I(rt^2) + rt, family = Gamma(link = "log")` | 0.943 | 1966.4 | 746.6 |

### GAM Models

| Model Details | Adjusted R2 | AIC | RMSE |
| --- | --- | --- | --- |
| `totale_ospedalizzati ~ s(mean_nuovi_positivi) + s(mean_dimessi_guariti_per_day), family = poisson(link = log)` | 0.992 | 2708.01 | 152.1 |
| `totale_ospedalizzati ~ s(mean_nuovi_positivi) + s(mean_dimessi_guariti_per_day) +I(rt^2), family = Gamma(link = log)` | 0.982 | 1746.353 | 313.3 |


