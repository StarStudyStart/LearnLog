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

## Merge Request

### 1、fork主仓，拉取到本地

### 2、主仓新建issue

### 3、本地新建带issue id的分支

```
git chekcout -b issue_id_title
```

### 4、在分支上提交代码

```
# 开发完成后直接在新建分支提交代码
git add .
git commit -m "xxx"
```

### 5、更新本地代码并合到当前分支

```
# 同步更新本地代码
git remote -v # 查看远程仓库，通常仅有origin
git reomvte add ups ssh/https:xxxx.git # 添加主仓为远程仓库
git remote -v # 再次查看远程仓库信息
git fetch ups/dev  # 拉取主仓最新的dev分支代码
git checout issue_id_title # 切换要更新的分支
1、git merge ups/dev  # 将ups/dev合并到本地分支
2、git rebase ups/dev # 将ups/dev合并到本地分支，并且保持本地新提交的代码在最上层

git push -f # push到fork仓库
```

### 6、fork仓提交MR

`github`提交`pull request`合并本地分支到主仓

1、新建pull request

2、选择要合并的分支、选择本地分支

3、选择issue
