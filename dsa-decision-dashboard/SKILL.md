---
name: dsa-decision-dashboard
description: Generate or validate the AI “decision dashboard” JSON for a single stock in the daily_stock_analysis project. Use when producing one-sentence core conclusions, buy/sell points, action checklists, or parsing GeminiAnalyzer dashboard output for reporting.
---

# DSA Decision Dashboard

## 概述（中文注释）
用于生成/校验单只股票的「决策仪表盘」JSON，输出一句话核心结论、精确买卖点位和操作清单。严格遵守数据缺失不编造规则。

## Overview
Use the project’s built-in decision dashboard schema and prompt to produce a structured JSON analysis for one stock. This skill anchors on `src/analyzer.py` (GeminiAnalyzer + AnalysisResult) and enforces the dashboard fields and “no fabrication when data is missing” rules.

## Workflow (single stock dashboard)
1. **Collect enhanced context**
   - Use `StockAnalysisPipeline._enhance_context()` output shape as the canonical context (see `src/core/pipeline.py`).
   - Ensure `context['today']`, `context['realtime']`, `context['chip']`, and `context['trend_analysis']` are present when available.
   - If `context['data_missing'] == True`, the output must explicitly state data gaps and avoid invented numbers.

2. **Generate dashboard JSON (no external AI key)**
   - **Do not call external LLM APIs** when API keys are not configured.
   - Manually compose the dashboard JSON using:
     - `TrendAnalysisResult` (trend_status, bias, volume_status, macd/rsi signals)
     - realtime quote (price/turnover/volume_ratio) if available
     - chip distribution if available
     - intel/news summary if available
   - Output **strict JSON** following the schema in `src/analyzer.py` (dashboard.core_conclusion, data_perspective, intelligence, battle_plan).
   - Must include: one-sentence conclusion, specific buy/stop/target prices, and checklist with ✅/⚠️/❌.

3. **Parse/validate output**
   - If JSON is wrapped in code fences, strip and parse as in `_parse_response()`.
   - Ensure `decision_type` is present or derive it from `operation_advice`.
   - If stock name is “股票{code}” or missing, resolve name using context or DataFetcherManager.

## Key fields to respect
- **core_conclusion.one_sentence**: ≤30 chars, explicit action.
- **battle_plan.sniper_points**: exact prices (no vague ranges).
- **action_checklist**: 5+ items with ✅⚠️❌ markers.
- **bias rule**: if MA5 bias > 5%, mark “严禁追高” and force “观望”.

## When data is missing
If `data_missing` is true or critical fields are `N/A`, explicitly say “数据缺失，无法判断”，and avoid numeric claims.

## Source anchors
- Decision dashboard prompt + parsing: `src/analyzer.py`
- Enhanced context fields: `src/core/pipeline.py` (`_enhance_context`)
