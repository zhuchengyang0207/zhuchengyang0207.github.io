---
layout: post
title: http协议_对于zul_server的开源
date: 2019-4-23 21:55:15 +0800
tag: http协议详解
---

* content
{:toc}

其实我觉得开源比较好。。。

于是把这次`zul_server`的最新代码放上来大家参考一下吧

转载请注明出处

上次那个有`bug`的就不放上来丢人现眼了。。。

```pascal
{$mode objfpc}
uses zul_socket,windows;
var
 trdno:dword;
 server:Tserver;
 b:Vlabel;
 port:Vport;
 procedure responder;stdcall;
 var
  bb:Vlabel;
  s,path,resp:ansistring;
  fin:text;
  ch:char;
 begin
  bb:=b;
  repeat
   s:=server.recv(bb);
  until s<>'';
  delete(s,length(s)-5,6);
  path:=copy(s,pos('/',s),pos('HTTP',s)-pos('/',s)-1);
  if path[1]='/' then
   delete(path,1,1);
  if (path='')or(path[length(path)]='/') then
   path:=path+'index.html';
  writeln('GET ',path);
  if pos('.css',path)<>0 then
   resp:='HTTP/1.1 200 OK'+#13#10+'Content-Type: text/css'+#13#10#13#10
  else
   if pos('.js',path)<>0 then
    resp:='HTTP/1.1 200 OK'+#13#10+'Content-Type: application/x-javascript'+#13#10#13#10
   else
    if pos('.jpg',path)<>0 then
     resp:='HTTP/1.1 200 OK'+#13#10+'Content-Type: application/x-jpg'+#13#10#13#10
    else
     resp:='HTTP/1.1 200 OK'+#13#10+'Content-Type: text/html'+#13#10#13#10;
  try
   assign(fin,path);
   reset(fin);
   while not eof(fin) do
    begin
     read(fin,ch);
     resp:=resp+ch;
    end;
   close(fin);
  except
   resp:=resp+'<p>HTTP served by zul_server</p>'+#13#10+'<h1>404 !found</h1>';
  end;
  server.send(resp,bb);
  server.close(bb);
 end;
 procedure delay(n:longint);
 var
  i:longint;
 begin
  for i:=1 to 100000000*n do;
 end;
begin
 if paramcount=0 then
  begin
   writeln('This is a html server made by ZCY');
   writeln('Copyright 2019');
   writeln('FOR EXAMPLE input in cmd:');
   writeln('"zul_server 8080" means opening the port of 8080');
  end;
 val(paramstr(1),port);
 server:=Tserver.create(port);
 writeln('opened the port of:',port);
 server.init;
 writeln('init success');
 while true do
  begin
   b:=server.accept;
   createthread(nil,0,@responder,nil,0,trdno);
   delay(1);//防止服务器超负荷
  end;
end.
```