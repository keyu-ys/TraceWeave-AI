# 缺陷聚合

## 目标

把大量执行失败和运行异常收束为可追踪、可排序、可关闭的问题记录。

## 聚合原则

- 相同根因聚合
- 相同症状但不同根因分离
- 忽略时间、随机 ID、一次性值带来的噪声
- 优先保留最近证据和最高影响样本

## 关键字段

- `defectClass`
- `stabilityHash`
- `sourceLayer`
- `routeFingerprint`
- `messageFingerprint`
- `sampleEvidence`
- `occurrenceCount`
- `status`

## 状态流转

- `open`
- `fixed`
- `ignored`
- `reopened`

## AI 的角色

AI 可以辅助归因和排序，但不能替代稳定 hash 逻辑。
