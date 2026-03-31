# Greed-and-history-report
This repository consist the analysis of Greed and history csv file preprocessing.
# Fear & Greed Index × Crypto Trader Behavior Analysis

## Overview
This project analyzes how the Crypto Fear & Greed Index influences trading performance and behavior on the Hyperliquid DEX. Using 211,224 trades from 32 accounts across 2024, we quantify how sentiment shapes PnL, trade frequency, leverage, and long/short bias — and derive actionable strategy rules.

---

## Setup & How to Run

### Requirements
```bash
pip install pandas numpy matplotlib seaborn scikit-learn jupyter
```

### Data Files
Place these files in the project root before running:
| File | Description |
|------|-------------|
| `historical_data.csv` | Extracted from `historical_data (1).zip` |
| `fear_greed_index.csv` | Renamed from the uploaded CSV |

### Run
```bash
jupyter notebook fear_greed_analysis.ipynb
```
Or run the standalone script:
```bash
python analysis.py
```

---

## Methodology

1. **Data Cleaning:** Parsed `DD-MM-YYYY HH:MM` timestamps from IST; zero missing values in either dataset. Merged on date (inner join, restricted to 2024).
2. **Metric Construction:** Isolated closing trades (non-zero Closed PnL). Built daily per-trader metrics: PnL, win rate, trade count, average size, leverage proxy (size/|start position|, capped at 50×), long/short ratio.
3. **Sentiment Bucketing:** Collapsed 5 F&G classifications → 3 buckets (Fear = Fear+Extreme Fear; Greed = Greed+Extreme Greed; Neutral).
4. **Segmentation:** Median-split on leverage and trade frequency; profitability-based consistency labels.
5. **Clustering (Bonus):** K-Means (k=3) on standardized [total_pnl, n_trades, win_rate, avg_lev, pnl_std] → labeled by median PnL.

---

## Key Insights

### Insight 1 — Greed Days Dominate PnL
Greed days produce **3.7× higher median daily PnL** ($944 vs $256 on Fear days) and a higher average win rate (86.6% vs 81.0%). Mean PnL on Fear days is deeply negative (−$2,027) due to catastrophic tail losses — even though the median is positive, a few blow-up events drag the average down dramatically.

### Insight 2 — Activity Surges on Greed Days
Trade frequency is **4.6× higher on Greed days** (50.5 trades/day vs 11.0 on Fear days). The long/short ratio nearly triples (6.3 vs 2.6), confirming strong momentum-chasing behavior during bullish sentiment. Leverage stays broadly similar across regimes — behavioral shifts are in volume and direction, not raw risk sizing.

### Insight 3 — High Leverage Destroys Alpha
Low-leverage traders significantly outperform high-leverage traders in total median PnL. The Win Rate Heatmap (Chart 5) shows high-leverage traders suffer most during Fear days — their win rates drop substantially, while low-leverage traders remain relatively stable across sentiment regimes.

---

## Strategy Recommendations

### Strategy 1 — Fear Day De-risking Protocol
> **"On Fear days (F&G < 35), high-leverage traders should halve position sizes and avoid chasing longs."**

- Cap leverage at 5× during Fear/Extreme Fear
- Reduce position size by 50%
- Avoid net-long bias unless intraday momentum confirms

### Strategy 2 — Greed Day Momentum Amplification (Cautious Grinders)
> **"On Greed days (F&G > 60), Cautious Grinders should increase trade frequency and maintain long bias."**

- Increase trade frequency up to 2× baseline
- Bias entries long with tighter stops
- Scale out into strength rather than holding reversals

### Strategy 3 — Regime Filter for All Segments
| F&G Zone | Rule |
|----------|------|
| < 40 (Fear) | Capital preservation — reduce size, avoid new longs |
| 40–60 (Neutral) | Maintain baseline behavior |
| > 60 (Greed) | Momentum mode — increase frequency, long bias |

---

## Output Charts
| File | Description |
|------|-------------|
| `charts/chart1_pnl_by_sentiment.png` | PnL distribution, median, win rate by sentiment |
| `charts/chart2_behavior_by_sentiment.png` | Trade frequency, size, leverage, L/S ratio |
| `charts/chart3_trader_segments.png` | Leverage/frequency segments, consistency scatter |
| `charts/chart4_timeseries_overlay.png` | Daily PnL time-series vs F&G index |
| `charts/chart5_segment_sentiment.png` | Segment×sentiment PnL + win rate heatmap |
| `charts/chart6_archetypes.png` | K-Means behavioral archetypes |
