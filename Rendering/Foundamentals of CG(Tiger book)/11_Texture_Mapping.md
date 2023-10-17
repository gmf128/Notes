# 11：Texture Mapping

## 11.1 获取纹理坐标

<img src=" https://box.nju.edu.cn/f/eba56fbb921c4d19b22c/?dl=1" style="zoom:67%;" />
$$
\phi:\ S \to \ T
$$

$$
:(x, y, z)\to(u, v)
$$

phi称为纹理坐标函数。(u, v)的分量一般限制在[0, 1]之间。

**注意：phi是三维空间到纹理空间的映射，不是屏幕空间到纹理空间的映射，这也是为什么后续要采用透视校正插值的原因**

作为一个最简单的例子，我们为一个水平的地板贴图。我们可以使用这样的映射：
$$
u = ax \ ; \ v = by
$$
再根据(u, v)坐标转换为纹理图对应的像素坐标(s, t)【注意：之前提到了uv坐标这里是归一化后的坐标，并非纹理图的像素坐标】

根据像素坐标(s, t)对应的纹理像素（**texel)** ，我们获得了纹理信息。

uv映射又被称为曲面参数化，是一个长久的难题。

我们希望设计的映射能够达到一下目标，尽管这是颇具挑战性的：

1. 双射。除了单一纹理重复的地板、地毯、墙壁等情境外，我们期望纹理映射是一个双射，使得当某个纹理点改变时，并不会影响太多的物体点。在需要纹理重复的场景，我们是已知的（not happen by accident).
2. 不要有尺寸失真。即不希望出现摩尔纹等情况的出现。翻译为数学语言是：希望映射的导数的范数上界尽可能小。
3. 连续性。即在物体上距离相近的点，我们希望在纹理上他们的纹理坐标也是相近的。尽可能减少不连续点。

对于那些有现成参数化方程的曲面，直接选用参数化方程即可（如：球）。

但是对于大多数用三角形面片定义的模型和用隐式曲面定义的模型，我们需要其他方式设计纹理映射。主要有两种方式：

## 11.2  Texture Coordinate Functions 

### 11.2.1 Geometrically Determined Coordinates

#### 11.2.1.1 Planar Projection(平面投影，平面贴图)

> planar: 平面的；在同一平面的。 

$$
φ(x, y, z)=(u, v) \ \ \  where \ \ \ 
\begin{aligned}
    \begin{bmatrix}
	u \\
	v\\
	*\\
	1
	\end{bmatrix}
	=M_t
	\begin{bmatrix}
	{}
	x \\
	y\\
	z\\
	1
	\end{bmatrix}
\end{aligned}
$$

适用于：贴图的模型几乎是一个平面，不能有正反面，不然不是单射。（**下图中待贴图的三维模型是左侧的面，不是一个正方体！**，侧面的线是辅助线，表示映射方向）

<img src=" https://box.nju.edu.cn/f/8c9a84a1a04c43ff8bba/?dl=1" style="zoom:80%;" />

引申：射影投影
$$
φ(x, y, z)=(u/w, v/w) \ \ \  where \ \ \ \begin{aligned}    \begin{bmatrix}    u \\    v\\    *\\    w    \end{bmatrix}    =P_t    \begin{bmatrix}    {}    x \\    y\\    z\\    1    \end{bmatrix}\end{aligned}
$$
<img src=" https://box.nju.edu.cn/f/fc9c2ba9e3284a2c86ee/?dl=1" style="zoom:80%;" />

#### 11.2.1.2 Spherical Projection(球面投影,球面贴图)

球坐标：
$$
(\rho, \theta, \phi)\ \ \  \theta\in[0, 2\pi], \ \phi\in [0, \pi]
$$

$$
\begin{cases}
x = \rho\sin{\phi} \cos{\theta} \\
y = \rho\sin{\phi} \sin{\theta} \\
z = \rho\cos\phi
\end{cases}
$$

定义（x,y,z) 到（u, v)的映射如下：**(默认三维模型定义在[-1,1]^3中)**
$$
φ(x, y, z) = ([π + \arctan2(y， x)]/2π, [π − \arccos(z/||x||)]/π).
$$
即无视球坐标的第一个分量（表示半径的分量），仅以第二个、第三个角度分量作为uv坐标，并将其归一化至[0, 1]间。

说明：

