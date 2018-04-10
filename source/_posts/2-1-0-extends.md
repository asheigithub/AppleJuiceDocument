---
title: 脚本继承
categories:
  - 2_advuse
  - 2_2adv
date: 2018-03-28 17:01:19
tags:
---
### 继承 ###
继承是指一种代码重用的形式，允许基于现有类开发新类。现有类通常称为基类 或超类，新类通常称为子类。继承的主要优势是，允许重复使用基类中的代码，但不修改现有代码。此外，继承不要求改变其它类与基类交互的方式。不必修改可能已经过彻底测试或可能已被使用的现有类，使用继承可将该类视为一个集成模块，可使用其它属性或方法对它进行扩展。因此，您使用 extends 关键字指明类从另一类继承。
通过继承还可以在代码中利用多态。有一种方法在应用于不同数据类型时会有不同行为，多态就是对这样的方法应用一个方法名的能力。

----------
如前所述，我们可以在宿主(Unity)工程中定义基类，然后在脚本中扩展它或者修改它。  
我们在Unity中定义一个名为 Shape 的基类就是一个简单的示例，该类有名为 Circle 和 Square 的两个子类。Shape 类定义了名为 area() 的方法，该方法返回形状的面积。如果已实现多态，则可以对 Circle 和 Square 类型的对象调用 area() 方法，然后执行正确的计算。使用继承能实现多态，实现的方式是允许子类继承和重新定义或覆盖 基类中的方法。我们可以在宿主工程中创建Circle类，在ActionScript3工程中创建Square类。  

1. 打开Unity工程，在Assets目录中创建一个C#脚本。  ![创建脚本](/apple-juice-actionscript/doc_cn/images/2-1-0/s1.jpg)
2. 将如下代码写入脚本中。
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

namespace extendtest
{
	public class Shape
	{
		public virtual double area()
		{
			return double.NaN;
		}
		
	}

	public class Circle : Shape
	{
		private double radius = 1;
		public override double area()
		{
			return (Mathf.PI * (radius * radius));
		}
	}


}
```
3. 打开ActionScript3工程，使用bat/CreateUnityAPI.bat将刚才写的代码导出API。  ![导出API](/apple-juice-actionscript/doc_cn/images/2-1-0/s2.jpg)
4. 打开Main.as。在Main.as底部，最后一个}后面写下如下代码:
```actionscript
import extendtest.Circle;
import extendtest.Shape;

//我们在ActionScript3中继承Shape,并且覆盖它的area方法。
class Square extends Shape 
{ 
    private var side:Number = 1; 
    override public function area():Number 
    { 
        return (side * side); 
    } 
} 
 
var cir:Circle = new Circle(); 
trace("Circele:",cir.area()); // output: 3.141592653589793 
var sq:Square = new Square(); 
trace("Square:", sq.area());  // output: 1

```

> 由于某些未知原因，在Unity工程中定义的类型，如果使用 abstract 定义为抽象类，导出工具在分析Assembly-CSharp.dll时，无非正确的得到它是抽象类这个信息。因此，不要在Unity工程中用抽象类定义API。

5. 点击编译。将代码发布到Unity。
6. 在Unity总执行场景，可以看到运行结果。其中，Circele是在Unity中定义，而Shape却是在脚本中定义的。  ![导出API](/apple-juice-actionscript/doc_cn/images/2-1-0/s3.jpg)

> 在Main.as底部，包体的外部写下代码，在ActionScript3中称为包外代码，在这里定义的类型叫包外类。这些代码会在Main第一次被实例化前被调用。在这里添加代码是一个方便的测试方式，Apple Juice也同样实现了这个ActionScript3的语言特性。

### 特殊情况 ###
某些类型没有构造函数，或者它们的使用方式比较特殊，是使用某种类似工厂的方法创建的。最典型的例子就是MonoBehaviour。对于这些特殊的类型，如果要在脚本中继承，则还需要一些额外的处理。后面的章节将讲述原理和解决方法。