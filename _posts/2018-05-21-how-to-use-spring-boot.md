---
layout:     post
title:      "How to Use Spring Boot"
subtitle:   "本文参考官方文档第三部分的介绍"
date:       2018-05-21
author:     "ZHR"
header-img: "img/spring-boot.png"
tags:
    - Spring Boot
---

> 本文参考 [官方文档第三部分](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-build-systems) ，但并没有对该部分的所有内容都进行详细的介绍，有些章节会一笔带过，有些章节尚未谈及。

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

[关于 Starter 在另一篇博文中已有介绍](http://www.bulibucai.site/2018/05/16/what-is-spring-boot/#starters)

## Structuring Your Code

### Using the "default" Package

如果一个类没有包含 package 声明，那么就认为它被包含在一个默认包里。但应该**避免这种情况**，因为对于使用 `@ComponentScan`、 `@EntityScan` 和 `@SpringBootApplication`注解的 Spring Boot 应用来说，这种情况可能会引起某些特别的问题。

另外，对于 default package 的命名应当遵守 Java 建议的包命名规范约定，即使用反向域名的形式，如 `com.example.project`。

### Locating the Main Application class

建议将 main application class 放置于 `com.example.project`。通常，我们会在 main application class 上使用 `@SpringBootApplication` 注解，这会隐式地定义一个 *search package*。

如果不想使用 `@SpringBootApplication` 注解，那么可以使用 `@EnableAutoConfiguration` 和 `@ComponentScan` 注解来替代。

## Configuration Classes

> Spring Boot favors Java-based configuration.

尽管能够以 XML 的方式来使用 `SpringApplication`，但是通常使用一个 `@Configuration` 注解的类。通常，定义了 `main` 方法的类是作为一个核心 `@Configuration` 的不错选择。

我们不必将所有的 `@Configuration` 放置于一个单独的类里，可以使用 `@Import` 注解将其他的 configuration classes 导入进来。

如果一定要使用基于 XML 的配置，那么仍然建议先定义一个 `@Configuration` 类，然后使用 `@ImportResource` 注解将 XML 配置文件加载进来。

## Auto-configuration

Spring Boot auto-configuration 会基于我们所添加的 jar 依赖，自动地对 Spring 应用进行配置。比如，如果应用的 classpath 存在 `HSQLDB`，那么我们不需要手工地配置 database connection beans，Spring Boot 会自动地配置一个内存数据库。

要启用自动配置，需要事先在某一个 `@Configuration` 类上添加 `@EnableAutoConfiguration` 或 `@SpringBootApplication` 注解。

需要注意的，无论是 `@EnableAutoConfiguration` 注解还是 `@SpringBootApplication` 注解，都只能添加一次。**另外，通常建议将其中一个注解添加到核心 `@Configuration` 类上。**

### Gradually Replacing Auto-configuration

Auto-configuration 是非侵入性的。在任何时候，我们都可以定义自己的配置来替换 auto-configuration 中相应的部分。

### Disabling Specific Auto-configuration Classes

如果不需要自动配置某些类，那么可以通过 `@EnableAutoConfiguration` 注解的 `exclude` 属性来禁止。

## Using the @SpringBootApplication Annotation

许多 Spring Boot 应用开发者都希望他们的应用能够使用 *auto-configuration*、*component scan*，同时能够在 application class 定义一些额外的配置。一个 `@SpringBootApplication` 注解可以启用上述的三个特性。

也就是说，使用 `@SpringBootApplication` 注解等同于同时使用 `@Configuration`、`@EnableAutoConfiguration`、`@ComponentScan` 注解。

