# Git

```
git checkout -b <本地分支> remotes/origin/<远端分支名>  # 拉取远程仓库的分支，并命名

git show <commit id> #查看本次提交的具体信息
```

## `git`概述

`git`主要分为工作区、暂存区、仓库三个部分

## `git`主要命令

`git add` --将文件从工作区提交到暂存区

`git commit` 将文件生成对应的节点快照，从暂存区提交到仓库

`git reset files` 将文件从仓库提取到暂存区，更新暂存区代码

`git checkout files` 将文件从暂存区，拉取到工作区。此时本地文件会更改