- 什么是[π + arctan2(y， x)] ？

  <img src=" https://box.nju.edu.cn/f/26635a8c3a1f45e2a62f/?dl=1" style="zoom:67%;" />

  atan2是一个函数，在C语言里返回的是指方位角，C 语言中atan2的函数原型为 double atan2(double y, double x) ，返回以弧度表示的 y/x 的反正切。y 和 x 的值的符号决定了正确的象限。也可以理解为计算复数 x+yi 的辐角，计算时atan2 比 atan 稳定。 

- 为什么是[π − arccos(z/||x||)] ？

与球坐标相似的还有柱坐标：
$$
(\rho, \theta, z)\ \ \  \theta\in[0, 2\pi]
$$

$$
\begin{cases}
x = \rho \cos{\theta} 
\\y = \rho \sin{\theta} 
\\z = z
\end{cases}
$$

定义（x,y,z) 到（u, v)的映射如下：**(默认三维模型定义在[-1,1]^3中)**
$$
φ(x, y, z) = （\frac{1}{2π} 
 [π + atan2(y, x)]/2π, \frac1
2[1 + z]）
$$
<img src=" https://box.nju.edu.cn/f/ad03553d6799405ea445/?dl=1" style="zoom:80%;" />

#### 11.2.1.3 Cubemaps

球面投影的缺点：在两极处会有严重失真。一个常见的替代是正方体贴图，虽然在正方体的棱处引入了很多的不连续处，但是更加整齐统一。正方体贴图是指由6个正方形纹理组成的纹理组合，再通过一个投影贴图公式将(x,y,z)坐标与(u,v)坐标对应。

另一个优点是uv坐标的计算相对简单，**不再需要计算反三角函数**。

(x,y,z)坐标与(u,v)坐标对应可以有很多选择，需要人为规定。OpenGL的标准是这样的：
$$
φ_{−x}(x, y, z) = \frac1
2 [1 + (+z, −y) / |x|] ,\\
φ_{+x}(x, y, z) = \frac1
2 [1 + (−z, −y) / |x|] ,\\
φ_{−y}(x, y, z) =\frac 1
2 [1 + (+x, −z) / |y|] ,\\
φ_{+y}(x, y, z) = \frac1
2 [1 + (+x, +z) / |y|] ,\\
φ_{−z}(x, y, z) = \frac1
2 [1 + (−x, −y) / |z|] ,\\
φ_{+z}(x, y, z) = \frac1
2 [1 + (+x, −y) / |z|] .\\
$$
一个用于正方体贴图的纹理由6个正方形组成部分，通常它们被组织成一个图片一遍储存，就像正方体的平面展开一样。

下图：正方体的4条对角线所在直线将空间分为6块，处在每块的面分别被映射到正方体的对应面。

<img src=" https://box.nju.edu.cn/f/35f7d513ea8e4c5288a9/?dl=1" style="zoom:67%;" />

### 11.2.2 Interpolated Texture Coordinates 

对于三角形网格表面，我们的通常做法是将顶点对应的uv坐标作为一个属性储存在顶点信息中，然后用重心插值法求出每个三角形内点对应的纹理坐标。它和其他的可能在mesh上定义的性质：如法线、颜色等处理流程几乎是一样的。

### 11.2.3 Wrapping Mode

**如何理解wrapping mode的概念：我们可以将纹理视作一种函数（映射），值域为颜色，定义域是二维平面的一个子集。我们现在想对这个映射做延拓，原本是定义在[0,1]\^2中的（通常情况下）；现在我们希望它拓展定义到整个R\^2中。延拓的方式就是wrapping mode**

两种延拓的方式：clamp/tile

```
Color texture_lookup_wrap(Texture t, float u, float v) {//Tile
int i = round(u * t.width() - 0.5)
int j = round(v * t.height() - 0.5)
return t.get_pixel(i % t.width(), j % t.height())
}
Color texture_lookup_wrap(Texture t, float u, float v) {//Clamp
int i = round(u * t.width() - 0.5)
int j = round(v * t.height() - 0.5)
return t.get_pixel(max(0, min(i, t.width()-1)),
(max(0, min(j, t.height()-1))))
}
```

分别的用途：

1. Tile.对应代码块1；应用场景：需要大量重复贴图，比如贴地板，为了节约花销，一般使用一个地板贴图反复重复使用，这个时候只需要定义[0,1]平方中的一块贴图，其他超出部分循环贴图（用取余数的方法）

2. Clamp: 对应代码块2；一些情况可能会算出超出[0,1]\^2的情况，我们用强制clamp的方式将其限制在[0,1]\^2内。(max(0, min(j, t.height()-1))))

   

