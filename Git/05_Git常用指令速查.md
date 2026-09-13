---
title: Git 常用指令速查
aliases:
  - Git 命令速查表
tags:
  - 开发工具/Git
  - 学习笔记
created: 2026-09-13
---

# Git 常用指令速查

返回 [Git 学习索引](00_Git学习索引.md)。本页适合在操作前快速查阅；涉及覆盖、改写历史的命令已标注风险。看不懂命令含义时，可回到对应专题笔记阅读背景。

> [!tip] 最安全的起手式
> 先执行 `git status -sb`，需要确认内容再执行 `git diff` 或 `git diff --staged`。这三个命令都不会修改文件。

## 初始化与身份

| 目的 | 命令 |
| --- | --- |
| 初始化当前目录 | `git init` |
| 克隆仓库 | `git clone <仓库地址>` |
| 配置全局提交姓名 | `git config --global user.name "你的名字"` |
| 配置全局提交邮箱 | `git config --global user.email "you@example.com"` |
| 查看生效配置及来源 | `git config --list --show-origin` |
| 查看远程地址 | `git remote -v` |
| 新增远程仓库 | `git remote add origin <仓库地址>` |

## 查看状态、差异与历史

| 目的 | 命令 |
| --- | --- |
| 查看简洁状态与当前分支 | `git status -sb` |
| 查看未暂存的改动 | `git diff` |
| 查看已暂存的改动 | `git diff --staged` |
| 查看指定文件的差异 | `git diff -- <文件路径>` |
| 图形化查看所有分支历史 | `git log --oneline --graph --decorate --all` |
| 查看最近 5 次提交 | `git log --oneline -5` |
| 查看某次提交的内容 | `git show <提交ID>` |
| 查看某一行的最后修改者 | `git blame <文件路径>` |
| 搜索历史提交信息 | `git log --all --grep="关键词"` |

## 暂存与提交

| 目的 | 命令 |
| --- | --- |
| 暂存指定文件 | `git add <文件路径>` |
| 交互式挑选改动暂存 | `git add -p` |
| 暂存所有已跟踪文件的修改和删除 | `git add -u` |
| 查看待提交内容 | `git diff --staged` |
| 创建提交 | `git commit -m "type: 简短说明"` |
| 修改最近一次提交的信息 | `git commit --amend -m "新的说明"` |
| 将暂存内容补入最近一次提交 | `git commit --amend --no-edit` |

`git add .` 会按当前目录范围暂存新建、修改、删除的文件。多人协作或改动较多时，优先指定文件或使用 `git add -p`，更容易避免误提交。

## 撤销与临时保存

| 目标 | 命令 | 风险 |
| --- | --- | --- |
| 取消暂存，保留工作区内容 | `git restore --staged <文件路径>` | 低 |
| 丢弃工作区中某文件的修改 | `git restore <文件路径>` | 高：会覆盖内容 |
| 用新提交撤销某次已提交改动 | `git revert <提交ID>` | 低：不改写历史 |
| 回退最近提交，保留暂存内容 | `git reset --soft HEAD~1` | 中：改写本地历史 |
| 回退最近提交，保留工作区内容 | `git reset HEAD~1` | 中：改写本地历史 |
| 彻底回退并覆盖工作区 | `git reset --hard HEAD~1` | **高：可能丢失改动** |
| 暂存当前工作（含未跟踪文件） | `git stash push -u -m "说明"` | 低 |
| 查看暂存列表 | `git stash list` | 低 |
| 恢复并删除最近一条 stash | `git stash pop` | 中：可能产生冲突 |
| 恢复但保留 stash | `git stash apply` | 低 |
| 查找本地引用的变动历史 | `git reflog` | 低 |

> [!warning] 公开历史优先使用 `revert`
> 已推送到共享分支的提交，不要轻易使用 `reset` 或 `commit --amend` 后强推。`git revert` 会产生一个可审查的反向提交，协作风险更低。

## 分支与合并

| 目的 | 命令 |
| --- | --- |
| 创建并切换到新分支 | `git switch -c feat/short-description` |
| 切换已有分支 | `git switch <分支名>` |
| 基于远程分支创建本地分支 | `git switch -c <本地分支> --track origin/<远程分支>` |
| 查看本地分支及上游 | `git branch -vv` |
| 查看本地与远程分支 | `git branch -a` |
| 删除已合并的本地分支 | `git branch -d <分支名>` |
| 合并指定分支到当前分支 | `git merge <分支名>` |
| 将当前分支提交重新接到目标分支后 | `git rebase <分支名>` |
| 继续已解决冲突的变基 | `git rebase --continue` |
| 放弃正在进行的合并 | `git merge --abort` |
| 放弃正在进行的变基 | `git rebase --abort` |

解决冲突后：先编辑并删除冲突标记，再运行 `git add <文件>`；合并时运行 `git commit`，变基时运行 `git rebase --continue`。

## 远程同步

| 目的 | 命令 |
| --- | --- |
| 下载远程引用，不修改工作区 | `git fetch origin` |
| 将远程主线合并到当前分支 | `git merge origin/main` |
| 拉取并只允许快进 | `git pull --ff-only` |
| 拉取并使用 rebase 整合本地提交 | `git pull --rebase` |
| 首次推送并设置上游 | `git push -u origin <分支名>` |
| 推送当前分支 | `git push` |
| 删除远程分支 | `git push origin --delete <分支名>` |
| 安全地强制推送独占分支 | `git push --force-with-lease` |

`git push --force-with-lease` 仅适用于自己独占的分支，并且应先 `git fetch`。不要在 `main`、`master` 或团队共享分支上使用它。

## 标签、文件与工作目录

| 目的 | 命令 |
| --- | --- |
| 创建附注标签 | `git tag -a v1.2.0 -m "Release v1.2.0"` |
| 查看标签说明 | `git tag -n` |
| 推送一个标签 | `git push origin v1.2.0` |
| 推送所有标签 | `git push origin --tags` |
| 停止跟踪但保留本地文件 | `git rm --cached <文件路径>` |
| 从 Git 删除文件并暂存删除 | `git rm <文件路径>` |
| 创建额外工作目录 | `git worktree add ../<目录名> -b <分支名>` |
| 查看工作目录 | `git worktree list` |

## 两个高频流程

### 开始一个功能

```bash
git switch main
git pull --ff-only
git switch -c feat/short-description
```

### 提交并推送

```bash
git status -sb
git add <文件路径>
git diff --staged
git commit -m "feat: 简短说明"
git push -u origin <当前分支>  # 仅首次推送时需要 -u
```

延伸阅读：[基础与仓库操作](01_Git基础与仓库操作.md)、[提交与撤销](02_提交与撤销.md)、[分支、合并与冲突](03_分支合并与冲突.md)、[远程协作与实用排查](04_远程协作与实用排查.md)。
