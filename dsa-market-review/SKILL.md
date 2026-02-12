---
name: dsa-market-review
description: Generate daily market recap reports (index performance, breadth, sector moves, news) in daily_stock_analysis. Use when producing “大盘复盘” and daily market overviews.
---

# DSA Market Review

## 概述（中文注释）
用于生成「大盘复盘」日报，包括指数表现、涨跌家数、板块强弱与新闻解读，并输出 Markdown 格式报告。

## Overview
Use `MarketAnalyzer` and `run_market_review` to produce daily market recap markdown, including indices, market breadth, sector winners/losers, and news-based insights.

## Workflow
1. **Get overview data**
   - `MarketAnalyzer.get_market_overview()` gathers indices, up/down counts, sector rankings.

2. **Gather market news**
   - `MarketAnalyzer.search_market_news()` uses SearchService (if configured).

3. **Generate report**
   - Preferred: `MarketAnalyzer.generate_market_review(overview, news)` via LLM.
   - Fallback: `_generate_template_review()` if LLM unavailable.

4. **Save / notify**
   - `run_market_review()` (in `src/core/market_review.py`) saves a markdown report and optionally pushes notifications.

## Output format constraints
- Pure Markdown (no JSON, no code fences).
- Sections: 市场总结 / 指数点评 / 资金动向 / 热点解读 / 后市展望 / 风险提示.

## Source anchors
- Market analyzer: `src/market_analyzer.py`
- Orchestration: `src/core/market_review.py`
