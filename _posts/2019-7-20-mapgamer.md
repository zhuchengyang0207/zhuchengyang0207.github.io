---
layout: post
title: MapGamer
date: 2019-7-20 14:54:44 +0800
tag: 虚拟软件发布会
---

* content
{:toc}

这个单元把`OpenGL窗口`划分成块状，以贴纹理

只有这么多功能了，`dll`版可能会出

[下载](https://github.com/zhuchengyang0207/resource/tree/master/zul_mapgamer)

还是只放上来接口部分：

```pascal
{$mode objfpc}
unit MapGamer;
interface
 uses gl,glu,glut,fpimage,fpreadbmp;
 type
  block_size=record
   width,height:longint;
  end;
  position=record
   x,y:longint;
  end;
  MapScreen=class(TObject)
    texture_list:array of gluint;//这个最好不要直接修改
    screen:block_size;
    box:array of array of longint;
    img_list:array of string;
    constructor create;
    destructor destroy;override;
    procedure reload(indx:longint);
    procedure display;
    procedure rearrange(x,y:longint);
    function check(x,y:longint):position;
  end;
```

`example`:
```pascal
uses MapGamer,gl,glu,glut;
var
 a:mapscreen;
 procedure display;cdecl;
 begin
  glclear(gl_color_buffer_bit);
  glenable(gl_texture_2d);
  a.display;
  gldisable(gl_texture_2d);
  glcolor3f(0,0,0);
  glbegin(gl_lines);
   glvertex2f(-1,-0.5);
   glvertex2f(1,-0.5);
   glvertex2f(-1,0);
   glvertex2f(1,0);
   glvertex2f(-1,0.5);
   glvertex2f(1,0.5);
   glvertex2f(-0.5,-1);
   glvertex2f(-0.5,1);
   glvertex2f(0,-1);
   glvertex2f(0,1);
   glvertex2f(0.5,-1);
   glvertex2f(0.5,1);
  glend;
  glflush;
  glutswapbuffers;
 end;
 procedure init;
 var
  i,j:longint;
 begin
  glclearcolor(0,0,0,0);
  a:=mapscreen.create;
  a.screen.height:=480;
  a.screen.width:=640;
  setlength(a.img_list,3);
  a.img_list[0]:='1.bmp';
  a.img_list[1]:='2.bmp';
  a.img_list[2]:='3.bmp';
  a.reload(0);
  a.reload(1);
  a.reload(2);
  a.rearrange(4,4);
  for i:=0 to 3 do
   for j:=0 to 3 do
    a.box[i,j]:=(i+j)mod 3;
 end;
 procedure reshape(x,y:longint);cdecl;
 begin
  glutreshapewindow(a.screen.width,a.screen.height);
 end;
 procedure mouse(but,sta,x,y:longint);cdecl;
 var
  pos:position;
 begin
  if (but=glut_left_button)and(sta=glut_down) then
   begin
    pos:=a.check(x,y);
    writeln(pos.x,' ',pos.y);
   end;
 end;
 procedure idle;cdecl;
 begin
  glutpostredisplay;
 end;
begin
 glutinit(@argc,argv);
 glutinitdisplaymode(glut_rgb or glut_double);
 glutinitwindowsize(640,480);
 glutinitwindowposition(100,100);
 glutcreatewindow('test');
 glenable(gl_texture_2d);
 init;
 glutdisplayfunc(@display);
 glutreshapefunc(@reshape);
 glutmousefunc(@mouse);
 //glutidlefunc(@idle);
 glutmainloop;
end.
```