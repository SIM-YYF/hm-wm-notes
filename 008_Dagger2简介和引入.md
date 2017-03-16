# 008\_Dagger2的简介和引入

## 学习目标

* 理解 Dagger2 的作用
* 熟练导入 Dagger2

## 学习基础要求

* 了解 Dagger2 的作用

## 引言和回顾

前面我们在开源项目的课程中，已经介绍过 Dagger2 的使用，它是在 Java 和 Android 中用来依赖注入的类库。在这个项目中我们就会用到 Dagger2 来进行层和层之间的隔离。

## 课堂内容

### 1. Dagger2 简介

#### 1.1. 什么是Dagger2

Dagger 是为 Android 和 Java 平台提供的在编译时进行依赖注入的框架。  
编译时，是指编译时自动生成所需对象注入的代码。  
依赖注入，是指不用 new 对象，而是靠函数参数传入对象或者函数返回值返回对象。

#### 1.2. 为什么使用Dagger2

Dagger2解决了基于反射带来的开发和性能上的问题。

#### 1.3. 做什么工作

本项目中Dagger2主要用于做界面和业务之间的隔离。

### 2. Dagger2使用步骤

我们打算不修改 MainActivityPresenter 代码，把 MainActivityPresenter 对象注入 MainActivity 中

![](img/architecture005.png)

#### 2.1.完成 Dagger2 的使用环境配置

1.在 Project 的 build.gradle 文件添加 apt 工具的 gradle 插件

```java
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.1.2'
        // 添加 apt
        classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
    }
}
```

2.在 Module 的 build.gradle 文件引入 apt 插件和 dagger2

```java
apply plugin: 'com.android.application'
// 引入 apt 插件
apply plugin: 'com.neenbedankt.android-apt'
......
dependencies {
    compile fileTree(include: ['*.jar'], dir: 'libs')
    compile 'com.android.support:appcompat-v7:24.2.1'
    // 添加 dagger2
    compile 'com.google.dagger:dagger:2.6'
    apt 'com.google.dagger:dagger-compiler:2.6'
}
```

> * `compile 'com.google.dagger:dagger:2.6'`是导入依赖的 dagger 相关的类
> * `apt 'com.google.dagger:dagger-compiler:2.6'`是使用 apt 工具在编译的时候自动产生依赖注入相关代码，apt 会在编译的时候运行 dagger-compiler 里面的代码，来自动产生依赖注入相关的代码
> * `apply plugin: 'com.neenbedankt.android-apt'`应用 apt gradle 插件
> * `classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'`添加 apt 插件

