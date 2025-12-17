# Stock Price Prediction


This project develops a robust pipeline to predict daily stock prices for major technology companies (e.g., AAPL). We compare a traditional statistical approach (**ARIMA**) against a modern machine learning method (**Gradient Boosting**) to determine which technique offers better predictive accuracy and utility for algorithmic trading.

**Key Findings:**
- **ARIMA (Rolling Forecast)** outperformed the ML model in short-term price precision with an **RMSE of $2.09**.
- **Gradient Boosting** struggled with precise level prediction (RMSE $3.94) but successfully identified momentum (Lagged Price & Moving Averages) as the primary driver of price action.
- The results strongly support the **Random Walk Hypothesis** for daily timeframes.


## Methodology

### 1. Data Preparation
- **Source:** Historical OHLCV data for `AAPL`, `MSFT`, `GOOGL`, `AMZN`, `TSLA`.
- **Cleaning:** Handled missing values, aligned timestamps, and filtered for market days.
- **Validation:** Utilized `TimeSeriesSplit` to prevent "look-ahead bias" (ensuring the model never trains on future data).

### 2. Feature Engineering
We engineered features specifically to capture market dynamics. **Crucially, all features were lagged by 1 day (`t-1`)** to simulate realistic trading conditions.
- **Trend:** 50-day & 200-day Moving Averages (`MA_50`, `MA_200`).
- **Momentum:** RSI (14-day), MACD, and Log Returns.
- **Volume:** On-Balance Volume (OBV) and Rolling Average Volume.
- **Volatility:** 30-day Rolling Standard Deviation.

### 3. Model Development
| Model | Strategy | Configuration |
| :--- | :--- | :--- |
| **ARIMA** | Statistical / Rolling Forecast | **Order (0, 1, 0)**: Re-trained daily on the full history. effectively a Random Walk model. |
| **Gradient Boosting** | Ensemble ML / Regressor | **Hyperparameters**: `n_estimators=100`, `learning_rate=0.05`, `max_depth=3`. Optimized via Grid Search. |

---

##  Analysis & Findings

### Model Performance (Last 100 Days)
We evaluated both models on the test set using standard regression metrics.

| Metric | ARIMA (Rolling) | Gradient Boosting | Interpretation |
| :--- | :--- | :--- | :--- |
| **RMSE** | **$2.09** | $3.94 | ARIMA is ~47% more accurate in dollar terms. |
| **MAE** | **$1.65** | $3.31 | The average error per share is minimal for ARIMA. |
| **MAPE** | **0.92%** | 1.81% | Relative error is under 1% for the statistical model. |


#### Forecast Comparison
*The Red line (ARIMA) closely tracks the Black line (Actual), while the Green line (ML) shows a visible lag, indicating it is reacting to trends rather than anticipating them.*
<img width="1600" height="800" alt="Code_Generated_Image" src="https://github.com/user-attachments/assets/bd2c17a5-4b00-465e-99a4-98b22678521b" />


##  Implications for Trading Strategies

Our findings have three major implications for quantitative finance:

### 1. The "Random Walk" Reality
The optimal ARIMA order was `(0,1,0)`, which represents a **Random Walk**. This implies that the best predictor of tomorrow's price is simply *today's price* plus random noise.
* **Strategy Implication:** Pure directional prediction based on daily OHLC data is extremely difficult. Strategies should avoid simple "up/down" betting and focus on **volatility arbitrage** or **mean reversion** within the standard error bands derived from the ARIMA residuals.

### 2. Execution vs. Alpha
* **ARIMA for Execution:** Because ARIMA accurately predicts the "center of gravity" for the next day's price (Low RMSE), it is excellent for **Algo Execution**—determining fair value to place Limit Orders and minimize slippage.
* **ML for Alpha:** Gradient Boosting's higher error suggests it is not suitable for price targeting. However, its reliance on momentum features suggests it could be repurposed as a **Classifier** (predicting Direction `1` or `0`) rather than a Regressor to filter trades.

### 3. Simplicity Wins
In low-latency or high-frequency environments, the computational cost of the ML model (feature generation + inference) did not yield better results than the simple statistical model.
* **Recommendation:** Use ARIMA (or simple Exponential Smoothing) as a baseline. Only deploy ML models if they are enriched with **alternative data** (Sentiment, Macro-econ, Order Flow) that statistical models cannot capture.

---


 **Clone the repo**
   ```bash
   git clone [https://github.com/rach2103/Stock-Prediction.git](https://github.com/rach2103/Stock-Prediction.git)
