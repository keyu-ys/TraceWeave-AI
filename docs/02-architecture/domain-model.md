# 领域模型

## CapturedRequest

表示采集到的请求事实，包括方法、URL、Header、Body、开始结束时间、大小和原始文本形态。

## CapturedResponse

表示与请求对应的响应事实，包括状态、Header、Body、错误信息和时间信息。

## NormalizedExchange

把请求和响应组合成统一分析对象，带有脱敏结果、聚类标签、风险标记和回放准备度。

## ApiDocSnapshot

表示某一时刻的接口文档知识，包括路由、参数、请求字段、响应字段和说明来源。

## ReplayReadyStep

表示一个可回放步骤。它已经剥离录制态噪声，具备可执行的请求模型、依赖注入和断言。

## TestCase

表示由多个 `ReplayReadyStep` 构成的场景资产，包含名称、目标、状态、上下文和关联报告。

## StepExtractor

定义从步骤响应中提取变量的规则，例如路径、来源、变量名和空值策略。

## StepInjector

定义把变量注入到后续步骤的规则，可作用于 path、query、header、body。

## AssertionRule

定义步骤成功标准，可表示状态、字段存在性、字段值、集合长度或业务警告。

## ExecutionRun

表示一次用例执行，包含运行时上下文、开始结束时间、状态、统计和摘要。

## ExecutionLog

表示单步执行证据，包括实际请求、实际响应、断言结果、耗时和错误归因。

## DefectRecord

表示从执行失败或运行异常中沉淀下来的可跟踪问题，支持去重、状态流转和复发统计。

## AiPatchProposal

表示 AI 对用例结构的建议修改，必须带置信度、原因和目标字段。
