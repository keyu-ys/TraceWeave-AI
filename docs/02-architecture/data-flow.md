# 数据流

## 主流程

1. `capture agent` 收到请求交换
2. `ingest service` 写入 `NormalizedExchange`
3. `analysis` 计算语义标签和异常线索
4. `recording` 选择一段交换序列生成 `TestCase`
5. `AI orchestrator` 产出 `AiPatchProposal`
6. `case workspace` 应用确定性 patch 和人工确认 patch
7. `replay executor` 运行 `ReplayReadyStep`
8. `diagnostics hub` 汇总 `ExecutionLog` 与 `DefectRecord`

## 关键分层

- 原始层：保留抓包事实
- 标准层：统一字段和脱敏结构
- 资产层：用例、步骤、断言、依赖
- 证据层：执行日志和报告
- 认知层：AI patch、诊断结论、缺陷聚合