Texture Transfoemation

对纹理做变换后当然要对映射函数做对应变换
$$
φ(x) = M_{T}\ φ_{model}(x)
$$
 where φmodel is the texture coordinate function provided with the model, and MT is a 3 by 3 matrix representing an affine or projective transformation of the 2D texture coordinates using homogeneous coordinates. Such a transformation, sometimes limited just to scaling and/or translation, is supported by most renderers that use texture mapping 

### 11.2.4 透视校正插值

之前已讨论过，不赘述。但是注意一点：**注意：phi是三维空间到纹理空间的映射，不是屏幕空间到纹理空间的映射，这也是为什么要采用透视校正插值的原因**

### 11.2.5 连续性和“缝隙”

拓扑中的一个基本结论：**闭曲面和矩形不同胚**

因此，不连续性在纹理映射上几乎一定会发生的，因为不存在一个从纹理图（矩形）到三维物体表面（闭曲面）的同胚映射。（连续的双射）

因此，一定会产生“缝隙”（seam)。在前面讨论的几何映射中，已经存在缝隙了。比如球坐标和柱面坐标映射中atan2在π 和-π 处；比如正方体映射在棱处。

对于三角形网络的插值映射，这个问题值得深入讨论。**首先，三角形网络定义出来的仍然是一个闭曲面。所以一定存在不连续的地方。但是我们定义三角形网络是共点共边的，即两个面公共顶点的顶点信息是相同的，而顶点信息之一的uv坐标也是相同的，这会导致一个三角形中可能两个顶点对应的纹理坐标在图片的最左侧，而另一个顶点在图片的最右侧**，（联想平面图），这导致渲染这个三角形时对三角形内插值时基本是对整个图片插值，造成让人困惑的结果。

如图：中间图的左侧中间的混沌颜色就是这种情况。

![]( https://box.nju.edu.cn/f/69d7d14c93a14e278286/?dl=1 )

为了解决/避免这个问题，唯一的办法就是避免在缝隙处共用顶点的uv坐标。如上图右侧的做法，我们复制处于缝隙上的顶点，使其具有相同的3D坐标和其他信息，但是有不同的uv坐标。

在obj文件中**（猜想）**可以这么做，v中1、2具有相同的3D坐标，但是在vt中有不同的纹理坐标，在f中不同的三角形也根据情况引用不同的1或者2

v: 1 1.0 1.0 1.0

​	2 1.0 1.0 1.0

vt: 1 0.5 0.5

​	 2 0.7 0.7

f: 1 1 3 7

f: 2 2 6 8

## 11.3 纹理映射的反走样

### 11.3.1 像素在纹理空间的“足迹”

我们定义像素在纹理空间中的“足迹”（texture space footprint)如下：即像素对应的三维表面对应的纹理的一块范围。即像素对应在纹理图中覆盖的一块范围。

<img src=" https://box.nju.edu.cn/f/7c4dd448ac88431a8c34/?dl=1" style="zoom:67%;" />

我们之前定义了映射pi：(x, y, z)->(x_s, y_s)是从3D坐标到屏幕空间的映射，phi是从三维空间到纹理空间的映射。因此，求footprint可以被定义为一个复合映射：
$$
ψ = φ ◦ π^{−1}
$$
该映射与观察条件(viewing situation)和uv坐标映射均有关。比如说，当物体离相机很近时，footprint较小；而当物体离相机很远的时候，footprint会变大。

对该足迹进行近似（假设映射是“光滑”的）：
$$
\psi(x) = \psi(x_0)+J(x-x_0)
$$

$$
J = \begin{bmatrix}
\frac{\partial{u}}{\partial{x}} & \frac{\partial{u}}{\partial{y}}\\
\frac{\partial{v}}{\partial{x}} & \frac{\partial{v}}{\partial{y}}
\end{bmatrix}
$$

<img src=" https://box.nju.edu.cn/f/0ad895d29ca642379aa1/?dl=1" style="zoom:67%;" />

这告诉我们很多信息，在approximated的意义上告诉我们足迹的大小（和偏导大小有关）和性状（和偏导向量的方向有关）。

因此，渲染也可以看成对这个footprint的平行四边形的颜色取平均得到对应像素的颜色结果；本质上是对其做box-filter的sample；也可以不用box-filter，比如说elliptical weighted average（EWA） [[GPU Pro3\] 渲染篇 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/362053970) 

### 11.3.2 重建

