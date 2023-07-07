# 常见错误

## Couldn't parse conditional expression error user expression has unknown return type cast the call to its declared return type

### 0 == strcmp($arg1, "/usr/lib/libsubstitute.dylib")

* 问题

XCode的lldb中，加断点的条件判断：

```bash
0 == strcmp($arg1, "/usr/lib/libsubstitute.dylib")
```
  * ![dlopen_condition_br_cast](../../../assets/img/dlopen_condition_br_cast.png)

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
  * ![added_cast_condition_work](../../../assets/img/added_cast_condition_work.png)

### 判断objc_alloc_init中+44行的汇编代码中参数是否是AADeviceInfo的实例

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
