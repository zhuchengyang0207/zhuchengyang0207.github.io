---
layout: post
title: 物理单元
date: 2019-8-1 21:26:46 +0800
tag: 虚拟软件发布会
---

* content
{:toc}

既然网上有很多比这个好的单元，就开源吧qaq

`zul_PE.pas` ：

```pascal
{
    made on 2019-7-31
    for zul_physical_engine
}
{$mode objfpc}
unit zul_PE;
interface
 type
  vct=class(TObject)
   x,y,z:extended;
   constructor create;
   constructor create(_x,_y,_z:extended);
   destructor destroy;override;
   procedure add(a:vct);
   procedure add(_x,_y,_z:extended);
   procedure sub(a:vct);
   procedure sub(_x,_y,_z:extended);
   procedure mul(a:extended);
   procedure mul(a:vct);
   procedure mul(_x,_y,_z:extended);
   procedure divi(a:extended);
   function dis(a:vct):extended;
   function length:extended;
   procedure simplify;
  end;
  obj=class(TObject)
   p,a,v:vct;//position acceleration velocity
   t,m:extended;//time and mass
   constructor create;
   destructor destroy;override;
   procedure add_v(now:vct);
   procedure add_a(now:vct);
   procedure add_f(now:vct);
   procedure add_p(now:vct);
   procedure sub_v(now:vct);
   procedure sub_a(now:vct);
   procedure sub_f(now:vct);
   procedure sub_p(now:vct);
   procedure mul_v(now:extended);
   procedure mul_a(now:extended);
   procedure divi_v(now:extended);
   procedure divi_a(now:extended);
   procedure settime(now:extended);
   function gettime:extended;
   procedure calc(now:extended);//calc to time "now"
   function get_p:vct;
   function get_a:vct;
   function get_v:vct;
   procedure add_m(now:extended);
   procedure sub_m(now:extended);
   procedure mul_m(now:extended);
   procedure divi_m(now:extended);
   procedure add_ug(c:obj);//universal gravitation
  end;
 function make_vct(_x,_y,_z:extended):vct;
 function copy(a:obj):obj;
 function copy(a:vct):vct;
 const
  g=0.0000001;//万有引力常数
implementation
 constructor vct.create;
 begin
  x:=0;
  y:=0;
  z:=0;
 end;
 constructor vct.create(_x,_y,_z:extended);
 begin
  x:=_x;
  y:=_y;
  z:=_z;
 end;
 destructor vct.destroy;
 begin
 end;
 procedure vct.add(a:vct);
 begin
  x:=x+a.x;
  y:=y+a.y;
  z:=z+a.z;
 end;
 procedure vct.add(_x,_y,_z:extended);
 begin
  x:=x+_x;
  y:=y+_y;
  z:=z+_z;
 end;
 procedure vct.sub(a:vct);
 begin
  x:=x-a.x;
  y:=y-a.y;
  z:=z-a.z;
 end;
 procedure vct.sub(_x,_y,_z:extended);
 begin
  x:=x-_x;
  y:=y-_y;
  z:=z-_z;
 end;
 procedure vct.mul(a:extended);
 begin
  x:=x*a;
  y:=y*a;
  z:=z*a;
 end;
 procedure vct.mul(a:vct);
 begin
  x:=x*a.x;
  y:=y*a.y;
  z:=z*a.z;
 end;
 procedure vct.mul(_x,_y,_z:extended);
 begin
  x:=x*_x;
  y:=y*_y;
  z:=z*_z;
 end;
 procedure vct.divi(a:extended);
 begin
  if a=0 then
   writeln('Error: 200 Division By Zero!')
  else
   begin
    x:=x/a;
    y:=y/a;
    z:=z/a;
   end;
 end;
 function vct.dis(a:vct):extended;
 begin
  exit(sqrt(sqr(x-a.x)+sqr(y-a.y)+sqr(z-a.z)));
 end;
 function vct.length:extended;
 begin
  exit(sqrt(sqr(x)+sqr(y)+sqr(z)));
 end;
 procedure vct.simplify;
 begin
  divi(length);
 end;
 constructor obj.create;
 begin
  p:=vct.create;
  a:=vct.create;
  v:=vct.create;
  t:=0;
  m:=0;
 end;
 destructor obj.destroy;
 begin
  p.destroy;
  a.destroy;
  v.destroy;
 end;
 procedure obj.add_v(now:vct);
 begin
  v.add(now);
 end;
 procedure obj.add_a(now:vct);
 begin
  a.add(now);
 end;
 procedure obj.add_f(now:vct);
 begin
  now.divi(m);
  a.add(now);
 end;
 procedure obj.add_p(now:vct);
 begin
  p.add(now);
 end;
 procedure obj.sub_v(now:vct);
 begin
  v.sub(now);
 end;
 procedure obj.sub_a(now:vct);
 begin
  a.sub(now);
 end;
 procedure obj.sub_f(now:vct);
 begin
  now.divi(m);
  a.sub(now);
 end;
 procedure obj.sub_p(now:vct);
 begin
  p.sub(now);
 end;
 procedure obj.mul_v(now:extended);
 begin
  v.mul(now);
 end;
 procedure obj.mul_a(now:extended);
 begin
  a.mul(now);
 end;
 procedure obj.divi_v(now:extended);
 begin
  v.divi(now);
 end;
 procedure obj.divi_a(now:extended);
 begin
  a.divi(now);
 end;
 procedure obj.settime(now:extended);
 begin
  t:=now;
 end;
 function obj.gettime:extended;
 begin
  exit(t);
 end;
 function copy(a:vct):vct;
 begin
  copy:=vct.create;
  copy.x:=a.x;
  copy.y:=a.y;
  copy.z:=a.z;
 end;
 function copy(a:obj):obj;
 begin
  copy:=obj.create;
  copy.a:=copy(a.a);
  copy.v:=copy(a.v);
  copy.p:=copy(a.p);
  copy.t:=a.t;
  copy.m:=a.m;
 end;
 procedure obj.calc(now:extended);//calc to time "now"
 var
  delta:extended;
  _v:vct;
 begin
  delta:=now-t;
  t:=now;
  _v:=copy(v);
  _v.mul(delta);
  p.add(_v);
  _v:=copy(a);
  _v.mul(sqr(delta));
  _v.divi(2);
  p.add(_v);
  _v:=copy(a);
  _v.mul(delta);
  v.add(_v);
 end;
 function obj.get_p:vct;
 begin
  exit(p);
 end;
 function obj.get_a:vct;
 begin
  exit(a);
 end;
 function obj.get_v:vct;
 begin
  exit(v);
 end;
 procedure obj.add_m(now:extended);
 begin
  m:=m+now;
 end;
 procedure obj.sub_m(now:extended);
 begin
  m:=m-now;
 end;
 procedure obj.mul_m(now:extended);
 begin
  m:=m*now;
 end;
 procedure obj.divi_m(now:extended);
 begin
  if now<>0 then
   m:=m/now
  else
   writeln('Error: 200 Division By Zero!');
 end;
 procedure obj.add_ug(c:obj);//universal gravitation
 var
  _v:vct;
 begin
  _v:=copy(c.p);
  _v.sub(p);
  _v.simplify;
  _v.mul(G*m*c.m/sqr(p.dis(c.p)));
  add_v(_v);
 end;
 function make_vct(_x,_y,_z:extended):vct;
 begin
  make_vct:=vct.create(_x,_y,_z);
 end;
end.
```

