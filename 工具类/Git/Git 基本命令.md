# 1. 常用命令

## 1. Git clone

## 2. Git pull/push

## 3. Git commit

## 4. Git branch

## 5.  Git checkout

- git checkout branchName 直接切换分支
- git checkout -b branchName 创建并且切换分支
- git checkout -b 分支名 origin/分支名 拉取远端分支到本地的新分支

## 6. Git merge

## 7. Git rebase

> 变基
>
> O--->O1--->O2  状态：OO2
>
> O--->S1--->S2
>
> pull的情况
>
> O--->O1--->O2--->Merge--->S1--->S2
>
> rebase
>
> O--->O1--->O2--->S1--->S2
>
> 相当于我分支的起点变成了OO2开始而不是O2

## 8. Git reflog

- git-reflog是用来恢复本地错误操作很重要的一个命令
- git log是显示当前的HEAD和它的祖先的，递归是沿着当前指针的父亲，父亲的父亲，…，这样的原则。git reflog根本不遍历HEAD的祖先。它是HEAD所指向的一个顺序的提交列表：它的undo历史。reflog并不是repo（仓库）的一部分，它单独存储，而且不包含在pushes，fetches或者clones里面，它纯属是本地的。
  reflog可以很好地帮助你恢复你误操作的数据，例如你错误地reset了一个旧的提交，或者rebase，…，这个时候你可以使用reflog去查看在误操作之前的信息，并且使用git reset --hard 去恢复之前的状态。

```sql
# 常用命令
# show 显示
# expire 设置过期
# delete 删除日志
# exists 子命令检查一个ref是否有一个reflog。

git reflog [show] [<log-options>] [<ref>]
git reflog expire [--expire=<time>] [--expire-unreachable=<time>]
	[--rewrite] [--updateref] [--stale-fix]
	[--dry-run | -n] [--verbose] [--all [--single-worktree] | <refs>…]
git reflog delete [--rewrite] [--updateref]
	[--dry-run | -n] [--verbose] <ref>@{<specifier>}…
git reflog exists <ref>
```



示例：

`HEAD@{2}`表示`HEAD`指针在两次移动之前的情况；而 `master@{one.week.ago}`表示`master`在本地仓库一周之前的情况。

```shell
$ git reflog show
ef64f10 (HEAD -> BlueLake_theme) HEAD@{0}: commit: 新增ethereum-programming-introduction
122e0ec (origin/BlueLake_theme) HEAD@{1}: commit: 移除了冗余的ethereum-rationale文章
c17fbbb HEAD@{2}: commit: 新增git-change-server-password文章
1603d1a HEAD@{3}: pull: Merge made by the 'recursive' strategy.
0ce1e93 HEAD@{4}: commit: 新增了以太坊原理
c73503c HEAD@{5}: commit: 修改了-X-Frame-Options的关键字
6af02f6 HEAD@{6}: commit: 新增了git-tag的文章；修改了git其他的文章，规范了名字、 关键字
9087fbd HEAD@{7}: commit: 新增了gti-reset文章
039d95c HEAD@{8}: commit: 移除了没用的目录
ff72601 HEAD@{9}: commit: 修改成了next主题
ef64f10 (HEAD -> BlueLake_theme) HEAD@{0}: commit: 新增ethereum-programming-introduction
```

可以通过 使用`git reset --hard` 去恢复之前的状态

## 9. Git log

## 10. Git status

## 11. Git stash

## 12. Git reset

# 2. 常用操作

## 2.1 合并commit

## 2.2 回退版本

## 2.3 回退rebase reset revert

## 2.4 从某个分支拉取新分支

