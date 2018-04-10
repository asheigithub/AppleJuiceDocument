---
title: 类型转换
categories:
  - 1_beginuse
  - 1_2useas3
date: 2018-03-20 16:43:01
tags:
---
### 基本类型 ###

ActionScript3内置有一些数据类型。当调用.net类库时，脚本系统会自动将它们进行转换成.net的相应类型传递给.net类库。  
当.net类库返回值时，脚本系统也会将它转换为ActionScript3类型。下面说明了基础类型的转换:
#### Boolean ####
对应C#的bool。

#### int ####
对应C#的int。
> 当.net类库需要接收short时，系统会自动转换为short。
> 当.net类库返回short时，系统会自动转换回 ActionScript3的int。


#### uint ####
对应C#的uint。
> 当.net类库需要接收ushort时，系统会自动转换为ushort。
> 当.net类库返回ushort时，系统会自动转换回 ActionScript3的uint。


#### Number ####
对应C#的double。
> 当.net类库需要接收float时，系统会自动转换为float。
> 当.net类库返回float时，系统会自动转换回 ActionScript3的Number。

#### String ####
对应C#的string。

### 保留类型 ###
ActionScript3语言中有些类型是ActionScript3特有的。  
它们不能被转换成.net的类型。

#### Array ####
ActionScript3中的Array是特殊的。他事实上是一个键值对类型，并非其他编程语言的数组。  
因此，Array只在脚本内部使用。  
.net的数组被作为API导入到脚本系统内。.net的System.Array的各种方法都被导入，可用于操作.net数组。

#### Object ####
ActionScript3中的Object是ActionScript3类型的基类。导入的.net的类库的基类是.net中的System.Object。System.Object导入到ActionScript3中成为 system._Object_,同样也继承自ActionScript3的Object。  
> 当调用.net类库时，如果是导入的类型，系统会自动转换成.net类型，否则将会发生类型错误。
> 当.net类库返回某个类型的值时，如果是这个类型导出过API，则系统能自动识别并在脚本中使用，否则将导致类型错误。

#### void ####
ActionScript3中可用 "*" 来表示任意类型。如果在脚本中这么做，则将丧失编译时类型检查，类型检查将在运行时进行。

#### function ####
ActionScript3中的任何function都是一个Function对象。
> 当.net类库需要接收一个委托时，将一个function作为参数传递过去，系统会自动将其包装成一个委托。  
> 当.net类库接收到这个委托后，当.net类库内部调用这个委托时，系统会将.net传递的参数转换成相应的ActionScript3对象并传递给此Function对象并调用。如果参数不匹配，则会引发异常。
> 因此，把function作为参数传递给.net时，还是需要注意函数参数和返回值的匹配。
> 如下代码演示如何给UGUI的Button添加响应。 

````actionscript
	var btn:Button = Button( GameObject.find("Button").getComponent(Button));	
			
	btn.onClick.addListener(			
		function(){
			trace("Clicked!");
		}			
	);
````
#### Class ####
ActionScript3中的Class,当被作为参数传递给.net时，系统将会将它转换成对应的System.Type对象。  
只有从.net导入的API类型才有此对应关系。如果不是从.net导入的类型，转换时会产生错误。
