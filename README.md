# Nigerian Stock Exchange (ASI) - Quantitative Analysis

A collection of advanced quantitative models analyzing the Nigerian Stock Exchange All-Share Index (ASI) using time series and regime-switching techniques.

## Project Overview

This repository contains two complementary Jupyter notebooks implementing sophisticated financial modeling approaches to understand and predict market behavior on the Nigerian Stock Exchange:

### 1. **ARIMA-GARCH Trading System** (`asi_garch.ipynb`)

Implements a rolling ARIMA-GARCH model for generating dynamic trading signals based on conditional volatility forecasting.

**Key Features:**
- Loads and preprocesses historical ASI data alongside macroeconomic variables (FX, oil prices, bond yields, FPI flows)
- Performs comprehensive feature engineering including rolling z-scores and standardization
- Fits time series models to capture mean reversion (ARIMA) and volatility clustering (GARCH)
- Generates trading signals scaled by predicted volatility for dynamic position sizing
- Evaluates strategy performance via annualized Sharpe ratio and maximum drawdown
- Flexible data source support (CSV, Excel, yfinance)

**Technical Stack:**
- ARIMA for mean modeling
- GARCH(1,1) for conditional heteroskedasticity
- Rolling window backtesting (configurable periods)
- Risk-adjusted signal scaling

---

### 2. **Hidden Markov Model - Regime Identification** (`asi_markov_chain.ipynb`)

Implements a 3-state Gaussian HMM to identify market regimes and their characteristics.

**Key Features:**
- Full feature engineering pipeline for multivariate regime detection:
  - Market microstructure: NGX returns, volatility, Sharpe ratio signals
  - Foreign exchange: USD/NGN returns and trends
  - Flow indicators: Foreign Portfolio Investment (FPI) z-scores
  - Macro stress: PCA-reduced bond and crude oil signals
- Gaussian HMM with 3 hidden states trained on full dataset
- States automatically ordered and interpreted as:
  - **Bear State**: Low returns, high stress
  - **Neutral State**: Moderate returns, balanced conditions
  - **Bull State**: High returns, favorable conditions
- Visualization of regime transitions and regime-specific statistics
- Statistical analysis of state-conditional returns and volatility

**Technical Stack:**
- `hmmlearn` Gaussian HMM implementation
- Principal Component Analysis (PCA) for macro stress compression
- State interpretation via return ordering
- Transition probability analysis

---

## Data Requirements

Both notebooks expect a file named **`master_dataset.xlsx`** containing the following columns:

| Column | Description |
|--------|-------------|
| `Date` | Trading date |
| `ASI` | All-Share Index closing price |
| `USDNGN` | USD/NGN exchange rate |
| `Net FPI` | Net Foreign Portfolio Investment flows |
| `Bond_10Y` | 10-year government bond yield |
| `Brent_Crude` | Brent crude oil price |

**Data Frequency:** Business days (Monday-Friday)

---

## Installation & Dependencies

### Requirements
```bash
pip install pandas numpy scipy scikit-learn matplotlib
pip install arch statsmodels hmmlearn torch yfinance
```

### Python Version
Python 3.8+

### GPU Support (Optional)
The GARCH notebook detects CUDA availability and automatically uses GPU if available. Falls back to CPU otherwise.



---

## Key Findings & Model Outputs

### ARIMA-GARCH Model
- **Signal Generation**: 1 for bullish, -1 for bearish based on 1-step ahead return forecast
- **Position Sizing**: Inversely scaled by predicted volatility for consistent risk exposure
- **Performance Metrics**:
  - Annualized Sharpe Ratio
  - Maximum Drawdown
  - Comparison vs. buy-and-hold benchmark

### Hidden Markov Model
- **Regime States**: 3 interpretable market conditions
- **State Characteristics**:
  - Conditional return distributions
  - Volatility regimes
  - Transition probabilities between states
- **Predictive Value**: Model detects regime shifts before explicit market signals

---

## Model Architecture

### Feature Engineering Pipeline

Both notebooks employ a unified standardization approach:

1. **Raw Features Calculation**
   - Log returns for price series
   - Rolling volatility (std deviation)
   - Trend extraction and deviations
   - Cross-asset relationships (FX trends, macro stress)

2. **Normalization**
   - 60-day rolling z-score standardization
   - Clipping to [-3, 3] to reduce outlier impact
   - Ensures features have mean ≈ 0, std ≈ 1

3. **Composite Indicators**
   - Sharpe Z: Risk-adjusted return signal (20-day rolling mean / std)
   - Macro Stress: PCA reduction of oil + bond movements
   - State-dependent feature scaling

### ARIMA-GARCH Workflow
```
Raw Returns → ARIMA(1,0,1) → Residuals → GARCH(1,1) → Volatility Forecast
                ↓                                       ↓
        Mean Forecast                          Position Sizing
```

### HMM Workflow
```
Normalized Features → 3-State GaussianHMM → State Assignment → 
  Regime Interpretation
```

---

## Performance Considerations

### GARCH Model
- **Lookback Window**: 252 trading days (~1 year) for rolling models
- **Max Predictions**: Limited to 500 most recent periods to reduce computation time
  - Full dataset mode available by setting `max_predictions=None`
- **Convergence**: ARIMA-GARCH fitting typically completes in seconds per window

### HMM Model
- **Training Time**: < 1 minute for full historical dataset
- **Scalability**: Efficiently handles thousands of observations
- **Convergence**: 1000 iterations typically sufficient

---

## File Structure

```
├── README.md                        # This file
├── asi_garch.ipynb                 # ARIMA-GARCH trading system
├── asi_markov_chain.ipynb          # Hidden Markov Model regime analysis
```

---

## References & Methodology

**ARIMA-GARCH Framework:**
- Engle, R. (1982). "Autoregressive Conditional Heteroskedasticity with Estimates of the Variance of UK Inflation"
- Nelson, D. (1991). "Conditional Heteroskedasticity in Asset Returns: A New Approach"

**Regime-Switching & HMM:**
- Ang, A., & Timmermann, A. (2012). "Regime Changes and Financial Markets"
- Hamilton, J. (1989). "A New Approach to the Economic Analysis of Nonstationary Time Series"

**Nigerian Market Context:**
- NGX All-Share Index (ASI) as primary equity benchmark
- USD/NGN as FX risk factor
- Brent crude as commodity/fiscal revenue proxy
- Government bonds as macro risk indicator

---

## Future Enhancements

- [ ] Multi-horizon forecasting (1-week, 1-month ahead)
- [ ] Kalman filter extension for adaptive state probabilities
- [ ] Integration with portfolio optimization frameworks
- [ ] Real-time data pipeline with automated signal delivery
- [ ] Ensemble methods combining GARCH + HMM predictions
- [ ] Regime-dependent portfolio allocation strategies

---

## License

This project is provided for educational and research purposes.

---

## Contact & Contributions

For questions or suggestions about these models, please open an issue or contact the repository maintainer.

**Last Updated:** June 2026

---

## Disclaimer

This repository is for educational and research purposes only. These models are provided as-is without any warranty. The user assumes all responsibility for trading decisions and losses. Past performance does not guarantee future results. Always conduct proper due diligence and risk management.
