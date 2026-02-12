---
name: dsa-trend-signal
description: Compute and interpret trend signals (MA alignment, bias, MACD, RSI, volume) for daily_stock_analysis. Use when generating technical trend scores, buy/sell signals, or explaining why a stock is buy/hold/sell.
---

# DSA Trend Signal

## 概述（中文注释）
用于计算趋势信号（均线、多空结构、乖离率、量能、MACD、RSI），并产出 0-100 的技术评分与买卖信号。

## Overview
Use the built-in `StockTrendAnalyzer` to compute technical signals and a 0–100 score based on MA alignment, bias to MA5, volume shape, MACD, and RSI. The output is the canonical technical signal used by the dashboard.

## Workflow
1. **Prepare OHLCV DataFrame**
   - Use historical daily data from DB (`storage.get_analysis_context`) or `DataFetcherManager.get_daily_data`.
   - Must include at least 20 rows; 60+ rows preferred for MA60 + RSI/MACD stability.

2. **Run analyzer**
   - Call `StockTrendAnalyzer.analyze(df, code)` in `src/stock_analyzer.py`.
   - Capture `TrendAnalysisResult` (trend_status, ma_alignment, bias, volume_status, macd_status, rsi_status, signal_score, buy_signal).

3. **Interpretation rules (must follow)**
   - **No chasing**: bias to MA5 > 5% ⇒ risk, do not recommend buy.
   - **Trend priority**: MA5>MA10>MA20 is required for strong buy.
   - **Volume**: “缩量回调” is positive; “放量下跌” is negative.
   - **Signals**: Use `buy_signal` and `signal_score` as the definitive technical stance.

## Output guidance
- Prefer the `TrendAnalysisResult.to_dict()` for downstream JSON.
- For narrative summaries, use `format_analysis()`.

## Source anchors
- Trend engine: `src/stock_analyzer.py`
- Input data + context: `src/core/pipeline.py` (trend_result in `_enhance_context`)
