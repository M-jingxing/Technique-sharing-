# Nodejs 文件系统介绍

本文重点介绍 nodejs 文件系统的实现原理， 而非 API。
如需获取相关API， 可查看官方文档 https://nodejs.org/api/fs.html


# 核心模块 fs

在使用文件相关 API 时， 都需要引入 fs 模块， 如 

`const fs = require('fs')`


我们在引入 fs 模块时 并不需要 加上 `'./'` 之类，表示指定路径。 这是因为 fs 作为一个nodejs 核心模块，在编译成可执行文件时就被编译成了二进制文件， 编译执行成功的模块对象 缓存在 NativeModule._cache 对象上， 文件模块则缓存在Module._cache 上





# Unix 的 I/O 模型


# I/O 多路复用

三种类型： Select、 Poll、 Epoll


