# iOS逆向之动态调试：断点

* 最新版本：`v1.4`
* 更新时间：`20230711`

## 简介

介绍iOS逆向期间涉及到的断点的各方面的知识和心得。先对于iOS逆向调试期间的断点进行概览介绍。接着介绍Xcode图形界面和lldb命令行中的断点的通用逻辑，包括相关背景知识，比如如何得到函数的实际地址；以及介绍普通断点的通用逻辑；接着介绍普通断点如何加断点，以及断点的一些高级用法，比如指定模块、条件判断断点和对应例子；接着介绍Xcode图形界面和lldb命令行中的关于断点的各自特有的内容。包括lldb中breakpoint的help语法。接着整理常见问题，包括断点没触发、Xcode中hook代码的断点以及lldb；以及各种经验和心得，包括通用的，比如加断点导致卡死等；以及ObjC方面的断点，和Xcode的各种心得，包括如何同时调试多个断点、临时关闭所有断点、确认断点是否加上、确保断点能加上且能触发等；以及lldb无名函数和恢复符号表；最后贴上附录资料。

## 源码+浏览+下载

本书的各种源码、在线浏览地址、多种格式文件下载如下：

### HonKit源码

* [crifan/ios_re_debug_breakpoint: iOS逆向之动态调试：断点](https://github.com/crifan/ios_re_debug_breakpoint)

#### 如何使用此HonKit源码去生成发布为电子书

详见：[crifan/honkit_template: demo how to use crifan honkit template and demo](https://github.com/crifan/honkit_template)

### 在线浏览

* [iOS逆向之动态调试：断点 book.crifan.org](https://book.crifan.org/books/ios_re_debug_breakpoint/website/)
* [iOS逆向之动态调试：断点 crifan.github.io](https://crifan.github.io/ios_re_debug_breakpoint/website/)

### 离线下载阅读

* [iOS逆向之动态调试：断点 PDF](https://book.crifan.org/books/ios_re_debug_breakpoint/pdf/ios_re_debug_breakpoint.pdf)
* [iOS逆向之动态调试：断点 ePub](https://book.crifan.org/books/ios_re_debug_breakpoint/epub/ios_re_debug_breakpoint.epub)
* [iOS逆向之动态调试：断点 Mobi](https://book.crifan.org/books/ios_re_debug_breakpoint/mobi/ios_re_debug_breakpoint.mobi)

## 版权和用途说明

此电子书教程的全部内容，如无特别说明，均为本人原创。其中部分内容参考自网络，均已备注了出处。如发现有侵权，请通过邮箱联系我 `admin 艾特 crifan.com`，我会尽快删除。谢谢合作。

各种技术类教程，仅作为学习和研究使用。请勿用于任何非法用途。如有非法用途，均与本人无关。

## 鸣谢

感谢我的老婆**陈雪**的包容理解和悉心照料，才使得我`crifan`有更多精力去专注技术专研和整理归纳出这些电子书和技术教程，特此鸣谢。

## 其他

### 作者的其他电子书

本人`crifan`还写了其他`150+`本电子书教程，感兴趣可移步至：

[crifan/crifan_ebook_readme: Crifan的电子书的使用说明](https://github.com/crifan/crifan_ebook_readme)

### 关于作者

关于作者更多介绍，详见：

[关于CrifanLi李茂 – 在路上](https://www.crifan.org/about/)
