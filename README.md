# android 架构师 （Android Architect Teaching）#

> 高焕堂（台湾）

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

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/1androidInheritanceSystem.png)</center>

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/2androidInheritanceSystem.png)</center>


Android另外一个例子（其例子说白了就是新函数调用旧函数）：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/3androidInheritanceSystem.png)</center>


由此看出，基类和子类构成了我们的app

### 3、基类\子类机构用途(二)：表达组合（难） ###

例子：如何让Thread与Tasks组合起来：以创建小线程为例

	这个例子就好比轮胎和引擎：引擎要让轮胎运作起来，就需要一个接口，好让轮胎调用引擎的能力，让轮胎运行起来，因此轮胎就好比Tasks，而Thread就好比引擎，也就是说使用Tasks去implements Runnable的接口

Java提供了一个Thread基类和一个Runnable接口；这两个元素就构成一个框架

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/4androidInheritanceSystem.png)</center>

然后Tasks实现Runnable接口：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/5androidInheritanceSystem.png)</center>

	于此图里，框架的Thread基类会先诞生一个小线程，然后该小线程透过Runnable接口，呼叫（或者执行）了Tasks类别的run()函数

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/6androidInheritanceSystem.png)</center>

	由以上几个环节总结出：首先就是说Thread里定义了一个接口Runnable，而Tasks只要遵循Thread给的接口Runnable去实现它，那么Thread的对象就可以和Tasks搭配起来去使用了。（重点）


而以下这种结构是继承的结构，而不是表达组合的结构

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/8androidInheritanceSystem.png)</center>

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/9androidInheritanceSystem.png)</center>

## 03 - 复习基本OOP知识c ##

### 4、基类\子类结构的接口（卡榫函数） ###

> 基类相当于桌子的桌面、子类相当于桌子的脚，而桌面和脚需要一个接口拼在一起，这节课就是说明接口是怎么回事。

接口：另一个说法叫卡榫，而接口中的函数，我们称为卡榫函数（Hook Function），也是把父类和子类拼接起来的函数

卡榫函数：

	所谓【卡榫（Hook）】，就是用来接合两个东西的接口。如果两个东西于不同时间出现，则一方会预留虚空，给予另一边于未来时刻能以实体来填补该空间，两者虚实相依，就密合起来了。设计优良的卡榫，可以让实体易于新陈代谢、抽换自如（Plug and Play，俗称PnP）


> Template Method设计模式【GoF】

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/10androidInheritanceSystem.png)</center>


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

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/11androidInheritanceSystem.png)</center>

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


## 04 - 复习基本OOP知识d ##

### 6、主动型 vs 被动型API ###

卡榫函数实现API：我们也称为主动型API

被动型API：子类调用父类的方法，即正向调用，称之为被动型API。

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/12androidInheritanceSystem.png)</center>

API的分类：

* API这个名词，有3个密切关联的动词：

	定义（Define）
	实作（Implement）
	呼叫（Invoke or Call）

* 根据这3个角度，可将API区分为【主动型】与【被动型】两种

	被动型API：子类的函数调用基类的函数

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/13androidInheritanceSystem.png)</center>

	主动型API： 相反，基类调用子类实现的接口，控制反转

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/14androidInheritanceSystem.png)</center>

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/15androidInheritanceSystem.png)</center>


API===>控制力：

* 大家都知道。接口（Interface）是双方接口的地方，也是双方实力或地盘的界线。
* 谁拥有接口的定制权，谁就掌握控制点，就能获得较大的主动权（或称为主导权），而位于强龙地位；而另一方则处于被动地位，成为弱势的一方，扮演地头蛇角色。


API的范例（Android）

* 范例1：接口是谷歌定义：onCreate（卡榫函数）

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/16androidInheritanceSystem.png)</center>


	谷歌是强龙，Activity的子类是地头蛇，其实事实上是基类Activity调用子类，也就是主动API


* 范例2：


<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/17androidInheritanceSystem.png)</center>


* 范例说明：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/18androidInheritanceSystem.png)</center>


### 7、结语与复习：接口与类 ###

接口的表示：

* 在OOP里，将接口定义为一种特别的类别（Class）
* 在C++里，类别包括3种：


	1、一般（具象）类别
		所有函数都是具象（内有指令）

	2、抽象（abstract）类别
		有一个或多个函数是抽象的（内无指令）

	3、纯粹抽象（pure abstract）类别
		所有函数都是抽象的。我们也称为接口