这个是今天写的，之前还写了一个带物体碰撞的：

有两个文件：

`vector.pas`:

```pascal
unit vector;
interface
 type
  vec=record
   x,y:extended;
  end;
 operator +(a,b:vec)c:vec;
 operator -(a,b:vec)c:vec;
 operator -(a:vec)b:vec;
 operator *(a:vec;b:extended)c:vec;
 operator *(a:extended;b:vec)c:vec;
 operator /(a:vec;b:extended)c:vec;
 operator =(a,b:vec)c:boolean;
 function make_vec(x,y:extended):vec;
 function length(a:vec):extended;
 procedure simple(var a:vec);
 function flip(a,b:vec):vec;
implementation
 operator +(a,b:vec)c:vec;
 begin
  c.x:=a.x+b.x;
  c.y:=a.y+b.y;
 end;
 operator -(a,b:vec)c:vec;
 begin
  c.x:=a.x-b.x;
  c.y:=a.y-b.y;
 end;
 operator -(a:vec)b:vec;
 begin
  b.x:=-a.x;
  b.y:=-a.y;
 end;
 operator *(a:vec;b:extended)c:vec;
 begin
  c.x:=a.x*b;
  c.y:=a.y*b;
 end;
 operator *(a:extended;b:vec)c:vec;
 begin
  c:=b*a;
 end;
 operator /(a:vec;b:extended)c:vec;
 begin
  c.x:=a.x/b;
  c.y:=a.y/b;
 end;
 operator =(a,b:vec)c:boolean;
 begin
  if(a.x=b.x)and(a.y=b.y) then
   c:=true;
  c:=false;
 end;
 function make_vec(x,y:extended):vec;
 begin
  make_vec.x:=x;
  make_vec.y:=y;
 end;
 function length(a:vec):extended;
 begin
  exit(sqrt(sqr(a.x)+sqr(a.y)));
 end;
 procedure simple(var a:vec);
 begin
  a:=a/length(a);
 end;
 function flip(a,b:vec):vec;//对称轴：a
 begin
  flip.x:=(b.x*(sqr(a.x)-sqr(a.y))+2*a.x*a.y*b.y)/(sqr(a.x)+sqr(a.y));
  flip.y:=(b.y*(sqr(a.y)-sqr(a.x))+2*a.x*a.y*b.x)/(sqr(a.x)+sqr(a.y));
 end;
end.
```

