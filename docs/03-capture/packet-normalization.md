# 报文标准化

## 目标

把不同来源、不同格式、不同编码习惯的请求交换数据归一到同一种分析结构。

## 统一动作

- Header 名归一
- URL 解析为 host、pathname、query
- 文本 Body 与 JSON Body 分离保存
- 状态和错误字段显式化
- 时间字段统一为可比较格式
- 请求和响应大小单独记录

## 不应做的事

- 不在标准化阶段做业务成功判断
- 不在标准化阶段推断鉴权逻辑
- 不在标准化阶段删除原始字段

## 输出信号

标准化后应额外产出：

- `contentTypeClass`
- `bodyShape`
- `isReplayCandidate`
- `hasSensitiveSegments`
