# 其他


## WARNING:  Unable to resolve breakpoint to any actual locations

* 问题

`lldb`中给ObjC函数加断点：

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

至于后续是否触发，则取决于是否真正调用到了。和此处加断点，没关系了。
