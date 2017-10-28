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

three.js
-
写3D程序，最好是用c++，但js计算能力因为google的V8引 擎得到了迅猛的增强，做3D程序也没有问题。

在Three.js中，要渲染物体到网页中，我们需要3个组建：场景（scene）、相机（camera）和渲染器（renderer）。有了这三样东西，才能将物体渲染到网页中去。
```javascript
  var scene = new THREE.Scene();  // 场景
  var camera = new THREE.PerspectiveCamera(75, window.innerWidth/window.innerHeight, 0.1, 1000);// 透视相机
  var renderer = new THREE.WebGLRenderer();
  renderer.setSize(window.innerWidth, window.innerHeight);
  document.body.appendChild(renderer.domElement);
```
渲染函数的原型如下：render( scene, camera, renderTarget, forceClear )
```
  scene：前面定义的场景
  camera：前面定义的相机
  renderTarget：渲染的目标，默认是渲染到前面定义的render变量中
  forceClear：每次绘制之前都将画布的内容给清除，即使自动清除标志autoClear为false，也会清除。
```

渲染循环
-
渲染有两种方式：实时渲染和离线渲染 。

电影它是事先渲染好一帧一帧的图片，然后再把图片拼接成电影的。这就是离线渲染。如果不事先处理好一帧一帧的图片，那么电影播放得会很卡。

实时渲染：就是需要不停的对画面进行渲染，即使画面中什么也没有改变，也需要重新渲染。
```javascript
  function render() {
    cube.rotation.x += 0.1;
    cube.rotation.y += 0.1;
    renderer.render(scene, camera);
    requestAnimationFrame(render);
  }
```

场景，相机，渲染器之间的关系
-
Three.js中的场景是一个物体的容器，开发者可以将需要的角色放入场景中，例如苹果，葡萄。同时，角色自身也管理着其在场景中的位置。

相机的作用就是面对场景，在场景中取一个合适的景，把它拍下来。

渲染器的作用就是将相机拍摄下来的图片，放到浏览器中去显示。

在Threejs中定义一个点
-
在三维空间中的某一个点可以用一个坐标点来表示。一个坐标点由x,y,z三个分量构成。在three.js中，点可以在右手坐标系中表示：
```javascript
  THREE.Vector3 = function ( x, y, z ) {
    this.x = x || 0;
    this.y = y || 0;
    this.z = z || 0;
  };
```
THREE.Vector3是表示Vector3是定义在THREE下面的一个类。以后要用Vector3，就必须要加THREE前缀。
```javascript
  var point1 = new THREE.Vecotr3(4,8,9);
  var point1 = new THREE.Vector3();
  point1.set(4,8,9);
```

右手坐标系
-
Threejs使用的是右手坐标系，在Threejs中，坐标和右边的坐标完全一样。x轴正方向向右，y轴正方向向上，z轴由屏幕从里向外。

场景移动
-
第一种方法是让物体在坐标系里面移动，摄像机不动。第二种方法是让摄像机在坐标系里面移动，物体不动。

如果我们改变了物体的位置或者颜色之类的属性，就必须重新调用render()函数，才能够将新的场景绘制到浏览器中去。不然浏览器是不会自动刷新场景的。
```javascript
  renderer.render(scene, camera);
```
比如物品向左移动，可以让相机往右移动。camera.position.x =camera.position.x + 1;

评估程序的性能
-
帧数：图形处理器每秒钟能够刷新几次，通常用fps（Frames Per Second）来表示。

当物体在快速运动时,当人眼所看到的影像消失后，人眼仍能继续保留其影像1/24秒左右的图像，这种现象被称为视觉暂留现象。是人眼具有的一种性质。

眼观看物体时，成像于视网膜上，并由视神经输入人脑，感觉到物体的像。一帧一帧的图像进入人脑，人脑就会将这些图像给连接起来，形成动画。

帧数越高，画面的感觉就会越好。所以大多数游戏都会有超过30的FPS。

在Three.js中，性能监视器被封装在一个类中，这个类叫做Stats

使用动画引擎Tween.js来创建动画
-
为了使程序编写更容易一些，我们可以使用动画引擎来实现动画效果。对Tween进行初始化：
```javascript
  function initTween(){
    new TWEEN.Tween( mesh.position)
            .to( { x: -400 }, 3000 ).repeat( Infinity ).start();
  }
```
TWEEN.Tween的构造函数接受的是要改变属性的对象，这里传入的是mesh的位置。Tween的任何一个函数返回的都是自身，所以可以用串联的方式直接调用各个函数。

to函数，接受两个参数，第一个参数是一个集合，里面存放的键值对，键x表示mesh.position的x属性，值-400表示，动画结束的时候需要移动到的位置。第二个参数，是完成动画需要的时间，这里是3000ms。



