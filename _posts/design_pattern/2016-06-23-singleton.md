---
layout: post
title: 单例模式
categories: JAVA设计模式
description: 单例模式
keywords: singleton,单例
---
http://blog.csdn.net/u010425776/article/details/48242337

# 单例模式

   **单例模式的意图是为了一个类有且仅有一个实例，并为它提供一个全局访问点。**
   
设计一个单例模式时，需要确定何时实例化该类的单例对象。一种方法是创建这个类的实例，并将它作为该类的静态成员变量。

```java
public class Factory {
	private static Factory factory = new Factory();
	
	public static Factory getFactory() {
		return factory;
	}
} 
```

如果不希望提前创建单例实例，还可以在第一次需要该实例时，延迟初始化它。延迟实例化单例对象一般有两个原因：

   * 在静态初始化时，没有足够信息对单例对象进行初始化。例如，工厂单例就必须等待真正工厂的机器，才能建立通信通道。
   * 选择延迟初始化单例对象与获取资源有关，例如数据库连接，尤其是在一个特定绘画中，它包含的应用程序并不需要该单例对象时。
   
如果在一个多线程的环境下延迟初始化一个单例模型，必须避免多个线程同时初始化它，所以用一个同步锁来解决这个问题。

```java
public class Factory {
	private static Object classLock = Factory.class;
	private static Factory factory;
	
	private long wipMoves;
	
	private Factory() {
		wipMoves = 0;
	}
	
	public static Factory getFactory() {
		synchronized (classLock) {
			if(factory == null) 
				factory = new Factory();
			return factory;
		}
	}
	
	public void recordWipMove() {
		synchronized (classLock) {
			wipMoves++;
		}
	}
	
}
```

## 小结
单例模式保证了类只有一个实例，并为其提供了一个全局访问点。通过延迟初始化（仅在第一次使用它时才初始化），一个单例对象是达到此目的的通用做法。在多线程下，必须小心管理线程间的协作，因为他们访问单例对象的方法和数据的时间，可能只有毫厘之差。

对象具有唯一入口，并不意味着使用了单例模式。单例模式通过隐藏构造函数，提供对象创建的唯一入口，从而将类的职责集中在类的单个实例中。
 






摘自：java设计模式（第二版）（（美）梅茨克尔（Metsker，S.J.），（美）韦克（Wake，W.C.） 著）

