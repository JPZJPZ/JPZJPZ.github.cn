---
layout: post
title: 职责链模式
categories: JAVA设计模式
description: 职责链模式
keywords: responsibility,职责链
---

# 职责链模式

   **职责链模式的目的是通过给予多个对象的处理请求的机会，以解除请求的发送者与接收者之间的耦合。**
   
## 现实中的职责链模式

当一个人负责某项任务时，可以选择自己做或是让别人做，这就是现实中的职责链模式。

举个例子，一个工厂有很多机器，其中一些复杂的机器可能由某些工程师负责，一些简单的机器没有直接负责的工程师，此时，就会由负责机器所在车间的工程师来管理。

当客户对象查询负责机器的工程师时，希望能避免查询多个对象。因此，可以使用职责链模式，为每个机器组件分配责任人对象。下图说明了这种设计思路。

![](/images/posts/design_pattern/chain-of-responsibilty_1.png)

每个Machine或MachineComposite对象都有父对象和责任人对象，他们均继承自MachineComponent类。

如果某台机器没有分配直接负责的工程师，可以把查询负责人的请求发送给父节点。一般而言，机器的父节点是生产线，生产线的父节点死车间，车间的父节点是工厂。在公司中，这个职责链的某处总有一位负责人。

这种设计思路的优点是机器组件的客户（代码）无须了解负责的工程师是如何分配的，客户（代码）可以查询任何机器组件的负责人。机器组件把客户（代码）与职责的分配标准相互独立。当然，这种设计也存在如下几点不合理的地方：

* 我们没有指定如何设置职责链，让机器可以知道其父节点。实际上，很难确保父节点永远都不为空。

* 可以想象的是，对父对象的搜索可能会陷入到无限循环中，这取决于父对象是如何建立的。

* 并非所有对象都需要提供这些新方法所表示的行为。（例如，最顶端的元素就没有父对象。）

* 当前设计局限于细节，关心系统如何知道是哪些工程师正在工厂，并是否可用。改设计并不清楚完成责任需要的”实际时间“。

## 重构为职责链模式

考虑对工具和工具车的建模。工具不属于MachineComponent类层次，但是在某些地方与机器类似。特别的，工具始终分配给工具车，并且工具车会包含一位负责的工程师。假设某可视化程序可以显示特定车间的所有工具和机器，并且提供弹出式信息以显示特定项的责任人。下图显示了查询指定设备的责任人时所涉及的类：

![](/images/posts/design_pattern/chain-of-responsibilty_2.png)

VisualizationItem接口具体指定了类需要的部分行为，以便于实现可视化，同时没有定义getResponsible()方法。事实上，并不是visualizationItem的所有项都知道具体的负责人。当可视化程序需要决定哪个工程师对特定设备负责时，取决于所选的那个设备项。机器、机器组合工具车都具有getResponsible()方法，而工具没有。为确定工具的责任工程师，程序代码必须查看这个工具属于哪个工具车，然后查看谁负责这个工具车。为确定指定设备项的负责人，应用程序的菜单代码使用了一些if语句和类型判断。这些特性表明重构可能有助于改善代码，具体代码如下：

```java
public class AmbitiousMenu {
	public Engineer getResponsible(VisualizationItem item) {
		if(item instanceof Tool) {
			Tool t = (Tool) item;
			return t.getToolCart().getResponsible();
		}
		if(item instanceof ToolCart) {
			ToolCart tc = (ToolCart)item;
			return tc.getResponsible();
		}
		if(item instanceof MachineComponent) {
			MachineComponent c = (MachineComponent) item;
			if(c.getResponsible() != null) {
				return c.getResponsible();
			}
			if(c.getParent() != null) {
				return c.getParent().getResponsible();
			}
		}
		return null;
	}
}
```

