---
title: Hello-World-powered-by-Java
date: 2023-12-21 16:56:39
tags:
    - 🌟
categories:
	- java
---

```java
public class Hello {
	public static void main(String[] args) {
		// 生成一些简单的输出
		System.out.println("Hello, World!");
	}
}
```

这是一个java代码，很明显能看出这是输出Hello, World的程序
但是，我们需要看懂
什么是看懂：
1. 知道对程序每个单词的称呼
2. 知道程序每个单词的功能
3. 知道单词与单词结合后的功能
4. 知道如何运行程序
5. 知道运行程序所需要的环境

<!--more-->

# 解析每个单词
`public`是访问修饰符之一，提供了最大的可访问性
`class` 是用来定义类的关键字
`Hello`和`main`是标识符，用于称呼自己+{}包含的所有内容，其基本的符号结构与“一切”这个词一样
`Hello`是一个公共类
一个Java源文件中只能有一个公共类，并且该类的名称必须与文件名相同
对该程序进行编译将生成对应的.class文件

`main`是一个特殊的方法名，是程序的入口点
而程序的入口点，必须定义在公共类中，并且必须有这样的签名
```
public static void main(String[] args)
```
`static` 是一个关键字，用于修饰类的成员（字段和方法），以及内部类
有
1. 静态字段
2. 静态方法
3. 静态块
4. 静态内部类
`void`代表main的返回值是没有
`String[] args`是main的参数列表，接受运行java程序时调用的命令行参数
`System`是Java的内置类，位于java.lang类库中，其中包含了一些基本的类和接口，它们被隐式导入到每个Java源文件中
`out`是一个对象，类型为PrintStream
`println`：是`PrintStream`类的一个方法，用于打印输出并在行末尾添加换行符。

# 如何运行程序
```
javac 1.java;java 1
```
javac编译程序源代码，生成名称相同的.class字节码
java运行.class文件，通过制定类名来解释执行对应的类

# 程序环境
## 开发环境
JDK（Java Development Kit）是Java开发工具包的缩写，是Java开发人员进行Java应用程序开发的核心工具。它包含了编译器、运行时环境（JRE）、调试器、开发工具和类库等组件。
## 运行环境
JRE是Java程序的运行时环境。JRE包含了Java虚拟机（JVM）和运行所需的类库等组件，用于执行Java程序
## 虚拟机
JVM（Java Virtual Machine）是Java虚拟机的缩写，它是Java平台的核心组件之一。JVM是一个虚拟计算机，可以在物理计算机上模拟执行Java字节码（由Java编译器生成的中间代码），使得Java程序具有跨平台的特性。
# 总结
1. java是一个面向对象的语言
2. 可以看出，java即进行了编译，又进行了解释
# 问题
什么是面向对象，类、接口、方法、成员、对象分别都是什么？
java有哪些常用的库？