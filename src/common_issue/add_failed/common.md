# 通用

## 函数名有误

* 现象：`The symbolic name is spelled correctly`
* 原因：函数名有误=函数名错了
* 解决办法：搞懂（尤其是iOS的ObjC的函数的写法）语法，把函数名写正确了
  * 举例
    * 把ObjC的加号误写成了减号
      * 错误写法：`+[AKSRPOperation performWithURL:SRPContext:error:]`
      * 正确写法：`-[AKSRPOperation performWithURL:SRPContext:error:]`
    * 把ObjC的函数末尾多写个了个冒号`:`
      * 错误写法：`-[__NSCFConstantString stringByAppendingString:]:`
      * 正确写法：`-[__NSCFConstantString stringByAppendingString:]`

### 特殊：带Deprecated的函数名

之前试过给ObjC函数：

```bash
+[AADeviceInfo(Deprecated) udid]
```

去加断点，发现加不上

后来才知道，其实是：

* 此处能加上断点，用的函数名是不带`Deprecated`字眼的
  ```bash
  +[AADeviceInfo udid]
  ```
* 如果起查找函数，可以发现底层函数就是带`Deprecated`字眼的函数 = 真正触发时，Xcode中显示的也是带Deprecated字眼的函数
  ```bash
  (lldb) image lookup -vn "+[AADeviceInfo udid]"
  1 match found in /Users/crifan/Library/Developer/Xcode/iOS DeviceSupport/15.0 (19A346)/Symbols/System/Library/PrivateFrameworks/AppleAccount.framework/AppleAccount:
          Address: AppleAccount[0x0000000191e0e558] (AppleAccount.__TEXT.__text + 176432)
          Summary: AppleAccount`+[AADeviceInfo(Deprecated) udid]
          Module: file = "/Users/crifan/Library/Developer/Xcode/iOS DeviceSupport/15.0 (19A346)/Symbols/System/Library/PrivateFrameworks/AppleAccount.framework/AppleAccount", arch = "arm64"
          Symbol: id = {0x00000473}, range = [0x0000000195ce6558-0x0000000195ce659c), name="+[AADeviceInfo(Deprecated) udid]"
  ```


## 符号不存在于当前库中

* 现象：`The symbol not exists in the library`=符号不存在于当前（所有的已加载的）库中
* 原因：hook的目标搞错了
* 解决办法：找对hook的目标
* 举例
  * 现象
    * ObjC中，调试`akd`时，函数`-[AKAppleIDAuthenticationContextManager shouldContinueWithAuthenticationResults:error:forContextID:completion:]`加不上断点
  * 原因
    * 该函数其实是存在于另外的二进制=库=app：`Preference`中
  * 解决办法
    * 需要把调试目标从`akd`改为`Preferences`才行

## 符号所在的二进制没有被加载

* 现象：`The library for the breakpoint is not loaded`=符号所在的二进制没有被加载
  * 举例
    * 抖音
      * `AwemeCore`中，已经没有符号表了，所以去给函数`-[AWEUserRecommendMutiTagsView followBtnClicked:]`加断点，加不上
        * ![xcode_add_breakpont_fail_followbtnclicked](../../assets/img/xcode_add_breakpont_fail_followbtnclicked.png)
   * `WhatsApp`
      * `WhatsApp`中，去给函数`-[WARegistrationManager performSameDeviceCheckForSession:updateRegistrationTokenIfNecessary:withCompletion:]`加断点，但加不上
        * ![xcode_add_breakpont_fail_waregistrationmanager](../../assets/img/xcode_add_breakpont_fail_waregistrationmanager.png)
* 原因：
  * 直接原因：对应的iOS的ObjC的二进制中，本身就没有该函数名=符号
  * 深层原因：因为（Mach-O）二进制中的符号表已经被去掉了
* 解决办法：恢复符号表
  * 详见
    * [恢复符号表](../../note_summary/lldb_unname/restore_symbol.md)
