---
layout: post
title: 接口型模式
categories: JAVA设计模式
description: 接口型模式
keywords: 接口
---


# 接口型模式


### 练习
[**1、写出在java中抽象类于接口的不同**](http://wumeiblog.com/exercise/java设计模式/interface)


[**2、下面哪些说法是正确的的？**](http://wumeiblog.com/exercise/java设计模式/interface)

```java
public interface RocketSim {
	abstract double getMass();
	public double getThrust();
	void setSimTime(double t);
} 
```
* A、尽管只有getMass()方法被显示声明为抽象，但RocketSim的三个方法都是抽象方法。
* B、尽管只有getThrust()方法被显示声明为公开，但RocketSim的三个方法都是公开方法。
* C、接口虽然被声明为“共有接口”，但即使省略public关键字，接口仍然是共有的。
* D、可以创建另外一个接口，例如RocketSimSoild，去扩展RocketSim接口。
* E、 每个接口必须包含一个方法。
* F、接口可以声明实例字段，实现该接口的方法也必须声明该字段。
* G、虽然不能实例化接口，但接口仍然可以定义构造函数，并要求实现类必须提供提供具有相同签名的构造函数。











摘自：java设计模式（第二版）（（美）梅茨克尔（Metsker，S.J.），（美）韦克（Wake，W.C.） 著）