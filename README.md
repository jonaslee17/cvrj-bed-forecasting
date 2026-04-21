# Central Virginia Regional Jail (CVRJ) Bed Forecasting

> A ten-year forecast of jail bed utilization to support long-term capacity planning at CVRJ and assess the feasibility of Culpeper County joining as a member jurisdiction.

---

## Abstract

The Central Virginia Regional Jail (CVRJ) currently serves five member jurisdictions and is evaluating whether to admit a sixth — Culpeper County. While the facility maintains excess capacity today, the inclusion of an additional jurisdiction risks inducing future overcrowding. In response to a direct request from the CVRJ Superintendent, this study delivers a ten-year forecast of jail bed utilization to support long-term capacity planning and formally assess the feasibility of Culpeper County's membership.

For nearly two decades, CVRJ has relied on a financial-indicator model to guide capacity and expansion decisions. While that approach has served previous planning cycles, current trends in jail utilization and inmate population dynamics reveal that financial signals alone no longer fully reflect operational demand. This study addresses those limitations by employing a **Seasonal Autoregressive Integrated Moving Average with eXogenous variables (SARIMAX)** framework, integrating crime statistics and jail booking records to produce more precise estimates of inmate length of stay and overall bed utilization.

Under the scenario in which Culpeper County joins CVRJ, the combined Average Daily Population (ADP) is projected to reach **569 beds by 2031** and **584 beds by 2035**, against a maximum facility capacity of 660 beds — with daily exceedance probabilities of **17.6%** and **27.2%** respectively. These findings indicate that CVRJ can accommodate Culpeper County within the next ten years, while underscoring the critical importance of continued data collection to refine long-term projections.

---

## Key Findings

| Metric | 2031 | 2035 |
|---|---|---|
| Projected Combined ADP (w/ Culpeper) | 569 beds | 584 beds |
| Daily Capacity Exceedance Probability | 17.6% | 27.2% |
| CVRJ Maximum Capacity | 660 beds | 660 beds |

> **Bottom line:** CVRJ is projected to remain below maximum capacity through 2035 under a Culpeper inclusion scenario — but rising exceedance probabilities make continued monitoring and annual model retraining essential.

---

## Background & Motivation

CVRJ serves the jurisdictions of Albemarle County, the City of Charlottesville, Fluvanna County, Louisa County, and Nelson County. Any decision to expand membership carries significant operational and financial consequences for all existing partners.

The prior planning model, derived primarily from financial data, was designed at a time when booking patterns and lengths of stay were more stable. Contemporary evidence — including shifting pretrial population dynamics and inmate-level length-of-stay variance — suggests that financial proxies increasingly diverge from actual bed demand. This project was initiated to close that gap.

Related prior work by UVA SIEDS teams has examined high utilizers of the Albemarle–Charlottesville criminal justice system (Afi et al., 2025) and home electronic incarceration candidates (Banino et al., 2024), building an empirical foundation that directly informs this forecasting effort.

---

## Methodology

### Data Sources

| Dataset | Source | Coverage |
|---|---|---|
| De-identified CVRJ inmate records | T. McDaniel, Central Virginia Regional Jail | Individual booking / release events |
| Culpeper Average Daily Population | VA Compensation Board · Local Inmate Data System | Historical ADP by jurisdiction |
| Virginia crime statistics | Virginia State Police · Virginia Crime Online | Offense counts by jurisdiction & year |

### Modeling Pipeline

1. **Length-of-Stay (LOS) Estimation** — Inmate-level booking records are processed to compute empirical LOS distributions. Censored stays (e.g., ongoing incarcerations) are handled via survival-analysis techniques.

2. **SARIMAX Forecasting** — A Seasonal ARIMA model with eXogenous crime covariates is fit to the historical ADP time series, following the foundational methodology of Lin, MacKenzie & Gulledge (1986). Seasonal orders are selected via AIC/BIC grid search; exogenous terms include lagged crime indices from the Virginia State Police database.

3. **Scenario Simulation** — Two primary scenarios are evaluated: (a) CVRJ without Culpeper, and (b) CVRJ with Culpeper as a member jurisdiction. Culpeper's projected ADP is derived from Compensation Board historical data and modeled independently before being combined with the CVRJ baseline forecast.

4. **Exceedance Probability Calculation** — Prediction intervals from the SARIMAX model are used to estimate the daily probability that combined ADP exceeds the 660-bed maximum at the 2031 and 2035 forecast horizons.

## Limitations & Future Work

- Forecasts beyond five years carry wider prediction intervals; the 2035 estimates should be treated as indicative rather than precise.
- Culpeper's ADP projection relies on external Compensation Board data rather than inmate-level records, introducing additional uncertainty compared to the CVRJ baseline model.
- Exogenous crime covariates are lagged; near-term policy shifts (e.g., decriminalization, diversion programs) are not captured by historical crime-rate inputs.
- Annual model retraining with updated booking data is strongly recommended, particularly following any changes to pretrial release policy or jail classification practices.

---

## References

1. Z. Afi et al., "High utilizers of the Albemarle and Charlottesville criminal justice system," in *Proc. 2025 Systems and Information Engineering Design Symposium (SIEDS)*, 2025, pp. 227–232. doi: [10.1109/SIEDS65500.2025.11021179](https://doi.org/10.1109/SIEDS65500.2025.11021179)

2. S. Banino et al., "Analyzing candidates for home electronic incarceration on return-to-custody rates for inmates," in *Proc. 2024 Systems and Information Engineering Design Symposium (SIEDS)*, 2024, pp. 505–510. doi: [10.1109/SIEDS61124.2024.10534739](https://doi.org/10.1109/SIEDS61124.2024.10534739)

3. Commonwealth of Virginia Compensation Board's Local Inmate Data System, "Average daily population (ADP) for Culpeper inmates," Unpublished dataset.

4. F. Dyer, Personal Interview, Jan. 23, 2026.

5. J. Lee, "CVRJ bed forecasting," GitHub. [Online]. Available: https://github.com/jonaslee17/cvrj-bed-forecasting

6. B. S. Lin, D. L. MacKenzie, and T. R. Gulledge, "Using ARIMA models to predict prison populations," *J. Quant. Criminol.*, vol. 2, no. 3, pp. 251–264, 1986. doi: [10.1007/BF01066529](https://doi.org/10.1007/BF01066529)

7. T. McDaniel, "De-identified dataset for CVRJ," Unpublished dataset, Central Virginia Regional Jail.

8. Virginia State Police, "Virginia Crime Online," [Database]. [Online]. Available: https://va.beyond2020.com/

9. S. M. Ross, *Introduction to Probability Models*, 13th ed. Academic Press, 2024.

10. New York City Department of Correction and Mayor's Office of Criminal Justice, "Jail Population Forecast Terms and Conditions Report," New York City Council, Mar. 2025. [Online]. Available: https://council.nyc.gov/budget/wp-content/uploads/sites/54/2025/03/DOC-MOCJ-FY25-TC-Report-Population-Forecast-1st-half.pdf [Accessed: Apr. 13, 2026].

---

*This project was conducted in partnership with the Central Virginia Regional Jail. All inmate data were de-identified prior to analysis. The findings represent academic research and do not constitute official CVRJ policy.*
