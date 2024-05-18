---
layout: post
title: "openjdk@21 compile on macos M1Start My First Tech Blog"
subtitle: 'openjdk@21 compile'
author: "Mihyun"
header-style: text
tags:
  - java
  - jvm
  - macos
---

### Ignoring sprintf Warnings in macOS Xcode (not recommended..)
- In macOS Xcode, the use of `sprintf` is strongly discouraged due to potential security risks such as buffer overflows. By default, Xcode enforces strict warnings for the use of `sprintf` to promote safer alternatives like snprintf. However, if you have a specific need to use sprintf and want to ignore these warnings, you can do so by adding a compiler flag.

- To bypass `sprintf` warnings when compiling openjdk, use `--disable-warnings-as-errors` with configure.
- https://hg.openjdk.org/jdk-updates/jdk9u/raw-file/tip/common/doc/building.html#troubleshooting

```
 $ ~/Desktop/java/openjdk > java --version # Boot JDK
openjdk 21.0.3 2024-04-16
OpenJDK Runtime Environment Homebrew (build 21.0.3)
OpenJDK 64-Bit Server VM Homebrew (build 21.0.3, mixed mode, sharing)

 $ ~/Desktop/java/openjdk > bash configure --disable-warnings-as-errors
 ...
 ====================================================
A new configuration has been successfully created in
/Users/user/Desktop/java/openjdk/build/macosx-aarch64-server-fastdebug
using configure arguments '--disable-warnings-as-errors --enable-debug --with-jvm-variants=server'.

Configuration summary:
* Name:           macosx-aarch64-server-fastdebug
* Debug level:    fastdebug
* HS debug level: fastdebug
* JVM variants:   server
* JVM features:   server: 'cds compiler1 compiler2 dtrace epsilongc g1gc jfr jni-check jvmci jvmti management parallelgc serialgc services shenandoahgc vm-structs zgc' 
* OpenJDK target: OS: macosx, CPU architecture: aarch64, address length: 64
* Version string: 21-internal-adhoc.user.openjdk (21-internal)
* Source date:    1715991414 (2024-05-18T00:16:54Z)

Tools summary:
* Boot JDK:       openjdk version "21.0.3" 2024-04-16 OpenJDK Runtime Environment Homebrew (build 21.0.3) OpenJDK 64-Bit Server VM Homebrew (build 21.0.3, mixed mode, sharing) (at /opt/homebrew/Cellar/openjdk/21.0.3/libexec/openjdk.jdk/Contents/Home)
* Toolchain:      clang (clang/LLVM from Xcode 15.4)
* Sysroot:        /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX14.5.sdk
* C Compiler:     Version 15.0.0 (at /usr/bin/clang)
* C++ Compiler:   Version 15.0.0 (at /usr/bin/clang++ -std=gnu++11)

Build performance summary:
* Build jobs:     8
* Memory limit:   16384 MB
```

### Issues with Compiling OpenJDK on macOS M1
- The Apple M1 chip was released in 2020, and OpenJDK received its last update in September 2021. During the compilation of OpenJDK on an M1 Mac, it became evident that the M1 architecture had not been fully addressed in the earlier versions of OpenJDK. This led to several issues during the build process.
- `error: invalid integral value '16-DMAC_OS_X_VERSION_MIN_REQUIRED=10120' in '-mstack-alignment=16-DMAC_OS_X_VERSION_MIN_REQUIRED=10120'`
   - https://bugs.openjdk.org/browse/JDK-8272700
   - https://github.com/openjdk/jdk/commit/d007be0952abdc8beb7b68ebf7529a939162307b
- `guarantee(val < (1ULL << nbits)) failed: Field too big for insn`
   - https://github.com/graalvm/labs-openjdk-17/issues/2
