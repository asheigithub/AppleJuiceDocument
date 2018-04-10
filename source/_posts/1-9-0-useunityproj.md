---
title: 内容说明
categories:
  - 1_beginuse
  - 1_3unityproj
date: 2018-03-21 18:52:28
tags:
---
当插件导入后，Unity的工程目录为:

> Assets  
> ├────── ASRuntimePlayer  
> │        ├─────Editor  
> │        │      ├──── ASRuntimeMenus.cs   
> │        │      └──── ASRuntimeSettings.cs  
> │        ├─────RegCode  
> │        ├─────Support  
> │        │      └──── extFunctions.cs  
> │        └─────ActionScriptStartUp.cs  
> ├────── Standard Assets  
> │        └─────ASRuntime  
> │               ├──── AS3VM  
> │               └──── ScriptSupport  
> │                      └──── Generated  
> ├────── StreamingAssets  
> │        └─────hotfix.cswc  
> ├────── HotFixDemoScene1.unity  
> └────── link.xml             

#### HotFixDemoScene1.unity ####
Demo示例场景。
#### hotfix.cswc ####
ActionScript3脚本编译后生成的二进制字节码文件。
#### ASRuntimePlayer/Editor ####
Unity插件代码目录。
#### ASRuntimePlayer/RegCode ####
包含如下2类文件：
- 生成API时，自动生成的一个注册所有API函数的代码文件。
- 那些在Unity工程中并且不在Standard Assets目录中定义的类库，当导出API代码时，将会生成到这个目录下。  


#### Standard Assets/ASRuntime/ScriptSupport/Generated ####
自动导出的API代码将会生成到这里。

> Unity会自动编译工程目录中的代码。在Standard Assets目录下的代码会首先被编译，生成一个Assembly-CSharp-firstpass.dll。  
> 其他目录下的代码将编译生成一个Assembly-CSharp.dll。
> Standard Assets目录下的代码，无法引用其他目录下的代码。如果在Standard Assets 目录中保存导出的API,则可以避免API频繁的编译，因为这部分代码是不经常变动的。虽然导出的API可能很多，编译速度慢，但是放在这个目录下，只需编译一次，不会经常改变。
> 因此将Unity工程内自定义的类库导出的API放在“ASRuntimePlayer/RegCode”中，其他API放在Standard Assets/ASRuntime/ScriptSupport/Generated中，可以保证生成代码的引用关系正确，并且UnityEngine.dll导出的API不会频繁编译。

#### link.xml ####
Unity在使用IL2CPP发布时，比如发布IOS包或者安卓选择IL2CPP发布时，会自动对Unity工程的DLL进行裁剪，将代码中没有引用到的类型裁剪掉，以达到减小发布后包体大小的目的。然而在实际使用过程中，很多类型可能被异常裁剪掉。link.xml可以配置哪些类型不会被裁剪。  
Apple Juice 自动生成的API代码都在Assembly-CSharp-firstpass.dll或者Assembly-CSharp.dll中，因此，link.xml里默认配置了如下几处程序集不会被裁剪。如无意外，可不必修改此文件。如果您有其他的Dll库文件导出了API,那么您可以将此DLL的程序集配置到此文件中。   
```xml
<linker>  
  <assembly fullname="Assembly-CSharp" preserve="all"/>
  <assembly fullname="Assembly-CSharp-firstpass" preserve="all"/>
  <assembly fullname="UnityEngine" preserve="all"/>
  <assembly fullname="UnityEngine.UI" preserve="all"/>	
  <assembly fullname="mscorlib" preserve="all"/>
</linker>
```
