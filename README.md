# Time Series Analysis & Forecasting in R

Statistical analysis and forecasting across three time series with different structural properties — macroeconomic (trend-dominated), climatic (seasonality-dominated), and business (trend + seasonality combined) — using the Box-Jenkins methodology (ARIMA/SARIMA) and classical multiplicative decomposition.

**Stack:** R · RStudio · `forecast` · `tseries` · `uroot` · `readxl`

## Why this matters

Time series data carries temporal dependency that standard statistics can't capture — today's value depends on yesterday's. Correctly diagnosing *why* a series is non-stationary (trend? seasonality? both?) determines which model family will work and which won't. This project deliberately picks three series with different underlying structure to demonstrate that diagnosis process end-to-end: an ADF/KPSS/Canova-Hansen toolkit, ACF/PACF-driven model selection, and residual diagnostics — rather than defaulting to one model type for every problem.

## What this project covers

### 1. Italian Unemployment Rate (ARIMA) — trend-dominated series
Monthly unemployment data (Eurostat, Jan 2022 – Mar 2026). The series shows a clear downward trend (9.1% → 5.5%) with no meaningful seasonality. First-differencing (d=1) achieves stationarity (ADF p=0.046); ACF/PACF of the differenced series show no significant lags, pointing to **ARIMA(0,1,0)** — a random walk, confirmed as the best fit by AIC (69.62) against 5 alternative specifications, and validated via Ljung-Box on residuals (p=0.247).

### 2. Kalamata Rainfall (SARIMA) — seasonality-dominated series
Monthly rainfall (mm) from the Hellenic National Meteorological Service, Jan 2016 – Dec 2025 (120 observations, 5 missing values imputed via seasonal mean). KPSS confirms no trend (d=0); strong seasonal non-stationarity (period s=12) is resolved via seasonal differencing (D=1). ACF/PACF of the seasonally-differenced series point to **SARIMA(0,0,0)(1,1,1)[12]**, which outperforms `auto.arima`'s suggestion on both residual variance (1649 vs 2306) and Ljung-Box (p=0.867 vs p=0.053).

### 3. Beer Sales (Classical Decomposition) — trend + seasonality combined
Quarterly beer sales (2016–2019, millions of bottles). Multiplicative decomposition (Y = T × S × C × I) via centered moving averages isolates seasonal indices (Summer +74%, Winter −59%), a weak but positive trend (b=0.082M bottles/quarter, R²=0.167, not statistically significant at p=0.116), and cyclical residuals reaching +36.4% (Spring 2018) and −46.0% (Winter 2019) — attributable to exogenous economic factors rather than seasonality or trend.

### ARIMA(0,1,0) forecast — Italian unemployment (2026–2028)
![ARIMA forecast](results/figures/arima_forecast_unemployment.png)

### SARIMA(0,0,0)(1,1,1)[12] forecast — Kalamata rainfall (2026)
![SARIMA forecast](results/figures/sarima_forecast_rainfall.png)

### Classical decomposition — deseasonalized beer sales
![Deseasonalized beer sales](results/figures/beer_sales_deseasonalized.png)

## Key findings

| Series | Non-stationarity source | Model | Validation |
|---|---|---|---|
| Italy unemployment | Trend only | ARIMA(0,1,0) | AIC=69.62 (best of 6); Ljung-Box p=0.247 |
| Kalamata rainfall | Seasonality only (s=12) | SARIMA(0,0,0)(1,1,1)[12] | Ljung-Box p=0.867 (vs p=0.053 for auto.arima) |
| Beer sales | Trend + seasonality | Multiplicative decomposition | R²=0.167 for trend; seasonal indices 0.41–1.74 |


## Repository structure

```
timeseries-r-analysis/
├── README.md
├── LICENSE                       # MIT (applies to code, not report.pdf)
├── .gitignore
├── exercises/
│   ├── 01_Askisi1_Anergia_Italias.Rmd        # ARIMA — Italian unemployment
│   ├── 02_Askisi2_Vrochoptosi_Kalamatas.Rmd  # SARIMA — Kalamata rainfall
│   └── 03_Askisi3_Poliseis_Mpiras.Rmd        # Classical decomposition — beer sales
├── notebooks_html/                # Knitted HTML reports — open directly in a browser, no R needed
│   ├── 01_Askisi1_Anergia_Italias.html
│   ├── 02_Askisi2_Vrochoptosi_Kalamatas.html
│   └── 03_Askisi3_Poliseis_Mpiras.html
└── results/figures/                # charts exported from the knitted HTML outputs
```

## Data

- **Exercise 1 (unemployment):** requires `italy_unemployment.xlsx` — download from [Eurostat](https://ec.europa.eu/eurostat) (unemployment by sex and age, monthly, unadjusted; filter for Italy)
- **Exercise 2 (rainfall):** requires `emi.csv` — monthly rainfall data for Kalamata, from the [Hellenic National Meteorological Service (ΕΜΥ)](http://oldportal.emy.gr/emy/el/climatology/climatology_city)
- **Exercise 3 (beer sales):** fully self-contained — the data is hardcoded directly in the `.Rmd` (16 quarterly observations), no external file needed

Neither raw dataset is included in this repository (see `.gitignore`); place them alongside the corresponding `.Rmd` file before knitting.

## Running it

Requires R and RStudio, with the following packages:

```r
install.packages(c("readxl", "forecast", "tseries", "uroot"))
```

Each `.Rmd` in `exercises/` is self-contained — open in RStudio and knit to HTML, or run:

```r
rmarkdown::render("exercises/01_Askisi1_Anergia_Italias.Rmd")
```

Exercises 1 and 2 require their respective data files (see **Data** above) to be present in the same folder before knitting.

## Author

MSc coursework project — Information Systems & Services, University of Piraeus, 2026.

## References

- Box, G. E. P., Jenkins, G. M., Reinsel, G. C., & Ljung, G. M. (2015). *Time series analysis: Forecasting and control* (5th ed.). Wiley.
- Eurostat. (2026). Unemployment by sex and age – monthly data – unadjusted [Dataset]. European Commission.
- Ελληνική Μετεωρολογική Υπηρεσία (Ε.Μ.Υ.). (2025). Κλιματικά δελτία – Καλαμάτα [Dataset].
- R Core Team. (2024). *R: A language and environment for statistical computing* (Version 4.4) [Software]. R Foundation for Statistical Computing.

## License

Code (R Markdown scripts) is licensed under [MIT](LICENSE). This does not extend to the third-party data (Eurostat, ΕΜΥ) used by the analysis, which remains subject to its respective source's terms — see **Data** above.