`physical.pas`:

```pascal
unit physical;
interface
{$mode delphi}
 uses vector;
 type
  mess_point=class(TObject)
   a,v,pos:vec;
   mess:extended;
   now_t:extended;
   constructor create;
   destructor destory;
   procedure calc(t:extended);//t>now_t
   procedure add_F(dire:vec);
   procedure add_v(dire:vec);
   procedure add_a(dire:vec);
   procedure del_F(dire:vec);
   procedure del_v(dire:vec);
   procedure del_a(dire:vec);
   { procedure drop(k:extended;dire:vec); }
   procedure drop(k:extended;var mp:mess_point);
   procedure dropXM(k:extended;dire:vec);
  end;
implementation
 constructor mess_point.create;
 begin
 end;
 destructor mess_point.destory;
 begin
 end;
 procedure mess_point.calc(t:extended);
 var
  dt:extended;
 begin
  dt:=t-now_t;
  pos:=pos+v*dt+0.5*sqr(dt)*a;
  v:=v+a*dt;
  now_t:=t;
 end;
 procedure mess_point.add_F(dire:vec);
 begin
  add_a(dire/mess);
 end;
 procedure mess_point.add_v(dire:vec);
 begin
  v:=v+dire;
 end;
 procedure mess_point.add_a(dire:vec);
 begin
  a:=a+dire;
 end;
 procedure mess_point.del_F(dire:vec);
 begin
  del_a(dire/mess);
 end;
 procedure mess_point.del_v(dire:vec);
 begin
  v:=v-dire;
 end;
 procedure mess_point.del_a(dire:vec);
 begin
  a:=a-dire;
 end;
 { procedure mess_point.drop(k:extended;dire:vec); }
 { begin }
 { end; }
 procedure mess_point.drop(k:extended;var mp:mess_point);
 var
  xx,yy:extended;
 begin
  { v.x:=k*((mess-mp.mess)*v.x+2*mp.mess*mp.v.x)/(mess+mp.mess); }
  { v.y:=k*((mess-mp.mess)*v.y+2*mp.mess*mp.v.y)/(mess+mp.mess); }
  { mp.v.x:=k*((mp.mess-mess)*mp.v.x+2*mess*v.x)/(mess+mp.mess); }
  { mp.v.y:=k*((mp.mess-mess)*mp.v.y+2*mess*v.y)/(mess+mp.mess); }
  xx:=((mess-k*mp.mess)*v.x+(1+k)*mp.mess*mp.v.x)/(mess+mp.mess);
  yy:=((mess-k*mp.mess)*v.y+(1+k)*mp.mess*mp.v.y)/(mess+mp.mess);
  mp.v.x:=((1+k)*mess*v.x+(mp.mess-k*mess)*mp.v.x)/(mp.mess+mess);
  mp.v.y:=((1+k)*mess*v.y+(mp.mess-k*mess)*mp.v.y)/(mp.mess+mess);
  v.x:=xx;
  v.y:=yy;
 end;
 procedure mess_point.dropXM(k:extended;dire:vec);
 begin
  v:=k*flip(dire,v);
 end;
end.
```

