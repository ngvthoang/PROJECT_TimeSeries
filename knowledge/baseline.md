# BASELINE CONTEXT: Volatility Forecasting

## 1. Core Stylized Facts (Financial Time Series)

- Volatility clustering exists → motivates GARCH-type models
- Returns exhibit fat tails → normal distribution is inappropriate
- Negative shocks often increase volatility more than positive shocks → leverage effect

---

## 2. Modeling Implications

- Use GARCH-family models to capture time-varying volatility
- Use asymmetric models (EGARCH, TGARCH) to capture leverage effect
- Always consider heavy-tailed distributions (Student-t)

---

## 3. Volatility Proxy

- True volatility is unobservable
- Use squared returns (R_t^2) as proxy for realized volatility

---

## 4. Forecast Evaluation Principles

- No single model dominates across all metrics
- Different loss functions may yield different rankings

**Primary metrics:**
    - RMSE
    - QLIKE (preferred for volatility)

---

## 5. Model Comparison

- Use Diebold-Mariano (DM) test to compare forecast accuracy
- Focus on whether differences are statistically significant

---

## 6. Practical Insights for This Project

- Daily data → more noise, but captures short-term shocks
- Weekly data → smoother, may improve stability

- Asymmetric models are expected to perform better, but must be empirically tested (do not assume)

---

## 7. Role of SVI (Investor Attention)

- SVI is a proxy for investor attention, not true sentiment
- SVI may contain predictive information for volatility
- Use lagged SVI to avoid simultaneity bias

---

## 8. Implementation Priority

- Focus on forecast performance (out-of-sample)
- Compare:
  - Base models (without SVI)
  - Augmented models (with SVI)

- Key question:
  → Does SVI improve forecast accuracy?
  