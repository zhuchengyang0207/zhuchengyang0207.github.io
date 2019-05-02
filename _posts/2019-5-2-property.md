---
layout: post
title: property的使用
date: 2019-5-2 21:27:30 +0800
tag: FreePascal语法
---

* content
{:toc}

`pascal` `class`的`property`关键字

几段代码就能说明问题：

```pascal
{$mode objfpc}
type
 a=class(TObject)
  value:longint;
  property b:longint read value write value;
 end;
var
 c:a;
begin
 c:=a.create;
 c.value:=100;
 writeln(c.b);
 c.b:=123;
 writeln(c.value);
end.
```

```pascal
{$mode objfpc}
type
 a=class(TObject)
  value:longint;
  function get:longint;
  procedure change(qaq:longint);
  property b:longint read get write change;
 end;
 function a.get:longint;
 begin
  exit(value);
 end;
 procedure a.change(qaq:longint);
 begin
  value:=qaq;
 end;
var
 c:a;
begin
 c:=a.create;
 c.value:=100;
 writeln(c.b);
 c.b:=123;
 writeln(c.value);
end.
```

```pascal
{$mode objfpc}
type
 a=class(TObject)
  v:array[1..3]of longint;
  function get(indx:longint):longint;
  procedure change(indx,k:longint);//要修改成什么值放最后面
  property b[indx:longint]:longint read get write change;
 end;
 function a.get(indx:longint):longint;
 begin
  exit(v[indx]);
 end;
 procedure a.change(indx,k:longint);
 begin
  v[indx]:=k;
 end;
var
 c:a;
begin
 c:=a.create;
 c.v[1]:=100;
 writeln(c.b[1]);
 c.b[2]:=123;
 writeln(c.v[2]);
end.
```