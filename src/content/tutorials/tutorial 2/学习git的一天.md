# 学习git的一天

想要用AI工具进行vibe coding来开发app，工具等，没有git管理真的寸步难行。从今往后，有了AI，我的网站，mac电子书阅读器，英语单词句子背诵app，obsidian的主题，我都自己做，可以做自己想要的功能。感谢AI，为此，我又学习了一遍git。

Git 是一个**分布式版本控制系统**（Distributed Version Control System）。一句话说：**它记录你项目每一次改动的历史，让你能随时查看、回退、对比、协作。**

根据自己的平台，选择安装git：

```
https://git-scm.com/install/windows
```

下面是一些git的基本使用教程。

## 一、核心概念

### 1. 三个区域（最重要的基础）

```
工作区              暂存区              版本库
Working Tree   →   Staging Area   →   Repository
                add              commit
```

| 区域       | 存什么                          |
| ---------- | ------------------------------- |
| **工作区** | 你正在编辑的磁盘文件            |
| **暂存区** | 用 `add` 挑出来、准备提交的内容 |
| **版本库** | 已 commit 的历史                |

**关键**：同一个文件在三个区域可以各不相同。`git commit` 提交的是**暂存区**，不是工作区。

### 2. 仓库的起点：`git init`

在讲三区之前，得先有仓库。**`git init` 是一切的起点**——它在当前目录创建隐藏的 `.git/` 目录，把普通目录变成 Git 仓库。

```bash
git init
```

执行后：

```
当前目录/
├── .git/          ← 新建的，Git 所有数据都在这
└── (你的文件)
```

常用变体：

```bash
git init                    # 在当前目录初始化
git init <目录名>           # 新建目录并初始化
git init --bare             # 裸仓库，无工作区（用作远程服务器）
git init -b main            # 初始化时指定默认分支为 main
```

> `git init` 用于**本地从零新建项目**。如果是**参与已有项目**，用 `git clone <url>`，它会自动完成 init + 关联远程 + 拉取历史，**不需要**再手动 `git init`。

### 3. commit（提交）

一次 commit = 项目在一个时间点的**快照**，带一个唯一 hash（如 `88b7c2f`）。它记录：改了什么、谁改的、什么时候、父 commit 是谁。

### 4. 分支（branch）

分支只是一个**指向某个 commit 的可移动指针**，不是文件的副本。所以建分支几乎零成本。

```
master    →  C3
feature   →  C2
```

`HEAD` 是"你现在在哪"的指针，通常指向当前分支。

### 5. 远程（remote）

远程仓库（origin）是云端副本，比如github，gitlab等代码托管平台。`push` 上传到远程仓库，`pull` 下载合并到本地。

### 6. 四个对象

Git 底层只有四种对象：`blob`（文件内容）、`tree`（目录）、`commit`（提交）、`tag`（标签）。日常不用管，知道即可。

## 二、日常最常用命令

### 初始化 / 获取仓库

```bash
git init                # 本地新建仓库
git init -b main        # 指定默认分支名
git clone <url>         # 克隆已有仓库
```

### 查看状态（用得最多）

```bash
git status              # 当前状态
git diff                # 工作区 vs 暂存区
git diff --cached       # 暂存区 vs HEAD
git log --oneline -10   # 最近 10 条提交
git log --oneline --graph --all   # 图形化看分支
```

### 提交

```bash
git add <文件>          # 加入暂存区
git add -A              # 全部（改、删、增）
git commit -m "信息"    # 提交
git commit --amend      # 修改上一次提交
```

### 分支

```bash
git branch              # 列出分支
git branch <名字>       # 新建分支
git checkout <名字>     # 切换（旧）
git switch <名字>       # 切换（新）
git switch -c <名字>    # 新建并切换
git branch -d <名字>    # 删除分支
git merge <分支>        # 合并到当前分支
```

### 撤销（重点，容易混）

