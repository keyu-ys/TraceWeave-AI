# TraceWeave AI

TraceWeave AI 是一套面向“AI 自动化抓包 + 测试工具”的开源提示词工程与抽象设计文档包。

它不提供代码脚手架，也不绑定具体技术栈。它提供的是一套可复用的方法：

- 如何接收与标准化抓包数据
- 如何把原始请求序列转成可回放的测试用例
- 如何让 AI 辅助补全文档、补断言、参数化依赖链
- 如何执行回放、归并异常、输出诊断报告
- 如何把这些能力组织成稳定的 prompt system

## 适合谁

- 想快速搭建内部抓包分析与测试平台的工程师
- 想把录制流量转成自动化资产的测试工程师
- 想把 AI 引入接口分析、用例编排、失败诊断的产品团队
- 想开源一个同类工具但不想被具体业务绑死的维护者

## 这个包解决什么问题

常见抓包工具擅长“看数据”，自动化测试工具擅长“跑脚本”，AI 工具擅长“给建议”。真正难的是把三者接起来：

- 抓到的流量不一定可回放
- 可回放的请求不一定有文档语义
- 用例之间的依赖链很难手工维护
- 失败报告常常只有现象，没有归因
- AI 如果没有清晰契约，很容易输出不可执行的建议

TraceWeave AI 解决的是这条链路的组织问题，而不是某个单点功能。

## 核心能力图

```text
Capture -> Normalize -> Analyze -> Document -> Record -> Orchestrate -> Replay -> Diagnose -> Improve
```

## 目录导览

- [产品定位](./docs/01-vision/product-positioning.md)
- [架构总览](./docs/02-architecture/system-overview.md)
- [抓包协议](./docs/03-capture/ingest-protocol.md)
- [异常分析](./docs/04-analysis/anomaly-detection.md)
- [用例模型](./docs/05-testcase/testcase-schema.md)
- [AI 编排流程](./docs/06-ai-orchestration/orchestration-pipeline.md)
- [回放执行器](./docs/07-replay/execution-engine.md)
- [系统提示词](./docs/08-prompts/system-prompt.md)
- [实施清单](./docs/09-open-source-kit/implementation-checklist.md)

## 如何使用这套文档

1. 先读 [product-positioning](./docs/01-vision/product-positioning.md) 和 [system-overview](./docs/02-architecture/system-overview.md)，确定你要做的是平台、工具还是内部工作台。
2. 读 [domain-model](./docs/02-architecture/domain-model.md) 与 [testcase-schema](./docs/05-testcase/testcase-schema.md)，统一团队术语。
3. 读 [orchestration-pipeline](./docs/06-ai-orchestration/orchestration-pipeline.md) 与 [prompt-library](./docs/08-prompts/prompt-library.md)，确定你的 AI 角色拆分。
4. 把 `docs/08-prompts/` 里的 prompt 交给你自己的 AI runtime。
5. 用 [implementation-checklist](./docs/09-open-source-kit/implementation-checklist.md) 做落地检查。

## 约束

- 文档只讲抽象设计，不给供应商耦合方案。
- 文档不包含真实流量样本、真实鉴权值、真实业务接口路径。
- 文档优先追求可迁移性，不追求某一栈的最优实现。

## 开源方式

- 许可证见 [LICENSE.md](./LICENSE.md)
- 贡献方式见 [CONTRIBUTING.md](./CONTRIBUTING.md)
- 路线图见 [ROADMAP.md](./ROADMAP.md)