然后既然昨天写的单元有万有引力了，就可以干一些奇奇怪怪的东西（比如写重力战争之类的）

`test1.pas`:

```pascal
uses zul_PE,gl,glu,glut,sysutils;
var
 a:array[1..100000]of obj;
 r,g,b:array[1..100000]of extended;
 n:longint;
const view=2;
 procedure display;cdecl;
 var
  i:longint;
 begin
  glbegin(gl_line_loop);
   glcolor3f(1,1,1);
   glvertex2f(-1/view,-1/view);
   glvertex2f(-1/view,1/view);
   glvertex2f(1/view,1/view);
   glvertex2f(1/view,-1/view);
  glend;
  glbegin(gl_points);
   for i:=1 to n do
    begin
     glcolor3f(r[i],g[i],b[i]);
     glvertex2f(a[i].p.x/view,a[i].p.y/view);
    end;
  glend;
  glflush;
  glutswapbuffers;
 end;
 var
  time:extended;
  fps:longint;
  //died:boolean=false;
 procedure idle;cdecl;
 var
  i,j:longint;
 begin
  //if died then
   //begin
    //glutpostredisplay;
    //exit;
   //end;
  for i:=1 to n do
   a[i].calc(a[i].gettime+0.003);
  for i:=1 to n-1 do
   for j:=i+1 to n do
    if abs(a[i].p.x-a[j].p.x)<1e-2 then
     if abs(a[i].p.y-a[j].p.y)<1e-2 then
      begin
       //writeln('!!!!! HIT !!!!!');
       //died:=true;
       a[i].m:=0;
       a[j].m:=0;
      end;
  for i:=1 to n do
   for j:=1 to n do
    if i<>j then
     a[i].add_ug(a[j]);
  glutpostredisplay;
  fps:=fps+1;
  if (now-time)*8640>1 then
   begin
    writeln('FPS:',fps);
    fps:=0;
    time:=now;
   end;
 end;
 procedure init;
 var
  i:longint;
 begin
  randomize;
  readln(n);
  for i:=1 to n do
   a[i]:=obj.create;
  for i:=1 to n do
   a[i].m:=10;
  for i:=1 to n do
   a[i].p:=make_vct(random*2-1,random*2-1,0);
  for i:=1 to n do
   a[i].v:=make_vct(random/10-0.05,random/10-0.05,0);
  for i:=1 to n do
   writeln('p : (',a[i].p.x:0:5,',',a[i].p.y:0:5,')');
  for i:=1 to n do
   writeln('v : (',a[i].v.x:0:5,',',a[i].v.y:0:5,')');
  for i:=1 to n do
   r[i]:=random/2+0.5;
  for i:=1 to n do
   g[i]:=random/2+0.5;
  for i:=1 to n do
   b[i]:=random/2+0.5;
 end;
begin
 init;
 glutinit(@argc,argv);
 glutinitdisplaymode(glut_rgb or glut_double);
 glutinitwindowsize(400,400);
 glutinitwindowposition(100,100);
 glutcreatewindow('test_pe');
 glutdisplayfunc(@display);
 glutidlefunc(@idle);
 glutmainloop;
end.
```

用法：输入点的个数，会随机出一个`n`星系统，（星球极度容易撞在一起）

比较好玩的数据：

