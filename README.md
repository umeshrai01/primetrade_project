# Trader Behavior vs Bitcoin Sentiment – Data Science Analysis

## Objective
To analyze whether Bitcoin market sentiment (Fear vs Greed) influences trader behavior and profitability on the Hyperliquid exchange and to build a predictive model that can assist in sentiment-aware risk management.

---

## Datasets

### 1. Bitcoin Sentiment Data
- Daily Fear & Greed classification  
- 2,644 records  
- Columns: timestamp, value, classification, date

### 2. Trader Transaction Data
- 211,224 trade records  
- Key fields:  
  - Account  
  - Size USD  
  - Closed PnL  
  - Side (BUY/SELL)  
  - Timestamp IST

---

## Methodology

### Data Preparation
- Loaded both datasets directly from Google Drive in Colab  
- Converted timestamps using `dayfirst=True`  
- Aggregated to **Account–Date level**
- Created metrics:

**Derived Features**
- `daily_pnl` – total PnL per account per day  
- `avg_trade_usd` – mean position size  
- `trade_count` – activity level  
- `win` – win rate  
- `drawdown_proxy` – average loss on losing days

Missing values were handled (1 null sentiment → filled as *Greed*).

---

## Sentiment Regimes

To align with assignment focus, sentiments were grouped:

- Extreme Fear → Fear  
- Fear → Fear  
- Neutral → Neutral  
- Greed → Greed  
- Extreme Greed → Greed

---

## Key Findings (From Actual Results)

### 1. Profitability by Regime

| Regime | Mean PnL | Median PnL | Count |
|-------|---------|------------|------|
| Fear | **5185** | 122 | 790 |
| Greed | 4176 | 266 | 1175 |
| Neutral | 3438 | 167 | 376 |

**Insight**
- Average daily PnL in **Fear is ~24% higher** than in Greed  
- Traders are more profitable when market is fearful.

---

### 2. Win Rate

- Fear: **35.7%**  
- Greed: 36.3%  
- Neutral: 35.5%

Win rates are similar →  
👉 Profit difference is driven by **trade behavior & sizing**, not hit rate.

---

### 3. Behavior Change

**Trade Frequency**

| Regime | Avg Trades/Day |
|-------|----------------|
| Fear | **105** |
| Greed | 76 |
| Neutral | 100 |

- Traders are **more active during Fear**  
- Greed periods show reduced participation.

**Position Size**

| Regime | Avg Size (USD) |
|-------|----------------|
| Fear | **8529** |
| Greed | 5962 |
| Neutral | 6963 |

👉 Traders take **larger positions in Fear**, smaller in Greed.

---

### 4. Long / Short Bias

| Regime | BUY | SELL |
|-------|-----|------|
| Fear | 49.5% | 50.4% |
| Greed | 47.0% | **52.9%** |
| Neutral | 50.3% | 49.6% |

- Greed → stronger **short bias**  
- Fear → more balanced positioning.

---

### 5. Risk (Drawdown Proxy)

Average losing PnL:

- Fear: **-1101**  
- Greed: -925  
- Neutral: -768  

👉 Fear days carry higher downside → need risk controls.

---

## Trader Segments

### Size Segment
- High Size vs Low Size (median split)

### Frequency Segment
- Frequent (>5 trades)  
- Rare

**Segment Outcome**

| Regime | Frequent | Rare |
|-------|----------|------|
| Fear | **6107** | 774 |
| Greed | 4065 | 410 |
| Neutral | 3924 | 829 |

👉 Frequent traders benefit most in Fear but underperform in Greed.

---

## Statistical Test

T-test Fear vs Greed PnL  
- p-value = **0.47**

Difference is directional but **not statistically significant** →  
sentiment should be used as **risk filter, not standalone signal**.

---

## Predictive Model

### Target
- 1 → Profitable day  
- 0 → Loss day

### Features
- trade_count  
- avg_trade_usd  
- win  
- sentiment_enc

### Random Forest Results

- **Accuracy: 94%**  
- Precision (profit class): 0.93  
- Recall: 0.98

👉 Behavior + sentiment strongly predict outcomes.

---

## Business Recommendations

### Strategy 1 – Fear Regime Playbook
- Allow larger size but:
  - Cap at median ×1.2  
- Keep frequent traders active  
- Tight stop-loss due to higher drawdown

### Strategy 2 – Greed Protection
- Reduce trade frequency  
- Limit size to ≤ median  
- Allow activity only for consistent winners

### Strategy 3 – Model Usage
- Use RF model as:
  - Daily go/no-go filter  
  - Position sizing guide

---

## Limitations

- Leverage column not available →  
  **avg_trade_usd used as risk proxy**
- No order-book context
- PnL only; funding/fees ignored

---

## Tools

- Python, Pandas, NumPy  
- Matplotlib, Seaborn  
- Scikit-learn  
- Google Colab

---

## Conclusion

Market sentiment meaningfully shapes **how** traders behave:

- Fear → larger size, higher activity, better PnL  
- Greed → over-shorting and lower returns

Sentiment should be used as a **risk management layer** rather than pure alpha signal.
