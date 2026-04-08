# CVRJ Bed Forecast: Summary for Stakeholders

**Purpose:** Support long-term capacity planning and assess whether CVRJ can add Culpeper County while staying within the 660-bed limit.

---

## 1. How We Calculated ADP for CVRJ Only

**ADP = Average Daily Population** (how many inmates are in the jail on an average day).

### Data source
- **CVRJ booking data** (`cvrj_dataset_v2.csv`): each row = one jail stay, with **Book Date** and **Release Date**.
- We **excluded Culpeper (County Code 47)** so “CVRJ only” = the five current member counties (Fluvanna, Greene, Louisa, Madison, Orange).

### Calculation (same idea for CVRJ and for Culpeper-in-CVRJ later)
1. For **each booking**: count +1 on the **book date** (person enters) and −1 on the **day after release** (person leaves).
2. For **every day** in the date range, add up those +1 and −1 changes, then take a **running total** → that’s the **daily census** (how many people were in the jail that day).
3. **Annual ADP** = **average** of that daily census over the year.

So: **length of stay is built in**—longer stays add more days to the count and raise ADP. We did **not** use a separate “length of stay” column; Book and Release dates are enough.

### In plain language
- We used **who was actually in CVRJ and when** (from the booking data).
- We got a **daily head count**, then averaged it by year to get **annual ADP for CVRJ only** (no Culpeper).

---

## 2. How We Handled Culpeper (Given Your Two Tables)

You provided two Culpeper numbers:

| Source | Meaning | Example (2021) |
|--------|--------|-----------------|
| **Culpeper County Jail ADP** | Inmates in **Culpeper’s own jail only** (not in CVRJ) | 83.35 |
| **Culpeper County Inmates (any jail)** | Inmates **held for Culpeper** in **any** facility (CVRJ + RSW + Culpeper Jail) | 185.75 |

Important: **“Any jail” is not the number we add to CVRJ.**  
If we added 185.75 to CVRJ, we’d be double-counting (Culpeper inmates in CVRJ are already part of “any jail”).  
At most, the **extra** beds CVRJ would need for Culpeper = (any jail − Culpeper Jail) shared with RSW, so **less than** 185.75 − 83.35 ≈ 102 in 2021.

### What we actually used in the model
- We used **CVRJ booking data** to see how many **Culpeper inmates were actually in CVRJ** (County Code 47 in the same CSV).
- We computed **Culpeper-in-CVRJ ADP** the same way as CVRJ: Book/Release dates → daily census → annual average.
- So:
  - **Historical “combined”** = CVRJ-only ADP + **Culpeper-in-CVRJ ADP** (from the CSV).
  - **Forecast “combined”** = forecast of CVRJ-only + forecast of **Culpeper-in-CVRJ** (not “any jail”).

Your two tables tell us:
- **Culpeper Jail ADP** = demand in Culpeper’s own facility.
- **Any jail** = total Culpeper demand everywhere; we use the **CSV** to get the **CVRJ share** of that (Culpeper-in-CVRJ), so the forecast stays realistic and never assumes all “any jail” inmates are in CVRJ.

---

## 3. How We Created the Forecast Models and What Influenced Them Most

### Two separate models
1. **CVRJ-only model**  
   - **Input:** Historical CVRJ-only annual ADP (from the booking data).  
   - **Extra factor:** Combined **population** of the five CVRJ counties (Fluvanna, Greene, Louisa, Madison, Orange).  
   - **Output:** Forecast of CVRJ-only ADP for 2026–2035.

2. **Culpeper-in-CVRJ model**  
   - **Input:** Historical **Culpeper-in-CVRJ** annual ADP (County 47 from the same CSV).  
   - **Extra factor:** **Culpeper County population**.  
   - **Output:** Forecast of how many Culpeper inmates would be in CVRJ each year (2026–2035).

### Type of model: SARIMAX
- **SARIMAX** = time-series model that uses **past values of ADP** plus **one outside driver** (population).
- We used **population** because jail demand is often related to how many people live in the area.
- Future population was **projected with a simple trend** (linear extrapolation), then fed into the model to get future ADP.

### What influenced the model the most
- **Past ADP:** The model leans heavily on **recent years’ ADP** (level and trend).
- **Population:** Used as an external driver; when population goes up, the model can project higher ADP, and vice versa.
- **No other factors** (e.g., crime rates, policy changes) are in the model—only **ADP history** and **population**.

### Combined forecast
- **Combined load** = CVRJ-only forecast + Culpeper-in-CVRJ forecast.
- That combined number is compared to **660 beds** to see if CVRJ would be over capacity if Culpeper joins.

---

## 4. Important Things to Know About This SARIMA Forecast

- **SARIMA** fits a pattern to **past** ADP and (optionally) an external variable (here: population), then **extends** that pattern into the future.
- It **does not** know about future policy, new programs, or one-off events; it assumes the **relationship** between past ADP and population continues.
- **Uncertainty:** We report a single forecast path. In reality there is a **range** of possible outcomes; the further out (e.g., 2035), the more uncertain.
- **Data limits:**  
  - CVRJ: good history from the CSV (2012–2025).  
  - Culpeper-in-CVRJ: fewer bookings in the CSV in early years, more in 2024–2025; the model uses that history.
