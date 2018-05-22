---
layout:     post
title:      "The Executable Jar Format"
subtitle:   "本文参考官方文档附录部分 E 章节"
date:       2018-05-22
author:     "ZHR"
header-img: "img/spring-boot.png"
tags:
    - Spring Boot
---

> 本文参考 [官方文档附录部分 E 章节](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#executable-jar)；本文忽略部分内容，如 `PropertiesLauncher`。

# The Executable Jar Format

`spring-boot-loader` 模块使得 Spring Boot 支持 *executable jar* 和 *war*。如果使用的构建系统是 Maven 或者 Gradle，那么 executable jar 是自动生成的，我们不需要知道它们的工作原理。

但是，如果你需要使用另一种不同的构建系统来生成 executable jar，或者只是对底层技术感到好奇，那么本文将会为你提供这方面的背景知识。

## Nested JARs

Java 没有提供标准的方法来加载 *nested jar*。许多开发者采用了 *shaded jar* 的方案来解决 *nested jar* 的相关问题。但 Spring Boot 提供了一种不同的方法。

[关于 nested jars 的具体内容已在其他文章中有所介绍。](http://www.bulibucai.site/2018/05/14/spring-boot-hello-world-application/#step5%E5%88%9B%E5%BB%BA%E5%8F%AF%E6%89%A7%E8%A1%8C%E7%9A%84-jar)

### The Executable Jar File Structure

Spring Boot Loader-compatible jar files 的文件结构组织如下。

```
example.jar
 |
 +-META-INF
 |  +-MANIFEST.MF
 +-org
 |  +-springframework
 |     +-boot
 |        +-loader
 |           +-<spring boot loader classes>
 +-BOOT-INF
    +-classes
    |  +-com
    |     +-mycompany
    |        +-project
    |           +-YourClasses.class
    +-lib
       +-dependency1.jar
       +-dependency2.jar
```

应用本身的类将会放置于 `BOOT-INF/classes` 目录下，而应用依赖的 jar 文件将会被放置于 `BOOT-INF/lib` 目录下。

### The Executable War File Structure

Spring Boot Loader-compatible war files 的文件结构组织如下。

```
example.war
 |
 +-META-INF
 |  +-MANIFEST.MF
 +-org
 |  +-springframework
 |     +-boot
 |        +-loader
 |           +-<spring boot loader classes>
 +-WEB-INF
    +-classes
    |  +-com
    |     +-mycompany
    |        +-project
    |           +-YourClasses.class
    +-lib
    |  +-dependency1.jar
    |  +-dependency2.jar
    +-lib-provided
       +-servlet-api.jar
       +-dependency3.jar
```

应用依赖通常放置于 `WEB-INF/lib` 目录下。

使用一个嵌入容器运行应用的方式所需要依赖放置于 `WEB-INF/lib-provided` 目录下。当将应用发布到一个传统的 Web 容器运行时（而不是使用嵌入容器），这些依赖是没有用的。

## Spring Boot's "JarFile" Class

Spring Boot 用于加载 *nested jar* 的核心类是 `org.springframework.boot.loader.jar.JarFile`。这个类可以从一个 standard jar 文件中加载其中的内容，也可以从一个 nested child jar 文件中进行加载。

当第一次加载时，每一个 `JarEntry` 的位置都会被映射为一个物理文件与外层 jar 文件的位移。有了这个映射信息，我们就可以指定加载 jar 文件中的某一个 *entry* ，而不需要 *unpack* 整个文档，也不需要将所有的 *Entry* 加载到内存中。

[关于具体的实现原理可以上 GitHub 查看源码。](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-tools/spring-boot-loader/src/main/java/org/springframework/boot/loader/jar/JarFile.java)

### Compatibility with the Standard Java "JarFile"

Spring Boot Loader 尽可能地与已有的代码和库保持兼容。`org.springframework.boot.loader.jar.JarFile` 继承于 `java.util.jar.JarFile`，它可以作为后者的 *drop-in replacement* 来使用。

## Launching Executable Jars

`org.springframework.boot.loader.Launcher` 是一个特殊的启动类，它是可执行 Jar 文件的 *main entry point*。也就是说，它是应用真正的入口：它会创建一个合适的 `URLClassLoader`，最后调用我们定义的 `main` 方法。

`Launcher` 存在三种子类：`JarLauncher`、`WarLauncher`、`PropertiesLoader`。它们的作用都是从 nested jar/war 文件中加载资源。`JarLauncher` 和 `WarLauncher` 加载资源的路径都是固定的，前者从 `BOOT-INF/lib/` 加载，后者从 `WEB-INF/lib/`  和 `WEB-INF/lib-provided/` 加载。

`PropertiesLoader` 默认情况下会从 `BOOT-INF/lib/` 加载，但我们也可以配置其他加载路径。具体的做法是在 `loader.properties`  文件中设置环境变量 `LOADER_PATH` 或 `loader.path`，多个路径之间使用逗号分隔。

### Launcher Manifest

使用哪一种 `launcher` 是在 `META-INF/MANIFEST.MF` 对 `Main-Class`  属性进行配置的。而应用的启动类（定义的 `main` 方法的类）则是通过对 `Start-Class` 属性进行配置来指定的。

我们不需要在 `META-INF/MANIFEST.MF` 配置 `Class-Path`，因为 `Class-Path`是可以从 nested jars 推断出来的。

