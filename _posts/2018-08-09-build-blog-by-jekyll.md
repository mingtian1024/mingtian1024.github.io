---
layout: post
title: '基于GitHub Pages和Jekyll的博客'
subtitle: '折腾一天，终于上线了'
date: 2018-08-09
categories: Blog
cover: 'https://i.loli.net/2018/08/09/5b6c5bdabb829.jpg'
coverMsg: 2016.6，庐山，如琴湖
tags: GitHub Jekyll
---

>今天花了点时间折腾了下GitHub Pages，用Jekyll搭了个简单的博客。记录下简要的步骤。

1. Github注册、配置等
2. 创建一个与Github用户名相同的仓库repo，再配置pages，就可以以`xxx.github.io`的域名形式访问
3. 安装ruby环境
4. 安装gem。gem是ruby包管理工具。有的ruby安装工具会附带gem
5. 用gem安装jekyll: `gem install jekyll`
6. 找到合适的jekyll主题模板，修改_config.yml里的配置
7. 在`_post`文件夹下发布文章
8. 本地命令行中 `jekyll server`既可在本地运行
9.  `jekyll server --watch`可以看到本地的改动并实时发布
10. 确认无误后提交到github远程仓库

常用命令
``` bash
jekyll help # 查看帮助
jekyll help subcommand # 查看子命令的帮助信息

jekyll build # 构建

jekyll server # 开启本地服务器查看效果
jekyll server -P 4001 # 指定端口, 默认端口4000
jekyll server -w # 文件发生变化时，自动重新编译
```