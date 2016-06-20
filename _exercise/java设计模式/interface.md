---
layout: exercise
title: 接口型模式
categories: JAVA设计模式
description: 接口型模式
keywords: 接口
---


# 接口型模式


### 练习
**1、写出在java中抽象类于接口的不同**

答案：

* 一个类可以继承多个接口；但只能继承一个抽象类
* 抽象类可以包含具体实现方法；接口的所有方法都是抽象的
* 抽象类可以声明和使用字段；接口则不能，但可以创建静态的final常量
* 抽象类中的方法可以是public、protected、private或者默认的package；接口的方法都是ipublic的
* 抽象类可以定义构造函数；接口则不能。

**2、下面哪些说法是正确的的？**()

```java
com.oozinoz.simulation

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

答案：

* A.正确。接口方法总是抽象的，不管你是否对此声明。
* B.正确。接口方法总是公开的，不管你是否对此声明。
* C.错误。接口的所见性会被限制在它所在的包当中。对于本例，接口应该被标记为public，这样，com.oozinoz.simulation包外的类才能访问它。
* D.正确。例如List和Set都继承自java.util接口Collection接口。
* E.错误。没有方法的接口称为标记接口（maker interface）。有时候，一个方法处于类继承层次的高处，例如object.clone()，但它不适用于所有子类。如果希望子类在此样式下能够有选择的实现，就可以创建一个标记接口。通过声明对Clone标记接口的实现，就能够要求子类也实现Object的Clone()方法。
* F.错误。接口虽然能够声明静态的final字段作为常量，但不能声明实例字段。
* G.错误。虽然是个不错的注意，但Java接口却不能这样做，这会使得实现类必须提供一个特定的构造函数。




摘自：java设计模式（第二版）（（美）梅茨克尔（Metsker，S.J.），（美）韦克（Wake，W.C.） 著）