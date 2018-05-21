---
layout:     post
title:      "Spring Boot: WWH questions on Spring Boot?"
subtitle:   "回答 Spring Boot 的 WWH 问题，并解释 Spring Boot 中的一些概念。"
date:       2018-05-16
author:     "ZHR"
header-img: "img/spring-boot.png"
tags:
    - Spring Boot
---

> 本文参考 [IBM Spring Boot 基础](https://www.ibm.com/developerworks/cn/java/j-spring-boot-basics-perry/index.html)

## What, Why & How

Spring Boot 是一个轻量级框架，可以完成基于 Spring 的应用程序的大部分配置工作。

以前，就算是编写一个基于 Spring 的 Hello World 应用都需要进行大量繁琐的配置工作以及一些公共的、约定俗成的开发过程。使用 Spring Boot 会缩短从创建、配置项目到开发的过程。

Spring Boot 根据默认值来快速地构建应用。我们也可以根据需要修改这些默认值，从而改变 Spring Boot 的构建行为。

## 相关站点

Spring Boot 的源码托管在 [Github ](https://github.com/spring-projects/spring-boot) 上。在这里的探索会让我们对 Spring Boot 有更深入的理解，比如 Spring Boot 包含了哪些 module。

![spring boot modules](/img/in-post/spring-boot-modules.jpg)

Spring Boot 的项目主页是：[http://projects.spring.io/spring-boot/](http://projects.spring.io/spring-boot/)。

## Starters

> 关于 Starter 的更多介绍参考 [Spring Boot Starters](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-starter)。

Starter 其本质是一组依赖，用于减少需要手动配置依赖的数量。比如，如果需要使用 Spring 和 JPA，那么我们添加一个 `spring-boot-starter-data-jpa` starter 即可。

Spring Boot 内置的 Starter 的名字都遵循 `spring-boot-starter-*`  模式，`*`  代表应用的类型。Starter 所包含的依赖都是特定类型的应用所独有的。

需要稍微提一下的是，Spring Boot 从功能的角度对 Starter 进行了分类：application starters, production starters, technical starters。

## Spring Boot 可执行 JAR PK Uber JAR

### Uber JAR

> 关于 Uber JAR  的原文解释参考 [StackOverflow: Uber JAR](https://stackoverflow.com/questions/13620281/what-is-the-maven-shade-plugin-used-for-and-why-would-you-want-to-relocate-java#)。

首先应该知道，Maven 的一个 artifact 只会包含自身的 `classes/resources`。当进行项目构建时，Maven 负责找出项目所依赖的所有 artifact，通常指的是 JARs。

所谓的 **Uber JAR**，指的是将项目所依赖的所有 JARs 的内容都抽取出来，然后和项目本身的 `classes/resources`  放在一起，得到一个 *big JAR*。这么做的原因其实是为了方便运行应用，因为在运行应用的时候我们只需要一个 *big JAR*，而不再需要一堆的 JARs。在某些情况下，这么做也可以方便应用发布。

在配置项目依赖时，要避免使用 Uber JAR ，因为 Uber JAR 破坏 Maven 的 *dependency resolution feature*。另外，也不应该将一个 Uber JAR 发布到 Maven 仓库中。

### Spring Boot 可执行 JAR

Spring Boot 定义了[一种不同于上述 Uber JAR 特殊的 JAR 文件布局](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#executable-jar)。同时，Spring Boot 提供了工具来生成这样的可执行 JAR；在运行这个可执行 JAR 时，Spring Boot 也会使用一种特殊的类加载器来处理 JAR 中的类。
