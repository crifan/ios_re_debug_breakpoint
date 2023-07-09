# 图形界面：Xcode中断点

* Xcode特有的
  * 当断点发生时，支持更多的设置
    * ignore
      * 忽略几次之后，再触发断点
    * action
      * 当触发断点时，执行额外某些动作
        * 举例
          * 触发某个音乐文件，比如响铃一声之类的
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

## 临时关闭所有断点

* （一键）临时关闭所有断点 + 后续恢复所有断点
  * 背景：有个需求，调试时，某些时候
    * 比如
      * 希望继续让程序尽快运行，但又不希望已加的多个断点，对程序产生影响
        * 比如
          * 某些会触发多次（设置加了额外判断条件）的断点，会降低程序运行速度，甚至导致程序卡死
    * 所以希望，可以临时的，暂时禁用所有断点，且待会在需要时，可以恢复所有断点
    * 即：临时禁用所有断点（且之后可以恢复被临时禁用的所有断点）
  * 解决办法
    * 推荐方法
      * Xcode中下方的调试控制面板中的：断点控制按钮，点击可以实现
        * 对应的效果
          * `Deactive breakpoints`=临时暂停所有断点（且保留之前的断点的状态）
            * 鼠标移动上去会显示：`Deactive breakpoints`
              * ![xcode_enable_all_br](../assets/img/xcode_enable_all_br.png)
          * `Active breakpoints`=激活（重新启用=重新开启）所有断点（且恢复之前的断点的状态）
            * 鼠标移动上去会显示：`Active breakpoints`
              * ![xcode_disabled_all_br](../assets/img/xcode_disabled_all_br.jpg)
        * 优势是：可以保持之前所有的断点的状态（是激活还是非激活）
    * 凑合用（但效果不够好）的方法
      * Xcode中的`Breakpoint Navigator`->右键对应（正在调试的项目），即可出现右键菜单，支持
        * 关闭或开启所有断点
          * 关闭所有断点=`Disable Breakpoints`
            * ![xcode_all_br_navi_dis_all](../assets/img/xcode_all_br_navi_dis_all.png)
          * 恢复（开启）所有断点 = `Enable Breakpoints`
            * ![xcode_all_br_navi_en_all](../assets/img/xcode_all_br_navi_en_all.png)
        * 缺点是：是`Disable`/`Enable`，关闭或开启，就：丢失了之前的各个断点的状态
          * 举例：
            * 之前有175个断点，其中只有85个是激活状态，剩余均为未激活，用了`Disable`/`Enable`后，结果全部175个断点就全是激活了（其中激活了很多，想要暂时关闭，但却又不想要删除的，原先是非激活状态的断点）
