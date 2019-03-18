---
layout: post
title: Pascal命令行参数
date: 2019-3-18 16:54:08 +0800
tag: FreePascal语法
---

* content
{:toc}

前言：如果你自己做了一个记事本，却不能设置它为打开方式，那岂不是很麻烦吗？

有的时候，你需要在命令行中传入一些参数

那么，你就需要用到`paramstr`了

用法:

`paramcount`就是参数的个数

`paramstr([position])`就是第`[position]`个参数，返回一个字符串

例子：
```pascal
var
	a,b:longint;
begin
	val(paramstr(1),a);
	val(paramstr(2),b);
	writeln(a+b);
end;
```
调用：

直接运行当然是会坏掉的,你需要打开控制台，输入`"文件名.exe 1 2"`

然后你就会惊讶的发现控制台里多了一个`3`

然后就解决了