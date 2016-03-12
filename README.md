# android 架构师 （Android Architect Teaching）#

## 01 - 复习基本OOP知识a ##

### 1.面向对象是什么 ###

	面向对象中包含：类、对象

	类中有：类的属性和行为，是软件的模子，在项目中利用这个模子来创建对象。例如：Rose rose = new Rose();，这样，电脑中就存在一个Rose的对象了

## 02 - 复习基本OOP知识b ##

### 2、基类\子类结构用途（一）：表达继承（基本） ###

	对众多对象加以分门别类，就可以形成一个类继承体系。
	
	例如：class Teacher extends PersonP{}

	extends 扩充

	在子类中调用super.方法，代表调用基类的方法

在安卓中的继承例子：

<center>![](1androidInheritanceSystem.png)</center>

<center>![](2androidInheritanceSystem.png)</center>


Android另外一个例子（其例子说白了就是新函数调用旧函数）：

<center>![](3androidInheritanceSystem.png)</center>


由此看出，基类和子类构成了我们的app

### 3、基类\子类机构用途(二)：表达组合（难） ###

例子：如何让Thread与Tasks组合起来：以创建小线程为例

	这个例子就好比轮胎和引擎：引擎要让轮胎运作起来，就需要一个接口，好让轮胎调用引擎的能力，让轮胎运行起来，因此轮胎就好比Tasks，而Thread就好比引擎，也就是说使用Tasks去implements Runnable的接口

Java提供了一个Thread基类和一个Runnable接口；这两个元素就构成一个框架

<center>![](4androidInheritanceSystem.png)</center>

然后Tasks实现Runnable接口：

<center>![](5androidInheritanceSystem.png)</center>

	于此图里，框架的Thread基类会先诞生一个小线程，然后该小线程透过Runnable接口，呼叫（或者执行）了Tasks类别的run()函数

<center>![](6androidInheritanceSystem.png)</center>

	由以上几个环节总结出：首先就是说Thread里定义了一个接口Runnable，而Tasks只要遵循Thread给的接口Runnable去实现它，那么Thread的对象就可以和Tasks搭配起来去使用了。（重点）


而以下这种结构是继承的结构，而不是表达组合的结构

<center>![](8androidInheritanceSystem.png)</center>

<center>![](9androidInheritanceSystem.png)</center>

## 03 - 复习基本OOP知识c ##

### 4、基类\子类结构的接口（卡榫函数） ###

> 基类相当于桌子的桌面、子类相当于桌子的脚，而桌面和脚需要一个接口拼在一起，这节课就是说明接口是怎么回事。

接口：另一个说法叫卡榫，而接口中的函数，我们称为卡榫函数（Hook Function），也是把父类和子类拼接起来的函数

卡榫函数：

	所谓【卡榫（Hook）】，就是用来接合两个东西的接口。如果两个东西于不同时间出现，则一方会预留虚空，给予另一边于未来时刻能以实体来填补该空间，两者虚实相依，就密合起来了。设计优良的卡榫，可以让实体易于新陈代谢、抽换自如（Plug and Play，俗称PnP）


> Template Method设计模式【GoF】

<center>![](10androidInheritanceSystem.png)</center>


基类与子类到底如何分类：

* 变与不变的分离（Separate code that changes from the code that doesn't）是设计卡榫（Hook）函数及应用框架之基本原则和手艺。
* 分离出变（Variant）与不变（Invariant）部分之后，就可以将不变部分写在父类别（Super-class）里，而变的部分就写在子类别（Subclass）里。


卡榫函数的Java实现

* 在Java里，使用抽象（abstract）函数或可覆写（overridable）函数来实现卡榫函数

		interface IShape{
			void template_paint(Graphics gr);
		}//一般接口
		
		//Shape.java
		import java.awt.*;
		public abstract class Shape implements IShape{
			public void templae_paint(Graphics gr){
				invariant_paint(gr);//画背景
				hook_paint(gr);//画前景
			}
		
			private void invariant_paint(Graphics gr ){
				gr.setColor(Color.black);
				gr.fillRect(10,30,200,100);
			}//默认行为
		
			//hook function
			abstract protected void hook_paint(Graphics gr);
		}
		
		//Bird.java
		public class Bird extends Shape{
			private void hook_paint(Graphics gr){
				//画图（海鸥）指令
				gr.setColor(Color.cyan);
				gr.drawArc(30,80,80,110,40,100);
				gr.drawArc(88,93,80,100,40,80);
				gr.setColor(Color.white);
				gr.drawArc(30,55,80,150,35,75);
				gr.drawArc(90,80,80,90,40,80);
			}
		}

<center>![](11androidInheritanceSystem.png)</center>

由此看出，背景是有基类来实现，而前景是由子类来实现。

### 5、IoC机制与Default函数 ###

卡榫函数实现IoC机制

* 控制反转（IoC：Inversion of Control）：基类控制子类！！！
* IoC机制源于OO语言（如C++等）的类别继承体系，例如C++语言中，基类的函数可以主动调用子类的函数，这就是典型的IoC机制。
* 基类与子类之间，主控权实在基类手上，透过Hook函数来调用子类。
* 通常基类是撰写在先，而子类则撰写在后，这种前辈拥有主导权，进而【控制】后背之情形，就通称为【控制反转】。

默认（Default行为）

* 基类的重要功能：提供默认（预设）行为，也称为Hook Function。
* 基类可事先定义许多【默认】（Default）函数。这些默认函数可让子类来继承（或调用）之。
