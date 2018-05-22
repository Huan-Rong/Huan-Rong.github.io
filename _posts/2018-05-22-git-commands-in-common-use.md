---
layout:     post
title:      "Git Commands in Common Use"
subtitle:   "持续更新一些常用的命令用法"
date:       2018-05-22
author:     "ZHR"
header-img: "img/git-logo.png"
tags:
    - Git 
---

## 分支操作

创建本地分支：`git branch branchName`

创建远程分支（将本地分支推送到远程仓库）：`git push --set-upstream remoteName branchName`

删除远程分支：`git push remoteName :heads/branchName`

查看远程分支：`git branch -r`

查看本地分支：`git branch`

检出分支：`git checkout branchName`

