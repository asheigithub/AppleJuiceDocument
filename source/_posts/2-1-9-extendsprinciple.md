---
title: 继承的原理
categories:
  - 2_advuse
  - 2_2adv
date: 2018-04-10 18:03:37
tags:
---
Apple Juice允许您在ActionScript3中继承并扩展C#的基类。这个特性通过以下方式实现：  
- 当导出API时，当导出工具发现某个类型可以被脚本继承时，将会自动创建一个这个类型的适配器类型。此适配器类型继承自此类型，并且还可以和脚本交互。作为一个桥梁的形式存在。
- 当ActionScript3引擎创建脚本中继承基类的对象时，实际上同时创建了这个适配器和AS3脚本对象两个对象。这两个对象互相绑定，当传给.net系统时，实际上是用的是这个适配器，当在脚本系统中使用时，则是操作AS3脚本对象。
- Apple Juice代码中定义了如下接口：  

```csharp
/// <summary>
/// 标记这是一个可以跨脚本语言继承的对象
/// </summary>
public interface ICrossExtendAdapter
{
	void SetAS3RuntimeEnvironment(Player player, ASBinCode.rtti.Class typeclass, ASBinCode.rtData.rtObjectBase bindAS3Object);

	ASBinCode.rtti.Class AS3Class { get; }
	ASBinCode.rtData.rtObjectBase AS3Object { get; }

}
```

- 所有适配器都会实现这个接口。当ActionScript3引擎创建适配器实例后，会调用SetAS3RuntimeEnvironment方法，将对应的AS3脚本对象以及脚本引擎传入进来。此适配器会保存它们，以备交互时使用。
- 适配器能覆盖基类的方法，然后适配器又能和脚本交互，以决定具体使用哪个版本的方法。

#### 实例说明 ####
以上一章的Shape为例，来具体讲解这个适配器是如何起作用的。  
```csharp
		public class extendtest_ShapeAdapter :extendtest.Shape ,ASRuntime.ICrossExtendAdapter
		{

			public ASBinCode.rtti.Class AS3Class { get { return typeclass; } }

			public ASBinCode.rtData.rtObjectBase AS3Object { get { return bindAS3Object; } }

			protected Player player;
			private Class typeclass;
			private ASBinCode.rtData.rtObjectBase bindAS3Object;

			public void SetAS3RuntimeEnvironment(Player player, Class typeclass, ASBinCode.rtData.rtObjectBase bindAS3Object)
			{
				this.player = player;
				this.typeclass = typeclass;
				this.bindAS3Object = bindAS3Object;
			}

			public extendtest_ShapeAdapter():base(){}
			private ASBinCode.rtData.rtFunction _as3function_0;
			private int _as3functionId_0 =-1;
			public override System.Double area()
			{

				if (_as3function_0 == null)
					_as3function_0 = (ASBinCode.rtData.rtFunction)player.getMethod(bindAS3Object, "area");
				if (_as3functionId_0 == -1)
				{
					_as3functionId_0 = ((ClassMethodGetter)typeclass.getBaseLinkSystemClass().classMembers.FindByName("area").bindField).functionId;
				}

				if (_as3function_0 != null &&
				(player == null || (player != null && NativeConstParameterFunction.checkToken(
				new NativeConstParameterFunction.ExecuteToken(
				player.ExecuteToken.tokenid, _as3functionId_0
				)
				))
				)
				)
				{
					return base.area();
				}
				else
				{
					return (System.Double)player.InvokeFunction(_as3function_0,0,null,null,null,null,null,null);
				}
			}


		}
```
这个extendtest_ShapeAdapter，继承自extendtest.Shape,同时实现了ASRuntime.ICrossExtendAdapter接口。  
1. player 成员保存播放器对象
2. typeclass 成员保存ActionScript3对象的Class
3. bindAS3Object 成员保存ActionScript3对象。

extendtest_ShapeAdapter覆盖extendtest.Shape的area方法。这个方法作为调用的桥梁。它判断究竟要调用脚本中的覆盖的方法还是调用基类的方法。当此方法第一次执行时，首先查找脚本中的子类中的对应方法。如果没有找到，则直接调用基类的方法。


1. 如果此方法是被脚本引擎调用的，则有可能是脚本中调用了基类的方法。脚本引擎在每次调用本地对象前，都会发出一个令牌。在这种情况下，先检测令牌是否匹配，如果匹配，则调用父类的方法。
2. 如果此方法是被C#代码调用的，如果子类中有对应的方法。则调用脚本引擎，执行子类中覆盖的方法。
3. 如果是由C#代码发起调用，脚本引擎执行子类方法，子类又调用父类方法时，则令牌生效，这样即可正确判断使用父类版本或者脚本覆盖版本。

