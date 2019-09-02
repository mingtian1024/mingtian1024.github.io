---
layout: post
title: '【转】Git常用命令总结'
date: 2018-08-14
categories: Git
cover: 'https://ae01.alicdn.com/kf/HTB1lxhddLWG3KVjSZPcq6zkbXXaM.jpg'
tags: Git 
---


## 名词

* master：默认开发分支
* origin：默认远程版本库
* Index/Stage：暂存区
* Workspace：工作区
* Repositotry：仓库区（或本地仓库）
* Remote：远程仓库

### 1. 新建代码库

1. 在当前目录新建一个Git代码库：`git init`
2. 新建一个目录，并将其初始化为Git代码库：`git init [project-name]`
3. 下载一个项目和整个代码历史：`git clone [url]`

### 2. 配置
Git的设置文件为`.gitconfig`,它可以在用户主目录下（全局配置），也可以在项目目录下（项目配置）

1. 显示当前Git配置：`git config --list`
2. 编辑Git配置文件：`git config -e [--global]`
3. 设置提交代码的用户信息：
* `git config [--global] user.name "[name]"` 
* `git config [--global] user.email "[email address]"`

### 3. 增加/删除/修改文件

1. 查看状态：`git status`
2. 查看变更：`git diff`
3. 添加指定文件到暂存区，包括子目录：`git add [dir]`
4. 添加当前目录的所有文件到暂存区：`git add`
5. 添加每个变化前，都会要求确认；对于同一个文件的多处变化，可以实现分次提交：`git add -p`
6. 删除工作区文件，并且将这次删除放入暂存区：`git rm [file1] [file2]...`
7. 停止追踪指定文件，但该文件会保留在工作区：`git rm --cached [file]`
8. 改名文件，并将这个更改放入暂存区：`git mv [file-original] [file-renamed]`

### 4. 提交代码

1. 提交暂存区所有代码到仓库：`git commit -m [message]`
2. 提交暂存区指定文件到仓库区：`git commit [file1] [file2]... -m [message]`
3. 提交工作区自上次commit之后的变化，直接到仓库区：`git commit -a`
4. 提交时显示所有diff信息：`git commit -v`
5. 使用一次新的commit，代替上一次提交：如果代码没有任何新变化，则用来改写上一次commit的提交信息：`git commit --amend -m [message]`
6. 重做上一次commit，并包括制定文件的新变化：`git commit --amend [file1] [file2]...`

### 5. 分支

1. 显示所有本地分支 `git branch`
2. 列出所有远程分支 `git branch -r`
3. 列出所有本地分支和远程分支 `git branch -a`
4. 新建一个分支，与指定的远程分支建立追踪追踪关系 `git branch --track [branch][remote-branch]`
5. 删除分支 `git branch -d [branch-name]` 
6. 删除远程分支 `git push origin --delete [branch-name]` `git branch -dr [remote/branch]`
7. 新建一个分支，并切换到该分支 `git checkout [branch-name]`
8. 切换到指定分支，并更新工作区 `git checkou [branch-name]`
9. 切换到上一个分支 `git checkout -`
10. 建立追踪关系，在现有分支与指定的远程分支之间 `git branch --set-upstream [branch][remote-branch]`
11. 合并指定分支到当前分支 `git merge [branch]`
12. 盐和指定分支到当前分支 `git rebase <branch>`
13. 选择一个commit，合并进当前分支 `git cherry-pick [commit]`

### 6.标签
1. 列出所有本地标签 `git tag`
2. 基于最新提交创建标签 `git tag <tagname>`
3. 删除标签 `git tag -d <tagname>  `
4. 删除远程tag `git push origin :refs/tags/[tagName]`
5. 查看tag信息 `git show [tag]`
6. 提交指定tag `git push [remote] [tag]`
7. 提交所有tag `git push [remote] --tags`
8. 新建一个分支，指向某个tag `git checkout -b [branch] [tag]`

### 7.查看信息

