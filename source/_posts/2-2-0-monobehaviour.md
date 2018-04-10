---
title: 关于MonoBehaviour
categories:
  - 2_advuse
  - 2_2adv
date: 2018-04-10 17:54:32
tags:
---
上一章描述的脚本继承，可以自动处理那些用new关键字在脚本中创建实例的情况。但是，某些类型却无法使用new关键字创建，它们或者需要某些工厂方法创建，或者使用new关键字会报错。Unity中，这样的典型对象有Component类型。MonoBehaviour是其中的典型，它必须使用GameObject.AddComponent等方法创建。本章描述如何处理这种情况。  
代码创建工具会帮我们创建好GameObject.AddComponent的API的大部分代码。并且已经注册到脚本引擎中。我只需将它拷贝一份出来，在它基础上进行一些修改，然后覆盖之前注册的代码即可。  
使用CSWC对象的regNativeFunction(NativeFunctionBase nativefunction, bool isReplace)方法注册本地代码，并且可以指定是否覆盖。  

#### 处理Component ####
1. 首先我们到自动生成的API代码中，找到 unityengine_GameObject_buildin 这个类。这个类内定义了unityengine_GameObject_addComponent嵌套类。这个嵌套类就是GameObject.AddComponet的API代码。
2. 我们新建一个叫GameObject_addComponent的类，然后将unityengine_GameObject_addComponent的代码拷贝到新的类型里去，并修改错误。
3. execute3方法，就是实际调用GameObject.AddComponent的地方。我们在AddComponent调用后，如果生成的对象是一个适配器对象，则继续完成适配器的初始化工作。只需在后面添加如下代码即可:  

```csharp
ICrossExtendAdapter crossExtendAdapter = _result_ as ICrossExtendAdapter;
if (crossExtendAdapter != null)
{
	ASBinCode.rtti.Class as3class = ((ASBinCode.rtData.rtObjectBase)argements[0]).value._class;
	stackframe.player.MakeICrossExtendAdapterEnvironment(crossExtendAdapter, as3class);
}
```

4. 将此方法注册到脚本引擎中。  


这样，即可正确生成可在脚本中使用的Component类型。

#### 处理MonoBehaviour ####
MonoBehaviour是Component的子类。因此，经过以上处理，已经可以通过脚本挂载从脚本中定义的MonoBehaviour子类。但是，MonoBehaviour非常特殊，Unity会根据MonoBehaviour中是否定义有例如Update等方法来根据实际调用这些方法。这些方法不是virtual的，因此MonoBehaviour的适配器也需要特殊处理。
1. 如前那样，从自动生成的代码中找到unityengine_MonoBehaviour_buildin，然后找到unityengine_MonoBehaviourAdapter和_unityengine_MonoBehaviourAdapter_ctor，将他们的代码复制到新建的类型里去。
2. MonoBehaviourAdapter_ctor 中有new MonoBehaviourAdapter() 的操作，将它删除，因为MonoBehaviour不会通过new创建
3. 修改MonoBehaviourAdapter的代码。在里面定义Update方法，然后将它转发到脚本系统中。代码如下:  


```csharp

		private ASBinCode.rtData.rtFunction _Update;
		private bool _Update_hasfound;
		private void Update()
		{
			if (!_Update_hasfound)
			{
				_Update = (ASBinCode.rtData.rtFunction)player.getMethod(bindAS3Object, "Update");
				_Update_hasfound = true;
			}
			if (_Update == null)
				return;
			player.InvokeFunction(_Update, 0, null, null, null, null, null, null);
		}

```
4. 将代码注册到系统中。

如此，在脚本中定义的MonoBehaviour即可正确处理Update。

#### 其他 ####
Apple Juice系统自带脚本中已经处理好常用MonoBehaviour的情况，包括AddComponent,GetComponent系列等，有些MonoBehaviour事件未实现，例如OnAnimatorIK等等，如果有需要，可以仿照实现。这部分代码在GObj_Special中。  
    
> 在脚本中可以执行SendMessage，给自定义的MonoBehaviour发送消息。这部分系统已处理，但是，**无法**从C#给脚本中定义的MonoBehaviour子类用SendMessage发送消息。因为MonoBehaviourAdapter中，不可能预知会有哪些方法名，因此，C#中直接用SendMessage是无法调用脚本中的方法的。如果确实需要，则需要调用
> MonoBehaviourAdapter中的
> internal bool F6554FF96D0D340948D3DCD4AA9225CCD(string arg0, object arg1, SendMessageOptions arg2)方法。其中arg0是方法名，arg1是参数。这是MonoBehaviourAdapter处理脚本中SendMessage的方法。可以直接通过它发送消息

