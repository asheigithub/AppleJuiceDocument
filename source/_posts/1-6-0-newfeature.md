---
title: 新增语言特性
categories:
  - 1_beginuse
  - 1_2useas3
date: 2018-03-20 18:29:36
tags:
---
为了更好的和.net之间互相调用，ActionScript3系统新增了一些语言特性。

### 枚举器接口 ###
这个功能是Apple Juice系统新增的。  
#### 接口定义 ####
如下枚举器接口是内置的:
````actionscript
	package system.collections 
	{
		
		/**
		 * ...
		 * @author 
		 */
		[_IEnumerator_]
		public interface IEnumerator
		{
			function get current():*;
	
			function moveNext():Boolean;
	
			function reset():void;
		}
		
	}
````
凡是实现此接口的对象都可用for each 或者 for in 语法遍历。
#### yield ####
系统新增加了一个关键字:yield。它可用自动将一个function的返回值包装成为Ienumerator接口。见如下代码

```actionscript
var functionEnumerator = function(count:int)
{
	for (var j:int = 0; j < count; j++) 
	{
		yield return j;
	}				
}

for each (var k in functionEnumerator(5)) 
{
	trace("k:", k);
}
```
输出:
```
k: 0
k: 1
k: 2
k: 3
k: 4
```

#### 枚举器转换 ####
Apple Juice在.net部分定义了一个Iterator类型。  
这个类型本身实现了System.Collections.IEnumerator接口，同时又可以操作ActionScript3脚本的枚举器接口。因此，可以作为ActionScript3和.net之间的枚举器的桥梁。  
使用ActionScript3的强制类型转换语法 Iterator()，将脚本对象包装成.net对象。当.net类库需要接收System.Collections.IEnumerator接口时，可以用它来包装脚本。
> 典型的使用场合：Unity中使用协程时，可以直接在脚本中定义IEnumerator对象，然后用Iterator包装后传给Unity。参加如下代码：先使用GameObject.find找到MonoBehaviour对象，然后用MonoBehaviour的startCoroutine方法启动一个协程。这个协程可以直接在脚本中定义。

```actionscript
var mono:MonoBehaviour = GameObject.find("AS3Player").getComponent(MonoBehaviour) as MonoBehaviour;
trace(mono.name);
mono.startCoroutine( Iterator(  
	(
	function()
	{
		trace("a",Time.frameCount);
		
		yield return 1;
		
		trace("b",Time.frameCount);
		
		yield return 2;
		
		trace("c",Time.frameCount);
		yield return 3;
	}
	)()
));
```

### 结构体 ###
.net类库中，既有值类型也有引用类型。结构体是值类型。  
.net中，直接基于值类型的变量包含值。 将一个值类型变量分配给另一个值类型变量将复制包含的值。 这不同于分配引用类型变量，后者复制对对象的引用，但不复制对象本身。 
绝大多数脚本语言都没有值类型，只有引用类型。因此，Apple Juice新增了对值类型的处理。
- 值类型不能在脚本中定义，但是如果.net类库中的某个类型是值类型，那么导入后它保持了值类型的特性。
- 在Apple Juice ActionScript3中,如果将一个值类型的变量赋值给另一个变量，那么将会把变量的值复制给另一个变量。
- 未赋值的值类型变量默认值为null。
- 综上，值类型导入脚本后，相当于.net中的System.Nullable<T>类型

见如下代码:
```actionscript
var v1:Vector3 = new Vector3(0, 0, 100);
var v2:Vector3;

trace("v2:",v2);
v2 = v1;
trace("v2:",v2);

v2.z = 999;
trace("v2:",v2,"v1:",v1);
```
```javascript
//v2未赋值，默认为null
v2: null
//v2的值从v1复制过来
v2: (0.0, 0.0, 100.0)
//由于v2的值是复制得来的，改变并未对v1造成影响。
v2: (0.0, 0.0, 999.0) v1: (0.0, 0.0, 100.0)
```

### 操作符重载 ###
.net可以使用操作符重载，为此Apple Juice也追加了这个特性。例如Unity中重载了GameObject的==和!=,以判断一个物体是否被销毁。我们也可以在AS3中这样判断。或者使用equals方法

```actionscript
var a:GameObject = new GameObject("aa");

UObject.destroyImmediate_(a);

trace(a == null);
//Unity控制台将显示
//true
//UnityEngine.Debug:Log(Object)
```