```
ap : (-0.80895,-0.78031)
bp : (-0.56219,0.88963)
cp : (-0.22107,0.40672)
av : (0.03728,-0.01116)
bv : (-0.04978,0.02081)
cv : (-0.04920,-0.01286)
-0.80895 -0.78031
-0.56219 0.88963
-0.22107 0.40672
0.03728 -0.01116
-0.04978 0.02081
-0.04920 -0.01286

ap : (-0.63931,0.83094)
bp : (0.35101,-0.66686)
cp : (0.13291,-0.10972)
av : (-0.02917,0.00141)
bv : (0.04345,0.04925)
cv : (-0.02859,0.01358)

ap : (0.19468,0.03975)
bp : (-0.57368,-0.41813)
cp : (-0.90872,0.94385)
av : (0.02945,-0.04838)
bv : (-0.03123,0.01144)
cv : (0.03605,-0.01073)

ap : (-0.83073,0.75887)
bp : (-0.13233,0.19897)
cp : (-0.41203,-0.70004)
av : (-0.01957,0.00302)
bv : (0.01779,0.00984)
cv : (-0.02442,0.01845)

ap : (0.67421,0.80021)
bp : (-0.56138,-0.10226)
cp : (-0.46152,0.07210)
av : (0.03272,0.01043)
bv : (-0.04112,0.00303)
cv : (0.04005,0.02967)
0.67421 0.80021
-0.56138 -0.10226
-0.46152 0.07210
0.03272 0.01043
-0.04112 0.00303
0.04005 0.02967

ap : (-0.83287,-0.95093)
bp : (0.46070,0.43562)
cp : (0.76106,-0.58622)
av : (0.04732,0.00389)
bv : (0.03648,0.04963)
cv : (-0.02497,0.03841)

ap : (-0.87720,-0.92674)
bp : (-0.70997,0.09393)
cp : (-0.04148,0.92737)
av : (-0.02766,0.01914)
bv : (0.02834,-0.03395)
cv : (0.04695,-0.02277)

ap : (-0.93286,0.44797)
bp : (0.22278,0.77414)
cp : (-0.39186,0.35884)
av : (-0.03283,-0.00130)
bv : (0.00704,-0.00883)
cv : (0.03882,-0.04569)
```

有字母的是程序的输出，转换成纯数字的可以在这里弄回放：

`review.pas`:

```pascal
uses zul_PE,gl,glu,glut,sysutils;
var
 a,b,c:obj;
const view=20;
 procedure display;cdecl;
 begin
  glbegin(gl_line_loop);
   glcolor3f(1,1,1);
   glvertex2f(-1/view,-1/view);
   glvertex2f(-1/view,1/view);
   glvertex2f(1/view,1/view);
   glvertex2f(1/view,-1/view);
  glend;
  glbegin(gl_points);
   glcolor3f(1,0,0);
   glvertex2f(a.p.x/view,a.p.y/view);
   glcolor3f(0,1,0);
   glvertex2f(b.p.x/view,b.p.y/view);
   glcolor3f(0,0,1);
   glvertex2f(c.p.x/view,c.p.y/view);
  glend;
  glflush;
  glutswapbuffers;
 end;
 var
  time:extended;
  fps:longint;
  died:boolean=false;
 procedure idle;cdecl;
 begin
  if died then
   begin
    glutpostredisplay;
    exit;
   end;
  a.calc(a.gettime+0.003);
  b.calc(b.gettime+0.003);
  c.calc(c.gettime+0.003);
  if abs(a.p.x-b.p.x)<1e-3 then
   if abs(a.p.y-b.p.y)<1e-3 then
    begin
     writeln('!!!!! HIT !!!!!');
     died:=true;
    end;
  if abs(a.p.x-c.p.x)<1e-3 then
   if abs(a.p.y-c.p.y)<1e-3 then
    begin
     writeln('!!!!! HIT !!!!!');
     died:=true;
    end;
  if abs(b.p.x-c.p.x)<1e-3 then
   if abs(b.p.y-c.p.y)<1e-3 then
    begin
     writeln('!!!!! HIT !!!!!');
     died:=true;
    end;
  a.add_ug(c);
  a.add_ug(b);
  b.add_ug(a);
  b.add_ug(c);
  c.add_ug(a);
  c.add_ug(b);
  glutpostredisplay;
  //writeln('idled');
  fps:=fps+1;
  if (now-time)*8640>1 then
   begin
    writeln('FPS:',fps);
    fps:=0;
    time:=now;
   end;
 end;
 procedure init;
// var
//  xx,yy:extended;
//  label 1;
 begin
  randomize;
  a:=obj.create;
  b:=obj.create;
  c:=obj.create;
  a.m:=10;
  b.m:=10;
  c.m:=10;
  readln(a.p.x,a.p.y);
  readln(b.p.x,b.p.y);
  readln(c.p.x,c.p.y);
  readln(a.v.x,a.v.y);
  readln(b.v.x,b.v.y);
  readln(c.v.x,c.v.y);
  //a.p:=make_vct(-1,0,0);
  { a.a:=make_vct(random*10-5,random*10-5,0); }
  { writeln('target at: (',(b.p.x+1):0:5,',',(b.p.y+1):0:5,')'); }
  { writeln('mass at: (',(c.p.x+1):0:5,',',(c.p.y+1):0:5,')'); }
  { writeln('gravity_acceleration : (',(a.a.x):0:5,',',(a.a.y):0:5,')'); }
  {1:
  write('x: ');
  readln(xx);
  if xx>30 then
   begin
    writeln('error!');
    goto 1;
   end;
  write('y: ');
  readln(yy);
  if yy>30 then
   begin
    writeln('error!');
    goto 1;
   end;
  a.v:=make_vct(xx,yy,0);}
  { a.v:=make_vct(1,4.9*(b.p.x+1)+(b.p.y+1)/(b.p.x+1),0); }
 end;
begin
 init;
 glutinit(@argc,argv);
 glutinitdisplaymode(glut_rgb or glut_double);
 glutinitwindowsize(400,400);
 glutinitwindowposition(100,100);
 glutcreatewindow('test_pe');
 glutdisplayfunc(@display);
 glutidlefunc(@idle);
 glutmainloop;
end.
```

