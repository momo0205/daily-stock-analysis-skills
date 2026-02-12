---
name: dsa-data-sources
description: Use and troubleshoot data sources for daily_stock_analysis (A股/港股/美股). Use when fetching daily OHLCV, realtime quotes, chip distribution, or stock names.
---

# DSA Data Sources

## 概述（中文注释）
用于数据源管理（历史行情、实时行情、筹码分布、指数与板块数据），包含自动切换与限流保护策略。

## Overview
Use `DataFetcherManager` to pull daily data, realtime quotes, chip distribution, and index/sector stats. This is the canonical data layer with failover and rate-limit handling.

## Core capabilities
1. **Daily OHLCV**: `get_daily_data(code, days=30)` (auto failover across Efinance/Akshare/Tushare/Pytdx/Baostock/Yfinance).
2. **Realtime quote**: `get_realtime_quote(code)` with configurable source priority (`REALTIME_SOURCE_PRIORITY`).
3. **Chip distribution**: `get_chip_distribution(code)` with circuit breaker protection.
4. **Index + market stats**: `get_main_indices()`, `get_market_stats()`, `get_sector_rankings(n)`.
5. **Stock name**: `get_stock_name(code)` / `batch_get_stock_names(codes)`.

## A/H/US support
- A/H codes: numeric strings (A股 6/0/3 prefixes; 港股 5-digit).
- US codes: ticker strings (AAPL, TSLA). `get_realtime_quote()` routes US tickers to Yfinance.

## Config flags (critical)
- `ENABLE_REALTIME_QUOTE` (default true)
- `ENABLE_CHIP_DISTRIBUTION` (default true)
- `REALTIME_SOURCE_PRIORITY` (e.g., `tencent,akshare_sina,efinance,akshare_em`)

## Failure handling
- If all data sources fail, return None and mark `data_missing` in context.
- Do not fabricate prices or indicators when data is missing.

## Source anchors
- Manager: `data_provider/base.py`
- Config: `src/config.py`
