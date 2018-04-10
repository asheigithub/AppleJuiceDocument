---
title: 创建Unity工程和脚本工程
categories:
  - 1_beginuse
  - 1_1configureproject
date: 2018-03-19 19:22:04
tags:
---
先下载最新的发布包，并解压。解压后的目录内容如下:
> .
> ├─── SDK[X.X.X]   
> └─── UnityPackage  
>       └───AS3RuntimeForUnity.unitypackage


#### SDK[X.X.X]
>是一个自定义的 AIRSDK.可被FlashDevelop识别并加载，使用它来进行代码的编译和发布。

#### UnityPackage
>内含Unity插件包 "AS3RuntimeForUnity.unitypackage"。  

项目创建步骤:
1. 先创建一个Unity工程。然后导入UnityPackage内的Unity插件包。
2. 点击菜单 ASRuntime/Create ActionScript3 FlashDevelop HotFixProj。
3. 根据提示先定位SDK的位置。
4. 然后根据提示创建AS3热更新项目。项目创建完成后，会根据默认配置生成Unity的API。  
>ActionScript3项目要求创建在一个空白的文件夹中。

项目测试：
1. 在Unity工程中，打开测试场景"Assets/HotFixDemoScene1.scene"。
2. 在刚才生成的ActionScript3工程目录中，用FlashDevelop打开<protname>.as3proj 工程文件。
3. 在AS3工程中修改代码
4. 点击FlashDevelop的编译按钮,进行编译
5. 回到Unity工程，点击播放，即可看到修改的结果。


**下面是一个gif动态示意图，如果没有显示，请稍等片刻**
![动态说明](/apple-juice-actionscript/doc_cn/images/as3_unity_demo4.gif)