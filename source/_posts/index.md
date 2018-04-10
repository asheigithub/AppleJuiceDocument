---
title: 欢迎
date: 2018-03-16 18:41:21
---
欢迎使用 [Apple Juice](https://github.com/asheigithub/apple-juice-actionscript "这是一个ActionScript3 编译器和虚拟机")
这是一个纯 .net2.0实现的ActionScript3 编译器和虚拟机。
>支持除了 namespace 和 with 和 E4X XML操作外的一切actionscript3语法特性。   

>可以将actionscript3代码编译为字节码，然后加载并动态执行。VM由纯C# 2.0代码实现，可以直接让Unity来读取并执行生成的字节码，就是可用Unity完成热更新操作。  
编译器部分实现了完整的编译期类型检查。并且有完整的错误提示。已经和FlashDevelop完成了集成，可以直接在FlashDevelop中开发并一键编译发布到Unity。   
Unity的API或者自己开发的C# API提供了工具直接转换为actionscript api文件和对接代码，并且直接注册到FlashDevelop工程中。

#### 特色 ####
- 对.net的结构体有特殊优化。在脚本中只要在方法的局部变量中使用，可以任意的new而不会导致gc.因此使用UnityEngine.Vector3之类类型时，可以放心使用。
- 对导出的类型有特殊优化。只要在方法的局部变量中使用，则脚本本身不产生额外的对象分配。
- 实现操作符重载。导出的对象如果有操作符重载，则在脚本中同样可以使用。如下代码是完全合法的。
````actionscript
    cube.transform.localPosition += v * Time.deltaTime;
````
- 可以使用yield语句返回一个IEnumerator,然后用Iterator包装为.net的IEnumerator接口。所以可以直接在脚本中写Unity的协程。如如下代码所示:  
````actionscript
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
````
- 自动将.net 委托对应到ActionScript3的function对象。例如:
````actionscript
	var btn:Button = Button( GameObject.find("Button").getComponent(Button));			
	btn.onClick.addListener(			
		onclick			
	);

	btn.onClick.addListener(
		function()
		{
			trace("hahaha",this);					
		}
	);
````
- 自动处理.net 类库中的 out ref类型的参数。这样的方法也可自动导出。例如 long.TryParse:
````actionscript
	/**
	* System.Int64.TryParse
	*parameters:
	*  s : System.String
	*  result : (Out)System.Int64
	*return:
	*   System.Boolean
	*/
	[native,static_system_Int64_tryParse];
	public static function tryParse(s:String,result:Int64,refout:as3runtime.RefOutStore):Boolean;

````

- 可以在ActionScript3脚本中继承.net类库。如以下脚本，在脚本中自定义了一个MonoBehaviour,然后创建100个物体，并将此对象挂在这些物体上。


```actionscript

var isstop:Boolean = false;

//Define a subclass of MonoBehaviour

class MyB extends MonoBehaviour
{
	
	private var v:Vector3;
	function Awake()
	{
		v = new Vector3(Random.range( -5, 5), Random.range( -5, 5), Random.range( -5, 5));
		v.normalize();
	}
	
	function Update()
	{
		if (isstop)
			return;
		
		var vv:Vector3 = new Vector3(4, 5, 6);
		
		gameObject.transform.localPosition += v * Time.deltaTime;
		
		var p:Vector3 = gameObject.transform.localPosition;
		if (p.x <-5 || p.y < -5 || p.z < -5 || p.x > 5 || p.y > 5 || p.z > 5)
		{
			v =-v;
		}
	}

}
var cube:UObject = GameObject.find("Cube");
for (var i:int = 0; i < 100; i++) 
{
	var c2:GameObject = GameObject.createPrimitive( PrimitiveType.Cube); //UObject.instantiate__(cube) as GameObject;
	MeshRenderer( c2.getComponent(MeshRenderer)).material = MeshRenderer( GameObject( cube).getComponent(MeshRenderer)).material;
	c2.transform.position = new Vector3( Random.range(-5,5),Random.range(0,5),Random.range(-5,5) );
	
	//Mount the MonoBehaviour just defined on the object
	c2.addComponent(MyB);
}
var btn:Button = Button( GameObject.find("Button").getComponent(Button));	
btn.onClick.addListener(			
	function()
	{
		isstop = !isstop;
		trace("isstop?" , isstop);		
		Text( GameObject.find("Canvas/Text").getComponent(Text)).text = "isstop?" + isstop +"我在AS3中热更";
	}
);

```

- 完整的编译期类型检查。脚本有完整的编译时类型检查，利于错误排查。
- 完全实现的面向对象支持。完整支持类继承和接口。
- FlashDevelop IDE支持。可以完全利用IDE的智能提示，编译错误也可在IDE中得到反馈。可在IDE中直接编译热更新代码成到Unity项目

#### api全自动导出 ####

> 自动将.net类库导出给脚本使用。并且保留有原始类型信息。例如**UnityEngine.Avatar**导出后的api形式为:
````actionscript
	package unityengine
    {


		/**
		* Sealed
		*  UnityEngine.Avatar
		*/
		[no_constructor]
		[link_system]
		public final class Avatar extends UObject
		{
			[creator];
			[native,unityengine_Avatar_creator];
			private static function _creator(type:Class):*;
			[native,$$_noctorclass];
			public function Avatar();
			//*********公共方法*******
			/**
			* UnityEngine.Avatar.get_isValid
			*return:
			*   System.Boolean
			*/
			[native,unityengine_Avatar_get_isValid]
			public final function get isValid():Boolean;
	
			/**
			* UnityEngine.Avatar.get_isHuman
			*return:
			*   System.Boolean
			*/
			[native,unityengine_Avatar_get_isHuman]
			public final function get isHuman():Boolean;
	
	
		}
    }
````
> IDE能提供智能感知提示。

> 自动导出的API为actionscript3风格。比如**UnityEngine.UI.Button** 将被导出为 **unityengine.ui.Button**

> 能将.net 类库中的类型包含继承关系和接口实现关系的导出。例如，**UnityEngine.MeshRenderer**  继承自 **UnityEngine.Renderer**  。那么导出后的as3类型也会保持以上的关系。
