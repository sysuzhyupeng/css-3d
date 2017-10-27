css-3d
-
transform中有这么几个属性方法：
```
  .trans_skew { transform: skew(35deg); }
  .trans_scale { transform:scale(1, 0.5); }
  .trans_rotate { transform:rotate(45deg); }
  .trans_translate { transform:translate(10px, 20px); }
```
斜拉(skew)，缩放(scale)，旋转(rotate)以及位移(translate)。
但这四种方法，底层都是调用同一个接口，也就是`transform`属性的`matrix()`方法。

transform与坐标系统
-
用过transform旋转的人可以发现了，其默认是绕着中心点旋转的，而这个中心点就是transform-origin属性对应的点，也是所有矩阵计算的一个重要依据点。
当我们通过transform-origin属性进行设置的时候，矩阵相关计算也随之发生改变。反映到实际图形效果上就是，旋转拉伸的中心点变了！

比如我们设置如下：
```
  -webkit-transform-origin: bottom left;
```
旋转的中心就会变成左下角。如果我们设置
```
  transform-origin: 50px 70px;
```
旋转中心就会变成距离左侧50px，距离上部分70px。

matrix()
-
`transform`属性的`matrix()`方法具体参数如下：
```
  transform: matrix(a,b,c,d,e,f);
```
![image](https://github.com/sysuzhyupeng/css-3d/raw/master/resources/img/matrix.gif)

x, y表示元素的初始坐标。ax+cy+e和bx+dy+f则是通过矩阵变换后得到新的坐标。

实际上transform: matrix(1, 0, 0, 1, 30, 30);就等同于transform: translate(30px, 30px);

所以其实移动的实际上的参数为：`matrix(1,0,0,1,Δx,Δy)`

缩放scale
-
同样可以推出缩放matrix参数为：matrix(kx,0,0,ky,0,0)（kx，和ky分别对应x和y轴缩放比率）

旋转rotate
-
旋转matrix参数为：matrix(cosθ,sinθ,-sinθ,cosθ,0,0)

-webkit-transform: rotate(45deg)，即对应着 -webkit-transform: matrix(0.53, 0.85, -0.85, 0.53, 0, 0);

斜切skew
-
斜切matrix参数为matrix(1,tan(θy),tan(θx),1,0,0)

-webkit-transform: skew(45deg);即对应着 -webkit-transform: matrix(1,0,1,1,0,0);

镜相对称
-
假设对称轴为y=kx直线，那么以这条直线对称的图形matrix为

(已知x,y,x', y')求矩阵。

 
