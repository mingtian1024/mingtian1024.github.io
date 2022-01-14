---
layout: post
title: '在Git中消灭.DS_Store'
date: 2018-08-14
categories: Git
cover: 'https://ae01.alicdn.com/kf/HTB1lxhddLWG3KVjSZPcq6zkbXXaM.jpg'
tags: Git 
---

### .DS_Store 与 .gitignore

macOS中，每个目录下都有一个`.DS_Store`文件，用来告诉Finder如何展示文件夹。99％的情况下我们不需要将`.DS_Store`文件上传到Git仓库中，于是我们将.DS_store添加到项目的`.gitignore`文件中。

```
......
*.DS_Store
......
```

如果我们不想为每一个项目都做此配置，如何全局忽略`.DS_Store`文件呢？设置全局`.gitignore`

### 全局`.gitignore`

Git 允许我们通过变量 `core.exclusudesfile` 指定全局 gignore 文件。

1. 在本地创建并填充全局`.gitignore`文件（不止.DS_Store，还加入了一些常见的Git需要忽略的文件配置）
    ```
    # Compiled source #
    ###################
    *.com
    *.class
    *.dll
    *.exe
    *.o
    *.so
    
    # Packages #
    ############
    # it's better to unpack these files and commit the raw source
    # git has its own built in compression methods
    *.7z
    *.dmg
    *.gz
    *.iso
    *.jar
    *.rar
    *.tar
    *.zip
    
    # Logs and databases #
    ######################
    *.log
    *.sql
    *.sqlite
    
    # OS generated files 
    ######################
    .DS_Store
    .DS_Store?
    ._*
    .Spotlight-V100
    .Trashes
    ehthumbs.db
    Thumbs.db   
    ```


2. 在项目目录下创建 `.gitignore_global`文件，并将其于步骤一中的文件关联起来

   ```
   ls -n ../../myGitRepo/.gitignore_global ~/gitignore
   ```

3. git全局设置

   执行命令配置 `core.excludesfile` 属性为步骤一中创建的文件

   ```
   git config --global core.excludesfile ~/.gitignore
   ```

   或者在项目`.gitignore`中添加一下内容：

   ```
   excludefile = ~/.gitignore
   ```



### 总结

为了避免对每一个项目 `.gitignore` 配置`.DS_Store`，我们引入了设置了`core.excludesfile` 属性，设置其值为本地的一个公共`.gitignore`文件，还需要为每一个项目创建`.gitignore_global`文件。看似一增一减，没有减少工作量，但是除了`.DS_Store`还有很多文件是所有项目都需要忽略的，这样一设置，还是比较省事的。



### 参考

[Eradicating-.DS_Store-From-Git](https://0xmachos.com/2020-01-22-Eradicating-.DS_Store-From-Git/)