职责链模式的意图在于减轻调用者的压力，使它们无须了解哪个对象可以处理调用请求。在本例中，菜单是个调用者，它要求找到对应的负责人。根据当前设计，调用者必须了解哪个设备项具有getReponsible()方法。借助于职责链模式为所有模拟项提供责任人，我们可以借此来简化代码。这样可以将了解哪些对象知道其负责人的相关职责从菜单代码中转移到模拟项。下面将getReponsible()方法移入visualizationItem接口，并将该方法加入Tool类。

![](/images/posts/design_pattern/chain-of-responsibilty_3.png)

在这个设计中，任何模拟对象的客户端都可以通过该模拟对象获知责任工程师的信息。这种方式可以让客户不必了解那个对象的职责，从而将负担转嫁给实现了visualizationItem接口的对象。

现在，菜单代码变得更加简单，可以直接通过调用visualizationItem对象的方法来查找负责的工程师，具体如下：

```java
public class AmbitiousMenu2 {
	public Engineer getResponsible(VisualizationItem item) {
		return item.getResponsible();
	}
}
```

每个设备项的getResponsible()方法也会变得更加容易实现。

MachineComponent对象拥有一个显示分配的责任人。如果没有，就将请求转交给它的父对象。

```java
public Engineer getResponsible() {
	if(responsible != null) 
		return responsible;
	if(parent != null) 
		return parent.getResponsible();
	return null;
}
```

Tool.Responsible类的代码反映了“工具总是指派给对应的工具车”原则。

```java
public Engineer getResponsible() {
	return toolCart.getResponsible();
}
```

ToolCart类的代码反映了“工具车都有一名责任工程师”原则。

```java
public Engineer getResponsible() {
	return responsible;
}
```

## 固定职责链

在为MachineComponent类编写getResponsible()方法时，必须考虑到它的父对象可能为空。一种解决方法是，让每个MachineComponent对象都有一个非空父对象。为了实现这一目的，可以为MachineComponent类的构造函数添加一个参数来提供父对象（如果父对象为空，甚至可以抛出一个异常，以便知道异常被捕获的位置）。此外，还要考虑根部的对象————该对象没有父对象。一种合理的做法是创建一个MachineRoot类，并让该类继承MachineComposite类（而不是MachineComponent）。为保证每个MachineComponent对象都对应一名负责人，可以这样做：

* MachineRoot类的构造函数需要一个Engineer对象

* MachineComponent类的构造函数需要一个类型为MachineComponent的父对象。

* 只有MachineRoot使用null作为其父对象的值。

下图保证每个Machinecomponent对象都对应一名负责的工程师。

![](/images/posts/design_pattern/chain-of-responsibilty_4.png)

通过固定职责链，我们的对象模型变得更加健壮，大妈也更为简洁。现在可以实现MachineComponent类的getResponsible()方法了。

```java
public Engineer getResponsible() {
	if(responsible != null) 
		return responsible;
	return parent.getResponsible();
}
```

## 没有组合结构的职责链模式

职责链模式也可以用于不带组合结构的对象模型，例如：

* 一群接线工程师组成一个环状链条，他们遵守轮流提供客户服务的标准。如果当前的接线工程师在规定时间内无法回答与产品有关的为题，就通知系统进行切换，由下一个工程师提供帮助。

* 用户输入事件日期之类的信息，解析器组成一个链条对用户文本依次进行解码。

## 小结

在运用职责链模式时，客户端不必事先知道对象集合中哪个对象可以提供自己需要的服务。当客户端发出调用请求后，该请求会沿着职责链转发请求，知道找到提供服务的对象为止。这就可以降低客户端与提供服务的对象间的耦合度。

如果某个对象链能够应用一系列不同的策略来解决某个问题，如解析用户的输入，这时也可以应用职责链模式。该模式更常见于组合结构，它具有一个包容的层次结构，为对象链提供了一种自然的查询顺序。简化对象链和客户端的代码时职责链模式的一个主要优点。

摘自：java设计模式（第二版）（（美）梅茨克尔（Metsker，S.J.），（美）韦克（Wake，W.C.） 著）

