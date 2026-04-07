# PROJECT SPECIFICATION: Investor Attention and Market Volatility (Vietnam)

## 1. Research Objective

**Research Question:**
Does investor attention (proxied by Google Trends Search Volume Index - SVI) have predictive power for stock market volatility in Vietnam (VN-Index)?

**Key Goals:**

- Evaluate whether lagged SVI improves volatility modeling using GARCH-family models.
- **Cross-Frequency Analysis:** Compare predictive power across **Daily** and **Weekly** frequencies.
- **Methodological Comparison:** Evaluate performance using both **Recursive** and **Rolling Window** forecasting methods.

---

## 2. Dataset Specification

### 2.1 Time Period & Frequency

- **Frequencies:** Daily (main), Weekly (robustness).
- **Range:** 2010-01-01 to 2025-12-31.

### 2.2 Market Data

- **Source:** VN-Index closing price.
- **Variable:** Log returns $R_t = \ln(P_t / P_{t-1}) \times 100$.

### 2.3 Investor Attention Data (SVI)

- **Source:** Google Trends (Search Volume Index).
- **Keywords:** "chứng khoán", "VN-Index", "cổ phiếu", "đầu tư".

---

## 3. Data Processing Pipeline

### 3.1 SVI Collection & Stitching

- Use `pytrends` API.
- **Stitching:** For periods > 9 months, extract overlapping windows (1-2 weeks overlap) and rescale to create a continuous series.

### 3.2 Transformation

- **Normalization:** $SVI_{normalized} = (SVI - \mu) / \sigma$.
- **Smoothing:** Simple Moving Average (k=3 or 5) to reduce noise.
- **Composite Index:** Equal-weighted average of smoothed keyword series.
- **PCA:** Extract first principal component if multiple keywords are used.

### 3.3 Weekly Aggregation

- **Weekly return:**
  - Compute log return using end-of-week closing prices
- **Weekly SVI:**
  - Average daily SVI within each week

---

## 4. Exploratory Data Analysis (EDA)

- **Visuals:** Returns, Volatility Clustering (squared returns), and SVI trends.
- **Descriptive Stats:** Mean, Std Dev, Skewness, Kurtosis (Check for leptokurtic characteristic).
- **Stationarity:** Augmented Dickey-Fuller (ADF) test.
- **ARCH Effects:** ARCH-LM test to confirm heteroskedasticity.

---

## 5. Model Specification

### 5.1 Distributional Assumption

- **CRITICAL:** Use **Student's t-distribution** for all models to accommodate fat tails and excess kurtosis.

### 5.2 Competing Models

Estimate and compare the following:

1. **Symmetric:** GARCH(1,1).
2. **Asymmetric:** EGARCH(1,1), TGARCH(1,1) (to capture leverage effects).
3. **Augmented (X):** EGARCH-X and TGARCH-X (incorporating lagged SVI).

### 5.3 EGARCH-X Variance Equation

$$\ln(\sigma_t^2) = \omega + \beta \ln(\sigma_{t-1}^2) + \alpha \left[ \left| \frac{\epsilon_{t-1}}{\sigma_{t-1}} \right| - \sqrt{\frac{2}{\pi}} \right] - \gamma \frac{\epsilon_{t-1}}{\sigma_{t-1}} + \delta \cdot SVI_{t-1}$$
*(Note: $\gamma$ captures the leverage effect.)*

- Similar specification applies for TGARCH-X

### 5.4 Lag Structure (CRITICAL)

- **Baseline specification:** SVI_{t-1}
- **Robustness:** Distributed lag: SVI_{t-1}, ..., SVI_{t-5}
- Do NOT use contemporaneous SVI_t

---

## 6. Forecasting & Evaluation

### 6.1 Out-of-Sample Framework

- **Training Set:** 2010–2020 | **Test Set:** 2021–2025.
- **Recursive Forecast (primary):** Increasing estimation window.
- **Rolling Window Forecast (robustness):** Fixed-length window (e.g., 5 years), moving forward.

### 6.2 Evaluation Metrics (Loss Functions)

Compare models using 5 loss functions:

- **RMSE:** Root Mean Square Error.
- **QLIKE:** Quasi-Likelihood (less sensitive to extreme observations).

### 6.3 Statistical Comparison

- **Diebold-Mariano (DM) Test:** Perform pairwise tests to determine if the inclusion of SVI (EGARCH-X) significantly improves forecast accuracy over base models.
  - Compare EGARCH vs EGARCH-X
  - Test whether SVI improves forecast accuracy.

---

## 7. Implementation Notes (for AI Agent)

- **Language:** Python.
- **Libraries:** `arch` (for GARCH models), `pandas`, `numpy`, `statsmodels`, `pytrends`.
- **Model Config:** Ensure `dist='t'` is set in the `arch_model` function.
- **Execution:**
    1. Load VN-Index data
    2. Compute daily returns
    3. Collect and stitch SVI data
    4. Normalize, smooth, and combine SVI
    5. Create daily and weekly datasets
    6. Run EDA and statistical tests
    7. Estimate GARCH-family models
    8. Implement rolling forecasting loop
    9. Compute RMSE and QLIKE
    10. Perform DM test
    11. Repeat for robustness (weekly + recursive)

---

## 8. Critical Constraints

- **Lagged SVI Only:** To avoid endogeneity and ensure predictive validity.
- **Student-t Distribution:** Must be used to prevent biased results from fat-tailed return series.
- **No Causal Claims:** Use language like "predictive power" or "information content".
- **Methodology Robustness:** Findings must be compared across Recursive vs. Rolling and Daily vs. Weekly.
