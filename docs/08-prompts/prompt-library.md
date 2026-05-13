# Prompt Library

## 角色列表

- [capture-analyst](./prompt-templates/capture-analyst.md)
- [api-doc-curator](./prompt-templates/api-doc-curator.md)
- [testcase-orchestrator](./prompt-templates/testcase-orchestrator.md)
- [testcase-fixer](./prompt-templates/testcase-fixer.md)
- [replay-diagnostician](./prompt-templates/replay-diagnostician.md)
- [defect-triager](./prompt-templates/defect-triager.md)

## 使用方式

- 采集后先用 `capture-analyst`
- 文档不足时用 `api-doc-curator`
- 从录制生成用例时用 `testcase-orchestrator`
- 回放失败后用 `replay-diagnostician`
- 需要沉淀缺陷时用 `defect-triager`
- 已有用例需要结构修复时用 `testcase-fixer`

## 失败模式

- 输入证据不足
- 输出过于自由
- 把低置信推断写成确定事实
- 引入业务私有字段

## 维护原则

- 每个 prompt 只做一个角色
- 每个 prompt 都要可独立测试
- 不共享隐式前提
