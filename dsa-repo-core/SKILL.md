---
name: dsa-repo-core
description: Understand and operate the daily_stock_analysis repository end-to-end. Use when explaining repo capabilities, running its pipeline, or generating decision dashboards without calling external LLM APIs.
---

# DSA Repo Core

## 概述（中文注释）
该技能用于理解与运行 daily_stock_analysis 仓库的完整流程，并在**不调用外部 LLM API**的前提下生成分析结论与仪表盘结构。适用于：解释仓库做什么、如何运行、如何在无 Key 情况下输出结论。

## Overview
This skill summarizes what the repository does and provides a no-external-LLM workflow to generate decision dashboards based on available data, rules, and signals.

## What this repo does (high-level)
- Fetches historical OHLCV data with multi-source failover.
- (Optionally) fetches realtime quote + chip distribution.
- Computes trend signals (MA alignment, bias, volume, MACD, RSI).
- Searches multi-dimensional news intel (latest, risk, earnings, industry).
- Generates a structured “decision dashboard” report.

## No-external-LLM requirement
**Do not call external LLM APIs** (Gemini/OpenAI/etc). Instead:
- Use deterministic signals (trend, bias, volume, MACD, RSI).
- Use available realtime quote + chip distribution if present.
- Use search intel summaries (Tavily/Bocha/SerpAPI) if configured.
- If data missing, explicitly say “数据缺失，无法判断”，do not fabricate numbers.

## Core components (repo anchors)
- Pipeline: `src/core/pipeline.py` (StockAnalysisPipeline)
- Trend engine: `src/stock_analyzer.py` (StockTrendAnalyzer)
- Dashboard schema + parsing: `src/analyzer.py` (AnalysisResult + dashboard JSON schema)
- Market review: `src/market_analyzer.py` + `src/core/market_review.py`
- Search intel: `src/search_service.py`
- Data sources: `data_provider/base.py`

## Workflow (no external LLM)
1. **Collect data**
   - Daily OHLCV via `DataFetcherManager.get_daily_data()`.
   - Realtime quote via `get_realtime_quote()` (if enabled).
   - Chip distribution via `get_chip_distribution()` (if enabled).
   - Intel via `SearchService.search_comprehensive_intel()`.

2. **Compute trend signal**
   - Use `StockTrendAnalyzer.analyze(df, code)` to get trend_status, bias, volume, MACD, RSI, score, buy_signal.

3. **Assemble dashboard JSON manually**
   - Follow schema in `GeminiAnalyzer.SYSTEM_PROMPT` (dashboard.core_conclusion / data_perspective / intelligence / battle_plan).
   - Infer:
     - `operation_advice` from `buy_signal` + bias rule (bias>5% ⇒ 观望).
     - `decision_type` from operation advice (buy/hold/sell).
     - `sniper_points` using MA5/MA10/MA20 levels if available; otherwise mark data missing.
   - Checklist must include ✅/⚠️/❌ and reflect actual signals.

4. **Summarize**
   - Provide per-stock conclusion and overall portfolio bias (多头/震荡/谨慎) based on score distribution.

## Guardrails
- Never invent prices, MA values, or exact levels when data is missing.
- If realtime/chip data missing, mark those fields as “N/A” or “无法判断”.
- Prefer clarity and conservative advice over speculation.

## Example triggers (when to use)
- “重新分析 daily_stock_analysis 仓库”
- “不用外部 API Key 做仪表盘分析”
- “解释仓库流程 + 给出结论”
