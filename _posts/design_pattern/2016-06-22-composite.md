---
layout: post
title: 合成模式
categories: JAVA设计模式
description: 合成模式
keywords: composite,合成
---


# 合成模式

 **合成模式的意图是为了保证客户端调用单对象与组合对象的一致性。**

## 常规组合
   
![](/images/posts/design_pattern/composite_1.png)

上图展示了一个经典的组合结构。Leaf类和Composite类都实现自一个抽象Component通用接口，同时，Composite对象又包含了其他的Composite和Leaf的对象集合。图中的Component类是一个抽象类，他未包含一个具体的方法，因而可以将其定义为一个接口，让Leaf和Composite去实现它。

合成模式的关键在于组合对象可以包含其他组合对象（不仅仅是叶子对象），且Leaf和Composite节点共享一个共同的接口。

## 合成模式中的递归行为

![](/images/posts/design_pattern/composite_2.png)

如上图，单台机器和机器的组合都给出了getMachineCount()方法，他会返回给定组件中的机器数量。

对于Machine类，getMachineCount()的实现如下：

```java
public int getMachineCount() {
   return 1;
}
```
类图显示MachineComposite使用了一个List类，以跟踪它的组件，要计算合成对象中机器的数量，可以这样实现：

```java
public int getMachineCount() {
	int count = 0;
	for(int i=0;i<composite.size();i++) {
		MachineComponent mc = (MachineComponent)composite.get(i);
		count += mc.getMachineCount();
	}
	return count;
}
```

## 组合，树与环

我们这样定义一个对象模型为树

* 存在一个没有任何引用指向自身的节点
* 其他任何节点都只有一个指向它的父节点

![](/images/posts/design_pattern/composite_3.png)

如上图是一个树模型

![](/images/posts/design_pattern/composite_4.png)

如上图不是一个树模型，因为mixer有两个父节点。根据上图创建的对象模型：

```java
public MechineComposite plant() {
	MachineComposite plant = new MachineComposite();
	MachineComposite bay = new MachineComposite();
	Machine mixer = new Machine();
	Machine press = new Machine();
	Machine assembler = new Machine();
	bay.add(mixer);
	bay.add(press);
	bay.add(assembler);
	plant.add(mixer);
	plant.add(bay);
	return plant;
}
```
当我们调用上面生成plant的getMechineCount()方法，返回的是4，因为mixer被添加了两次，为了避免这种错误，可以在添加节点是检查该节点是否在树中存在。一个简单的实现是维护一个已存在的节点的集合。可以编写一个isTree()方法来检查组合对象是否为树形结构。
倘若在遍历对象模型之后，没有哪个节点被访问两次，我们就认为该对象模型是一棵树。

## 小结

合成模式包含两个互相关联的重要概念。一个概念是一个组合对象可以包含其他的单对象或者组合对象。与之相关的另一个概念是单对象和组合对象共享一个接口。将这些概念应用于对象建模上，就可以在组合对象或者单对象上创建抽象类或者Java接口，以此顶一个公共行为。

在对组合对象建模时，通常需要给组合节点引入递归定义。倘若存在递归定义，在编写代码时需要注意防止死循环。为避免这一问题，可以确保组合对象都是树形结构。此外，虽然允许环出现在合成模式中，但必须修改算法避免可能出现的死循环。


摘自：java设计模式（第二版）（（美）梅茨克尔（Metsker，S.J.），（美）韦克（Wake，W.C.） 著）

其他的参考文章  [http://www.cnblogs.com/java-my-life/archive/2012/04/17/2453861.html](http://www.cnblogs.com/java-my-life/archive/2012/04/17/2453861.html)

## 代码

合成模式可以使客户端将单纯元素与复合元素同等看待。如下面的例子，他们都有共同的方法，getPeopleNum()。

首先定义一个接口，这是单对象和组合对象共有的方法：

```java
public interface DeptComponent {
	public int getPeopleNum();
}
```

单对象：

```java
public class Dept implements DeptComponent{
	private int peopleNums;
	public Dept(String name,int peopleNums){
		this.peopleNums = peopleNums;
	}
	
	@Override
	public int getPeopleNum() {
		return peopleNums;
	}

}
```

组合对象

```java
public class DeptComposite implements DeptComponent{
	
	private List<DeptComponent> depts;
	
	public DeptComposite(String name) {
		depts = new ArrayList<DeptComponent>();
	}
	
	public void addDept(DeptComponent dept) {
		depts.add(dept);
	}
	
	public void removeDept(int index) {
		depts.remove(index);
	}

	@Override
	public int getPeopleNum() {
		int peopleNums = 0;
		for(DeptComponent dept : depts) {
			 peopleNums +=dept.getPeopleNum();
		}
		return peopleNums;
	}

}
```

一个公司类，可以通过方法获取公司的人数

```java
public class Company {
	public static void main(String[] args) {
		DeptComposite compony = new DeptComposite("公司");
		DeptComposite deptDevelop = new DeptComposite("开发部");
		Dept deptPersonal = new Dept("人事部",5);
		Dept deptApp = new Dept("APP开发部",6);
		Dept deptBack = new Dept("后台开发",10);
		
		compony.addDept(deptDevelop);
		compony.addDept(deptPersonal);
		deptDevelop.addDept(deptApp);
		deptDevelop.addDept(deptBack);
		
		System.out.println(compony.getPeopleNum());
		
	}
}
```

