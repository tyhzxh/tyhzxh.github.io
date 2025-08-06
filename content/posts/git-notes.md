---
title: "Git 版本控制学习笔记"
date: 2024-02-09T17:44:43+08:00
draft: false
tags: ["Git", "版本控制", "开发工具", "学习笔记"]
categories: ["技术文档"]
description: "Git版本控制系统的基础概念和常用操作，包括工作区、暂存区、版本库的概念以及各种Git命令的使用方法"
cover:
    image: ""
    alt: "Git版本控制"
    caption: "Git版本控制系统学习笔记"
---

> 本文是学习廖雪峰Git教程的学习笔记，整理了Git的基础概念和常用操作。

## 基础概念

***在.git文件所在目录下进行操作***

Git有三个重要的区域：

1. **工作区(Working Directory)** - 所处的文件目录
2. **暂存区(Stage)** - 重点概念，临时存储修改的地方
3. **版本库(Repository)** - 工作区有一个隐藏目录 `.git`，这个不算工作区，而是Git的版本库

> **重要提示**：创建Git版本库时，Git自动为我们创建了唯一一个master(main)分支，所以`git commit`就是往master(main)分支上提交更改。

## 基础操作(Windows下)

**核心理念**：`git add`命令实际上就是把要提交的所有修改放到暂存区（Stage），然后，执行`git commit`就可以一次性把暂存区的所有修改提交到分支。

**推荐环境**：在PowerShell或Git Bash下输入命令
*建议使用Git Bash - 支持很多Linux下的命令*

### 初始化和添加文件

1. **初始化Git仓库**：使用`git init`命令
2. **添加文件到Git仓库**，分两步：
   - 使用命令`git add <file>`，注意，可反复多次使用，添加多个工作区文件到暂存区
   - 使用命令`git commit -m <message>`添加暂存区所有文件到Git仓库，完成

### 💡 实用技巧

```bash
# 一次性添加所有改动
git add .
```

## 检查操作

### 查看状态

检查 ***工作区+暂存区*** 的状态，使用`git status`命令：

```bash
Changes to be committed:
(use "git restore --staged <file>..." to unstage)
        modified:   1.md

Changes not staged for commit:
(use "git add <file>..." to update what will be committed)
(use "git restore <file>..." to discard changes in working directory)
        modified:   1.md
```

### 查看修改内容

如果`git status`告诉你有文件被修改过，用`git diff`可以查看修改内容：

```bash
$ git diff readme.txt 
diff --git a/readme.txt b/readme.txt
index 46d49bf..9247db6 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,2 +1,2 @@
-Git is a version control system.
+Git is a distributed version control system.
 Git is free software.
```

### 查看历史记录

在Git中，我们用`git log`命令查看历史修改记录：

```bash
$ git log
commit 1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master)
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:06:15 2018 +0800

append GPL
```

如果嫌输出记录太多，可以加上`--pretty=oneline`参数：

```bash
$ git log --pretty=oneline
049c19eb21cbe21986810b7de105d3d7ab584d1c (HEAD -> main) 1
a87bfdefbae52977a72b7955a406eab118a04df5 w
1dc3373085994a336323614fa726732b582f22f2 1
```

**说明**：一大串类似`049c19eb21cbe...`的是commit id（版本号），由SHA1计算出来的一个非常大的数字，用十六进制表示。

### 版本指针概念

在Git中：
- **HEAD** 表示当前版本，也就是最新的提交
- **HEAD^** 表示上一个版本
- **HEAD^^** 表示上上一个版本
- **HEAD~100** 表示往上100个版本

## 回退操作

### 工作区回退

```bash
# 丢弃工作区的修改
git checkout -- file

# 示例
git checkout -- readme.txt
```

**注意**：`--` 很重要，不能省略！

### 暂存区回退

```bash
# 把暂存区的修改撤销掉（unstage），重新放回工作区
git reset HEAD <file>

# 示例
git reset HEAD 1.md
```

### 版本库回退

```bash
# 回退到上一个版本
git reset --hard HEAD^
```

**个人理解**：版本回退就是修改HEAD指针的指向（HEAD指向当前所处版本）

如果回退后想要恢复，可以使用`git reflog`来查看每一次命令的记录：

```bash
$ git reflog
e475afc HEAD@{1}: reset: moving to HEAD^
1094adb (HEAD -> master) HEAD@{2}: commit: append GPL
e475afc HEAD@{3}: commit: add distributed
eaadf4e HEAD@{4}: commit (initial): wrote a readme file
```

从输出可知，可以通过commit id来恢复到指定版本。

## 删除操作

### 普通删除 vs Git删除

**普通情况下**：直接在文件管理器中删除文件，或者用`rm`命令删除：
```bash
rm test.txt
```

**在Git中**，删除文件后查看状态：
```bash
$ git status
On branch master
Changes not staged for commit:
(use "git add/rm <file>..." to update what will be committed)
(use "git checkout -- <file>..." to discard changes in working directory)

deleted:    test.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

可以看出工作区和版本库不一致，此时有两个选择：

### 选择1：确实要删除

```bash
# 从版本库中删除文件
git rm test.txt

# 提交删除操作
git commit -m "remove test.txt"
```

### 选择2：误删恢复

```bash
# 从版本库恢复文件到工作区
git checkout -- test.txt
```

> **重要提醒**：`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以"一键还原"。但是，从来没有被添加到版本库就被删除的文件，是无法恢复的！

## 远程仓库

### 基本操作

```bash
# 推送到远程仓库
git push origin master

# 添加远程仓库
git remote add origin https://github.com/tyhzxh/tyhzxh.github.io.git
```

### 个人经验

如果是通过`git clone`获取的仓库，本地仓库和远程仓库会自动同步，所以每次只需要：
1. 在本地完成修改和提交
2. 使用`git push`推送到远程仓库

## 总结

Git的核心概念是理解三个区域（工作区、暂存区、版本库）之间的关系，掌握了这个概念，Git的各种操作就变得清晰明了。记住：

- `git add` 是将修改从工作区提交到暂存区
- `git commit` 是将暂存区的修改提交到版本库
- `git push` 是将本地版本库同步到远程仓库

---

*参考资料：廖雪峰Git教程*