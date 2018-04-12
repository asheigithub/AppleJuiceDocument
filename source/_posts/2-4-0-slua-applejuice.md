---
title: Unity交互性能和Lua的对比
categories:
  - 2_advuse
  - 2_3performance
date: 2018-04-12 18:15:43
tags:
---
网络上有一篇主要关于Unity交互的Lua方案的性能对比测试。  
[https://zhuanlan.zhihu.com/p/22673880](https://zhuanlan.zhihu.com/p/22673880 "Lua测试")

AppleJuice也进行了一次此测试。测试平台为手机 魅族Note5。对比对象为SLUA 1.5.5。双方均使用il2cpp打包。

> AppleJuice是C#代码，因此在UnityEditor中测试结果不准确，需要发布到手机实测，或者发布为PC的exe,才能实际测试性能。

```actionscript
package 
{
	import flash.system.System;
	import system.DateTime;
	import system.TimeSpan;
	import unityengine.GameObject;
	import unityengine.Transform;
	import unityengine.Vector3;
	/**
	 * ...
	 * @author 
	 */
	public final class Perf 
	{
		var cube:GameObject = new GameObject("Script cube");
		public function Perf() 
		{
			
		}
		
		public function Test1():void
		{
			var d:DateTime = DateTime.now;
			
			var transform:Transform = cube.transform;
			for (var i:int = 0; i < 200000	; i++) 
			{
				transform.position = transform.position;
			}			
			trace(  TimeSpan (DateTime.now-d).totalMilliseconds );
		}
		
		public function Test2():void
		{
			var d:DateTime = DateTime.now;
			
			var transform:Transform = cube.transform;
			for (var i:int = 0; i < 200000	; i++) 
			{
				transform.rotate____(Vector3.up, 90);
			}			
			trace(  TimeSpan (DateTime.now-d).totalMilliseconds );
		}
		
		public function Test3():void
		{
			var d:DateTime = DateTime.now;
			
			
			for (var i:int = 0; i < 2000000	; i++) 
			{
				var v:Vector3 =new Vector3(i,i,i)
				Vector3.normalize(v)
			}			
			trace(  TimeSpan (DateTime.now-d).totalMilliseconds );
		}
		
		public function Test4():void
		{
			var d:DateTime = DateTime.now;
			
			
			for (var i:int = 0; i < 200000; i++) 
			{
				var v:GameObject = GameObject.constructor_();
			}			
			trace(  TimeSpan (DateTime.now-d).totalMilliseconds );
			
		}
		
		
	}

}
```



```lua
function test1()
	local transform = cube.transform
	local start = os.clock()
	for i=1,200000 do
        transform.position=transform.position
	end
	print("test1/lua " .. (os.clock() - start));
end

function test2()
	local transform=cube.transform
	local start = os.clock()
	for i=1,200000 do
		transform:Rotate(Vector3.up, 90)
	end
	print("test2/lua " .. (os.clock() - start));
end

function test3()
	local start = os.clock()
	for i=1,2000000 do 
		local v = Vector3(i,i,i)
		Vector3.Normalize(v)
	end
	print("test3/lua " .. (os.clock() - start));
end

function test4()
	local t = cube.transform
	local v = Vector3.one
	local start = os.clock()
	for i=1,200000 do
		local v = GameObject()	
	end
	print("test4/lua " .. (os.clock() - start));
end
```
#### 以下为测试结果 单位为秒。越小越好 ####
1. test1 slua 1.09  apple juice 1.14
2. test2 slua 2.57  apple juice 1.84
3. test3 slua 0.058 apple juice 25.9 
> test3 实际上slua并没有和unity交互，因此这个测试不适于评估。
4. test4 slua 7.25  apple juice 4.128




![结果截图说明](/apple-juice-actionscript/doc_cn/images/slua_applejuice_perf.jpg)