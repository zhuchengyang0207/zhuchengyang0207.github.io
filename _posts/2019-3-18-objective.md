---
layout: post
title: Pascal面向对象
date: 2019-3-18 16:50:09 +0800
tag: FreePascal语法
---

* content
{:toc}

# **Pascal类与对象**

众所周知，Pascal是一种面向过程的语言。然而，Pascal也是支持面向对象的。

### 0x00 面向对象是什么？

出门左转[百度](https:\\www.baidu.com)

### 0x01 如何创建类和对象？

我们可以这样：
```pascal
type
    MyClass=class
        ......
    end;
var
    MyObject:MyClass;
begin
    MyObject:=MyClass.Create;
    ....
end.
```
正如你所看到的，Pascal中的对象在定义时不会自动创建，需要在用时手动Create。这也避免了不必要的内存浪费。

### 0x02 继承

Pascal中的继承大部分与其他语言相同，只是定义有些区别：
```pascal
type
    Class1=class
        ......
    end;
    Class2=class(Class1)
        ......
    end;
```
这时Class2就会继承Class1

### 0x03 泛型

Pascal中的泛型与C++中的泛型不同，而与Java中的泛型相似，如：
```pascal
type
    generic Class1&lt;T&gt;=class
        a:T;
        ......
    end;
    Class1Int=specialize Class1&lt;longint&gt;;
var
    Object1:Class1Int;
```
