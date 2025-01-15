# Cointegration-Based Pair Trading with DCC-eGARCH Volatility Clustering and Portfolio Weight Optimization

This repository implements a **cointegration-based pair trading strategy** enhanced by **DCC-eGARCH volatility clustering** and **portfolio weight optimization**. The strategy leverages statistical arbitrage techniques, dynamic volatility modeling, and weight optimization to generate profitable trading signals from market inefficiencies.

---

## **Overview**

The trading strategy combines **cointegration analysis**, **DCC-eGARCH modeling**, and **z-score-based trading signals** to exploit the mean-reverting behavior of asset price spreads. Key features include:

1. **Cointegration Testing**:
   - Identifies pairs of assets with long-term equilibrium relationships using the Engle-Granger test.
   - Implements the Kalman filter to dynamically adjust hedge ratios.

2. **Volatility Modeling**:
   - Uses the **DCC-eGARCH** model to account for volatility clustering and asymmetric shocks.
   - Adopts the studentized-t distribution for heavy-tailed data.

3. **Signal Generation**:
   - Employs z-score thresholds to generate systematic trading signals.
   - Optimizes thresholds for maximum profit or Sharpe ratio using grid search.

4. **Portfolio Weight Optimization**:
   - Allocates weights based on confidence scores derived from cointegration test statistics and predicted volatility.
   - Limits exposure to avoid excessive concentration risk.

5. **Backtesting**:
   - Compares multiple strategies using the **BackTrader** framework.
   - Evaluates performance metrics such as annualized returns, Sharpe ratio, drawdown, and system quality number (SQN).

---

## **Installation**

### **Dependencies**
The project requires the following libraries and tools:
- **Python**: `numpy`, `pandas`, `matplotlib`, `scipy`, `yfinance`, `backtrader`
- **R**: `rugarch`, `rmgarch` for DCC-eGARCH modeling
- Jupyter Notebook (optional for visualization)

### **Setup**
1. Clone the repository:
   ```bash
   git clone https://github.com/yourusername/fina4380-pair-trading.git
   cd fina4380-pair-trading
   ```
2. Install Python dependencies:
   ```bash
   pip install -r requirements.txt
   ```
3. Install R dependencies in an R environment:
   ```R
   install.packages(c("rugarch", "rmgarch"))
   ```

---

## **Code Flow**

The project is divided into several steps, each implemented in a separate script:

| Program File                  | Purpose                                                                                      |
|-------------------------------|----------------------------------------------------------------------------------------------|
| `Pairformation_HedgeRatio.py` | 1. Fetch price data from Yfinance. <br> 2. Perform cointegration testing and Kalman filtering. |
| `DCC_GARCH_model.R`           | Optimize z-score thresholds using DCC-eGARCH.                                               |
| `z_score_optim.py`            | Preliminary backtest to evaluate optimized z-score thresholds.                              |
| `Weight_Calculation.py`       | Calculate optimal portfolio weights for cointegrated pairs.                                 |
| `Backtest_BackTrader.py`      | Backtest trading strategies and generate performance metrics/graphs.                        |

---

## **Methodology**

### **1. Pair Formation**
- **Asset Pool**: Includes 50 US equities and ETFs across sectors like technology, healthcare, and commodities.
- **Cointegration Testing**:
  - Uses the Engle-Granger test to identify stationary price spreads.
  - Forms 73 cointegrated pairs with a p-value < 0.05.
- **Kalman Filter**:
  - Dynamically estimates hedge ratios to adjust for changing market conditions.
  - Smooths price series using rolling averages for better signal clarity.

### **2. Trading Signals**
- **Z-Score-Based Signals**:
  - Defines thresholds for long/short entry and exit (LET, LETX, SET, SETX).
  - Generates signals based on deviations of z-scores from the mean.

### **3. Volatility Clustering**
- **DCC-eGARCH Model**:
  - Estimates conditional covariance matrices for multivariate spreads.
  - Captures asymmetric volatility shocks (leverage effect) and heavy tails using the studentized-t distribution.
  - Hyperparameters (p, q) are optimized using AIC and BIC.

### **4. Portfolio Weight Optimization**
- Introduces a confidence parameter (\( \gamma \)) based on:
  - Cointegration test statistics and p-values.
  - Predicted volatility from the DCC model.
- Allocates weights with a cap of 4x equal-weight allocation to limit risk exposure.

### **5. Backtesting**
- **Strategies Tested**:
  - Equal weight with fixed entry thresholds (EW-FE).
  - Equal weight with optimized entry thresholds (EW-OE).
  - Optimized weight with optimized entry thresholds (OW-OE).
- **Metrics Evaluated**:
  - Annualized returns, Sharpe ratio, drawdown, and number of trades.
- **Transaction Costs**: Assumes 0.1% per trade with no slippage.

---

## **Backtest Results**

### **Performance Metrics**
| Strategy    | 2021 (Aug-Dec) | 2022       | 2023       | Sharpe Ratio | Max Drawdown | SQN  | Number of Trades |
|-------------|----------------|------------|------------|--------------|--------------|------|------------------|
| **EW-FE**   | 8.26\%         | 6.34\%     | 10.78\%    | 0.96         | 28.58\%      | 3.68 | 540              |
| **EW-OE**   | 16.49\%        | 14.48\%    | 18.44\%    | 0.87         | 32.75\%      | 2.72 | 386              |
| **OW-OE**   | 105.11\%       | 129.98\%   | 130.34\%   | 0.95         | 25.59\%      | 3.28 | 393              |

### **Portfolio Value Graphs**
- **EW-FE**: Shows moderate growth with lower risk.
- **EW-OE**: Higher returns due to optimized entry thresholds.
- **OW-OE**: Drastically outperforms others due to optimized weights and thresholds.

---

## **How to Run**

1. **Pair Formation**:
   Run `Pairformation_HedgeRatio.py` to identify cointegrated pairs and calculate hedge ratios:
   ```bash
   python Pairformation_HedgeRatio.py
   ```

2. **DCC-eGARCH Modeling**:
   Use `DCC_GARCH_model.R` to optimize z-score thresholds:
   ```R
   source("DCC_GARCH_model.R")
   ```

3. **Z-Score Optimization**:
   Evaluate the effectiveness of optimized z-scores:
   ```bash
   python z_score_optim.py
   ```

4. **Portfolio Weight Calculation**:
   Compute optimal portfolio weights:
   ```bash
   python Weight_Calculation.py
   ```

5. **Backtesting**:
   Run `Backtest_BackTrader.py` to backtest strategies:
   ```bash
   python Backtest_BackTrader.py
   ```

---

## **Appendix**

### **Optimized Parameters**
- **Max Profit**:
  - LET: -0.65 to -0.7
  - LETX: 0.05
  - SET: 0.6 to 0.65
  - SETX: -0.05
- **Max Sharpe Ratio**:
  - Similar thresholds with minor variations.

### **Heatmaps and Plots**
- Heatmap of training spreads.
- Q-Q plots for spread normality testing.
- Portfolio value, PnL, and drawdown graphs for each strategy.

---

## **Authors**
- **LAM Lok**
- **CHEUNG Chun Hin**
- **NG Shing Cheong**
- **SIN Chi Man**

---

## **License**
This project is licensed under the MIT License. See the `LICENSE` file for details.
