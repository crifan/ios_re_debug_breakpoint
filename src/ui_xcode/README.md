# Xcode图形界面中的断点

* Xcode特有的
  * 当断点发生时，支持更多的设置
    * ignore
      * 忽略几次之后，再触发断点
    * action
      * 当触发断点时，执行额外某些动作
        * 举例
          * 触发某个音乐文件，比如响铃一声之类的
          * Action=`Debug Command`为`po indexPath`去打印当前索引值
            * ![xcode_br_action_cmd_po_indexpath](../assets/img/xcode_br_action_cmd_po_indexpath.png)
  * 全局的异常类的断点
    * Xcode中，新增断点时，可以看到其他更多选项
      * ![xcode_add_br_global](../assets/img/xcode_add_br_global.png)
    * 包括
      * `Swift Error Breakpoint`
        * 含义：当有`Swift`代码方面的错误，会触发断点
        * ![xcode_swift_error_br](../assets/img/xcode_swift_error_br.png)
      * `Exception Breakpoint`=`Objective-C Exceptions Breakpoint`
        * 含义：当有`Objective-C`方面的异常，会触发断点
        * ![xcode_objc_excepition_br](../assets/img/xcode_objc_excepition_br.png)
      * `Runtime Issue Breakpoint`
        * 含义：当有`Objective-C`的运行时（Runtime）方面的问题，会触发断点
        * ![xcode_runtime_issue_br](../assets/img/xcode_runtime_issue_br.png)
      * `Constraint Error Breakpoint`
        * 含义：当有（比如iOS中布局等方面的）限制（不满足）方面的错误时，触发异常？
        * ![xcode_constraint_error_br](../assets/img/xcode_constraint_error_br.png)
      * `Test Failure Breakpoint`
        * 含义：当项目代码中有测试代码时，且运行时测试代码运行发现条件不满足，测试失败时，触发断点
        * ![xcode_test_failure_br](../assets/img/xcode_test_failure_br.png)
