# 10：Surface Shading

## 10.1 Diffuse Shading

### 10.1.1 朗博体（Lambertian objects)

很大一部分物体的颜色并不会随着观察角度的变化而发生变化。比如纸张，这些物体总是matte(无光泽的，表面粗糙的）。这些物体被成为朗博介质物体。

>  朗伯体是指当入射能量在所有方向均匀反射，即入射能量以入射点为中心，在整个半球空间内向四周各向同性地反射能量的现象，称为漫反射，也称各向同性反射，一个完全的漫射体称为朗伯体。 

> 若一扩展光源的[发光强度](https://baike.baidu.com/item/发光强度?fromModule=lemma_inlink)为dI∝cosθ，即其亮度B与方向有关。这类发射体称为[余弦](https://baike.baidu.com/item/余弦?fromModule=lemma_inlink)发光体，或朗伯（J.H.Lambert）发光体，上述按cosθ规律发射[光通量](https://baike.baidu.com/item/光通量?fromModule=lemma_inlink)的规律，称为朗伯余弦定律。式中dI为扩展光表面的每块面元dS沿某方向**r**的发光强度，θ为**r**与[法线](https://baike.baidu.com/item/法线/6492874?fromModule=lemma_inlink)**n**的夹角。
>
> 一个均匀的球形余弦发光体，从远处的观察者看来，与同样半径同样亮度的一个均匀发光圆盘无异。太阳看起来近似像一个亮度均匀的圆盘，这表明它接近于一个余弦发光体。此外，日常生活中常见的光源，许多也接近于余弦发光体。
>
> [发光强度](https://baike.baidu.com/item/发光强度/1073260?fromModule=lemma_inlink)和亮度的概念不仅适用于自己发光的物体，也可以应用到反射体。光线射到光滑的表面上，定向地发射出去；射到粗糙的表面上时，它将朝向所有方向[漫射](https://baike.baidu.com/item/漫射?fromModule=lemma_inlink)。一个理想的漫射面，应是遵循朗伯定律的，即不管[入射光](https://baike.baidu.com/item/入射光?fromModule=lemma_inlink)来自何方，沿各方向[漫射光](https://baike.baidu.com/item/漫射光/3073348?fromModule=lemma_inlink)的发光强度总与cosθ成正比，从而亮度相同。积雪、刷粉的白墙或十分粗糙的白纸表面，都很接近这类理想的漫射面。这类物体称为朗伯[反射体](https://baike.baidu.com/item/反射体/1614339?fromModule=lemma_inlink)。

### 10.1.2 朗博反射模型

在介绍反射模型之前，首先要说明的是接下来的所有公式都是在world cordinate中的（即没有经过射影变换之前）,因为射影变换会导致角度发生改变（不保证平行，不保角），而朗博模型关键的就是法线和光线的夹角余弦，因此只有在世界坐标系下才能得到正确的结果。

**Lambert`s cosine law:**
$$
c \propto {n}·{l}
$$
where c is color, n is surface normal and l is the direction of light source.

l常常与物体位置无关，这是因为我们经常使用方向光（正如softras中），方向光假定了光源位置里物体很远，如自然光。

在实际操作中，还有两个参数，**一个参数c_r称为漫反射系数**，它是一个三维向量，分别对应物体表面对RGB三色光的不同反射程度。每个分量被限制在[0, 1]；**另一个参数是光强系数c_l**。

“完全体”的公式可以写成：
$$
c = c_rc_lmax(0, n·l)
$$
max(0, n·l)的原因：<img src=" https://box.nju.edu.cn/f/206a1638efd54f2eabbf/?dl=1" style="zoom: 80%;" />

### 10.1.3 环境光（Ambient Shading)

真实世界中，并不是照不到的地方就一片漆黑，总要有些环境光的存在。

加上环境光的补正后，公式变为：
$$
c = c_r(c_a+c_l\ max(0, \ n·l))
$$
为了保证算出来的RGB值不超上限，我们一般保证
$$
c_a+c_l \le (1, 1, 1)
$$

### 10.1.4 基于顶点的漫反射

这里虎书提了一下如何获得顶点法线，一些模型已经提供了，对于没有提供顶点法线的模型，一个简单得方法是吧共用该顶点的面的面法向量取平均，然后归一化变为单位向量。此外，一些特殊模型可以特殊获取点法向量：如一个近似球的多面体，顶点的法向量就是球心到顶点的连线所在的直线的方向向量。

## 10.2 Phong Shading

Phong反射模型适用于那些：**大体上是漫反射的，但是有高光**的材质。

高光的颜色一般是光源的颜色，材质本身的颜色似乎影响不是很大。这是因为高光是光的反射，物体表面的颜色是漫反射光带来的。

### 10.2.1 Phong反射模型

我们希望视线方向**等于反射光方向及其附近**时感受到高光，并且在离开反射光方向后**快速衰减**。

因此，我们希望不是：
$$
c \propto (e·l)
$$
而是
$$
c \propto (e·l)^p
$$
一个初步的公式，表达高光项：
$$
c = c_lmax(0,e·l)^p
$$
**p成为Phong指数**。改变p的效果变化：

<img src=" https://box.nju.edu.cn/f/dff6848e980842be9f2e/?dl=1" style="zoom:67%;" />

<img src=" https://box.nju.edu.cn/f/122824a27e284aa0a4bc/?dl=1" style="zoom:80%;" />

加上漫反射项，环境光项后的综合公式：
$$
c = c_r(c_a+c_lmax(0, n·l))+c_lc_p(h·n)^p
$$
值得说明一下的是c_p:	c_p是镜面反射控制项，三维，让我们可以改变高光的颜色，这对于一些特殊材质，如金属有用（金属c_p = c_r)。

Phong 顶点插值：利用重心坐标插值，**我认为有失真嫌疑**



## 10.3 艺术化渲染

![]( https://box.nju.edu.cn/f/cdfb0bdca36a4a7997fd/?dl=1 )

具体line drawing 和cool-to-warm shading可以看虎书，但是内容也不多，艺术化渲染可以之后了解。