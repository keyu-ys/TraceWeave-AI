# 抓包与分析算法

## 目标

这一层解决五件事：

1. 把原始抓包转换成稳定结构
2. 判断哪些交换具备 replay value
3. 把相似请求聚到同一接口模板
4. 从多次抓包中补全文档快照
5. 尽早识别异常与漂移

## 算法 1: URL Canonicalization

### 输入

- 原始 URL
- HTTP method
- 可选 query map

### 输出

- `templatePath`
- `normalizedQuery`
- `endpointKey`

### 步骤

1. 规范 scheme、host 大小写。
2. 移除已知无业务含义的追踪参数。
3. 对 path segment 做分类：
   - 纯静态文本保留
   - 高概率 ID 的 segment 替换为 `{id_like}`
   - 时间戳、UUID、哈希、长随机串替换为 `{dynamic}`
4. 将 query 参数按 key 排序。
5. 对高波动 query 值替换为占位符。
6. 生成 `endpointKey = method + templatePath + bodyShapeClass`。

### 判定阈值

- 长度 >= 8 且高熵字符串，视为随机段
- UUID 模式、纯数字长串、Base64 类串可直接视为动态段

### 误判来源

- 业务路径里合法存在数字但不是 ID
- query 中某些时间字段本身是业务语义的一部分

### 伪代码

```text
function canonicalize(url, method, parsedQuery):
  pathSegments = splitPath(url.path)
  templateSegments = []
  for seg in pathSegments:
    if isStaticWord(seg):
      templateSegments.append(seg)
    else if looksLikeUuid(seg) or looksLikeLongNumber(seg):
      templateSegments.append("{id_like}")
    else if looksRandom(seg):
      templateSegments.append("{dynamic}")
    else:
      templateSegments.append(seg)
  normalizedQuery = sortAndMask(parsedQuery)
  templatePath = "/" + join(templateSegments, "/")
  endpointKey = method + " " + templatePath + " " + inferBodyShapeClass()
  return templatePath, normalizedQuery, endpointKey
```

## 算法 2: Replay Readiness 判定

### 目标

不是所有抓包都应该回放。这个算法的输出必须让系统在录制阶段就知道哪些请求只用于分析。

### 输入

- `NormalizedExchange`
- 同 trace 前后文

### 输出

- `replayReadiness.level`: `ready` / `conditional` / `analysis_only`
- `replayReadiness.reasons`

### 评分维度

| 维度 | 说明 |
| --- | --- |
| `completeness` | 请求和响应是否完整 |
| `determinism` | 输入是否大部分可重建 |
| `sensitivity` | 是否依赖不可安全回放的敏感值 |
| `sideEffectRisk` | 是否可能造成不可接受副作用 |
| `dependencyVisibility` | 上游依赖是否可见 |

### 建议公式

```text
replay_score =
  completeness
  + determinism
  + dependencyVisibility
  - sensitivity
  - sideEffectRisk
```

### 建议阈值

- `>= 0.75`: `ready`
- `0.45 ~ 0.75`: `conditional`
- `< 0.45`: `analysis_only`

### 伪代码

```text
function classifyReplayReadiness(exchange, traceContext):
  completeness = scoreCompleteness(exchange)
  determinism = scoreDeterminism(exchange)
  sensitivity = scoreSensitivity(exchange)
  sideEffectRisk = scoreSideEffectRisk(exchange)
  dependencyVisibility = scoreDependencyVisibility(exchange, traceContext)
  score = completeness + determinism + dependencyVisibility - sensitivity - sideEffectRisk
  if score >= 0.75:
    return ready
  if score >= 0.45:
    return conditional
  return analysis_only
```

## 算法 3: 请求聚类

### 目标

把多条抓包聚成稳定的接口模板，后续文档补全和异常检测都依赖它。

### 输入

- `NormalizedExchange` 列表

### 输出

- `clusterId`
- cluster 摘要

### 特征

- `method`
- `templatePath`
- query key 集合
- header 语义桶
- `bodyShape`
- 响应 shape

### 聚类策略

1. 先按 `method + templatePath` 粗分桶。
2. 在桶内比较 `bodyShape` 和 query key 集合。
3. 如果形状差异超过阈值，拆为子簇。
4. 如果响应 shape 高度一致，可合并边界样本。

### 决策表

| 条件 | 动作 |
| --- | --- |
| path 模板不同 | 必拆 |
| bodyShape 差异大且 query key 差异大 | 拆子簇 |
| bodyShape 差异小、响应 shape 一致 | 合并 |
| 单样本离群 | 暂不合并，标为 orphan |

## 算法 4: API 文档补全

### 目标

通过多次抓包生成 `ApiDocSnapshot`，为 AI 编排和人工理解提供低成本上下文。

### 输入

- 某个 cluster 的 `NormalizedExchange` 列表

### 输出

- `ApiDocSnapshot`

### 步骤

1. 收集所有 path/query/header/body 字段。
2. 统计字段出现率。
3. 字段出现在 95% 以上样本，标记为 `required_candidate`。
4. 字段出现在 20% 到 95%，标记为 `optional_candidate`。
5. 对值集合求类型、长度分布、枚举稳定性。
6. 对响应字段做相同统计。
7. 生成文档快照并附带置信度。

### 误判来源

- 样本量过少
- 聚类错误导致混入不同语义接口
- 某些字段只在特定条件下出现但非常重要

## 算法 5: 异常检测

### 目标

在正式生成用例前，提前识别异常流量、漂移和不稳定接口。

### 输出类别

- `status_drift`
- `shape_drift`
- `latency_outlier`
- `missing_field`
- `schema_break`

### 检测规则

| 异常类型 | 核心判断 |
| --- | --- |
| `status_drift` | 同 cluster 内状态分布明显偏移 |
| `shape_drift` | 响应结构主干字段变化 |
| `latency_outlier` | 超过历史 P95 或均值多倍标准差 |
| `missing_field` | 高稳定字段突然缺失 |
| `schema_break` | 字段类型变化或容器结构变化 |

### 伪代码

```text
function detectAnomalies(clusterHistory, currentExchange):
  anomalies = []
  if statusDistributionShift(clusterHistory, currentExchange):
    anomalies.append("status_drift")
  if responseShapeChanged(clusterHistory, currentExchange):
    anomalies.append("shape_drift")
  if latencyIsOutlier(clusterHistory, currentExchange):
    anomalies.append("latency_outlier")
  if stableFieldsMissing(clusterHistory, currentExchange):
    anomalies.append("missing_field")
  if typeSystemBroken(clusterHistory, currentExchange):
    anomalies.append("schema_break")
  return anomalies
```

## 实施建议

- 先做标准化，再做聚类；不要直接用原始抓包聚类
- `Replay Readiness` 必须在生成 `TestCase` 前给出
- 文档快照和异常结果都应该带置信度和证据来源
- 样本不足时宁可保守，不要强行补全文档
