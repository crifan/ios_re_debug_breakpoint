# lldb中的无名函数

iOS逆向期间，常会遇到：`lldb无名函数`=名字是`___lldb_unnamed_symbolxxx`的函数，其中`xxx`是数字编号

根据经验，可以分为2类：

* lldb无名函数
  * 函数名（末尾）中包含模块名的：`___lldb_unnamed_symbolxxx$$BinaryFileName`
    * 举例
      * `___lldb_unnamed_symbol2575$$akd`
      * `___lldb_unnamed_symbol148$$AwemeCore`
  * 函数名中不包含模块名的
    * 举例
      * `___lldb_unnamed_symbol972`

对此，给这类函数加断点，略有不同：

* 给 函数名（末尾）中包含模块名的 加断点：
  * 方式1：直接用函数名加断点即可
    * 说明：因为函数名已包含模块，所以能定位到对应函数
  * 方式2：计算出函数实际地址，再通过实际地址加断点
* 给 函数名中不包含模块名的 加断点
  * 需要指定模块名才可以
    * 具体做法，详见：[指定模块](../../../common_logic/advanced_usage/module.md)

下面举例说明：

## 举例

### 函数名（末尾）中包含模块名的lldb无名函数

#### `___lldb_unnamed_symbol2575$$akd`

想要给 `___lldb_unnamed_symbol2575$$akd` 加断点：

##### 方式1：直接通过（无名函数的）函数名

* Xcode图形界面中
  * 添加断点`___lldb_unnamed_symbol2575$$akd`
* lldb命令行中
  ```bash
  (lldb) b ___lldb_unnamed_symbol2575$$akd
  Breakpoint 1: where = akd`___lldb_unnamed_symbol2575$$akd, address = 0x0000000102e70460
  ```

##### 方式2：通过无名函数的实际地址

如[得到函数的实际地址](../../../common_logic/background/get_func_real_addr.md)所介绍，先去：

* 计算出模块的ALSR基地址

```bash
(lldb) image list -o -f | grep akd
[  0] 0x0000000002dd0000 /System/Library/PrivateFrameworks/AuthKit.framework/akd(0x0000000102dd0000)
```

得到：

* akd的ALSR：`0x0000000002dd0000`

再去：

* 加了VM address后是：`0x0000000102dd0000`

当前无名函数的二进制内偏移量=文件偏移量=file offset：`0xA0460`

算出最终的：

* 函数的实际地址：`0x102E70460` == `0x0000000102e70460`

如果要去去通过地址加断点，就是：

```bash
b 0x0000000102e70460

br s -a 0x0000000102e70460
```
