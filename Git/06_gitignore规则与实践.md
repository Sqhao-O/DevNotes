---
title: .gitignore 规则与实践
aliases:
  - gitignore 教程
  - Git 忽略文件
tags:
  - 开发工具/Git
  - 学习笔记
created: 2026-09-13
---

# `.gitignore` 规则与实践

返回 [Git 学习索引](00_Git学习索引.md)。`.gitignore` 用来声明**不应被 Git 新增跟踪**的文件：例如依赖目录、构建产物、日志、本地环境配置和系统文件。它是仓库约定的一部分，应与项目代码一起提交。

> [!warning] `.gitignore` 不是安全边界
> 它不会阻止已跟踪的文件继续被提交，也无法从 Git 历史中抹去已经泄露的密钥。误提交密钥后，应立即吊销或轮换该密钥，并按团队安全流程处理历史。

## 最小示例

将 `.gitignore` 放在仓库根目录：

```gitignore
# 依赖和构建产物
node_modules/
dist/

# 本地配置：保留可提交的示例文件
.env
.env.*
!.env.example

# 操作系统文件
.DS_Store
Thumbs.db
```

以 `#` 开头的行是注释，空行用于分组。通常每行写一条规则。

## 常用匹配语法

| 写法 | 匹配含义 | 示例 |
| --- | --- | --- |
| `文件名` | 任意目录中同名文件或目录 | `*.log` 忽略所有 `.log` 文件 |
| `/路径` | 仅匹配当前 `.gitignore` 所在目录下的路径 | `/dist/` 只忽略根目录的 `dist` |
| `目录/` | 仅匹配目录 | `cache/` 不影响名为 `cache` 的普通文件 |
| `*` | 匹配除 `/` 外的任意字符 | `*.tmp` |
| `?` | 匹配除 `/` 外的一个字符 | `file?.txt` |
| `[abc]` | 匹配方括号中的任一字符 | `file[12].txt` |
| `**` | 跨目录匹配 | `**/coverage/` 忽略任意层级的 `coverage` 目录 |
| `!规则` | 取消忽略、重新包含 | `!.env.example` |

以下模式尤其常用：

```gitignore
# 任意层级名为 logs 的目录
**/logs/

# docs 目录下的所有内容（含多层子目录）
docs/**

# a 与 b 中间可有零个或多个目录
a/**/b.txt

# 仅忽略仓库根目录的配置文件
/local.settings.json
```

## 规则顺序与优先级

在同一个 `.gitignore` 文件中，**后出现且匹配的规则生效**。因此，通常先忽略一类文件，再用 `!` 保留例外：

```gitignore
*.env
!.env.example
```

多个位置都能提供忽略规则，常见优先级从高到低为：

1. 命令行指定的忽略规则。
2. 工作树中 `.gitignore` 文件；离目标文件更近的子目录规则优先于父目录规则。
3. 本地仓库的 `.git/info/exclude`。
4. 用户全局忽略文件（`core.excludesFile`）。

### 不能直接重新包含被忽略目录内的文件

Git 不会进入已经被整个忽略的目录来寻找例外规则。因此下面的写法**不能**保留 `logs/keep.txt`：

```gitignore
logs/
!logs/keep.txt
```

改为忽略目录内容，而不是目录本身：

```gitignore
logs/*
!logs/keep.txt
```

若需要保留更深层目录，也要逐层取消忽略对应目录。

## 三类忽略文件的位置

| 位置 | 是否提交 | 适用场景 |
| --- | --- | --- |
| 项目中的 `.gitignore` | 是 | 团队一致的依赖、构建产物、项目本地配置 |
| `.git/info/exclude` | 否 | 仅自己使用、无需共享的仓库级规则 |
| 全局忽略文件 | 否 | 所有项目都不需要的个人编辑器或系统文件 |

设置全局忽略文件的示例：

```bash
git config --global core.excludesFile ~/.config/git/ignore
```

随后把个人规则写入该文件，例如 `.DS_Store`、编辑器临时文件等。团队规则仍应写在项目 `.gitignore`，不要隐藏在个人全局配置中。

## 已跟踪文件为什么不会被忽略

`.gitignore` 只作用于**未跟踪文件**。如果文件已经提交过，加入规则后仍会出现在变更中。要停止跟踪但保留本地文件：

```bash
# 停止跟踪一个文件，磁盘上的文件仍保留
git rm --cached <文件路径>

# 停止跟踪一个目录内的文件，磁盘上的文件仍保留
git rm -r --cached <目录路径>
git commit -m "chore: stop tracking generated files"
```

执行前先用 `git status` 确认路径；`--cached` 很关键，省略它会同时删除工作区文件。

## 验证规则是否生效

```bash
# 显示某个路径是否被忽略、由哪份文件的哪一行规则命中
git check-ignore -v <文件路径>

# 列出所有被忽略的未跟踪文件
git ls-files --others --ignored --exclude-standard

# 查看 Git 当前会报告的状态
git status --ignored
```

`git check-ignore -v` 是排查的首选：它会输出命中的规则来源、行号和内容，能快速发现规则拼写、路径层级或顺序问题。

## 通用 Web 项目模板

下面是可按项目需要删减的模板：

```gitignore
# 依赖与包管理缓存
node_modules/
.npm/

# 构建、测试与日志
dist/
build/
coverage/
*.log

# 本地环境变量；可提交示例文件
.env
.env.*
!.env.example

# 编辑器设置：忽略目录内容，但保留可共享配置
.vscode/*
!.vscode/settings.json
!.vscode/extensions.json
.idea/

# 系统文件
.DS_Store
Thumbs.db
```

这里用 `.vscode/*` 而不是 `.vscode/`，是为了让后面的 `!` 规则能够保留指定配置文件。

## Java 与 Python 常见补充

```gitignore
# Java：Maven / Gradle / IDE 产物
target/
build/
*.class
.gradle/

# Python：缓存、虚拟环境、打包产物
__pycache__/
*.py[cod]
.venv/
venv/
.pytest_cache/
*.egg-info/
```

不要不加区分地复制大而全的模板。先理解每条规则对应的文件；团队需要提交的配置、测试夹具或示例环境变量应显式保留。

## 提交前检查清单

- `.gitignore` 是否只忽略可再生成、个人化或敏感的文件？
- 是否保留了团队需要的模板和共享编辑器配置？
- 对已跟踪文件，是否已使用 `git rm --cached` 停止跟踪？
- 是否运行过 `git check-ignore -v <路径>` 验证例外规则？
- 是否确认没有密钥、私钥或生产环境配置进入提交？

相关内容：[基础与仓库操作](01_Git基础与仓库操作.md#gitignore不需要纳入版本控制的文件)、[常用指令速查](05_Git常用指令速查.md)。
