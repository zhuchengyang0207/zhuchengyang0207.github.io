---
layout: post
title: zul_remote
date: 2019-8-1 21:26:46 +0800
tag: 虚拟软件发布会
---

* content
{:toc}

因为之前的`Teamviewer`被检测为商业用途了，故自己用`zul_socket`写一个极其简陋但是还是可以用的版本

[Download](https://github.com/zhuchengyang0207/resource/tree/master/zul_remote)

使用说明：

服务端：

- 默认开启`8000`端口
- 为了保证安全，在同一时间只允许一个用户连接
- 客户端打出的无法识别的命令会在服务端有提示

客户端：

- 如果在同一个局域网内可使用内网地址连接，否则请用外网地址
- 直接打bat命令就可以让这些命令跑在服务端上
- `#tran <filename>`就是把本地的文件转到服务端中（默认都存在根目录下）
- `#fetch <filaname>`就是把服务端根目录的文件发回来
- `#close`就是关闭连接，当然你也可以直接叉掉（出`bug`了我不管）