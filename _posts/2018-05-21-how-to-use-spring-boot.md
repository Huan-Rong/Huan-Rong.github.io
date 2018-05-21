---
layout:     post
title:      "How to Use Spring Boot: Part 1"
subtitle:   "本文参考官方文档第三部分第 13、14 章"
date:       2018-05-21
author:     "ZHR"
header-img: "img/spring-boot.png"
tags:
    - Spring Boot
---

> 本文参考 [官方文档第 13、14 章](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-build-systems)

# How to Use Spring Boot 

How to use spring boot? The question covers topics such as build systems, auto-configuration, and how to run your appllication. Additionally it also covers some Spring Boot best practices.

## Build Systems

Spring Boot 推荐使用 Maven 或 Gradle 作为构建系统。当然， Spring Boot 也支持其他的构建系统，比如 Ant，但 Spring Boot 对它们的支持并没有 Maven 和 Gradle 那么好。

### Dependency Management

> 首先，先看看使用构建系统来管理一个 Spring Boot 应用时，如何管理依赖。

> 本节参见 [Dependency Management](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#using-boot-dependency-management) 

每一个版本的 Spring Boot 都提供了一系列它所支持的 *curated* 依赖，这些依赖包括了所有的 Spring 模块以及一些第三方库。在实际使用时，我们不必在配置文件中指定这些依赖的版本号，因为 Spring Boot 会为我们做好这件事情。当我们对 Spring Boot 进行升级时，这些依赖也会以一种方便的方式进行升级。

当然，如果有需要，我们是可以指定这些依赖的版本号，来覆盖 Spring Boot 自身所推荐的版本。但是由于每一个版本的 Spring Boot 都和某一个版本的 Spring 关联。因此，在这个问题上的 best practice 就是不要在配置中指定版本，让 Spring Boot 来管理这一切。

#### Maven

当使用 Maven 来管理项目时，我们有三种方式可以让 Maven 来替我们管理上述的 Dependency Management 问题。第一种是使用 `spring-boot-starter-parent` ，第二种方式是以 import 的方式使用 `spring-boot-dependencies` 的 pom，第三种是明确地直接地在项目 pom 中配置需要的依赖。之所以可以使用第三种方式的原因，是因为 [Spring Boot 的自动配置机制并不是和 Starter 有着不可解耦的绑定关系的](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#getting-started-first-application-auto-configuration)。

### Starter