`view`常数表示界面缩小多少倍

`test2.pas`:

```pascal
uses zul_PE,gl,glu,glut,sysutils;
var
 a:array[1..10000]of obj;
 { r,g,b:array[1..10]of extended; }
 aa:obj;
 n:longint;
const view=2;
 procedure display;cdecl;
 var
  i:longint;
 begin
  glbegin(gl_line_loop);
   glcolor3f(1,1,1);
   glvertex2f(-1/view,-1/view);
   glvertex2f(-1/view,1/view);
   glvertex2f(1/view,1/view);
   glvertex2f(1/view,-1/view);
  glend;
  glbegin(gl_points);
   for i:=1 to n do
    begin
     { glcolor3f(r[i],g[i],b[i]); }
     glcolor3f(1,1,1);
     glvertex2f(a[i].p.x/view,a[i].p.y/view);
    end;
   glcolor3f(1,0,0);
   glvertex2f(aa.p.x/view,aa.p.y/view);
  glend;
  glflush;
  glutswapbuffers;
 end;
 var
  time:extended;
  fps:longint;
  died:boolean=false;
 procedure idle;cdecl;
 var
  i:longint;
 begin
  if died then
   begin
    glutpostredisplay;
    exit;
   end;
  aa.calc(aa.gettime+0.003);
  for i:=1 to n do
   if abs(aa.p.x-a[i].p.x)<1e-3 then
    if abs(aa.p.y-a[i].p.y)<1e-3 then
     begin
      writeln('!!!!! HIT !!!!!');
      died:=true;
     end;
  for i:=1 to n do
   aa.add_ug(a[i]);
  glutpostredisplay;
  //writeln('idled');
  fps:=fps+1;
  if (now-time)*8640>1 then
   begin
    writeln('FPS:',fps);
    fps:=0;
    time:=now;
   end;
 end;
 procedure init;
 var
  xx,yy:extended;
  i:longint;
  label 1;
 begin
  readln(n);
  randomize;
  for i:=1 to n do
   a[i]:=obj.create;
  aa:=obj.create;
  for i:=1 to n do
   a[i].m:=10;
  aa.m:=10;
  for i:=1 to n do
   a[i].p:=make_vct(random*2-1,random*2-1,0);
  for i:=1 to n do
   writeln(i,' : (',a[i].p.x:0:5,',',a[i].p.y:0:5,')');
  aa.p:=make_vct(-1,0,0);
  {for i:=1 to n do
   r[i]:=random/2+0.5;
  for i:=1 to n do
   g[i]:=random/2+0.5;
  for i:=1 to n do
   b[i]:=random/2+0.5;}
  1:
  write('x: ');
  readln(xx);
  if xx>30 then
   begin
    writeln('error!');
    goto 1;
   end;
  write('y: ');
  readln(yy);
  if yy>30 then
   begin
    writeln('error!');
    goto 1;
   end;
  aa.v:=make_vct(xx,yy,0);
 end;
begin
 init;
 glutinit(@argc,argv);
 glutinitdisplaymode(glut_rgb or glut_double);
 glutinitwindowsize(800,800);
 glutinitwindowposition(100,100);
 glutcreatewindow('test_pe');
 glutdisplayfunc(@display);
 glutidlefunc(@idle);
 glutmainloop;
end.
```

使用方法：先一行输入点的个数，再输入初始速度的分量，会显示出图像

注意：因为这些东西（时间，距离，质量）与现实中极度不符，所以星球很容易撞在一起（有可能会撞飞，或者会检测到碰撞然后停止运行