* 在Java里，将上述的纯粹抽象类别称为接口（Interface）

* 在UML里，以圆圈来表示接口

## 05 - 架构设计的UML图形思考a ##

> 假如一个产品，需要十个月的开发时间，那么，架构师在第一个月内就要把产品的蓝图给设计出来，让之后的9个月内，开发者可以都围绕这个蓝图来开发，这就是架构设计。

### 1.建模与图形思考 ###

* 架构师（Architect）的职责就是创意设计与人际沟通。在规划架构或框架的阶段，还没开始动工撰写Android程序码，那么架构师如何进行创意思考呢？又如何将创意设计表达出来，争取自己公司老板和业主的支持（例如投资）呢？
* 身为架构师，其图形绘制和思考能力越好，其创意设计与人际沟通的能力越好，因此，培养Android架构师的图形思考能力是极为重要的。



### 2.UML软件图形语言和工具 ###

> 图形思考的表达：图形语言

* 图形建模：模型（Model）内含一组基本概念，及其之间关系；如果以图形表示出来，就能发挥图形思考的效益了。
* UML图形语言，协助架构师发挥其图形思考，表达出系统架构（Architecture）的模型。
* 由于人们对这些图形元素有了共同的认知，所以系统架构之描述（即模型）也就成为人与人之间可以认知和理解的东西。因而，人与人之间采用共同的（图形）模型时，就易于沟通，易于互相合作了。

> 软件：UML是一种图形语言

* 模型的建立，可以协助软件人员进一步了解系统。
* UML是一个很好的图形语言，也有很多UML建模工具，对于Android架构师或者开发者来说，都是非常重要的。无论是培养图形思考或者团队沟通上，对于Android软件出创意或管理上，是无可取代的。


红路灯控制软件的UML建模范例


<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/19androidInheritanceSystem.png)</center>

五子棋游戏软件的UML建模范例

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/20androidInheritanceSystem.png)</center>

> UML的建模工具

* Astah Professional（原名JUDE）是UML建模工具。以下是该工具支持的UML图形

	* Class Diagram （类别图）
	* Use Case Diagram（用例图）
	* Statemachine Diagram*（状态机图）
	* Sequence Diagram（顺序图）
	* Activity Diagram（活动图）
	* Communication Diagram（通信图）
	* Component Diagram（模块图）
	* Deployment Diagram（部署图）
	* Composite Structure Diagram（组合结构图）


## 06 - 架构设计的UML图形思考b ##

### 3.绘制UML类别图：表达（基类\子类） ###

* 启动了Astah，在主画面上点选<Diagram>，出现，

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/21androidInheritanceSystem.png)</center>

* 接着，点选<Class D iagram> ，就会出现一张空白的类别图，如下：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/22androidInheritanceSystem.png)</center>

* 在空白的类别图上方，有一排类别图的元素（Element），简称[图素]，如下：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/23androidInheritanceSystem.png)</center>

* 此列元素中的左边第二个就是【类别】（Class）图素。当你点选此图素（如上图所示），然后移动鼠标（Cursor）到图表里的特定位置，并按下鼠标左键，就在图表里出现一个类别图素，如下：


<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/24androidInheritanceSystem.png)</center>


> 画出基类的图形

* 这就是在Android程序里，大家都熟悉的Activity基类，例如：大家都熟悉的Android代码片段：

		public class myActivity extends Activity{
			//...
			//....
		}

* 这让你输入类名的名称，例如取名为：Activity类别，如下：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/25androidInheritanceSystem.png)</center>

* 这样的图文对照，能有效培养架构师的图形思考和创意，提升架构师与项目经理、业主的沟通能力。在图形上，也能增加美感，培养架构师对软硬件的感觉（Feeling）而不是只能逻辑的理解（Understanding）。有助于与设计师进行创意交流。
* 例如，点选这个Activity类别图素，并按右键；出现如下：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/26androidInheritanceSystem.png)</center>

* 于是，可以选择<Set Color> 选项来改变图素的颜色（Color）。此时，出现如下：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/27androidInheritanceSystem.png)</center>

* 就能任选所喜欢的颜色了，例如，选取粉色颜色，如下：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/28androidInheritanceSystem.png)</center>

> 画出子类的图形

* 这就是在Android程序里，大家都熟悉的Activity的子类（Subclass）。例如，大家很熟悉Android代码片段。

		public class myActivity extends Activity{
			//...
			//...
		}

