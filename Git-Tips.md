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

`git fetch`：仅从远程拉取最新的代码到本地仓库，并不会更新工作区；不会改变工作区文件.`git fetch`+`git merage`/`git rebase` = `git pull`

`git pull`：`git pull`命令会拉取远程仓库最新的更新，并强制更新到工作区；会改变当前工作区文件

`git merge <branch>`: 合并其他分支<branch>的代码

常见的merge场景如下：

1、在master分支的`C2`节点，新建一个分支`iss53`，提交自己更改的代码；经过两次提交后`iss53`的分支已经指向`C5`

2、与此同时主分支上的代码，也被其他同事提交了自己的代码，master节点移动至`C4`

<img src="https://git-scm.com/book/en/v2/images/basic-merging-1.png" style="zoom:100%;" />

3、这时想要将`iss53`分支合并到master上，就必须调用`git merge` 命令，基于`C2`的基点合并`C4`、`C5`的提交,此时master分支自动指向合并后的`C6`节点

![https://git-scm.com/book/en/v2/images/basic-merging-2.png](https://git-scm.com/book/en/v2/images/basic-merging-2.png)



`git rebase <branch> `: 变基操作，改变当前分支的拉取基点，使得整个分支像是一条直线，不会有合并的分叉

使用`rebase`命令会将其他分支的所有修改都移动到当前分支，并且当前分支新的`commit`都在最前面

![https://git-scm.com/book/en/v2/images/basic-rebase-2.png](https://git-scm.com/book/en/v2/images/basic-rebase-2.png)



```
git checkout experiment
git rebase master  # 将experiment的拉取基点改变到C3
git checkout master
git merge experiment # 合并到主分支，这样就像一条直线一样，没有`merge`的分叉
```



![](https://git-scm.com/book/en/v2/images/basic-rebase-3.png)



>  不要对仓库上已有的`commit`进行变基操作，`rebase`会改变提交历史。
>
>  - 不要在公共分支使用rebase
>  - 本地和远端对应同一条分支,优先使用rebase,而不是merge

变基操作的实质是丢弃一些现有的提交，然后相应地新建一些内容一样但实际上不同的提交。 如果你已经将提交推送至某个仓库，而其他人也已经从该仓库拉取提交并进行了后续工作，此时，如果你用 `git rebase` 命令重新整理了提交并再次推送，就会变得一团乱麻.

## `git checkout -b <branch> <remote_branch>`

创建本地分支，并与指定的远端分支绑定

## `git checkout <branch>` 

自动追踪远端仓库同样的分支，如果远端不存在同样的分支名，则仅创建本地分支。

## `git branch -a`

显示所有分支

## `git reset`和`git checkout` 的区别

### 常用概念介绍

| 树                | 用途                                 |
| :---------------- | :----------------------------------- |
| HEAD              | 上一次提交的快照，下一次提交的父结点 |
| Index             | 预期的下一次提交的快照               |
| Working Directory | 沙盒                                 |

HEAD（快照/指针）: 指向当前分支最后一次提交的指针

Index（暂存区）: 索引，预期的下一次提交

work directory(工作区)：本地工作目录

1. `git reset`移动HEAD指向的节点，会改变当前分支；而`git checkout <branch>`移动HEAD自身，指向其他分支，不会改变当前分支的提交

    ```
    # 假设当前分支为dev分支
    git checkout master # 切换分支
    git resset master # 移动分支节点到master的提交
    ```

    ![https://git-scm.com/book/en/v2/images/reset-checkout.png](https://git-scm.com/book/en/v2/images/reset-checkout.png)

下面的速查表列出了命令对树的影响。

 “REF” 表示该命令移动了 HEAD 指向的提交（切换当前分支的快照），而 “HEAD” 则表示只移动了 HEAD 自身（切换分支）。 

|                             | HEAD | Index/暂存区 | Workdir/工作区 | WD Safe? |
| :-------------------------- | :--- | :----------- | :------------- | :------: |
| **Commit Level**            |      |              |                |          |
| `reset --soft [commit]`     | REF  | NO           | NO             |   YES    |
| `reset [commit]`            | REF  | YES          | NO             |   YES    |
| `reset --hard [commit]`     | REF  | YES          | YES            |  **NO**  |
| `checkout <commit>`         | HEAD | YES          | YES            |   YES    |
| **File Level**              |      |              |                |          |
| `reset [commit] <paths>`    | NO   | YES          | NO             |   YES    |
| `checkout [commit] <paths>` | NO   | YES          | YES            |  **NO**  |

`git reset --soft HEAD~`： 移动当前HEAD指向的分支,到上一次的提交；暂存区：不受影响  工作区不受影响

`git resset HEAD~`:  相当于 `git resset --mixed HEAD~`, 移动当前HEAD指向的分支,到上一次的提交, 并且取消暂存区的文件

`git resset --hard HEAD~`:  移动当前HEAD指向的分支,到上一次的提交, 并且强制覆盖暂存区以及当前工作目录中的文件


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
