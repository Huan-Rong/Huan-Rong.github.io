---
layout:     post
title:      "Spring Boot 基础教程"
subtitle:   "官方文档第 11 章总结"
date:       2018-05-14
author:     "ZHR"
header-img: "img/spring-boot.png"
tags:
    - Spring Boot
---

> 本文参考 [Spring Boot Reference Guide 第 11 章](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#getting-started-first-application)

## Developing Your First Spring Boot Application

### 前提条件

本文使用 Maven 来创建 *Hello Wolrd!* 项目。在开始之前，需要检查 JDK 版本和 Maven 的版本。

不同的版本的 Spring Boot 对于 JDK 和 Maven 的版本要求可能不同，这一点可以查阅 Spring Boot 的官方文档。

### 具体步骤

#### Step1：创建项目目录和 pom.xml 文件

创建一个项目目录，如 *myproject*。接下来，所有的操作都在该目录中进行，也就是说，*myproject* 是当前工作目录（即命令 `pwd` 的输出结果）。

接下来，在 *myproject* 目录中创建 *pom.xml* 文件，该文件的内容如下。我们使用 *pom.xml* 文件来构建项目。我们可以使用 `mvn package` 命令来测试下能不能成功地构建。 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>myproject</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.1.0.BUILD-SNAPSHOT</version>
	</parent>

	<!-- Additional lines to be added here... -->

	<!-- (you don't need this if you are using a .RELEASE version) -->
	<repositories>
		<repository>
			<id>spring-snapshots</id>
			<url>https://repo.spring.io/snapshot</url>
			<snapshots><enabled>true</enabled></snapshots>
		</repository>
		<repository>
			<id>spring-milestones</id>
			<url>https://repo.spring.io/milestone</url>
		</repository>
	</repositories>
	<pluginRepositories>
		<pluginRepository>
			<id>spring-snapshots</id>
			<url>https://repo.spring.io/snapshot</url>
		</pluginRepository>
		<pluginRepository>
			<id>spring-milestones</id>
			<url>https://repo.spring.io/milestone</url>
		</pluginRepository>
	</pluginRepositories>
</project>
```

到目前为止，其实我们已经可以将该项目导入到 IDE 进行开发了，如下图所示。但我们继续使用手工的方式来完善这个 *Hello World!*。

![idea import maven project](/img/in-post/idea-import-maven-project.jpg)

#### Step2：添加依赖

Spring Boot 提供了许多用来把 *jar* 文件添加到 *classpath* 的 *Starter*。在本文中，我们已经在 *pom.xml* 文件的 `parent` 节点中使用了 `spring-boot-starter-parent` 。

`spring-boot-starter-parent` 是一种特殊的 *Starter*，它提供了许多有用的 Maven 默认配置。在其 *pom.xml* 文件中也提供了 `dependency-management` 节点。

除了 `spring-boot-starter-parent` 之外，其他的  *Starter* 则是在开发特定类型的应用时会用到。比如，在本文中我们开发的是一个 web  应用，所以，我们需要添加一个 `spring-boot-starter-web` 依赖。我们在 *pom.xml*  文件的 `parent` 节点之后加上这个依赖的配置。

```xml
<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
</dependencies>
```

我们可以在加上这段配置的前后使用命令 `mvn dependency:tree` 来查看发生的变化。

#### Step 3：编写代码

有了项目和项目依赖，我们可以开始编写代码了。默认情况下，Maven 会编译 `src/main/java` 路径下的代码。因此我们需要在项目的根目录下创建这样一个目录结构，然后才在其中创建一个源代码文件，如 `src/main/java/Example.java`。

```java
import org.springframework.boot.*;
import org.springframework.boot.autoconfigure.*;
import org.springframework.web.bind.annotation.*;

@RestController
@EnableAutoConfiguration
public class Example {
    
    @RequestMapping("/")
    String home() {
        return "Hello World!";
    }
    
    public static void main(String[] args) throws Exception {
        SpringApplication.run(Example.class, args);
    }
}
```

虽然这段代码并不长，但它做的事情却有些多。接下来我们将会就重要的部分进行说明。

##### @RestController 和 @RequestMapping

`@RestController` 和 `@RequestMapping` 是 Spring MVC 的注解，并不是 Spring Boot 特有的。如需了解更多，参见 [Spring MVC](https://docs.spring.io/spring/docs/5.0.6.RELEASE/spring-framework-reference/web.html#mvc)。

`@RequestMapping` 注解提供了路由信息。任何使用 `/` 路径的 HTTP 请求都应该被映射到 `home` 方法进行处理。

`@RestController` 注解告诉 Spring 将结果字符串直接返回。

##### @EnableAutoConfiguration

`@EnableAutoConfiguration` 注解会让 Spring Boot 依据 *pom.xml*  文件中配置的依赖去配置 Spring。在本文中，我们添加了 `spring-boot-starter-web` 依赖，因此，自动配置机制 （the auto-configuration）会认为我们正在开发一个 web 应用，并以此来配置 Spring。

##### main 方法

这只是一个符合 Java 标准规范的方法，用作整个程序的入口。具体的执行委托给了 Spring Boot 的 `SpringApplication` 类的 `run`  方法。`SpringApplication` 将会引导启动整个应用。

我们需要将 `Example.class` 作为参数传递给 `run` 方法，以此来告诉 SpringApplication 哪一个才是 primary Spring component。

#### Step 4：运行

到目前为止，这个 *Hello World!* 应用示例已经可以正常工作了。

由于我们使用了 `spring-boot-starter-web`，因此我们可以在项目根目录下执行 `mvn spring-boot:run` 以此来启动应用。

通过在浏览器地址栏输入：`localhost:8080` 来查看运行效果。效果图如下。

![spring boot hello world](/img/in-post/spring-boot-hello-world.jpg)

#### Step5：创建可执行的 jar

我们通过创建一个可以在生产环境中运行的完全自包含的可执行 *jar* 文件来完成我们的示例。可执行 *jar* （有时称为 *fat jars*）是包含 `.class` 的存档文件，以及代码需要运行的所有 *jar* 依赖。

Java 没有提供加载嵌套 *jar* 文件的标准方法。所谓的嵌套 *jar* 文件，是指一个 *jar* 文件包含在另一个 *jar*  文件中。如果要发布一个自包含的应用程序，这可能会有问题。

为了解决这个问题，许多开发人员使用 *uber jar*。一个 *uber jar* 会将应用程序所有依赖的所有类都打包成一个归档文件。这种方法的问题在于，很难看到应用程序中有哪些库。并且，如果在多个 *jar* 中使用相同的文件名(但使用不同的内容)，也会有问题。

Spring Boot 采用了一种不同的方法，我们可以直接嵌套 *jar*。

为了创建一个可执行的 *jar*，我们需要将 `spring-boot-maven-plugin` 添加到 *pom.xml* 文件中。具体的做法是先将下面的配置添加到 *pom.xml* 文件的 `dependencies` 节点之后：

```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
    </plugin>
   </plugins>
</build>
```

保存 *pom.xml* 文件之后重新执行 `mvn package` 命令，之后我们可以在 *target/* 文件夹中看到生成的文件：

![nested jar](/img/in-post/nested-jar.jpg)

其中，`myproject-0.0.1-SHAPSHOT.jar.orininal`  是在加上以上 `build` 配置之前生成的，而 `myproject-0.0.1-SHAPSHOT.jar` 则是一个可执行的嵌套 *jar*。使用命令 `jar tvf jar_name` 可以查看这二者的内容。

最后，通过命令 `java -jar target/myproject-0.0.1-SHAPSHOT.jar` 来启动应用，在浏览器地址栏输入 `localhost:8080` 来查看运行的效果：

![spring boot hello world](/img/in-post/spring-boot-hello-world.jpg)

### 其他

`mvn dependency:tree` 命令会以树形结构的形式把项目的依赖打印出来。