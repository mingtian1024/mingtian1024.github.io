---
layout: post
title: '在一台电脑上配置多个代码托管平台账号'
subtitle: '如何同时配置github、gitlab、gitee账号？'
date: 2019-06-13
categories: GIT GitHub
tags: GIT GitHub
---
### 问题
公司代码从svn迁移到了gitlab上后，需要在电脑上配置gitlab。自己一直用的github，所以配置上有些冲突，
本文介绍如何在一台电脑上同时配置多个多个代码托管平台，如github、gitlab、gitee
### 一. 配置ssh秘钥
1. 生成默认ssh key
``` bash
$ ssh-keygen -t rsa -C "xxx@yyy.com"
```
如果是常规配置，连续三个回车即可，依次跳过秘钥文件命名、设置密码、确认密码，
会生成如下两个文件![秘钥文件](assets/resource/Snipaste_2019-06-15_00-05-50.png)

2. 一路回车



### 二. 配置git 用户名与邮箱
* 作用
本地代码push到github或gitlab远端后，本地配置的用户名与邮箱会与github等账号关联。
如果本地邮箱与github邮箱一致，可以通过点击提交者名字跳转至首页，且会在此用户的首页显示提交记录；
如果不一致，就不会显示提交记录。

* 全局配置
可以在任意目录下操作，针对本地所有git仓库都有效，但如果某个仓库单独配置了，则全局配置对此库不生效
``` bash
git config --global user.name "name"
git config --global user.email "email@xxx.com"
```

* 针对单个项目配置
需要在对应项目下操作。
``` bash
git config  user.name "name"
git config  user.email "email@xxx.com"
```