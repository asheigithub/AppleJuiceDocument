---
title: 概述
categories:
  - 1_beginuse
date: 2018-03-19 19:21:45
tags:
---
#### 什么是Apple Juice? ####
Apple Juice是一个纯 .net2.0 代码实现的ActionScript3引擎。  
设计之初就是为了能够离线的编译ActionScript3代码，并且在任意有.net2.0运行环境的场合执行。  
目前，最典型的应用场合就是在Unity3D的应用中执行ActionScrpt3脚本。
#### 什么是ActionScript3 ####
ActionScript3就是事实上的ECMAScript4的唯一实现。主要运行于Adobe Flash Player和Adobe AIR环境。是一门面向对象的编程语言，同时又兼容JS的动态语言特性。我们用.net2.0 实现了一个ActionScript3引擎，可以在更多的环境下执行ActionScript3脚本。  
[这里是ActionScript3的语言参考](https://www.adobe.com/devnet/actionscript/learning.html)
#### 为什么要在Unity中运行 ####
Unity是一个跨平台的3D游戏引擎。它使用C#语言进行开发工作。本脚本引擎提供了让Unity执行ActionScript3代码的能力。并且不受其他环境因素限制，只要Unity发布的App可以运行，则ActionScript3就可以运行。
#### 工作原理 ####
Apple Juice提供了如下功能：
1. ActionScript3编译器。它离线编译您的ActionScript3代码，并生成一个二进制字节码文件。
2. 除了极个别特例外，所有ActionScript3语法都有支持。包括类型，接口，数据类型，编译时期强类型检查,dynamic动态类型，prototype原型链扩展等等。以下为明确不支持的语法特性:
- namespace ActionScript3中，命名空间可用于控制对类中的一组属性和方法的访问。本脚本系统仅支持public protected private 等内置访问修饰符，自定义namespace功能不支持。
- ECMAScript for XML(E4X) 内嵌XML以及E4X相关操作符等功能明确不支持。
3. Apple Juice ActionScript3脚本可以调用其他.net类库提供的公开功能。因此Unity的API也可被调用。由于ActionScript3本身拥有完整的编译时期类型检查和完整的类型继承系统，因此.net类库的结构可以被大部分完整的映射到ActionScript3中。Apple Juice提供了API代码生成工具，可以自动的将需要的API生成到ActionScript3工程。
4. 一旦API生成后，就可以在ActionScript3脚本中对Unity类库进行操作。编译器在编译时能做出类型检查，提前发现代码中的错误。强类型系统能提高脚本的执行性能。一旦编译通过，编译器会生成一个二进制字节码文件。这个文件可以直接部署到Unity工程中，也可以部署到网络上按需要下载。
5. ActionScript3运行时。是一个纯C#编写的类库，可以直接在Unity项目中使用。Unity启动时，先创建ActionScript3运行时实例，再注册上一步生成的API,然后加载编译好的二进制字节码，即可执行脚本。
> 这个二进制字节码可以从任意地方加载，比如从网络下载，等等
 

#### 下载 ####
可以在这里下载最新的运行时和Unity插件。
[https://github.com/asheigithub/apple-juice-actionscript](https://github.com/asheigithub/apple-juice-actionscript)
#### 需要的环境 ####
要运行Apple Juice很简单，不过在用它编写Unity脚本前，要注意最好有以下环境:  
- [FlashDevelop](http://www.flashdevelop.org/ "FlashDevelop") 推荐的ActionScript3 IDE.
- [JRE](https://www.java.com) FlashDevelop正常编译需要JRE支持。
- .net framework2.0以上。目前的Windows版本应该都是自带的。
- Windows. 目前Apple Juice的Unity编译工具流程时假设在Windows上运行的
- Unity 4.X以上版本推荐5.X以上版本。 插件中的Demo场景是搭建于Unity5.6的，但是Apple Juice ActionScript3引擎确实能在Unity 4.7上正常运行。

> 其中JAVA和FlashDevelop不是必须的，如果不安装IDE,则您需要用其他任意文本编辑器来编写代码，并手动执行一个编译的批处理命令来进行编译和发布。但是，FlashDevelop可以提供如智能感知，自动补全，错误提示，代码格式化等等方便的功能。为了方便起见，建议安装JRE和FlashDevelop。

