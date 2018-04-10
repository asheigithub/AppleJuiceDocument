---
title: .net API的导出
categories:
  - 1_beginuse
  - 1_2useas3
date: 2018-03-20 20:04:22
tags:
---
.net API可以由SDK中的工具LinkCodeGen.exe进行导出。但是由于语言限制，某些类型是无法被导出的。
#### 不能被导出的类型 ####
- 不在任何命名空间下的类型不会被导出。
- 标记为System.ObsoleteAttribute的类型。这些类型被标记为过时的，有可能导致编译失败。因此，工具检查到这样的类型或成员时，将不会导出.
- System.Type类型。 System.Type类型和ActionScript3的Class有特殊关联,System.Type类型本身不会被导出。
- 非公开的类型和成员不会被导出。只有Public的类型才能导出。
- 泛型定义类型不会被导出。但是如果.net类库中有泛型类型，这个泛型类型是可以被导出的。例如 List&lt;T> 本身是不会被导出的，但是如果.net类库中有List&lt;String> 这样的类型，则List&lt;String>会被自动处理。  
- 如果某个类型继承自不可导出的类型，那么它也同样不能导出。

#### 不能被导出的成员 ####
- 非公开的成员不会被导出。
- 如果某个方法的参数或者返回值是不能导出的类型，则它不会被导出。
- 如果某个成员被标记为System.ObsoleteAttribute的，则它不会被导出。
- 如果某个方法是泛型定义方法，则它不会被导出。但是泛型方法是可以被导出的。例如 MonoBehaviour.GetComponent<T>,这个方法不会被导出。但是如果某个类型继承了MonoBehaviour,又定义了 GetComponent<Renderer>,则这个方法是可以被导出的。

#### 导出时的特殊处理 ####
- 泛型类型导出时，名字改为 &lt;TypeName> _Of_ &lt;GenericType>。例如  


```
UnityEngine.Events.UnityAction<System.Boolean> 
```
将导出为
```actionscript
public final class UnityAction_Of_Boolean extends system.MulticastDelegate implements system.ICloneable
{
}
```
- 方法重载。.net支持重载，ActionScript3则不支持重载。因此，当遇到这样的情况时，工具将会在重载的函数后面加上一个下划线_。例如，System.DateTime.GetDateTimeFormats有若干重载，则会导出为如下API:  


```actionscript
/**
* System.DateTime.GetDateTimeFormats
*return:
*   System.String[]
*/
[native,system_DateTime_getDateTimeFormats];
public final function getDateTimeFormats():_Array_;

/**
* System.DateTime.GetDateTimeFormats
*parameters:
*  provider : System.IFormatProvider
*return:
*   System.String[]
*/
[native,system_DateTime_getDateTimeFormats_];
public final function getDateTimeFormats_(provider:IFormatProvider):_Array_;

/**
* System.DateTime.GetDateTimeFormats
*parameters:
*  format : System.Char
*return:
*   System.String[]
*/
[native,system_DateTime_getDateTimeFormats__];
public final function getDateTimeFormats__(format:Char):_Array_;

/**
* System.DateTime.GetDateTimeFormats
*parameters:
*  format : System.Char
*  provider : System.IFormatProvider
*return:
*   System.String[]
*/
[native,system_DateTime_getDateTimeFormats___];
public final function getDateTimeFormats___(format:Char,provider:IFormatProvider):_Array_;
```

- 如果是构造函数重载，则保留找到的第一个构造函数，其他构造函数改名为constructor_。若有多个重载，则追加下划线。例如System.DateTime有许多构造函数，则导出为:  



```actionscript
//*********构造函数*******
[native,system_DateTime_constructor_];
public static function constructor_(ticks:Int64,kind:DateTimeKind):DateTime;

[native,system_DateTime_constructor__];
public static function constructor__(year:int,month:int,day:int):DateTime;

[native,system_DateTime_constructor___];
public static function constructor___(year:int,month:int,day:int,calendar:system.globalization.Calendar):DateTime;

[native,system_DateTime_constructor____];
public static function constructor____(year:int,month:int,day:int,hour:int,minute:int,second:int):DateTime;

[native,system_DateTime_constructor_____];
public static function constructor_____(year:int,month:int,day:int,hour:int,minute:int,second:int,kind:DateTimeKind):DateTime;

[native,system_DateTime_constructor______];
public static function constructor______(year:int,month:int,day:int,hour:int,minute:int,second:int,calendar:system.globalization.Calendar):DateTime;

[native,system_DateTime_constructor_______];
public static function constructor_______(year:int,month:int,day:int,hour:int,minute:int,second:int,millisecond:int):DateTime;

[native,system_DateTime_constructor________];
public static function constructor________(year:int,month:int,day:int,hour:int,minute:int,second:int,millisecond:int,kind:DateTimeKind):DateTime;

[native,system_DateTime_constructor_________];
public static function constructor_________(year:int,month:int,day:int,hour:int,minute:int,second:int,millisecond:int,calendar:system.globalization.Calendar):DateTime;

[native,system_DateTime_constructor__________];
public static function constructor__________(year:int,month:int,day:int,hour:int,minute:int,second:int,millisecond:int,calendar:system.globalization.Calendar,kind:DateTimeKind):DateTime;

[native,system_DateTime_ctor];
public function DateTime(ticks:Int64);
```

- 操作符重载。 .net支持操作符重载。对于这样的类型，导出后同样保留了可用的操作符重载。例如System.Decimal,导出后保留了各种操作符重载，在脚本中同样可用。  


```actionscript
/**
 * 计算 d1 % d2
 */
[native,static_system_Decimal_op_Modulus];
public static function op_Modulus(d1:Decimal,d2:Decimal):Decimal;

[operator,"%"];
[native,static_system_Decimal_op_Modulus];
private static function _operator_op_Modulus(d1:Decimal,d2:Decimal):Decimal;

```
如下代码
```actionscript
var d1:Decimal = new Decimal(77);
var d2:Decimal = new Decimal(65);

trace("d1 % d2 =",d1 % d2);
```
执行结果
```
d1 % d2 = 12
```

> 委托的操作符重载有 +和-。因此+=和-=是可用的。但是需要注意一点，如果委托变量为null,则不能直接使用+=,必须使用=进行赋值。当变量不为null时，可用+=,-=来操作多路委托。

