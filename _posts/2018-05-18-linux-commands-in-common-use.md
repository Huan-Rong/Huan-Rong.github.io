---
layout:     post
title:      "Linux Commands in Common Use"
subtitle:   "持续更新一些常用到的命令用法"
date:       2018-05-18
author:     "ZHR"
header-img: "img/arch-linux-2.png"
tags:
    - Linux
---

## 参考列表

* [如何在 Linux 中使用 find](https://zhuanlan.zhihu.com/p/36947532)

## find

**`find ~ -name '*jpg'`**

递归查找家目录下名字以 `jpg` 结尾 的文件，包括目录。**根据文件名字查找**。

**`find ~ -iname '*jpg'`**

递归查找家目录下名字以 `jpg` 结尾的文件(包括目录)，并且不考虑大小写。

**`find ~ -name '*jpg' -type d`**

递归查找家目录下名字以 `jpg` 结尾的目录。

**`find ~ \( -iname '*jpeg' -o -iname \)`**

递归查找家目录下名字以 `jpg` 或 `jpeg` 结尾的文件(包括目录)，并且不考虑大小写。**`-o` 表示或**。

**`find /data -owner ian`**

递归查找 `/data` 目录下用户 `ian` 拥有的文件。**根据文件拥有者查找**。

**`find ~ -perm -o=r`**

递归查找家目录中对 `other` 可读的文件。**根据权限查找**。

**`find /var/log -size +1G`**

递归查找 `/var/log` 目录下大小超过 1G 的文件。**根据文件大小查找**。

**`find ~ -name '*jpg' -maxdepth 1`

**查找家目录下名字以 `jpg` 结尾的文件，但**查找深度**为 1，也就是不进行递归查找。

