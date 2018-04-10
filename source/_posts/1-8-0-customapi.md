---
title: 使用自定义API
categories:
  - 1_beginuse
  - 1_2useas3
date: 2018-03-21 17:27:43
tags:
---
您也可以在Unity工程中创建您自己的API代码。SDK中的工具LinkCodeGen.exe同样能将您自定义的类库导出为可与ActionScript3交互的API。再次说明要注意的是，只有Public的类型，并且处在某个命名空间下的类型才会被导出，否则是不会导出的。
#### 自定义API示例 ####
以下步骤演示如何自定义一个类库。
1. 打开Unity工程，在Assets目录下，右键菜单 "Create/C# Script",创建一个TestAPI的脚本。
2. 将TestAPI.cs的内容改为如下代码:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

namespace myapi
{
	public class TestAPI 
	{
		public int AddTest(int a, int b)
		{
			return a + b;
		}
		
	}
}
```
3. 打开ActionScript3项目所在文件夹。(可点击菜单 "ASRuntime/Open ActionScript3 Project Folder" 来打开)
4. 用FlashDevelop打开ActionScript3项目。
5. 找到 "bat/CreateUnityAPI.bat",单机右键，从弹出菜单中选择执行。等待API生成完毕。
6. 打开Main.as，找到第100行左右的 onclick 方法。将它改为如下代码:

```actionscript
		private function onclick()
		{
			var testAPI:TestAPI = new TestAPI();
			Text( GameObject.find("Canvas/Text").getComponent(Text)).text = "100+50" + testAPI.addTest(100, 50);
			
			
			//isstop = !isstop;
			//trace("isstop?" , isstop);
			//
			//Text( GameObject.find("Canvas/Text").getComponent(Text)).text = "isstop?" + isstop
			//
			//+"我在AS3中热更"
			//;
			
		}
```
7. 点击编译。
8. 回到Unity,点击运行，然后点击按钮，即可看到我们的API被成功调用了。

![运行结果](/apple-juice-actionscript/doc_cn/images/customapi/myapi1.jpg)