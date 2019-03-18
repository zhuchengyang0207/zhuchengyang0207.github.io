---
layout: post
title: Pascal匿名函数
date: 2019-3-18 21:47:20 +0800
tag: FreePascal语法
---

* content
{:toc}

# **Pascal匿名函数**

### 0x01 引子

假设你写了一个快排，需要支持从大到小和从小到大排序，大多数人可能会这么写：

```pascal
	procedure qsort1(l,r:longint);
	.....
	procedure qsort2(l,r:longint);
	.....
```
这岂不是很烦?

匿名函数，就可以解决这个问题。

-----

### 0x01 匿名函数的作用

匿名函数，本质上是一个指针，指向一个函数或过程。需要时，可以将它作为参数传入另一个函数，在另一个函数中调用。

------

### 0x02 如何使用匿名函数

<span style="color:#FF0000">注意！一定要加{$MODE DELPHI（或OBJFPC）}！否则你可能会为一个编译错误调试半小时!</span>

 · 首先，你需要定义一个类型，such as:
```pascal
type
	cmpfunc=function(a,b:longint):boolean;
```
 · 然后，你需要几个这种定义的函数，如：
```pascal
function cmp1(a,b:longint):boolean;
begin
	exit(a>b);
end;
function cmp2(a,b:longint):boolean;
begin
	exit(a<b);
end;
```
 · 最后，你就可以在快排里使用了。完整代码：
```pascal
{$MODE DELPHI}
type
	cmpfunc=function(a,b:longint):boolean;
var
	a:array[1..100]of longint;
procedure qsort(l,r:longint;cmp:cmpfunc);
var
	i,j,m,t:longint;
begin
	i:=l;
	j:=r;
	m:=a[(i+j)div 2];
	repeat
		while cmp(a[i],m) do
			i:=i+1;
		while cmp(m,a[j]) do
			j:=j-1;
		if(i<=j)then begin
			t:=a[i];
			a[i]:=a[j];
			a[j]:=t;
			i:=i+1;
			j:=j-1;
		end;
	until i>j;
	if(i<r)then
		qsort(i,r,cmp);
	if(l<j)then
		qsort(l,j,cmp);
end;
function cmp1(a,b:longint):boolean;
begin
	exit(a>b);
end;
function cmp2(a,b:longint):boolean;
begin
	exit(a<b);
end;
var
	i,n:longint;
	t:char;
begin
	readln(n);
	for i:=1 to n do
		read(a[i]);
	readln;
	readln(t);
	if t='u' then
		qsort(1,n,cmp1);
	if t='d' then
		qsort(1,n,cmp2);
	for i:=1 to n do
		write(a[i],' ');
end.
```