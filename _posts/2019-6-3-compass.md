---
layout: post
title: 单规作图
date: 2019-6-3 16:22:52 +0800
tag: 小应用
---

* content
{:toc}

最近在研究之用一个圆规可以做出的线段长度：

用的方法是`bfs`

但是实际上这可能并不能找出最快的解法，因为你不能在平面上“任取一点”

通过输入一个长度（原来就有的长度定为1），会输出解法

解法的解释见最下面

```pascal
{$macro on}
{$define extended:=real}
uses math;
type
 point=record
  x,y:extended;
  cir1,cir2:longint;
 end;
 circle=record
  o:point;
  r:extended;
 end;
 _length=record
  l:extended;
  p1,p2:longint;
 end;
 points=array of point;
 circles=array of circle;
 lengths=array of _length;
 plengths=^lengths;
 pcircles=^circles;
 ppoints=^points;
 node=record
  pt:points;
  cc:circles;
  len:lengths;
 end;
 pque=^que;
 que=record
  statue:node;
  prev,next:pque;
 end;
const
 eps=1e-9;
function same(a,b:extended):boolean;
begin
 if abs(a-b)<eps then
  exit(true);
 exit(false);
end;
function big(a,b:extended):boolean;
begin
 if a-b>=eps then
  exit(true);
 exit(false);
end;
function small(a,b:extended):boolean;
begin
 exit(b>a);
end;
function dis(a,b:point{;s:ansistring}):extended;
begin
 { writeln(s); }
 { writeln('  a.x=',a.x:0:2); }
 { writeln('  a.y=',a.y:0:2); }
 { writeln('  b.x=',b.x:0:2); }
 { writeln('  b.y=',b.y:0:2); }
 exit(hypot(abs(a.x-b.x),abs(a.y-b.y)));
end;
function triangle(a,b,c:extended):longint;
begin
 if big(a+b,c)and big(a+c,b)and big(b+c,a) then
  exit(2);
 if same(a+b,c)or same(a+c,b)or same(b+c,a) then
  exit(1);
 exit(0);
end;
function sqrt(a:extended):extended;
begin
 if a<0 then
  exit(0);
 exit(system.sqrt(a));
end;
function jiaodian(a,b:circle):points;
var
 x1,x2,y1,y2,r1,r2,d,x0,t1,t2,_A,_B,V1,V2,V3,solx1,solx2:extended;
 tk:longint;
 { i:longint; }
begin
 x1:=a.o.x;
 y1:=a.o.y;
 r1:=a.r;
 x2:=b.o.x;
 y2:=b.o.y;
 r2:=b.r;
 d:=dis(a.o,b.o{,'jiaodian'});
 tk:=triangle(d,r1,r2);
 if tk=0 then
  begin
   setlength(jiaodian,0);
   exit;
  end
 else
  setlength(jiaodian,2);
 t1:=sqr(r1)-sqr(x1)-sqr(y1);
 t2:=sqr(r2)-sqr(x2)-sqr(y2);
 if same(y1,y2) then
  begin
   x0:=-(t1-t2)/2/(x1-x2);
   jiaodian[0].x:=x0;
   jiaodian[1].x:=x0;
   jiaodian[0].y:=y1+sqrt(sqr(r1)-sqr(x0-x1));
   jiaodian[1].y:=y1-sqrt(sqr(r1)-sqr(x0-x1));
   { writeln('Method1'); }
  end
 else
  begin
   _A:=-(x1-x2)/(y1-y2);
   _B:=-(t1-t2)/2/(y1-y2);
   V1:=sqr(_A)+1;
   V2:=2*_A*_B-2*x1-2*y1*_A;
   V3:=sqr(_B)-2*y1*_B-t1;
   solx1:=(-V2-sqrt(sqr(V2)-4*V1*V3))/(2*V1);
   solx2:=(-V2+sqrt(sqr(V2)-4*V1*V3))/(2*V1);
   jiaodian[0].x:=solx1;
   jiaodian[1].x:=solx2;
   jiaodian[0].y:=_A*solx1+_B;
   jiaodian[1].y:=_A*solx2+_B;
   { writeln('Method2'); }
  end;
 if tk=1 then
  setlength(jiaodian,1);
 { writeln('circle A: (',x1,',',y1,',',r1,')'); }
 { writeln('circle B: (',x2,',',y2,',',r2,')'); }
 { for i:=0 to length(jiaodian)-1 do }
  { writeln('(',jiaodian[i].x,',',jiaodian[i].y,')'); }
end;
var
 head,tail:pque;
procedure init;
begin
 new(head);
 head^.prev:=nil;
 head^.next:=nil;
 tail:=head;
end;
procedure push(a:node);
var
 p:pque;
begin
 new(p);
 p^.statue:=a;
 p^.next:=head;
 head^.prev:=p;
 head:=p;
end;
function pop:node;
var
 p:pque;
begin
 if tail^.prev=nil then
  begin
   writeln('wtf');
   exit;
  end;
 p:=tail^.prev;
 dispose(tail);
 tail:=p;
 p^.next:=nil;
 exit(p^.statue);
end;
var
 initstatue,now:node;
 t:node;
{ const }
 { succeed=1.7320508075688772935274463415059; }
 { succeed=0.5; }
 { succeed=1.4142135623730950488016887242097; }
 { succeed=0.7653668647301795434569199680608; }
 { succeed=0.61803398874989484820458683436564; }
 { succeed=0.5176380902050415246977976752481; }
 { succeed=0.33333333333333333333333333333333; }
 { succeed=0.36749903563314066314881767924146; }
 { succeed=0.86776747823511624095153666569672; }//impossible
var
 succeed:extended;
var
 already_succeed:boolean=false;
function add_length(target:plengths;a:_length):boolean;
var
 i:longint;
begin
 if same(a.l,succeed) then
  already_succeed:=true;
 for i:=0 to length(target^)-1 do
  if same(target^[i].l,a.l) then
   exit(false);
 setlength(target^,length(target^)+1);
 target^[length(target^)-1]:=a;
 exit(true);
end;
function add_circle(target:pcircles;a:circle):boolean;
var
 i:longint;
begin
 for i:=0 to length(target^)-1 do
  if same(target^[i].r,a.r)and same(target^[i].o.x,a.o.x)and same(target^[i].o.y,a.o.y) then
   exit(false);
 setlength(target^,length(target^)+1);
 target^[length(target^)-1]:=a;
 exit(true);
end;
function add_point(target:ppoints;a:point):boolean;
var
 i:longint;
begin
 for i:=0 to length(target^)-1 do
  if same(target^[i].x,a.x)and same(target^[i].y,a.y) then
   exit(false);
 { writeln('point add: (',a.x:0:2,',',a.y:0:2,')'); }
 setlength(target^,length(target^)+1);
 target^[length(target^)-1]:=a;
 exit(true);
end;
function make_point(x,y:extended;cir1,cir2:longint):point;
begin
 make_point.x:=x;
 make_point.y:=y;
 make_point.cir1:=cir1;
 make_point.cir2:=cir2;
end;
function make_circle(o:point;r:extended):circle;
begin
 make_circle.o:=o;
 make_circle.r:=r;
end;
function make_length(l:extended;p1,p2:longint):_length;
begin
 make_length.l:=l;
 make_length.p1:=p1;
 make_length.p2:=p2;
end;
var
 i,j,k,l,m,celebrate:longint;
 now_cir:circle;
 jiaodian_pts:points;
begin
 readln(succeed);
 init;
 with initstatue do
  begin
   setlength(len,1);
   len[0].l:=1;
   setlength(pt,2);
   pt[0].x:=0;
   pt[0].y:=0;
   pt[1].x:=-1;
   pt[1].y:=0;
   setlength(cc,1);
   cc[0].o.x:=0;
   cc[0].o.y:=0;
   cc[0].r:=1;
  end;
 push(initstatue);
 //BFS start
 while true do//because never ends
  begin
   now:=pop;
   for i:=0 to length(now.pt)-1 do
    begin
     for j:=0 to length(now.len)-1 do
      begin
       t:=now;
       now_cir:=make_circle(now.pt[i],now.len[j].l);
       if add_circle(@t.cc,now_cir) then
        begin
         { writeln('circles_count:',length(t.cc)); }
         for k:=0 to length(t.cc)-2 do
          { if (t.cc[k].o.x<>now_cir.o.x)or(t.cc[k].o.y<>now_cir.o.y)or(t.cc[k].r<>now_cir.r) then }
           begin
            jiaodian_pts:=jiaodian(t.cc[k],now_cir);
            for l:=0 to length(jiaodian_pts)-1 do
             { begin }
              if add_point(@t.pt,make_point(jiaodian_pts[l].x,jiaodian_pts[l].y,k+1,length(t.cc))) then
               { begin }
                { writeln(length(t.pt)); }
                { for m:=0 to length(t.pt)-2 do }
                 { writeln('prepare: (',t.pt[m].x:0:2,',',t.pt[m].y:0:2,')'); }
                for m:=0 to length(t.pt)-2 do
                 begin
                  { writeln('(',t.pt[m].x:0:2,',',t.pt[m].y:0:2,')'); }
                  add_length(@t.len,make_length(dis(jiaodian_pts[l],t.pt[m]{,'main'}),m+1,length(t.pt)));
                  if already_succeed then
                   begin
                    writeln('succeed!');
                    writeln('circles:');
                    for celebrate:=0 to length(t.cc)-1 do
                     writeln(celebrate+1:3,'   (',t.cc[celebrate].o.x:19:16,',',t.cc[celebrate].o.y:19:16,',',t.cc[celebrate].r:19:16,')');
                    writeln('points:');
                    for celebrate:=0 to length(t.pt)-1 do
                     writeln(celebrate+1:3,'   (',t.pt[celebrate].x:19:16,',',t.pt[celebrate].y:19:16,') (',t.pt[celebrate].cir1:3,',',t.pt[celebrate].cir2:3,')');
                    writeln('lengths:');
                    for celebrate:=0 to length(t.len)-1 do
                     writeln(celebrate+1:3,'   ',t.len[celebrate].l:19:16,' (',t.len[celebrate].p1:3,',',t.len[celebrate].p2:3,')');
                    halt;
                   end;
                 end;
               { end; }
             { end; }
           end;
         { for k:=0 to length(t.pt)-1 do }
          { writeln('pts: (',t.pt[k].x:0:2,',',t.pt[k].y:0:2,')'); }
         push(t);
        end;
      end;
    end;
  end;
 //BFS end
end.
```

例如输入2的时候，输出了以下东西：

```
succeed!
circles:
  1   ( 0.0000000000000000, 0.0000000000000000, 1.0000000000000000)
  2   (-1.0000000000000000, 0.0000000000000000, 1.0000000000000000)
  3   ( 0.0000000000000000, 0.0000000000000000, 1.7320508075688772)
points:
  1   ( 0.0000000000000000, 0.0000000000000000) (  0,  0)
  2   (-1.0000000000000000, 0.0000000000000000) (  0,  0)
  3   (-0.5000000000000000, 0.8660254037844386) (  1,  2)
  4   (-0.5000000000000000,-0.8660254037844386) (  1,  2)
  5   (-1.4999999999999998, 0.8660254037844388) (  2,  3)
lengths:
  1    1.0000000000000000 (  0,  0)
  2    1.7320508075688772 (  3,  4)
  3    2.0000000000000000 (  4,  5)
```

`succeed`表示可以做出来

`circles`表示每个圆的原点和半径

`points`表示每个点的坐标，以及是由那几个圆构造出来的

`lengths`表示每个长度是由哪两个点构造出来的

为了方便描述，给出了点的坐标，防止你不知道两个圆有两个交点的时候那个是那个