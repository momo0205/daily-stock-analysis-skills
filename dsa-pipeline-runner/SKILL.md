---
name: dsa-pipeline-runner
description: Run or modify the end-to-end analysis pipeline for daily_stock_analysis. Use when executing full stock analysis runs, adjusting concurrency, or generating reports/notifications.
---

# DSA Pipeline Runner

## 概述（中文注释）
用于执行完整分析流水线（数据获取→趋势分析→情报搜索→LLM仪表盘→推送），并支持并发/报告类型等配置。

## Overview
Run the full stock analysis pipeline (data fetch → trend analysis → news intel → dashboard → notification) using `StockAnalysisPipeline` in `src/core/pipeline.py`. When no external AI key is configured, **do not invoke LLM APIs**; generate dashboards manually from trend + data + intel.

## Quick run
- Instantiate `StockAnalysisPipeline()`.
- Call `run(stock_codes=None, dry_run=False, send_notification=True)`.

## Key steps (internal)
1. `fetch_and_save_stock_data()` – daily OHLCV data.
2. `analyze_stock()` – realtime quote + chip + trend + intel + LLM dashboard.
3. `process_single_stock()` – optional single-stock notify.
4. `_send_notifications()` – decision dashboard report.

## Options to know
- Concurrency: `max_workers` (from config).
- Single stock push: `SINGLE_STOCK_NOTIFY`.
- Report type: `REPORT_TYPE` (`simple` / `full`).
- Analysis delay: `ANALYSIS_DELAY` (avoid rate limits).

## When to use
- User asks to run daily analysis, generate dashboard reports, or adjust pipeline behavior.
- Need to debug missing results: check each step’s outputs and fallbacks.

## Source anchors
- Pipeline: `src/core/pipeline.py`
- Notifier/report formatting: `src/notification.py`
