# 常见问题

## Xcode设置

### was compiled with optimization - stepping may behave oddly; variables may not be available

* 问题

Xcode中编译的iOSOpenDev的dylib插件，去调试时，右下角调试窗口输出警告信息：

`jailAppleAccount.dylib was compiled with optimization - stepping may behave oddly; variables may not be available.`

![xcode_compiled_with_optimization](../assets/img/xcode_compiled_with_optimization.png)

* 原因

Xcode在编译代码时（默认就）启用了优化：

`Xcode`->`Targets`->`xxx`->`Build Settings`->`Apple Clang - Code Generation`->`Optimization Level`中的`Release`，默认是：`Fastest, Smallest [-Os]`

含义是：程序尽可能的快，文件尽可能的小。

涉及到的内部优化，就可能会把部分调试相关内容优化去掉，从而可能导致调试时出现上述的现象：

* stepping may behave oddly
  * 单步（进入或跳过）调试时，会出现奇怪的现象
    * 因为部分代码可能被优化掉了
      * 从而该代码单步执行可能执行不到，或者和源码对不上
* variables may not be available
  * 部分变量可能会被优化掉

* 解决办法：去掉优化，保留调试信息
* 具体步骤：
  * 把上述中，`Release`的`Optimization Level`的值改为`None [-O0]`
    * 图
      * ![xcode_optimization_level_values](../assets/img/xcode_optimization_level_values.png)
      * ![xcode_optimization_level_none_O0](../assets/img/xcode_optimization_level_none_O0.png)
    * 表示：不（做任何）优化
      * 从而保留了调试信息
        * 后续
          * 单步调试就正常了，和代码对得上了
          * 变量值也不会丢失了

## 条件判断断点

### Couldn't parse conditional expression error user expression has unknown return type cast the call to its declared return type

#### 0 == strcmp($arg1, "/usr/lib/libsubstitute.dylib")

* 问题

XCode的lldb中，加断点的条件判断：

```bash
0 == strcmp($arg1, "/usr/lib/libsubstitute.dylib")
```
  * ![dlopen_condition_br_cast](../assets/img/dlopen_condition_br_cast.png)

报错：

```bash
Stopped due to an error evaluating condition of breakpoint 10.2: "0 == strcmp($arg1, "/usr/lib/libsubstitute.dylib")"
Couldn't parse conditional expression:
error: <user expression 7>:1:6: 'strcmp' has unknown return type; cast the call to its declared return type
0 == strcmp($arg1, "/usr/lib/libsubstitute.dylib")
     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```

* 原因

此处无法识别`strcmp`的返回值类型`size_t`

注：`size_t`本身一般是`unsigned int`或`unsigned long`

* 解决办法

加上强制转换，为普通的int类型：

```bash
0 == (int)strcmp($arg1, "/usr/lib/libsubstitute.dylib")
```
  * ![added_cast_condition_work](../assets/img/added_cast_condition_work.png)

#### 判断objc_alloc_init中+44行的汇编代码中参数是否是AADeviceInfo的实例

* 写法1：`(bool)[$x0 isKindOfClass: objc_getClass("AADeviceInfo")]`

之前经过`lldb`命令测试发现`objc_getClass`返回的结果类型无法识别：

```bash
(lldb) po [$x0 isKindOfClass: objc_getClass("AADeviceInfo")]
error: expression failed to parse:
warning: <user expression 25>:1:2: receiver type 'unsigned long' is not 'id' or interface pointer, consider casting it to 'id'
[$x0 isKindOfClass: objc_getClass("AADeviceInfo")]
 ^~~
error: <user expression 25>:1:21: 'objc_getClass' has unknown return type; cast the call to its declared return type
[$x0 isKindOfClass: objc_getClass("AADeviceInfo")]
```

所以最后要改为：

```bash
(bool)[$x0 isKindOfClass: (Class)objc_getClass("AADeviceInfo")]
```

才至少确保语法上是正确的（至少po可以正常解析执行）

```bash
(lldb) po [$x0 isKindOfClass: (Class)objc_getClass("AADeviceInfo")]
 nil
```

* 写法2：`[$x0 isKindOfClass: (Class)objc_getClass("AADeviceInfo")]`

表达式最前面没有加上bool强制转换，所以Xcode无法识别（是条件判断类型的语句）

* 写法3：`(bool)[NSStringFromClass($x0) isEqualToString: @"AADeviceInfo"]`
  * 此处已经是`AADeviceInfo`的`Instance`，而不是`Class`，所以不能用`NSStringFromClass`
    * 因为函数定义是：
      * `NSString * NSStringFromClass(Class aClass);`
    * `NSStringFromClass`的参数，应该是`Class`，而非`Instance`
  * 详见：
    * 【已解决】iOS的ObjC中如何获取Class类名


## 其他

### WARNING:  Unable to resolve breakpoint to any actual locations

* 问题

`lldb`中给`ObjC`函数加断点：

```bash
br s -n "-[NSMutableURLRequest setValue:forHTTPHeaderField:]"
```

报错：`WARNING:  Unable to resolve breakpoint to any actual locations.`

* 直接原因

当前被调试的二进制中，的确没有这个类的函数，可供加断点，所以报错。

* 深层次原因

此处的被lldb调试的对象，iOS的app：`Preferences`（或者是此刻iOS中系统的库），是没有包含调试的信息的，或者是经过特殊处理了，去掉了可以调试的信息

导致此处ObjC的类的函数：

`-[NSMutableURLRequest setValue:forHTTPHeaderField:]`

内部找不到，所以就加不上断点。

* 解决办法：没法解决
* 规避办法

此处特殊的，可以去找：其他的，个别的，继承了该类的函数，去加断点

比如：

```bash
image lookup -rn "setValue:forHTTPHeaderField:"
```

找到的部分类，有此函数，所以可以去加断点：

```bash
br s -n "-[SSMutableURLRequestProperties setValue:forHTTPHeaderField:]"
```