<img src=" https://box.nju.edu.cn/f/ec68cd1656464140bcf8/?dl=1" style="zoom:67%;" />

上图：对不同footprint重建应该采用的方法，当足迹很小时，应该增加采样率（双线性采样等）；当足迹较大时，应该采用mipmap等方式。

**附：有关摩尔纹的基础了解** [从摩尔纹谈谈所谓的成像“玄学” - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/21478979) 

双线性采样：

```
Color tex_sample_bilinear(Texture t, float u, float v) {
u_p = u * t.width - 0.5
v_p = v * t.height - 0.5
iu0 = floor(u_p); iu1 = iu0 + 1
iv0 = floor(v_p); iv1 = iv0 + 1
a_u = (iu1 - u_p); b_u = 1 - a_u
a_v = (iv1 - v_p); b_v = 1 - a_v
return a_u * a_v * t[iu0][iv0] + a_u * b_v * t[iu0][iv1] +
b_u * a_v * t[iu1][iv0] + b_u * b_v * t[iu1][iv1]
}
```

对于很多系统来说，双线性插值是一个性能瓶颈，因为查找周围4个texel的颜色值有一定的memory latency(查找内存带来的延迟)。因此一些高性能系统会使用专门的硬件，包括专门的cache存放texel颜色值（这是因为渲染的空间连续性）。

### 11.3.3 MipMapping

对于足迹较大的情况，我们当然可以取所有覆盖的纹理像素取平均，但是如果等到渲染的时候再做这件事，可能会面临要取出数以千计的纹理像素的颜色值计算，这十分耗时，应该做预处理，这种预处理就是mipmap。

MIP map: MIP stands for ***multim in parvo***, means *much in a small space*.

MIPmap生成方法： For instance, starting with a 1024 × 1024 texture image, we could generate a mipmap with 11 levels: level 0 is 1024 × 1024; level 1 is 512 × 512, and so on until level 10, which has just a single texel. This kind of structure, with images that represent the same content at a series of lower and lower sampling rates, is called an **image pyramid**, based on the visual metaphor of stacking all the smaller images on top of the original 

问题变为：我们应该如何确定取第几级map上的哪个像素？

回答：

1. 先考虑最简单的情况，如果足迹就是一个正方形，其边长为D，那么我们应该取对数
   $$
   D \approx 2^k
   $$
   

   or precisely
   $$
   k = [log_2D]
   $$
   然后再第k层取纹理值，如果想更精确的话，可以再k和k+1层分别取值然后加权平均。

2. 如何确定足迹平行四边形的“长度”:    取最长边
   $$
   D = max \{{||u_x||, ||u_y||}\}
   $$
   其中
   $$
   u_x= （\frac{\partial{u}}{\partial{x}} ， \frac{\partial{v}}{\partial{x}}）\\
   u_y= （\frac{\partial{u}}{\partial{y}} ， \frac{\partial{v}}{\partial{y}}）
   $$
   算法的伪代码如下：（trillnear mipmap)

   ```
   Color mipmap_sample_trilinear(Texture mip[], float u, float v,
   matrix J) {
   D = max_column_norm(J)
   k = log2(D)
   k0 = floor(k); k1 = k0 + 1
   a = k1 - k; b = 1 - a
   c0 = tex_sample_bilinear(mip[k0], u, v)
   c1 = tex_sample_bilinear(mip[k1], u, v)
   return a * c0 + b * c1
   }
   ```

   **值得注意的是，由于mipmap得到的结果仍然是正方形，在处理大多数为非矩形平行四边形时不可避免地回出现artifact**

   <img src=" https://box.nju.edu.cn/f/4ef88529739a4d8ea014/?dl=1" style="zoom: 80%;" />

   **Anisotropic filtering:**  D的取值不取最长边，而取最短边。 A mipmap can be used with multiple lookups to approximate an elongated footprint better. The idea is to select the mipmap level based on the shortest axis of the footprint rather than the largest, then average together several lookups spaced along the long axis. (**见上图 Figure 11.21.**) 

   我的理解：用多个以短边为边长的小正方形近似平行四边形。

   

## 11.4 纹理映射的应用

### 11.4.1 控制着色参数

如金属度，粗糙度，漫反射系数等等量都可以在纹理上定义。

### 11.4.2 Normal map & Bump map(法线贴图/凹凸贴图)

