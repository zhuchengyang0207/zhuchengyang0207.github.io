---
layout: post
title: http协议应用
date: 2019-5-1 17:19:04 +0800
tag: http协议详解
---

* content
{:toc}

今天封装了理论上来说还比较全面的`http`协议中的内容，用来让代码实现`http`反馈

一句话就是用`pascal`写后台和前端

下面给出`zul_http.pas`的接口部分：

```pas
{$mode objfpc}
unit zul_http;
interface
 uses zul_socket,windows;
 type
  field=record
   key,value:ansistring;
  end;
  http_message_C=record
   method:ansistring;
   url:ansistring;
   agreement:ansistring;
   opt:array of field;
   data:ansistring;
  end;
  http_message_S=record
   agreement:ansistring;
   status_number:longint;
   status:ansistring;
   opt:array of field;
   data:ansistring;
  end;
  response=function (req:http_message_C):http_message_S;
 var
  resp:response;
  max_online:longint=0;
  port:Vport;//longint
 procedure start;
```

关于调用方法，我写了一个例子，大家可以参考下：

```pascal
{$mode objfpc}
uses zul_http;
var
 s:ansistring='';
 function display(req:http_message_C):http_message_S;
 var
  i:longint;
 begin
  writeln('Method:',req.method);
  writeln('Url:',req.url);
  writeln('Agreement:',req.agreement);
  writeln('Opt:');
  for i:=0 to length(req.opt)-1 do
   writeln(' key=',req.opt[i].key,'value=',req.opt[i].value);
  writeln('data=',req.data);
  
  display.agreement:='HTTP/1.1';
  display.status_number:=200;
  display.status:='OK';
  setlength(display.opt,1);
  display.opt[1].key:='Content-Type';
  display.opt[1].value:='text/html';
  if req.url='/' then //筛选
   s:=s+'!';
  display.data:='<html><body><h1>'+s+'</h1></body></html>';
 end;
begin
 resp:=@display;
 port:=8000;
 readln;
 start;
end.
```

关于`.ppu`和`.o`可以在[这里](https://github.com/zhuchengyang0207/resource/tree/master/zul_http)下载

所以`zul_server`并没有什么用。。。