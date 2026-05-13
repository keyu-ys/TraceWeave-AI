# 用例 Schema

## TestCase

建议包含：

- 标识
- 名称
- 描述
- 标签
- 状态
- 录制来源
- 目标说明
- 步骤列表

## ReplayReadyStep

建议包含：

- 顺序
- 方法
- 路径模板
- query 模型
- header 模型
- body 模型
- extractors
- injectors
- assertions
- baseline 摘要

## 设计原则

- schema 表达意图，不表达数据库方言
- 录制事实和回放模型分离
- 运行时注入字段不要写死在录制层