* 在空白的类别图上方，有一排类别图的元素（Element），简称【图素】，如下：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/29androidInheritanceSystem.png)</center>

* 依据同样的过程，可以再拉出一个myActivity类别的图素，如下：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/30androidInheritanceSystem.png)</center>

> 画出基类\子类之间的<扩充>关系图形。

* 就建立了两个类别之间的<Generation> 关系，这又称为【继承】（Inheritance）关系，或称为【扩充】（Extend）关系。如果再对照到大家所熟悉的代码：

		public class myActivity extends Activity{
			//.....
			//.....
		}

* 接着，可以在图形上表达两个类别之间的关系（Relationship）。例如，选取<Generation>图素，如下：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/31androidInheritanceSystem.png)</center>

* 请点选了这个图素，接着将鼠标移动到myActivity类别图素，按住并拖拉到Activity类别图素才放开，就出现： 

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/32androidInheritanceSystem.png)</center>

* 上图就表达了这段代码里，Activity与myActivity之间的扩充关系了。
* 由于Activity是由Google团队所撰写的，属于框架（Framework）的一部分；而myActivity则是由一般应用（App）开发者所撰写的。

## 07 - 架构设计的UML图形思考c ##

### 4.绘制UML类别图：表达接口（Interface） ###

> 接口的表达

* 对于架构师而言，【接口】（Interface）的角色比【类别】（Class）来得重要的多，
* 例如，对照到大家所熟悉的代码：

		public class myActivity extends Activity implements OnClickListener{
			//.,,,,,
			//,,,,,
		}

* 于是，就来看看如何将接口图素呈现类别图上。例如，选取<Interface>图素，如下：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/33androidInheritanceSystem.png)</center>

* 点选了这个<Interface>图素，接着将鼠标移动到类别图里的任何位置，并按键，出现如下：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/34androidInheritanceSystem.png)</center>

* 就可以填入接口的名称了，例如：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/35androidInheritanceSystem.png)</center>

> 接口的注释

* 可选取<Note>图素，如下：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/36androidInheritanceSystem.png)</center>

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/37androidInheritanceSystem.png)</center>

* 接着，可选取<NoteAnchor>图素，如下：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/38androidInheritanceSystem.png)</center>

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/39androidInheritanceSystem.png)</center>

> 类<实现>接口的表达

* 对照到大家所熟悉的代码：

		public class myActivity extends Activity implements OnClickListener{
			//.,,,,,
			//,,,,,
		}

* 接着，可以在图形上表达类别与接口之间的关系，例如，选取<Association>图素，如下：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/40androidInheritanceSystem.png)</center>

* 先点选这个图素，从myActivity拉出一条线到OnClickListener接口，出现


<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/41androidInheritanceSystem.png)</center>

* 此关系说明了：myActivity类别【实现】了OnClickListener接口。其意味着，myActivity类别里含有一个实现函数：onClick（）函数，如下图：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/42androidInheritanceSystem.png)</center>


> 接口的使用（调用接口的函数）

* myActivity实现了onClickListener接口之后，其他类别就能透过此接口来调用myActivity里的onClick（）函数。

范例（Android）：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/43androidInheritanceSystem.png)</center>

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/44androidInheritanceSystem.png)</center>

* 接着，可选取<Association>图素，如下：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/45androidInheritanceSystem.png)</center>

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/46androidInheritanceSystem.png)</center>

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/47androidInheritanceSystem.png)</center>

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/48androidInheritanceSystem.png)</center>

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/49androidInheritanceSystem.png)</center>

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/50androidInheritanceSystem.png)</center>


<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/51androidInheritanceSystem.png)</center>


<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/52androidInheritanceSystem.png)</center>

* 这表达出：当人们按下屏幕画面上的按钮时，Android框架（及其幕后的系统服务）会透过OnClickListener接口而调用了myActivity类别里的onClick()函数

### 5.演练：UML的类别与接口 ###

> 接口的表示

* 在OOP里，将接口定义为一种特殊的类别（Class）

* 如果一个类别的某些函数是抽象函数的话，就称为【抽象函数】（Abstract Class）。如果一个抽象类别，它的所有的函数全部都是抽象函数的话，就称为【纯粹抽象类别】（Pure Abstract Class）；这种类别又称为【接口】（Interface）。

