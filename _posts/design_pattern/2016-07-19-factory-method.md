---
layout: post
title: 工厂方法模式
categories: JAVA设计模式
description: 工厂方法模式
keywords: 工厂,factory
---

# 工厂方法模式

**工厂方法模式的意图是定义一个用于创建对象的接口，并控制返回哪个类的实例**
   
## 经典范例：迭代器

迭代器实例经常运用工厂模式来创建。Java JDK1.2引进了一个容器类接口，其中包含了一个iterator()方法，所有容器类均实现了该方法，它使得调用者并不知道iterator对象是有哪个类实例化的。

```java
public static void main(String[] args) {
	List list = Arrays.asList(new String[]{"fountain","rocket","sparkler"});
	Iterator iter = list.iterator();
	while(iter.hasNext()) 
		System.out.prinln(iter.next());
}
```

## 识别工厂方法

什么是工厂方法模式？

并非每个能创建并返回一个新对象的方法，都是工厂方法模式的实例。工厂方法模式不仅要求有一个能够创建新对象的方法，还要让客户代码无须了解具体实例化的类。工厂方法模式通常包含了若干类，这些类实现了相同的操作，反回了相同的抽象类型，然而这些操作的内部，实际上却实例化了不同的类，并且，这些类都实现了上述抽象类型。当客户代码请求一个新对象时，这个新对象该由哪个类实例化，取决于工厂对象接收创建请求时的行为。

## 控制要实例化的类

假设你开发了一个信贷系统，其中申请方式有线上和线下两种，当线上没人在线后使用线下系统，提交一个身份证后作出信贷决定。

![](/images/posts/design_pattern/factory-method_1.png)

现在的问题是，这些类的使用者要知道究竟实例化了哪个类。然而，事实上却只有你知道是否有人在在线。

在这种情况下，需要把创建对象的工作转交给一个接口，来完成实例化的控制。一种解决方案是让这两个类同时实现一个标准接口，并创建一个工厂方法来返回该接口的某个实例。具体做法如下：

   * 创建一个名为creditCheck的接口，使其包含creditLimit()方法。
    
   * 使所有信贷核对类声明实现CreditCheck接口。
   
   * 创建一个名为CreditCheckFactory的类，此类包含一个名为createCreditCheck()的方法，并返回一个类型为CreditCheck的对象。
   
![](/images/posts/design_pattern/factory-method_2.png)

通过应用工厂方法模式，使用此服务的用户可以通过调用createCreditCheck()方法获得一个信贷核对的对象，这样不管信贷代理机构是否在线都可以满足顾客的服务请求。

```java
public static CreditCheck createCreditCheck() {
	if(isAgencyUp())  //是否在线
		return new CreditCheckOnline();
	return new CreditCheckOffline();
}
```

## 并行层次结构中的工厂方法模式

在使用并行层次结构对问题域进行建模时，常常会使用工厂方法模式。一个并行层次结构是一对类层次结构。其中，一个类在一个层次，与其相关的类在另一层次。当将现有类层次结构的部分行为移出该层次后，并行层次结构就会显露出来。

下图展示了制造焰火弹的各种机器：

![](/images/posts/design_pattern/factory-method_3.png)

假设你想用getAvailable()方法来预测机器完成加工的时间，从而决定是否可以执行接下来的工作。该方法需要某些私有方法的支持，总体而言，就是为每个机器类添加一定数量的逻辑。但是这些规划逻辑不应该添加到Machine的类层次结构中，更好的方式是独立出MachinePlanner层次结构。对于大多数机器类型而言，需要一个独立的计划类。

![](/images/posts/design_pattern/factory-method_4.png)

通过将规划逻辑一直到并行层次结构中，可以缩减Machine类的层次结构

Machine类的creatPlanner():

```java
public MachinePlanner creatPlanner() {
	return new BasicPlanner(this);
}
```

StartPress类的creatPlaner()方法：

```java
public MachinePlanner creatPlanner() {
	return new StartPressPlanner(this);
}
```

## 小结

工厂方法模式的意图是让服务的提供者确定实例化哪个类，而不是客户代码。这种模式在Java类库中极为常见，比如Collection接口的iterator()方法。

当你不想让客户代码决定实例化哪个类时，常常可以运用工厂方法模式。此外，工厂方法模式还可用于当客户代码不知道他需要创建哪个对象类的时候，例如客户代码无法获知信贷机构是否在线的情况。另外，在并行类层次结构中使用该模式可以避免类的规模过于庞大。工厂方法模式可以根据一个类层次中的子类，确定另一个相关层次中哪一个类被实例化，从而建立对应的并行层次结构。



摘自：java设计模式（第二版）（（美）梅茨克尔（Metsker，S.J.），（美）韦克（Wake，W.C.） 著）

