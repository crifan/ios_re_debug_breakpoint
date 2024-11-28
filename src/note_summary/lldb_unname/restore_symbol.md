# 恢复符号表

对于之前所介绍的 [lldb无名函数](../../note_summary/lldb_unname/README.md) ，如果有机会，可以去恢复符号表，就可以：

把相关lldb无名函数，变成对应的有名字的函数了。

由此，则就可以通过函数名去添加断点了。

* 恢复符号表前后效果对比
  * 恢复符号表之前
    * 用函数名添加断点失败
      * Xcode中
        * `-[WARootViewController updateOfflineAssignABProperties]`
      * lldb命令行中
        * `b "-[WARootViewController updateOfflineAssignABProperties]"`
  * 恢复符号表之后
    * 可以用函数名添加断点
      * 详见
        * [恢复符号表前后对比 · iOS逆向分析：恢复符号表](https://book.crifan.org/books/ios_re_restore_symbol/website/before_after/)
* 如何恢复符号表
  * 概述
    * 推荐
      * 用我crifan的成套工具：[exportIDASymbol.py](https://github.com/crifan/restore-symbol/blob/master/tools/IDAScripts/export_ida_symbol/exportIDASymbol.py) + [crifan版restore-symbol](https://github.com/crifan/restore-symbol)
        * 可以一次性实现
          * 自动给很多函数命名
          * 恢复ObjC符号表
          * 恢复Block符号表
          * 其他细节优化
        * 详见
          * [crifan版restore-symbol · iOS逆向分析：恢复符号表](https://book.crifan.org/books/ios_re_restore_symbol/website/how/restore_symbol_crifan/)
    * 之前普通做法
      * [HeiTanBc的restore-symbol](https://book.crifan.org/books/ios_re_restore_symbol/website/how/restore_symbol_orig/)
  * 详见
    * [如何恢复符号表 · iOS逆向分析：恢复符号表](https://book.crifan.org/books/ios_re_restore_symbol/website/how/)
