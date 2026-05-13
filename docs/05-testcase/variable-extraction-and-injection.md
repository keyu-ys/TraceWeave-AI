# 变量提取与注入

## 提取器

提取器定义从哪个来源取值、如何命名、空值是否报错。

常见来源：

- response body
- response header
- response status
- request echo field

## 注入器

注入器定义把变量放回哪里。

常见目标：

- path
- query
- header
- body

## 命名原则

- 变量名应表达语义而不是只叫 `id`
- 若无法确定语义，用资源名加后缀
- 不要把一次性随机值暴露成稳定变量名

## 风险控制

- 对低置信提取保留人工确认标记
- 不把敏感凭证当作通用变量传播
