# 基础表格

| 命令 | 作用 | 示例 |
| --- | --- | --- |
| `git status` | 查看工作区状态 | `git status --short` |
| `git diff` | 查看未暂存变更 | `git diff` |

```md
| 命令 | 作用 | 示例 |
| --- | --- | --- |
| `git status` | 查看工作区状态 | `git status --short` |
| `git diff` | 查看未暂存变更 | `git diff` |
```

## 对齐方式

| 左对齐 | 居中 | 右对齐 |
| :--- | :---: | ---: |
| alpha | beta | 42 |
| gamma | delta | 100 |

```md
| 左对齐 | 居中 | 右对齐 |
| :--- | :---: | ---: |
| alpha | beta | 42 |
| gamma | delta | 100 |
```

## 单元格中的竖线

| 表达式 | 说明 |
| --- | --- |
| `a \| b` | 使用反斜杠转义竖线 |

```md
| 表达式 | 说明 |
| --- | --- |
| `a \| b` | 使用反斜杠转义竖线 |
```

> [!tip] 表格的适用场景
> 表格适合字段说明、参数对照、兼容性矩阵和简短的命令速查。需要放代码块、长段落或复杂嵌套内容时，改用小标题和列表通常更易读，也更适合手机端。

## 删除线

~~已废弃的 API：`/v1/users`~~

```md
~~已废弃的 API：`/v1/users`~~
```

## 自动链接

在 GitHub 等支持 GFM 的平台中，直接输入 https://example.com 通常会自动识别为链接。

```md
在 GitHub 等支持 GFM 的平台中，直接输入 https://example.com 通常会自动识别为链接。
```

> [!warning] 自动链接并非通用语法
> 为了确保在所有 Markdown 渲染器中可点击，优先使用 `[链接文本](https://example.com)` 或 `<https://example.com>`。

## GitHub 提示块

> [!NOTE]
> 这是 GitHub 支持的提示块写法之一。

```md
> [!NOTE]
> 这是 GitHub 支持的提示块写法之一。
```

> [!IMPORTANT]
> 生产环境配置需要经过评审。

```md
> [!IMPORTANT]
> 生产环境配置需要经过评审。
```

> [!WARNING]
> 此操作可能导致数据丢失。

```md
> [!WARNING]
> 此操作可能导致数据丢失。
```

GitHub 常用类型包括 `NOTE`、`TIP`、`IMPORTANT`、`WARNING` 和 `CAUTION`。不同平台可能将其当作普通引用；跨平台文档应确保提示文字本身足以表达含义。



