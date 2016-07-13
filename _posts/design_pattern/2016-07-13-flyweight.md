---
layout: post
title: 享元模式
categories: JAVA设计模式
description: 享元模式
keywords: flyweight,享元
---

# 享元模式

   **享元模式的意图是通过共享来有效地支持最大量细粒度的对象**
   
## 不变性

享元模式让多个客户对象间共享访问限定数量的对象。为了实现这个目的，你必须要考虑到当某个对象改变了该共享对象的状态时，该状态变化会影响到每个访问它的对象。当多个客户对象要共享访问某个对象时，若要保证对象间不会相互影响，一种最简单而又常用的做法是，限制客户对象调用任何可能改变共享对象的方法。你可以通过将对象设置为不变类型来达到这一目的。然而，这样做会导致对象在创建后无法改变。

若存在大量相似对象，而你可能需要共享访问它们，但它们可能并非一成不变。在这种情况下，使用享元模式的第一步是将对象中不可变的部分抽取出来，共享它们。

## 抽取享元中不可变的部分

Substance类是一个化学药品类，其中包含四个变量name（名字）、symbol（分子式）、atomicWeight（相对分子质量）、grams（摩尔质量）和5个方法。

![](/images/posts/design_pattern/flyweight_1.png)

一个Substance对象代表特定的摩尔数。我们使用Mixture类来建模化学药品的组成成分。例如下图展示了黑火药的对象图，它包含了硝酸钾、硫磺以及碳粉：

![](/images/posts/design_pattern/flyweight_2.png)

假设化学药品越来越多，因而决定使用享元模式来减少程序中Substance对象的数量。为了将Substance对象变成享元模式，首先需要做的事情就是将勒种不变的部分与变化的部分分离出来。现在重构Substance类，将不变部分抽取到chemical类中：

![](/images/posts/design_pattern/flyweight_3.png)

## 共享享元

指出对象中不变的部分仅仅只是完成了享元模式的一半，另一半包括创建享元工厂、实例化享元，以及让客户对象共享享元对象。我们还需要确保客户对象应使用享元工厂创建享元对象，而不是自己创建。

为了创建化学药品的享元对象，需要定义一个ChemicalFactry类作为享元工厂。该类包含一个静态方法，负责接收一个名称，返回对应的化学药品。你可以将化学药品存在一个哈希表中，在初始化工厂的时候创建它们。下图展示了ChemicalFactory类的设计：

![](/images/posts/design_pattern/flyweight_4.png)

```java
public class ChemicalFactory{
	private static Map chemicals = new HashMap();
	
	static {
		chemicals.put("carbon", new Chemical("Carbon","C",12));
		chemicals.put("sulfur", new Chemical("sulfur","S",32));
		chemicals.put("saltpeter", new Chemical("Saltpeter","KNO3",101));
		//...
	}
	
	public static Chemical getChemical(String name) {
		return (Chemical)chemicals.get(name.toLowerCase())
	}
}
```

创建完化学药品工厂后，需要执行一些步骤确保让开发人员使用工厂创建享元对象，而不是直接创建Chemical对象。一种简单的方式是借助Chemical类的可见性。

为了确保ChemicalFactory是唯一一个创建Chemical实例的类，需要把Chemical类定义为ChemicalFactory的内部类。要访问内嵌的类型，客户对象需要使用如下代码：

```java
ChemicalFactory.Chemical c = ChemicalFactory.getChemical("saltpeter");
```

可以通过将Chemical定义为接口，内嵌类定义为该接口的实现，从而简化对内嵌类型的使用。Chemical接口可以定义如下三个访问方法：

```java
public interface Chemical {
	String getName();
	String getSymbol();
	String getAtomicWeight();
}
```

对象永远不会引用该内部类，因此，可以把它定义成私有类型，以确保只有个ChemicalFactory2类才能访问它。

```java
public class ChemicalFactory2{
	private static Map chemicals = new HashMap();
	
	class ChemicalImpl implements Chemical {
		private String name;
		private String symbol;
		private double atomicWeight;
		
		ChemicalImpl(String name,String symbol,double atomicWeight) {
			this.name = name;
			this.symbol = symbol;
			this.atomicWeight = atomicWeight;
		}
		
		public String getName() {
			return name;
		}
		
		public String getSymbol() {
			return symbol;
		}
		
		public double getAtomicalWeight() {
			return atomicWeight;
		}
	}
	
	static {
		chemicals.put("carbon", new Chemical("Carbon","C",12));
		chemicals.put("sulfur", new Chemical("sulfur","S",32));
		chemicals.put("saltpeter", new Chemical("Saltpeter","KNO3",101));
		//...
	}
	
	public static Chemical getChemical(String name) {
		return (Chemical)chemicals.get(name.toLowerCase())
	}
}
```

这段代码解决了如下三个问题：

   * ChemicalImpl嵌套类应该是私有的，这样就只有ChemicalFactory2类才能使用该类。注意,嵌套类的访问范围必须是包级别或共有的，这样包含的类才可以实例化嵌套类。即使将构造函数定义为共有的，如果嵌套类本身被标记为私有，就没有其他类可以调用该构造函数。
   
   * ChemicalFactory2的构造函数使用了一个静态的初始化器，以确保类只会构造一次化学药品表。
   
   * getChemical()方法可以在累的哈希表中根据名称查找化学药品。示例代码使用了小写的化学品来存储与查询化学品。
   
## 小结

享元模式可以使你共享地访问那些大量出现的细粒度对象，例如字符、化学药品以及边界等。享元对象必须是不可变的，可以将那些需要共享访问，并且不变的部分提取出来。为了确保你的享元对象能够被共享，需要提供并强制客户对象使用享元工厂来查找享元对象。访问修饰符对其他开发者进行了一定的限制，但是内部类的使用限制更进一步，完全限制了该类仅能由其玩不容器访问。在确保客户对象正确地使用了享元工厂后，你就可以提供大量细粒度对象的安全共享访问了。


摘自：java设计模式（第二版）（（美）梅茨克尔（Metsker，S.J.），（美）韦克（Wake，W.C.） 著）

