# Markdown 开发笔记

Markdown 是一种用纯文本编写、可被渲染为富文本的轻量标记语言。它常用于 `README.md`、接口文档、变更日志、Issue、Pull Request、Wiki 和技术笔记。

## 阅读顺序

1. [Markdown 基本语法](01_md基本语法.md)：标题、强调、引用和基础列表。
2. [文本、段落与转义](02_文本段落与转义.md)：换行、强调、行内代码和特殊字符。
3. [列表、任务与引用](03_列表任务与引用.md)：嵌套结构、待办事项和多层引用。
4. [链接、图片与附件](04_链接图片与附件.md)：相对路径、锚点、参考式链接和附件规范。
5. [代码与开发文档](05_代码与开发文档.md)：代码围栏、语言标识、终端输出和差异展示。
6. [表格与 GitHub 常用扩展](06_表格与GitHub常用扩展.md)：表格、删除线、自动链接和提示块。
7. [公式、图表与脚注](07_公式图表与脚注.md)：LaTeX、Mermaid 和补充说明。
8. [Obsidian 常用扩展](08_Obsidian常用扩展.md)：属性、Callout、标签、注释和库内链接。
9. [工程文档规范与速查](09_工程文档规范与速查.md)：README 模板、CHANGELOG 和发布前检查。

> [!tip] 先确认渲染环境
> CommonMark、GitHub Flavored Markdown（GFM）和 Obsidian 支持的扩展并不完全相同。需要跨平台发布时，优先使用标题、段落、列表、链接、图片、表格和代码围栏等通用语法；公式、Mermaid、Callout 等应确认目标平台支持后再使用。

## 常用文件名

`README.md` 用来说明项目，`CONTRIBUTING.md` 说明协作方式，`CHANGELOG.md` 记录版本变化，`LICENSE` 或 `LICENSE.md` 存放许可证，`.github/PULL_REQUEST_TEMPLATE.md` 可作为 Pull Request 模板。

```md
project/
├── README.md
├── CONTRIBUTING.md
├── CHANGELOG.md
├── LICENSE
└── docs/
    ├── api.md
    └── deployment.md
```

## 最小记忆

- 空行用于分隔段落；缩进通常表示嵌套。
- 代码、路径、命令和配置键使用反引号或代码围栏。
- 仓库内链接优先使用相对路径，并在提交前检查链接是否仍有效。
- 图片和附件应和文档一起纳入版本控制，避免引用个人电脑上的绝对路径。
