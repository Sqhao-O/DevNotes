---
title: Git 基础与仓库操作
aliases:
  - Git 基础
tags:
  - 开发工具/Git
  - 学习笔记
created: 2026-09-13
---

# Git 基础与仓库操作

返回 [Git 学习索引](00_Git学习索引.md)。

## 首次配置

Git 会把这两个身份写入后续提交。通常只需在一台电脑上配置一次：

```bash
git config --global user.name "你的名字"
git config --global user.email "you@example.com"

# 查看所有生效配置及其来源
git config --list --show-origin
```

如果项目要求不同的提交邮箱，在仓库目录内省略 `--global` 再执行一次即可；仓库级配置会覆盖全局配置。

## 创建或取得仓库

| 目标 | 命令 | 说明 |
| --- | --- | --- |
| 将当前目录变为 Git 仓库 | `git init` | 创建隐藏的 `.git` 目录 |
| 克隆已有项目 | `git clone <仓库地址>` | 下载文件、分支与提交历史 |
| 克隆到指定目录 | `git clone <仓库地址> <目录名>` | 适合目录名需要不同的场景 |

```bash
# 新项目示例
mkdir demo-project
cd demo-project
git init
git branch -M main
```

> [!warning] 不要手动删除 `.git`
> `.git` 保存提交历史、分支和配置。删除它会使当前目录不再是原来的仓库；若只是想清理未跟踪文件，应先查看 `git status`。

## 每天最常用的查看命令

```bash
# 简洁地看当前分支、修改和未跟踪文件
git status -sb

# 看尚未暂存的改动
git diff

# 看已暂存、将写入下一次提交的改动
git diff --staged

# 图形化查看提交历史和分支
git log --oneline --graph --decorate --all
```

`git diff` 默认比较「工作区」与「暂存区」；`git diff --staged` 比较「暂存区」与最近一次提交（`HEAD`）。

## 认识状态标记

`git status --short` 的前两列分别表示暂存区和工作区的状态：

| 标记 | 含义 | 常见处理 |
| --- | --- | --- |
| `??` | 未跟踪的新文件 | `git add <文件>`，或写入 `.gitignore` |
| ` M` | 工作区已修改、尚未暂存 | 检查后 `git add <文件>` |
| `M ` | 修改已暂存 | 用 `git diff --staged` 复核 |
| `MM` | 同一文件既有暂存改动，也有未暂存改动 | 分别复核两部分差异 |
| `D ` / ` D` | 文件被删除 | 确认删除是否应提交 |

## `.gitignore`：不需要纳入版本控制的文件

在仓库根目录创建 `.gitignore`，例如：

```gitignore
# 依赖与构建产物
node_modules/
dist/

# 本地环境变量和编辑器文件
.env
.idea/
.vscode/

# 系统文件
.DS_Store
```

- 规则只影响**尚未被 Git 跟踪**的文件。
- 已提交过的文件即使后来加入 `.gitignore`，仍会继续被跟踪；需要先执行 `git rm --cached <文件>`，再提交该变更。
- 不要把密码、令牌或私钥提交到仓库；即使后来删除，历史中仍可能保留它们。

## 小练习

```bash
git init
echo "# Demo" > README.md
git status
git add README.md
git diff --staged
git commit -m "docs: add readme"
git log --oneline
```

下一篇：[提交与撤销](02_提交与撤销.md)。
