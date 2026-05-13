# 系统总览

## 抽象组件

- `capture agent`
  负责从代理、浏览器、移动端或网关采集请求交换数据。
- `ingest service`
  接收采集数据并写入统一存储。
- `document runtime`
  管理 API 文档快照、补全文档语义、提供路径匹配能力。
- `case workspace`
  管理录制片段、用例、步骤、断言和 AI patch。
- `AI orchestrator`
  负责从请求序列推导变量依赖、参数化路径、补默认断言、给出低置信建议。
- `replay executor`
  负责用例执行、超时控制、重试、报告落库。
- `diagnostics hub`
  聚合异常、失败报告、缺陷记录和 AI 诊断结果。

## 组件关系

```text
capture agent
  -> ingest service
  -> normalized store
  -> document runtime
  -> case workspace
  -> AI orchestrator
  -> replay executor
  -> diagnostics hub
```

## 架构边界

- 采集层负责事实数据，不做强推断
- 分析层负责归纳、聚类、补全
- 编排层负责把素材转成执行资产
- 执行层负责产出证据
- 诊断层负责解释与聚合