- **Capacity check:** If **combined load** stays **below 660**, the model suggests CVRJ can add Culpeper without exceeding current capacity; if it goes **above 660**, the model suggests risk of overcrowding.

---

## 5. Daily trend vs day-to-day noise (365-day moving average)

The chart **`visuals/adp_daily_and_annual.png`** shows **total daily census** (all counties in the booking file) and a **centered 365-day moving average** of that series—the same smoothing choice that minimizes squared residuals between the daily line and the smooth trend.

A companion figure, **`visuals/adp_ma365_difference_residuals.png`**, shows only **difference residuals** = **daily census minus the centered 365-day moving average** (the same smoother as **`adp_daily_and_annual.png`**). These are the day-to-day swings left over after removing the annual-scale trend. Typical magnitude of these residuals is on the order of **~18 inmates** RMSE for 2013–2025 on this series.

**How to read it:** Large positive residuals are days when the jail was unusually full relative to the year-scale trend; large negative residuals are relatively light days. The moving average is **not** a forecast—it is a descriptive smoother for the same historical daily series as the first chart.

---

## 6. When does combined load cross 660 beds?

Two different questions get confused if we are not careful:

| Question | What to use | Approximate answer (from current model outputs) |
|----------|-------------|--------------------------------------------------|
| When does the **forecast combined load** (CVRJ baseline without Culpeper + **forecast** Culpeper-in-CVRJ) go **above 660**? | Annual **combined** projection in **`data/outputs/forecast_results.csv`** | **First calendar year-end above 660: 2030** (~665 projected ADP). **Linear interpolation** between year-end 2029 (~640) and 2030 (~665) places the crossing near **mid-October 2030**. |
| When would the **smoothed daily census** (365-day MA alone) reach **660** if a simple straight-line trend continued? | OLS on the MA over a chosen historical window | A fit to **2022–2025** on the centered MA implies roughly **+24 inmates per year** on the smoothed series; extrapolating that line from **late 2025** reaches **660 around 2035**. That is **not** the same as the SARIMAX combined forecast and **ignores** the model’s projected Culpeper-in-CVRJ path. |

**Better estimate for stakeholders (capacity vs the model):** Use the **forecast table**: treat **2030** as the first year the **combined projected ADP** exceeds **660**, with a **within-year** refinement of roughly **October 2030** if you interpolate linearly between December 2029 and December 2030. The residual plot explains why **eyeballing raw daily peaks** can overstate how fast the *underlying* trend is moving: after **mid-2024**, the **365-day average** of total daily census **flattened and eased** into 2025 even when individual days still spiked high.

---

## 7. Bullet Points You Can Use When Presenting

### What we did
- Used **CVRJ booking data** (Book/Release dates) to compute **average daily population (ADP)** for CVRJ only and for Culpeper-in-CVRJ.
- Built **two forecast models** (SARIMAX): one for **CVRJ-only** beds, one for **Culpeper-in-CVRJ** beds, both using **population** as the main external factor.
- Combined the two forecasts and compared the total to **660 beds** to assess capacity if Culpeper joins.
- Charted **daily census** with a **365-day moving average** and a follow-on **`adp_ma365_difference_residuals.png`** figure that shows **difference residuals** (daily minus that smoothed trend) only.

### How we got CVRJ ADP
- Every booking gives a “+1” on book date and “−1” the day after release.
- We turned that into a **daily head count**, then took the **average per year** → **annual ADP for CVRJ only** (excluding Culpeper).

### How we handled Culpeper
- Your **two tables** (Culpeper Jail ADP and “any jail” Culpeper) describe **total** Culpeper demand and demand in Culpeper’s own jail.
- We did **not** add “any jail” to CVRJ (that would overstate CVRJ need).
- We used the **same booking data** to count **Culpeper inmates actually in CVRJ** (County Code 47) and computed **Culpeper-in-CVRJ ADP** the same way as CVRJ ADP.
- Historical and forecast **combined** = CVRJ-only + **Culpeper-in-CVRJ** only.

### What drives the forecast
- **Past jail usage (ADP)** and **population** are what the model uses.
- The model assumes the **relationship** between population and ADP in the past continues into the future.
- It does **not** include crime rates, policy changes, or other factors.

### What stakeholders should remember
- **ADP** = average number of inmates per day; we get it from **who was in the jail and when** (Book/Release dates).
- **CVRJ-only** = five current counties; **Culpeper-in-CVRJ** = only the Culpeper inmates that the data shows in CVRJ.
- **660-bed line** = current capacity; we compare the **combined forecast** to this to see if adding Culpeper fits.
- The forecast is **not a guarantee**; it’s a **projection** based on past patterns and population. The further out the year, the more uncertainty.

---

*Document generated for the CVRJ bed forecast project. Questions can be directed to the project team.*
