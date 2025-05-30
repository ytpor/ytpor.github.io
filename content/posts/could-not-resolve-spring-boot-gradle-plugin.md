---
title: 'Could not resolve org.springframework.boot:spring-boot-gradle-plugin'
date: 2025-05-30T10:00:00+08:00
draft: false
url: /2025/05/could-not-resolve-spring-boot-gradle-plugin
tags:
- gradle
- java
- spring-boot
- vscode
---

A snippet of my `build.gradle` looks like this:

```
plugins {
	id 'java'
	id 'org.springframework.boot' version '3.5.0'
	id 'io.spring.dependency-management' version '1.1.7'
}

java {
	toolchain {
		languageVersion = JavaLanguageVersion.of(17)
	}
}
...
```

While using `VS Code`, for reason unknown to me, the `PROBLEMS` tab shows the below:

```
The supplied phased action failed with an exception.
A problem occurred configuring root project 'springboot-challenge'.
Could not resolve all artifacts for configuration 'classpath'.
Could not resolve org.springframework.boot:spring-boot-gradle-plugin:3.5.0.
Required by:
    root project : > org.springframework.boot:org.springframework.boot.gradle.plugin:3.5.0
Dependency requires at least JVM runtime version 17. This build uses a Java 11 JVM.
```

There was no issue running `./gradlew build` from the `Terminal`.

The JVM version used is correct:

```
$ java --version

openjdk 17.0.15 2025-04-15
OpenJDK Runtime Environment (build 17.0.15+6-Ubuntu-0ubuntu124.04)
OpenJDK 64-Bit Server VM (build 17.0.15+6-Ubuntu-0ubuntu124.04, mixed mode, sharing)
```

Eventually, I've used the steps below to make the problem go away.

1. `Ctrl + Shift + P`
2. Look for `java.import.gradle.java.home`
3. Click on `Java: Import Java Project Into Workspace`
