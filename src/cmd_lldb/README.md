# 命令行：lldb中断点

* lldb命令行中
  * 加断点
    * 普通断点
      * 通过函数名添加
        * `n`=`name`
          ```bash
          br s -n FunctionName
          ```
          * 举例
            * iOS的ObjC函数
              ```bash
              breakpoint set -n "-[AAUISignInController _performAuthenticationForAccount:serviceType:inViewController:completion:]"
              ```
        * 或
        * `s`=`symbol`
          ```bash
          br s -n FunctionName
          ```
      * 通过地址添加
        ```bash
        br s -a FunctionAddress
        ```
        * 完整写法
          ```bash
          breakpoint set --address FunctionAddress
          ```
        * 举例
        ```bash
        (lldb) br s -a 0x0000000100d5bbb0
        Breakpoint 4: where = RzGame`___lldb_unnamed_symbol275$$RzGame + 685120, address = 0x0000000100d5bbb0
        ```
  * 查看已有断点（的列表）
    ```bash
    br list
    ```
    * 注：`br list`=`breakpoint list`