* 在C++里，类别包括3种：


	1、一般（具象）类别
		所有函数都是具象（内有指令）

	2、抽象（abstract）类别
		有一个或多个函数是抽象的（内无指令）

	3、纯粹抽象（pure abstract）类别
		所有函数都是抽象的。我们也称为接口

* 在Java里，将上述的纯粹抽象类别称为接口（Interface）

* 在UML里，以圆圈来表示接口

* 请问，下图的Counter是一个什么类别呢？

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/53androidInheritanceSystem.png)</center>

		Counter是抽象类

* 请问，下图里的Counter是一个什么类别呢？

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/54androidInheritanceSystem.png)</center>

		Counter是接口

* 上图相当于（也能表示为）下图，理由是什么？

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/55androidInheritanceSystem.png)</center>

* 请说明下图的含义：


<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/56androidInheritanceSystem.png)</center>

		首先，有两个接口IA和ICounter，而类ac01实现了接口IA，myCounter实现了接口ICounter，而ac01调用了接口ICounter中的方法request()，myCounter调用了接口IA中的方法setN()，所以，在子类ac01中，先运行方法request()，然后再运行setN()方法。

## 08 - 业务内涵的分析抽象&表达a ##

### 1.系统分析（System Analysis） ###

> 系统分析的含义：

* 许多人在学习系统分析（SA）时，常迷失于其字面上的意义，以为分析的对象是【系统】，这是一种常见的迷思！其实，分析的对象是系统所处的【业务领域知识】（Domain Konwledge）才是正确的。就如同计算机专家James Martin所说：

		【OOA（Object-oriented analysis）不是要去分析实际的系统；而是用来分析人们对系统的专业认知和做法----从收集到的领域概念来分析出业务内涵。】

> 业务（领域）知识=业务内涵

* 所以系统分析的主要对象并非【系统】本身，而是分析专家们如何以其专业知识来叙述系统，亦即，专家心中的【业务（领域）知识】才是系统分析的主要对象。所以焦点是业务知识（Domain Konwledge），而不是系统。

> 业务（领域）概念

* 知识的组成要素是【概念】（Concepts）

* 领域知识（Domain Knowledge）的组成要素是领域概念（Domain Concepts）。
* 概念（相对于系统中的类）有它的属性（Attribute），我们可以把其属性分为两类（static和dynamic），static就相当于类中的data，而Dynamic就相当于类中的method。概念之间有其关系（Relationship）
* 系统分析（或OOA）就是要分析领域知识里的概念，并以UML的类别（Class）等示来表示之。
* 概念（Concept）是抽象的，代表一群实体，是沟通的重要媒介。例如：【请买杯咖啡】，咖啡是个概念，具有这种概念的人，都会了解这句话的意思。他会凭其经验而想到真是的咖啡。
* 概念代表一个群体----【类别】（Class），人们藉由天赋的能力运用经验去想到其所代表的实际东西---【对象】（Object）。
* 例如您听到【买一只吉他】，这【吉他】（Class）概念让您想到经验中的吉他，而去乐器行买一只【真是的吉他】（Object）回家。
* 找出领域知识里的概念，就是找出软件系统的对象和类别。
* 例如麦当劳企业有汉堡、薯条、玩具、特餐、点餐、订购玩具、顾客、员工、玩具商、分店等等的概念，将对应到软件系统的类别，所以在麦当劳的软件系统里就会有汉堡、薯条、玩具、特餐、点餐、订购玩具、顾客、员工、玩具商、分店等等的类别。
* 【概念是人人互相分享的。概念提供了能让人人互相了解的共通词汇。】
* 例如【收据】是个重要的商业概念。在商店里，【收据】代表能归还所购货品的权利，也是人皆知道的，人们也知道实际收据的模样。这样的概念，自然称为人们沟通的主要字汇。



### 2.举例（一）：东方传说 ###

> UML与建模工具

* 【后羿从西王母处请来不死之药，嫦娥偷吃了这颗灵药，成仙了，身不由主飘飘然地飞往月宫之中，在那荒芜的月宫之中度着无边的寂寞岁月。】
* 虽然嫦娥可能是传说虚构的，并非事实，但是确确实实是我们心中的清晰概念，传说中的主角，所以是个重要的类别，表示如下： 


<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/57androidInheritanceSystem.png)</center>

* 跟【嫦娥】具有密切关联的概念是：月亮和仙丹。常表达如下：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/58androidInheritanceSystem.png)</center>

