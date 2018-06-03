---
layout: post
title:  "Starting out with Spring"
date:   2018-06-03 09:00:00
---

I have been a .NET and Node.js web developer for the last 10 years. Builing sites and APIs with ASP.NET, ASP.MVC and Express.js.

Recently I've been working on some projects that are built with Java using various different Java technologies such as JSP and JSF. 

While I have been getting my head around this new stack I came across the [Spring framework](https://spring.io/) and really liked the way it approached building web applications. The [Spring Boot](https://spring.io/projects/spring-boot) project is a realy amazing initative and solves a lot of "where do I start?" questions when using Spring.

When starting out in a new web technology there are a general set of questions I start with:

- [Is it fee?](#is-it-free)
- [What tools are available?](#what-tools-are-available)
- [What is the documentation like](#what-is-the-documentaiton-like)
- [What version of the language is supported?](#what-version-of-the-language-is-supported)
- [Where do you start?](#where-do-you-start)
- [How do you deploy the app?](#how-do-you-deploy-the-app)
- [How does data access work?](#how-does-data-access-work)
- [What logging tools are available?](#what-logging-tools-are-available)
- [How to create a REST end point?](#how-to-create-a-rest-end-point)
- [How to render a web page?](#how-to-render-a-web-page)

This is not a definative list by any means, but acts as good starting point for getting my head around it. Here are my findings after a weekend of spelunking into the Spring world.

### Is it free

Yes. Spring is released under the Apache License 2.0.

### What tools are available

In short loads:

- [NetBeans](https://netbeans.org/)
- [Spring Tool Suite](https://spring.io/tools) (Built on top of Eclipse)
- [IntelliJ](https://www.jetbrains.com/idea/)

I played with them all and found I prefer IntelliJ. I already had a subscription to [JetBrains](https://www.jetbrains.com/) as I use [WebStorm](https://www.jetbrains.com/webstorm/) a lot as well as some of their other tools so I didn't have to shed a load of cash for it which might put people off. NetBeans and Spring Tool Suite are both free so there is no barrier to entry.

### What is the documentation like

Great. There are a number of [guides](https://spring.io/guides/gs/accessing-data-jpa) available which are up to date and really helpful.

There is also a really good YouTube channel [SpringDeveloper](https://www.youtube.com/user/SpringSourceDev/featured) which has lots of useful presentations.

Another website that comes up all the time when searching for information is [baeldung.com](http://www.baeldung.com/).

This all makes life a lot easier when starting out. There is notthing worse than difficut to use or out of date documentation.

### What version of the language is supported

At this point (June 2018) Java 10 is out, Spring is at version 5.0.6 and Spring Boot is 2.0.2. Spring requires at least Java 8 and works with Java 10.

I like this as when starting with a new technolgy it's always annoying if you have to use an older version of a language or libraries.

### Where do you start

[start.spring.io](http://start.spring.io). This tool will generate a template Spring Boot application which you can open in your IDE of choice and get coding. It allows you to pick common deopendencies and include them in the pom.xml file for the project.

### How do you deploy the app

What's really nice about Spring Boot is that it builds your app into a single .jar file which includes Tomcat. This means to you use `java -jar myapp.jar` command and your app will be up and running with its own internal version of Tomcat. This makes it really easy to have something that works quickly without having to become an expert in Tomcat.

### How does data access work

This is a big qurstion that I am not going to attempt to answer in detail. Basically if you add the dependency `spring-boot-starter-data-jpa` to your project this will add in a load of data access related tools, the main one being [Hibernate](http://hibernate.org/orm/). This means you have an ORM and is extendable when things ger more complicated that 1 object mapping to 1 table.

### What logging tools are available

[Logback](https://logback.qos.ch/) is included as part of the `spring-boot-starter-web` dependency. For more details on how to tweak the settings or switch to [log4j 2](https://logging.apache.org/log4j/2.x/) see the [logging how-to](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-logging.html) guide.

### How to create a REST end point

See this [building a RESTful web service](https://spring.io/guides/gs/actuator-service/) guild. In essesnce decorate a class with the `@Controller` annotation and a methond with the `@ResponseBody` and `@GetMapping` annotateions e.g.

```java
@Controller
public class HomeController {
    @GetMapping("/home")
    @ResponseBody
    public Greeting sayHello() {
        return new Greeting("Hello");
    }
}

// returns
// {
//   "message": "Hello"
// }
```

### How to render a web page

This works in a similar way to the above. Add this dependency to the pom.xml file:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

To use thymeleaf templates and then change the contoller to be:

```java
@Controller
public class HomeController {
    @GetMapping("/home")
    public String sayHello() {
        return "hello";
    }
}
```

This will render a template called `hello.html`.

## Wrap up

I really like Spring. It provides out of the box answers to common web development questions. The documentation and the tooling are also really good.

### Issues encountered

There were a couple of "why does this now work?" questons when I was getting my first project setup.

1. Red warning text when running the project:

```
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by org.springframework.cglib.core.ReflectUtils$1 (file:/Users/blah/.m2/repository/org/springframework/spring-core/5.0.6.RELEASE/spring-core-5.0.6.RELEASE.jar) to method java.lang.ClassLoader.defineClass(java.lang.String,byte[],int,int,java.security.ProtectionDomain)
WARNING: Please consider reporting this to the maintainers of org.springframework.cglib.core.ReflectUtils$1
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
```

This is nothing to worry about and is caused by using Java 10 and should not be displayed in the next version of Spring.

2. Tests would not run

I had a project that included the `spring-boot-starter-data-jpa` and when I ran the default test I got a big error.

I asked about it on [Github](https://github.com/spring-io/initializr/issues/678) and was directed to a fix. Just inclide this dependency:

```
<dependency>
    <groupId>javax.xml.bind</groupId>
    <artifactId>jaxb-api</artifactId>
</dependency>
```
Again this was caused by using Java 10.
