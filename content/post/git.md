+++
date = "2014-12-23T14:08:23+08:00"
description = "git notes"
title = "git"

+++

设置git默认编辑器：

<br />

`git config --global core.editor \"/usr/local/bin/vim\"`

<br />

全局设置：

```
git config --global user.name xxx ；
git config --global user.email \"xxx@xxx.com\"
```

要忽略的文件：`.gitignore`

## 基本 

`git init` 新仓库

`git clone /path/to/repository [repository_dir_name]`

`git status` –> 查看当前缓冲区状态 或 查看哪些文件出现冲突

`git mv oldfile.txt newfile.txt` 缓冲区和工作目录同时更改

`git show HEAD~4` 查看 前前前前 一版修改的資料

`git blame filename` 查看文件修改记录

`git add .(*|filename)`  add将内容读入暂存区。修改完冲突也要add，才会记录到stage。

`git rm --cached FILENAME`  取消跟踪但不删除工作目录的文件

<br />

扩展：

`git add -u` 只加修改過的檔案, 新增的檔案不加入.

`git add -i` 進入交互模式

`git commit -m \"代码提交信息\"` 此时已经在本地的HEAD，但未提交到远端

`git commit --amend` 修改上一次的 commit 訊息。

`git commit --amend 檔案1 檔案2...` 將檔案1、檔案2加入上一次的 commit。

## 分支

`git checkout -b somename`  创建分支并立即切换过去

`git branch -d deletename`  强制删除分支

`git branch -f master HEAD~3` 单独前移某分支(当前 HEAD 不变)

<br />

扩展：

`git branch` 列出目前有多少分支

`git branch new-branch master` 由 master 產生新的 branch

`git diff <source_branch> <target_branch>`

`git merge <branch>` 合并某分支到当前所在分支

## 比较

`git diff --cached` 比較 staging area 與 Repository 差異

`git diff HEAD` 比較目前位置(工作目录) 與 Repository 差別

## rebase：

```
$ git checkout A
$ git rebase B
```

A以B作为基点，命令会把你的A分支里的每个提交(commit)取消掉，并且把它们临时保存为补丁(patch),切到”B”分支，最后把保存的这些补丁应用到”B”分支上。

<br />

`$ git rebase --abort` A分支会回到rebase开始前的状态。

<br />

rebase过程中的冲突解决：

```
git add -u
git rebase --continue
```

有冲突继续解决，重复这这些步骤，直到rebase完成。

## 远程

`git pull` 拉远端数据之后合并更新

`git remote -v` 查看远端分支在本地的情况

## 冲突

解决冲突后 `git add <filename>`

## 标签

`git tag 1.0.0 1b2e1d63ff(ID)`  获取ID用 `git log`命令

## 回退

`git revert HEAD`  回复工作目录到上次提交时，commit增加一项 revert 的记录，相当与一次新提交

```
git fetch origin;
git reset --hard origin/master 
```

彻底刷新成远端服务器最新版本（origin那里的master），丢弃本地

<br />

`git ls-files -d` 查看已刪除的檔案

`git ls-files -d | xargs git checkout --`  將已刪除的檔案還原

<br />

扩展：

<br />

缓存区就是提交时要发送的文件（附带文件的更改） 或 要发送的文件的当前状态（未提交时）

<br />

`git checkout HEAD(-f) hello.txt` 从指定commit中检出，工作目录变化。

`git checkout (—) hello.txt` 从缓存区取得（add但没commit的数据），缓存区没变。

`git reset --mixed` 本地文件未发生改变（`—hard` 时才会），缓存区和commit变化

`git reset --soft` 只恢复 commit,而保留 (当前)工作目录 和 (当前)缓存区 的状态

`git reset --hard` 彻底回复

<br />

扩展：

在之前的版本创建分支 `git branch test_branch -v old_commit_id`

```
git reflog；
git checkout some_commit_id
```

## Grep

`git grep \"te\" v1`  查 v1 是否有 “te” 的字串

`git grep \"te\"` 查現在版本是否有 “te” 的字串

## 二分排错法

```
git bisect start
git bisect good f608824 //first commit
git bisect bad master   //HEAD
```

会转到两者之间的提交点，如果检查没错输入 `git bisect good` 反之输入 `git bisect bad` 继续二分下去

## cherry-pick：

From:

```
A-B  master
   \\
    C-D-E-F-G topic
```

To:

```
A-B-D-F  master
       \\
        C-E-G topic
```

以上的实现步骤：

```
git checkout master
git cherry-pick D
git cherry-pick F
git checkout topic
git rebase master
```

## 暫存操作(stash)

`git stash` 將目前所做的修改都暫存起來。

`git stash apply` 取出最新一次的暫存。

`git stash pop` 取出最新一次的暫存並將他從暫存清單中移除。

`git stash list` 顯示出所有的暫存清單。

`git stash clear` 清除所有暫存。
