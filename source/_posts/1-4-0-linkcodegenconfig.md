---
title: API导出工具
categories:
  - 1_beginuse
  - 1_1configureproject
date: 2018-03-20 14:55:49
tags:
---
API导出工具LinkCodeGenCLI.exe在SDK目录中的/linkcodegencli子目录下。
这是一个控制台程序。可以接收如下参数:  
config=&lt;configfilename>.xml 来指定一个配置文件。  
ActionScript3项目中，默认生成的配置文件是 genapi.config.xml。  

执行bat/CreateUnityAPI.bat将会重新生成API文档，并生成Unity工程中的API注册代码。

主要配置节有：
```xml
   <appSettings><!--配置SDK，代码文件等的路径--></appSettings>
   <includelibcode><!--一同编译的类库代码--></includelibcode>
   <buildassemblys><!--要导出API的程序集--></buildassemblys>
   <resolvepath><!--依赖程序集的查找位置--></resolvepath>
   <skipcreatortypes><!--指定已手工创建API的类型--></skipcreatortypes>
   <notcreatenamespace><!--指定哪些命名空间的类型不会被导出--></notcreatenamespace>
   <notcreatetypes><!--指定哪些类型不会被导出--></notcreatetypes>
   <notcreatemembers><!--指定哪些类型成员不会被导出--></notcreatemembers>
```

#### appSettings ####
配置各种文件的输出路径和输入路径。
- csharpcodepath  ActionScript3引擎要调用.net类库需要有对应的调用代码。每一个.net的Type,都会对应一个代码文件。指定这些文件的输出目录。
- csharpcodenamespace  输出的API调用代码的命名空间.
- regfunctioncodefile  当ActionScript3引擎启动时需要注册前面生成的调用代码。这个文件包含了这些注册代码。
- regfunctioncodenamespace 注册代码的命名空间
> 以上配置如果改动，则相应需要改动Unity工程中的ActionScript3引擎加载代码。如无必要保持默认即可

- combiedcodefile 可以把csharpcodepath中配置的输出目录中的所有C#代码合并为一个文件。
- sdkpath  指定SDK地址。默认值是auto。如果LinkCodeGenCLI.exe所在目录就在SDK目录的/linkcodegencli子目录中,则配置为auto就表示所在目录就在SDK目录中。
- as3apipath 将.net类库转换为ActionScript3的代码。这些代码仅包含类型声明和调用函数的对应关系，FlashDevelop可读取这些代码作为智能提示。
- as3libfile 将API编译成二进制字节码。由于API不会手工变动，因此编译一次以后直接加载即可。
> 约定：这个文件必须在ActionScript项目的lib目录中，并且文件名为as3unitylib.cswc。 如果缺少此文件，则编译会失败并提示。
#### includelibcode ####
配置其他要预编译的ActionScript3代码。如果有些类库是ActionScript3代码编写的，并且不会频繁修改，则可以一起编译。默认包含SDK目录下的/as3corelib目录。
```xml
   <includelibcode>
    <item value="$(sdkpath)\as3corelib"></item>
  </includelibcode>
```
#### buildassemblys ####
配置想要导出API的dll。每个*assembly*子节点配置一个dll。*assembly*子节点下还可以配置*type*节点，如果这么做了，那么只有配置的类型会被导出，否则将导出所有可以导出的类型。
> 例如:
```xml
    <buildassemblys>
    <assembly value="C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v3.5\Profile\Unity Full v3.5\System.dll">
      <type definetype="type" value="aa"></type>
	  <type definetype="namespace" value="bb"></type>
    </assembly>
    </buildassemblys>
```
> 只有名字叫aa的类型或者命名空间为bb的类型才会被导出，因此实际上不会有任何类型被导出。

#### resolvepath ####
配置dll的查找路径。当解析dll时，可能需要加载它依赖的dll,例如当加载ScriptAssemblies\Assembly-CSharp.dll时，可能需要到UnityAssemblies下查找UnityEngine.CoreModule.dll等。  
在这里配置dll查找路径.如果有问题，还可以到Unity安装目录下去查找。
> 要定位Unity的dll,请到Unity的安装目录下查找。  
> Unity工程的Library里面的dll有些读取时会引发BadImageFormatException异常。当发生这种情况时，请到Unity安装目录的/Editor/Data/Managed/目录下加载UnityEngine.dll, /Editor/Data/UnityExtensions/Unity/GUISystem/下加载UnityEngine.UI.dll。  
> Unity2017 v2之后,Unity将UnityEngine.dll拆成了许多小dll,其中工程目录Library/UnityAssemblies中的UnityEngine.dll是无法被加载的。  
> 因此查找路径要注意顺序，先到安装目录下找，再到Library/UnityAssemblies中查找，否则可能会导致dll加载失败。

#### skipcreatortypes ####
已经手工写过api的类型。由于某些类型比较特殊，需要特别对待以满足特殊需求，这些类型被在这里指明。当生成器遇到这些类型时，就不会为它们生成代码。
#### notcreatenamespace ####
配置的命名空间下的全部类型都不会被导出。任何引用，继承到它们的方法，类型都不会被导出。
#### notcreatetypes ####
配置的类型不会被导出。任何引用，继承到它们的方法，类型都不会被导出。
#### notcreatemembers ####
配置的成员在遇到时会被跳过。Unity在运行时，某些类型的某些成员会不可用。为此，只能在导出api时跳过这些成员。在这里配置这些成员。
>例如
>UnityEngine.Light.areaSize在编辑器状态下可用，但是发布时候确不可用。因此，必须配置这些成员以在导出API时跳过。

