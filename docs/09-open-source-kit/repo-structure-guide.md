# 仓库结构指南

## 顶层建议

- `README.md`
- `LICENSE.md`
- `CONTRIBUTING.md`
- `ROADMAP.md`
- `docs/`

## 文档分层原则

- `01-vision` 只讲目标和原则
- `02-architecture` 只讲组件和模型
- `03-07` 分能力面展开
- `08-prompts` 专门存放 prompt
- `09-open-source-kit` 专门存放落地与维护文档

## 为什么这样分

- 便于外部读者逐层进入
- 便于 prompt 与设计分离维护
- 便于未来增加实现参考而不污染当前抽象层