* 不仅上述的名词概念而已，其攸关的动作也常是重要概念。动词常常代表一项事件（Event）的发生，而人们常从人、事、时、地、物等去描述一个事情的发生情境。
* 譬如，吃仙丹就有动作（吃）的对象---仙丹，动作的主角--嫦娥，当然还有地点、时间，甚至仙丹来源等等。

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/59androidInheritanceSystem.png)</center>

## 09 - 业务内涵的分析抽象&表达b ##

### 3.举例（二）：西方通话 ###

> 通话【青蛙王子】的故事

* 从前有一位美丽的公主，喜欢玩金球。有一天，不小心让球滚进了井里。她哭泣了，青蛙向她说：我可以帮你捡起金球。但是我希望跟你约会。。。
* 如果约会时，公主心情好又亲吻青蛙的话，青蛙可能摇身一变成为一位英俊的王子。从此王子、公主过着快乐的日子。

> 故事的概念

* 这很容易找到几个核心的概念，例如：青蛙、公主等是用以描述事实的概念，而王子是用来描述未来可能成真的概念；还有约会、亲吻等动作也都是显而易见重要概念。
* 如此，就更明显地看出来故事的主角是：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/60androidInheritanceSystem.png)</center>

* 他希望公主亲她，所以又找到两个概念：亲吻和公主。

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/61androidInheritanceSystem.png)</center>

* 一旦公主亲吻了青蛙，他就变成王子，于是又找到一个新概念了：王子。虽然青蛙和王子是一体的两个身份，其在人们的知识里，仍然是两个不同的概念。随着找到的概念不断地增多，类别图就不断地扩大，如下：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/62androidInheritanceSystem.png)</center>

### 4.举例（三）：点餐服务 ###

> 【餐馆点餐服务】的分析步骤：

* Step-1：从最明显的重要事情出发。例如，餐厅人员与客人沟通的主要交易是【点餐】：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/63androidInheritanceSystem.png)</center>

* Step-2：联想到【OrderLine】和【餐点】：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/64androidInheritanceSystem.png)</center>

* Step-3：从餐点联想到它可分为【单点菜色】，以及【套餐】两大分类。
* Step-4：从单点菜色又分为【牛排】、【沙拉】和【饮料】等不同之分类，这些都是重要的领域概念。于是得到美好的类别图。如下图：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/65androidInheritanceSystem.png)</center>

* Step-5：还可以继续联想下去，例如【沙拉】又可以细分为水果沙拉、凯萨沙拉等等，则整个UML类别图，就更加完整了。


## 10 - 业务内涵的分析抽象&表达c ##

### 5.举例（四）：五子棋 ###

> 【五子棋】的分析步骤

* Step-1：找到主角--棋子手

	* 很容易发现核心的概念，例如：五子棋游戏的主角是棋手（玩家），棋手有两种--电脑和人；其中，电脑棋手又分为数个不同棋力等级，例如：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/66androidInheritanceSystem.png)</center>

* Step-2：抽象出抽象类别（Superclass）--BaseAI：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/67androidInheritanceSystem.png)</center>

* Step-3：再增添一种棋子手--HumanPlayer（人），而且再度进行抽象，得到：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/68androidInheritanceSystem.png)</center>

* Step-4：再联想到人之外的物---棋盘（ChessBoard），它必须呈现出UI画面上，所以设计成为View的子类别，得到：

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/69androidInheritanceSystem.png)</center>

* Step-5：再从棋盘联想到相关的概念--棋（Chess）；以及用来控制UI显示的GobangActivity类别，如下图。

<center>![](https://raw.githubusercontent.com/faithyee/Android-Architect-Teaching/master/img/70androidInheritanceSystem.png)</center>

* Step-6：还可以继续联想下去，就更加完整了。

### 6、讨论：模型与代码 ###

* 在传统观点里，大多先绘制UML模型图，然后才开始构思程序码的撰写，使得UML建模成为撰写程序码的前置工作，因此许多程序员将UML建模视为多余的负担。为了节省开发成本，就将省略掉UML建模的工作了。

		Domain-->Concepts--->class-->UML-->code

* 在新潮的观点里，UML模型与代码是软件系统本体的两个观点（或面向），两者没有先后顺序关系，而是并存和兼具于同一个人的脑海里。这就像两只眼睛看到的景象并存于一个人的脑海里一般，如此才能看到更真实的世界，也能做出更完美的软件系统来。



