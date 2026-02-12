---
name: dsa-intel-search
description: Perform multi-dimensional news and sentiment search for daily_stock_analysis (latest news, risk checks, earnings, industry). Use when collecting external intel or formatting news context for AI analysis.
---

# DSA Intel Search

## 概述（中文注释）
用于多维度舆情/新闻搜索（最新消息、风险排查、业绩预期、行业分析），并格式化为模型可用的情报上下文。

## Overview
Use `SearchService` to gather recent news, risk alerts, and earnings/industry intel, then format a clean context block for the LLM dashboard.

## Workflow
1. **Initialize SearchService**
   - Use `SearchService(bocha_keys, tavily_keys, serpapi_keys)` from `src/search_service.py`.
   - Tavily key: set `TAVILY_API_KEYS` in `.env`.
   - Check `is_available` before searching.

2. **Run multi-dimensional search**
   - Preferred: `search_comprehensive_intel(stock_code, stock_name, max_searches=5)`.
   - Fallback: `search_stock_news()` or `search_stock_events()` for focused queries.

3. **Format for LLM**
   - Use `format_intel_report(intel_results, stock_name)`.
   - Include risk checks (减持/处罚/利空) and earnings signals.

4. **Data-source failure fallback**
   - When行情数据失败, use `search_stock_price_fallback()` or `search_stock_with_enhanced_fallback()` and add a disclaimer (search data may be delayed).

## Output tips
- Prioritize 3–5 items per dimension.
- Include source + published_date when available.
- Avoid overlong snippets; keep each item ≤150–200 chars.

## Source anchors
- Search orchestration: `src/search_service.py`
- Pipeline usage: `src/core/pipeline.py` (news_context)