1. 为什么要法线贴图/凹凸贴图：我们希望表现凹凸不平的表面，但是如果用建模来表示过于繁琐，也增大计算量。不如用贴图来解决让集合表面凹凸不平的目的。如果想要表现出凹凸不平，本质上是改变光滑物体表面的法线，使其与原来光滑几何表面的法线不同（这里表面很多时候是三角形网格）

2. 如何定义法线贴图：第一时间想到的是就用真实世界中的三维坐标不就行了？但是处于几方面考虑：1.比较复杂 2.如果物体发生形变，则作废。实际上的法线贴图定义在“正切空间”（tangent space)中。这不是一个绝对坐标系，而是对于曲面上的每个点都有自己对应的坐标系。该坐标系是三维坐标系，可以这么构建：利用
   $$
   u_x= （\frac{\partial{u}}{\partial{x}} ， \frac{\partial{v}}{\partial{x}}）\\u_y= （\frac{\partial{u}}{\partial{y}} ， \frac{\partial{v}}{\partial{y}}）
   $$
   的u_x.u_y正交化后作为基底，再加上二者外积的向量构成一个三维坐标系。可见，这是一个“**相对**”坐标系，对于光滑物体表面的每个点，其法向量应该是再（0，0，1)^T附近的。 [18.3 纹理/正切空间 (enjoyphysics.cn)](https://enjoyphysics.cn/Article1585#:~:text=现在把三角形平面法线 N 合并进来，我们便得到了一个在三角形平面上的3D TBN基（3D,TBN-basis），我们将它称为纹理空间（texture space）或正切空间（tangent space）。 注意，正切空间通常会随着三角形而变化（参见图18.5）。) 

3. 凹凸贴图：可以是做“**高度图**”，用灰值表示与“原来的光滑表面”的高度差，白色代表高于，黑色代表低于。凹凸贴图也是一个表示**相对关系**的贴图

4. **事实上，法线贴图是凹凸贴图的导数**（ **Deriving a normal map from a bump map is simple: the normal map (expressed in the tangent frame) is the derivative of the bump map**）

   

### 11.4.3 Displacement Map

根据上面对法线贴图和凹凸贴图的阐述，我们可以发现这两者并没有真正地改变几何，而只是把凹凸“画上去”了而已，这会导致一些问题，比如：**一个看起来凹凸的物体（经过凹凸贴图）的影子确实光滑的（与渲染的其他部分失去联动）。**

有一些方法可以让纹理并不只是***shading tricks***，而让其真正地影响几何。其中一个最简单的方法就是displacement map。

它和凹凸贴图一样，也是一张灰度图，也是用灰值表示与“原来的光滑表面”的高度差，白色代表高于，黑色代表低于。不一样的是，它会改变几何，它会将白色（高点）拔高，把低点在原来的基础上降低。（沿着该点法线移动，移动前后法线方向基本不变） Rather than deriving a shading normal from the height map while using the smooth geometry, a displacement map actually changes the surface, moving each point along the normal of the smooth surface to a new location. The normals are roughly the same in each case, but the surface is different. 

该方法一般适用于由很多小正方形构成的光滑表面。

### 11.4.4 Shadow map

阴影是表现场景中物体的几何位置关系的很重要的线索。在光追中，阴影生成被包含在渲染程序中，但是对于光栅化来说，如何表现阴影是一个难题。因为在光栅化中，渲染是object-oriented的，是一个一个物体孤立渲染的，并不考虑物体之间的位置关系（z-buffer是考虑，但是没完全考虑）。shadow map是借助纹理映射的思想解决光栅化生成阴影的一个方法。

**shadow map应用条件/限制：点光源**

shadow map原理：**光能照到的地方，把人眼移过去，也能看见。因此shadow map本质上就是zbuffer**；与zbuffer的区别在于，zbuffer在光栅化的过程中是不断更新的，表示的是“目前为止”离相机最近的点集，而shadow map是固定的，是整个场景离光源最近的点集。

具体：懒得翻译：

 A shadow map is calculated in a separate rendering pass **ahead of time:** simply rasterize the whole scene as usual, and retain the resulting depth map (there is no need to bother with calculating pixel values). Then, with the shadow map in hand, you perform an ordinary rendering pass, and **when you need to know whether a fragment is visible to the source, you project its location in the shadow map (using the same perspective projection that was used to render the shadow map in the first place) and compare the looked-up value dmap with the actual distance d to the source**. If the distances **are the same**, the fragment’s point is illuminated; if the d>d_map, that implies there is a different surface closer to the source, so it is shadowed. 

注意：**are the same**：在计算机中，**要考虑精度问题**，一般会设计一个小epsilon，小于这个误差的都被认为相等。在shadow map中，这个误差被称为阴影误差shadow bias. 

 The phrase “if the distances are the same” should raise some red flags in your mind: since all the quantities involved are approximations with limited precision, we can’t expect them to be exactly the same. For visible points, the d ≈ dmap but sometimes d will be a bit larger and sometimes a bit smaller. For this reason, a tolerance is required: a point is considered illuminated if d − dmap < 	. This tolerance 	 is known as shadow bias 

### 11.4.5 Environment Map

想法：**用贴图表示复杂的光照条件**

对于从很远处照来的光，我们可以近似认为其为方向光（比如自然光：太阳光）。ENVIRONMENT MAP假设环境中各个方向有许多方向光，在三维空间中，和方向有关的函数都可以用球面上的函数来表示，而球面又可以通过纹理坐标映射转换为一个平面图。因此，用来表示环境中各个方向光强（和各个分量RGB强度，即颜色）的贴图--环境光贴图诞生了。其应用：

- 在光追中：

  ```
  trace_ray(ray, scene) {
  if (surface = scene.intersect(ray)) {
  return surface.shade(ray)
  } else {
  u, v = spheremap_coords(r.direction)
  return texture_lookup(scene.env_map, u, v)
  }
  }
  ```

  即当trace ray发出且没有与任何物体相交时，返回environment map定义的该方向的方向环境光的颜色和光强

- 在光栅化中：

  ```
  shade_fragment(view_dir, normal) {
  out_color = diffuse_shading(k_d, normal)
  out_color += specular_shading(k_s, view_dir, normal)
  u, v = spheremap_coords(reflect(view_dir, normal))
  out_color += k_m * texture_lookup(environment_map, u, v)
  }
  ```

  以上技术被称为 ***reflection mapping*** ，但是只是非常简单的镜面反射，即只能反射环境光，不能发射其他物体的样子。（ which is computed in the same way as in a ray tracer, but simply looks up directly in the environment map with no regard for other objects in the scene: ）

关于environment map使用的从球面到平面的映射：普遍做法就是球面映射，但是正方体映射会更好，更有效率且没有两极的distortion。

## 11.5 Procedrual 3D Texture

3D texture: 在三维空间中的每个点都定义RGB值。在渲染中，只有点在待渲染物体表面时才查询或计算值，在物体内部或外部时不使用。

3D texture的优缺点：

- 优点：平凡映射。不需要3D->2D; 没有distortion问题
- 缺点：3D texture储存将占用大量内存，查询某个三维点的RGB值也将有更大的memory latency

为了克服以上缺点，我们一般使用Procedrual 3D Texture(程式化生成的三维纹理)

**何谓程式化生成的纹理？**纹理的颜色值生成/排布是有规律的，是根据数学算法程式计算得出的，不需要使用时一个一个查询

>  The downside to 3D textures is that storing them as 3D raster images or volumes consumes a great deal of memory. For this reason, 3D texture coordinates are most commonly used with procedural textures in which the texture values are computed using a mathematical procedure rather than by looking them up from a texture image.  

### 11.5.1 3D stripe texture(线条纹理)

假设条纹颜色为c0；条纹间隙颜色为c1；则生成纹理的算法为：

```c
RGB stripe( point p )
if (sin(xp) > 0) then
return c0
else
return c1
```

如果希望条纹的间距可控（通过参数w控制）

```c
RGB stripe( point p, real w)
if (sin(πxp/w) > 0) then
return c0
else
return c1
```

如果希望条纹的边界模糊一点，那就使用插值：

```c
RGB stripe( point p, real w )
t = (1 + sin(πpx/w))/2
return (1 − t)c0 + tc1
```

以上三者的示意图：

<img src=" https://box.nju.edu.cn/f/ad5492c4130141a3be13/?dl=1" style="zoom:50%;" />

### 11.5.2 Solid Noise

具体内容见虎书相关部分

相关：  pseudorandom  [伪随机数_百度百科 (baidu.com)](https://baike.baidu.com/item/伪随机数/104358#生成方法) 

 mach bands :  [解密视错觉 | 马赫带和亮度对比错觉 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/25720633?group_id=827796943773237248) 

lattice 格  [Lattice学习笔记01：格的简介 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/161411204) 

 Hermite interpolation ： [埃尔米特插值公式_百度百科 (baidu.com)](https://baike.baidu.com/item/埃尔米特插值公式/18983057) 

### 11.5.3 Turbulence