| 目的                        | 命令                                     |
| --------------------------- | ---------------------------------------- |
| 撤销工作区改动              | `git restore <文件>`                     |
| 取消暂存（add）             | `git restore --staged <文件>`            |
| 撤销 commit（保留改动）     | `git reset --soft HEAD~1`                |
| 撤销 commit（改动回工作区） | `git reset HEAD~1`                       |
| 撤销 commit（改动全丢）     | `git reset --hard HEAD~1`                |
| 安全撤销已 push 的 commit   | `git revert <hash>`                      |
| 找回误删的 commit           | `git reflog` + `git reset --hard <hash>` |

### 远程

```bash
git clone <url>              # 克隆
git remote -v                # 查看远程
git push origin <分支>       # 推送
git pull                     # 拉取并合并
git fetch                    # 只拉取不合并
```

### 临时存放

```bash
git stash push -u -m "说明"  # 暂存改动（含新文件）
git stash list               # 查看
git stash pop                # 取回并删除
git stash apply              # 取回但保留
```

### 其他高频

```bash
git cherry-pick <hash>       # 挑某个 commit 过来
git rebase <分支>            # 变基，整理历史
git tag v1.0                 # 打标签
git clean -fd                # 删除未跟踪文件（危险）
git worktree add <路径> <分支>  # 新建工作树
```

## 三、典型工作流

### 从零新建项目

```bash
git init                      # 1. 初始化仓库
git add .                     # 2. 加入暂存区
git commit -m "first commit"  # 3. 首次提交
git remote add origin <url>   # 4. 关联远程
git push -u origin main       # 5. 推送
```

### 参与已有项目

```bash
git clone <url>               # 克隆，自带 init 和历史
# ... 开发 ...
git add -A
git commit -m "feat: xxx"
git push
```

### 单人开发（已 init 过）

```bash
git add -A
git commit -m "feat: xxx"
git push
```

### 功能分支

```bash
git switch -c feature/login   # 建分支
# ... 开发 ...
git add -A && git commit -m "add login"
git switch main
git merge feature/login       # 合并
git branch -d feature/login   # 删分支
```

### 团队协作（PR 流程）

```bash
git switch -c feature/xxx
# ... 开发、commit ...
git push origin feature/xxx
# 在 GitHub 上开 PR → Review → 合并
```

### 同步远程最新

```bash
git fetch
git rebase origin/main    # 或 git merge origin/main
```

## 四、易混点速查

| 疑问                   | 答案                                                       |
| ---------------------- | ---------------------------------------------------------- |
| `init` vs `clone`      | init 本地从零建，clone 复制已有仓库                        |
| `reset` vs `revert`    | reset 改历史（本地用），revert 加反向 commit（已 push 用） |
| `fetch` vs `pull`      | fetch 只下载，pull = fetch + merge                         |
| `merge` vs `rebase`    | merge 保留分叉，rebase 拉成直线                            |
| `restore` vs `reset`   | restore 管文件，reset 管 commit                            |
| `checkout` vs `switch` | switch 专管切分支，checkout 是老命令啥都管                 |
| 改动丢了能找回吗       | 已 commit 的用 `reflog`；未 commit 的找不回                |

## 五、记住这几条就够日常用

1. **从零开始先 `git init`，已有项目用 `git clone`**
2. **改 → add → commit → push** 是主循环
3. **提交前先 `git status` + `git diff`** 看清改了什么
4. **撤销记住三档**：`restore`（文件）、`reset`（commit，本地）、`revert`（commit，已 push）
5. **不确定就先 `git stash`**，别硬来
6. **`git reflog` 是后悔药**，误操作先别慌

## 小结

| 层级 | 内容                                                 |
| ---- | ---------------------------------------------------- |
| 起点 | `git init`（新建）/ `git clone`（复制）              |
| 概念 | 三区、commit、分支、远程                             |
| 高频 | status / add / commit / switch / merge / push / pull |
| 撤销 | restore / reset / revert / reflog                    |
| 协作 | 分支 + PR + fetch/rebase                             |

**一句话**：Git 从 `git init` 开始 —— 在工作区改，用 add 挑进暂存区，用 commit 存成快照，用分支隔离，用 push/pull 同步；撤销则是「文件用 restore，commit 用 reset/revert，误删用 reflog」。