---
title: ActionScript3项目配置
categories:
  - 1_beginuse
  - 1_1configureproject
date: 2018-03-20 11:34:55
tags:
---
ActionScript3项目目录结构如下
>.  
>├─── as3_unity  
>├─── bat  
>│     ├───CompileCode.bat  
>│     └───CreateUnityAPI.bat  
>├─── bin  
>├─── lib  
>│     └───as3unitylib.cswc  
>├─── obj  
>│     └───&lt;projectname>Config.xml  
>├─── src  
>│     └───Main.as  
>├─── application.xml  
>├─── genapi.config.xml
>├─── ASRuntime_readme.txt  
>└─── &lt;projectname>.as3proj  

#### &lt;projectname>.as3proj ####
项目配置文件。  
其中配置了SDK的路径:  
````xml
    <output>
     ...  
     <movie preferredSDK="F:\ASTool_UnityTest\publish\SDK1.0.0" />
    </output>
````

工程中的源码路径和类库路径
````xml
    <classpaths>
        <!--src code-->
	    <class path="src" />
	    <!--api code-->
        <class path="F:\ASTool_UnityTest\publish\SDK1.0.0\as3_commapi\api" />
	    <class path="F:\ASTool_UnityTest\publish\SDK1.0.0\as3_commapi\sharpapi" />
	    <class path="as3_unity" />
    </classpaths>
````

#### bat ####
包含了几个有用的bat文件。  
1. CompileCode.bat 如果没有安装FlashDevelop或者没有安装JAVA,则运行此bat可以编译代码并发布到Unity工程。
2. CreateUnityAPI.bat 运行此bat可以重新创建Unity API。

#### lib/as3unitylib.cswc ####
当创建Unity API时会自动编译生成此文件，这是一个预编译的ActionScript3 API字节码文件。当编译ActionScript3工程时，编译器会加载此文件对代码进行类型检查等操作。

#### &lt;projectname>Config.xml ####
传递给编译器的工程配置文件。如果使用FlashDevelop,则每次编译时FlashDevelop会自动生成这个文件。但是，如果不使用FlashDevelop,则必须手工编辑此配置文件，然后用 bat/CompileCode.bat 进行编译。当然，第一次生成ActionScript3工程时，也会自动创建好这个文件，如果确保脚本代码都写在
src目录中，则无需对此进行修改。
````xml
    <source-path append="true">
      <path-element>F:\ASTool_UnityTest\AS3HotFixU47\HotFixProj\src</path-element>
      <path-element>F:\ASTool_UnityTest\publish\SDK1.0.0\as3_commapi\api</path-element>
      <path-element>F:\ASTool_UnityTest\publish\SDK1.0.0\as3_commapi\sharpapi</path-element>
      <path-element>F:\ASTool_UnityTest\AS3HotFixU47\HotFixProj\as3_unity</path-element>
      <path-element>D:\Program Files (x86)\FlashDevelop\Library\AS3\classes</path-element>
    </source-path>
````

#### src ####
源代码目录。

#### genapi.config.xml ####
API代码生成器的详细配置。这个工具可用来创建.net类库和ActionScript3之间交互的API。
{% post_link 1-4-0-linkcodegenconfig API配置说明 %}