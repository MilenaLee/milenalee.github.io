---
layout: post
title: "[Spring Boot] gradlew build error"
subtitle: '공부한 내용 정리'
author: "Mihyun"
header-style: text
tags:
- spring
- spirng boot
- gradle
---

아래와 같은 에러가 발생했다
```cmd
$ ./gradlew build
Error: Could not find or load main class org.gradle.wrapper.GradleWrapperMain
Caused by: java.lang.ClassNotFoundException: org.gradle.wrapper.GradleWrapperMain
```

이 에러는 `gradle-wrapper.jar`가 없어서 나는 에러라는데, 나의 경우는 해당 파일이 있었다.
```agsl
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties

```
그래서 한참을 삽질하닥 그냥 새로 파일을 만들어주었더니 문제가 해결되었다.

macos의 경우 gradle이 설치되어있지 않다면 homebrew로 설치하면 된다.
```agsl
$ brew install gradle
$ gradle wrapper
$ ./gradlew build
```

끝
