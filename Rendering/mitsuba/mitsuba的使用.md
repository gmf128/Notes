# Mitsuba

## 1. Scene

Scene的格式：xml

一个典型的场景文件的组成：

```xml
<scene version="3.0.0">

...

</scene>
```

组成部分：

### 1.1 超参数及变量定义

```xml
<default name="spp" value="128"/>
<default name="res" value="256"/>
<default name="max_depth" value="6"/>
<default name="integrator" value="path"/>
```

1. spp：sample per pixel
2. res: 分辨率
3. max_depth：迭代次数
4. integrator: 积分器

### 1.2 integrator

Example 1

```xml
<integrator type='$integrator'>
        <integer name="max_depth" value="$max_depth"/>
</integrator>
```

Example 2

```xml
<integrator type='direct'/>
```

integrator的类型：

1. path：路径追踪
2. prb：path replay backpropagation 可微渲染器（delio vicini et al. ）
3. rb: radiative backpropagation （nimier-david et al.）

### 1.3 shape

```xml
<shape type="ply" id="teapot">
        <string name="filename" value="meshes/teapot.ply"/>
        <bsdf type="diffuse">
            <rgb name="reflectance" value="0.9 0.9 0.0"/>
        </bsdf>
</shape>
```

### 1.4 sensor

### 1.5 bsdf

例子

```xml
<bsdf type="diffuse" id="gray">
        <rgb name="reflectance" value="0.85, 0.85, 0.85"/>
</bsdf>
```

具体见2：BSDF

### 1.6 emitter

一个典型的Sensor:

```
PerspectiveCamera[
  x_fov = [39.3077],
  near_clip = 0.001,
  far_clip = 100,
  film = HDRFilm[
    size = [128, 128],
    crop_size = [128, 128],
    crop_offset = [0, 0],
    sample_border = 0,
    compensate = 0,
    filter = TentFilter[radius=1.000000],
    file_format = OpenEXR,
    pixel_format = rgb,
    component_format = float32,
  ],
  sampler = IndependentSampler[
    base_seed = 0
    sample_count = 128
    samples_per_wavefront = 1
    wavefront_size = 0
  ],
  resolution = [128, 128],
  shutter_open = 0,
  shutter_open_time = 0,
  to_world = [[-1, 0, 0, 0],
              [0, 1, 0, 0],
              [0, 0, -1, 4],
              [0, 0, 0, 1]]
]
```

## 2.BSDFs

In Mitsuba 3, **BSDFs are assigned to *shapes***, which describe the visible surfaces in the scene. In the scene description language, this assignment can **either be performed by nesting BSDFs within shapes, or they can be named and then later referenced by their name**. The following fragment shows an example of both kinds of usages: 

```xml
<scene version=3.0.0>
    <!-- method 1(recommanded) -->
    <!-- Creating a named BSDF for later use -->
    <bsdf type=".. BSDF type .." id="my_named_material">
        <!-- BSDF parameters go here -->
    </bsdf>

    <shape type="sphere">
        <!-- Example of referencing a named material -->
        <ref id="my_named_material"/>
    </shape>
    
	<!-- method 2>
    <shape type="sphere">
        <!-- Example of instantiating an unnamed material -->
        <bsdf type=".. BSDF type ..">
            <!-- BSDF parameters go here -->
        </bsdf>
    </shape>
</scene>
```

第一种方式（即先定义BSDF，后在shape里引用BSDF）更被推荐，原因即是可复用，而且减少内存开销。

下面分小节展示BSDF的种类：

### 2.1 Smooth diffuse material (diffuse)

理想漫反射材质。

参数：reflectance。RGB值，表示albedo

有两种定义方式：

1. 单色RGB
2. 图片纹理贴图

```xml
<!-- 单色RGB -->
<bsdf type="diffuse">
    <rgb name="reflectance" value="0.2, 0.25, 0.7"/>
</bsdf>

<!-- 图片纹理贴图 -->
<bsdf type="diffuse">
    <texture type="bitmap" name="reflectance">
        <string name="filename" value="wood.jpg"/>
    </texture>
</bsdf>
```

效果：

<img src="mitsuba%E7%9A%84%E4%BD%BF%E7%94%A8.assets/diffuse.png" style="zoom:50%;" />

纹理贴图的效果：

<img src="mitsuba%E7%9A%84%E4%BD%BF%E7%94%A8.assets/1695295009732.png" alt="1695295009732" style="zoom: 50%;" />

### 2.2 Smooth dielectric material (dielectric)

光滑的绝缘体材质（如塑料，玻璃）

参数：

1. int_ior：**ior指的是index of reflectance**  Interior index of refraction specified numerically or using a known material name. (Default: bk7 / 1.5046) **bk7指的是BK7玻璃**
2. ext_ior:  Exterior index of refraction specified numerically or using a known material name. (Default: air / 1.000277) 

3.  specular_reflectance ： Optional factor that can be used to modulate the specular reflection component. Note that for physical realism, this parameter should never be touched. (Default: 1.0) 
4.  specular_transmittance ： Optional factor that can be used to modulate the specular transmission component. Note that for physical realism, this parameter should never be touched. (Default: 1.0) 

这个插件模拟了两个折射率不匹配的介质之间的界面（例如，水↔空气）。可以独立指定外部和内部的折射率值，其中“外部”指的是包含表面法线的一侧。当没有给出参数时，插件激活默认值，描述了硼硅酸盐玻璃（BK7）↔空气界面。

在这个模型中，**假设表面的微观结构是完全光滑的，导致一个由狄拉克δ分布描述的退化BSDF。这意味着对于任何给定的入射光线，模型总是散射到一组离散的方向，而不是连续的。如果需要描述粗糙表面微观结构的类似模型，请参考roughdielectric插件**。

以下代码片段描述了一个简单的空气到水的界面。

```xml
<shape type="...">
    <bsdf type="dielectric">
        <string name="int_ior" value="water"/>
        <string name="ext_ior" value="air"/>
    </bsdf>
<shape>
```

效果：

<img src="mitsuba%E7%9A%84%E4%BD%BF%E7%94%A8.assets/dielectric.png" style="zoom:50%;" />

### 2.3 Thin dielectric

<img src="mitsuba%E7%9A%84%E4%BD%BF%E7%94%A8.assets/1695296142848.png" alt="1695296142848" style="zoom:50%;" />



区别

### 2.4 Rough Dieletric

和前面的区别就是在于：

参数中终于有了distribution！（说明不再是理想情况）

不同的参数：

1. distribution的取值：beckmann或ggx

2. alpha, alpha_u, alpha_v.:  Specifies the **roughness** of the unresolved surface micro-geometry along the tangent and bitangent directions. When the Beckmann distribution is used, this parameter is equal to the *root mean square* (RMS) slope of the microfacets. **alpha is a convenience parameter to initialize both alpha_u and alpha_v to the same value**. (Default: 0.1) 

 **为了直观了解表面粗糙度参数的效果，请考虑以下粗略分类：数值为0.01对应于表面光洁度上的轻微瑕疵，数值为0.1表示相对粗糙，数值为1表示极其粗糙（例如，蚀刻或磨削表面）。数值显著高于此的可能不太现实。** 

```xml
<bsdf type="roughdielectric">
    <string name="distribution" value="beckmann"/>
    <float name="alpha" value="0.1"/>
    <string name="int_ior" value="bk7"/>
    <string name="ext_ior" value="air"/>
</bsdf>
```

采用Beckmann分布：

<img src="mitsuba%E7%9A%84%E4%BD%BF%E7%94%A8.assets/rough_dielectric.png" style="zoom:50%;" />

采用GGX分布：

<img src="mitsuba%E7%9A%84%E4%BD%BF%E7%94%A8.assets/rough_dielectric_2.png" style="zoom:50%;" />







### 2.X Principled BRDF

精确的BSDF是一个复杂的BSDF，具有许多反射和透射的模式。**它能够产生从金属到粗糙介质等多种材料类型。此外，输入参数集设计为艺术家友好型，不直接对应物理单位。**

该实现基于Brent Burley的论文《迪士尼的基于物理的着色》[Bur12]和《将迪士尼BRDF扩展为具有综合次表面散射的BSDF》[Bur15]。

<img src="mitsuba%E7%9A%84%E4%BD%BF%E7%94%A8.assets/1695297750357.png" alt="1695297750357" style="zoom:80%;" />

```xml
<bsdf type="principled">
    <rgb name="base_color" value="1.0,1.0,1.0"/>
    <float name="metallic" value="0.7" />
    <float name="specular" value="0.6" />
    <float name="roughness" value="0.2" />
    <float name="spec_tint" value="0.4" />
    <float name="anisotropic" value="0.5" />
    <float name="sheen" value="0.3" />
    <float name="sheen_tint" value="0.2" />
    <float name="clearcoat" value="0.6" />
    <float name="clearcoat_gloss" value="0.3" />
    <float name="spec_trans" value="0.4" />
</bsdf>
```



Film

Abstract film base class - used to store samples generated by Integrator implementations.

To avoid lock-related bottlenecks when rendering with many cores, rendering threads first store results in an “image block”, which is then committed to the film using the put() method.

IndependentSampler



## 3.Sampler

在渲染图像时，Mitsuba 3需要解决一个涉及场景中的几何、材质、光源和传感器的高维积分问题。由于这些积分的数学复杂性，通常无法通过解析的方式来求解它们，而是通过在大量不同位置上评估被积函数的数值来进行数值求解。样本生成器是这个过程中的一个关键组成部分：它们在一个（假设的）无限维超立方体中生成构成这些样本的点的规范表示。

为了完成其工作，渲染算法或积分器将向样本生成器发送许多查询。**一般来说，它会请求该无限维点的连续的1D或2D分量，并将其映射到一个更方便的空间（例如，表面上的位置）。这使得它能够构建光路径，最终评估光线在场景中的传播**。

### 3.1 IndependentSampler

参数：

| **Parameter** | Type    | Description                              |
| ------------- | ------- | ---------------------------------------- |
| sample_count  | integer | Number of samples per pixel (Default: 4) |
| seed          | integer | Seed offset (Default: 0)                 |

该独立采样器产生一系列**独立且均匀分布的伪随机数**。在内部，**它依赖于Melissa O'Neill开发的PCG32随机数生成器**。

这是最基本的样本生成器，因为没有采取任何措施来避免样本聚集，使用该插件生成的图像通常需要更长时间才能收敛。从下面的图中可以看到，将样本投影到一个二维单位正方形上，我们可以看到既有接收到很少样本的区域（即我们对该区域的函数行为了解较少），也有很多样本非常接近的区域（这些样本可能具有非常相似的值），这将导致渲染图像的方差较高。

该采样器使用确定性过程进行初始化，这意味着在后续的Mitsuba运行中应该创建相同的图像。然而，在使用多线程和/或机器进行渲染时，由于样本的排序受操作系统调度器的影响，这不再成立。尽管这些影响应该是可以忽略的，单精度浮点数的相对误差大约为机器epsilon（ $6\times10^{-8}$）。

<img src="mitsuba%E7%9A%84%E4%BD%BF%E7%94%A8.assets/1695052016603.png" alt="1695052016603" style="zoom:50%;" />

### 3.2 Stratified

 分层采样生成器将域分成离散数量的分层，并在每个分层中生成一个样本。通常情况下，与独立采样器相比，这会减少样本聚集现象，并且具有更好的收敛性。 

| **Parameter** | Type    | Description                                                  |
| ------------- | ------- | ------------------------------------------------------------ |
| sample_count  | integer | Number of samples per pixel (Default: 4)This number should be a square number |
| seed          | integer | Seed offset (Default: 0)                                     |
| jitter        | boolean | Adds additional random jitter withing the stratum (Default: True) |

<img src="mitsuba%E7%9A%84%E4%BD%BF%E7%94%A8.assets/1696744550390.png" alt="1696744550390" style="zoom:33%;" />









## 4. Textures

 [Textures - Mitsuba 3](https://mitsuba.readthedocs.io/en/latest/src/generated/plugins_textures.html) 

### 4.1 Bitmap texture(bitmap)

推荐预先了解：什么是gamma encode/decode & sRGB

 [Gamma、Linear、sRGB 和Unity Color Space，你真懂了吗？ - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/66558476) 

> 通常要使用成对的γ进行编码和解码，比如sRGB空间，它实际上是一个规范，它规定用于编码的γ值近似为1/2.2（即0.45，别忘了，编码用的γ都是小于1.0的），它规定用于解码的γ值近似为2.2（别忘了解码用的γ都是大于1.0的），也就是说，当我们说sRGB空间时，实际上是指它用于编码和解码的成对γ值。
> 因为我们的显示器几乎都是sRGB空间值（也就是用γ=2.2进行解码），所以我们在jpg或png中基本上保存的也是sRGB空间值（也就是用γ=1/2.2进行编码）。
> **简而言之，sRGB实际上就是定义了用于编码和解码的γ**，所以，你可以说γ1/2.2和γ2.2其实都是sRGB空间。 

>  **到底什么纹理应该是sRGB，什么是Linear？** 
>
> 关于这一点，我个人有一个理解：所有需要人眼参与被创作出来的纹理，都应是sRGB（如美术画出来的图）。所有通过计算机计算出来的纹理（如噪声，Mask，LightMap）都应是Linear。
>
> 这很好解释，人眼看东西才需要考虑显示特性和校正的问题。而对计算机来说不需要，在计算机看来只是普通数据，自然直接选择Linear是最好的。

有了上面的前置知识，就可以理解Mitsuba对Bitmap型纹理做的预处理了：

This plugin provides a bitmap texture that performs interpolated lookups given a JPEG, PNG, OpenEXR, RGBE, TGA, or BMP input file.

When loading the plugin, the data is **first converted into a usable color representation** for the renderer:

- In rgb modes, sRGB textures are converted into a **linear color space.**
- In spectral modes, sRGB textures are *spectrally upsampled* to plausible smooth spectra [[JH19](https://mitsuba.readthedocs.io/en/latest/zz_bibliography.html#id9)] and stored an intermediate representation that enables efficient queries at render time.
- In monochrome modes, sRGB textures are converted to grayscale.

These conversions **can alternatively be disabled with the raw flag**, e.g. when textured data is already in linear space or does not represent colors at all.

再来看纹理参数；

| **Parameter** | **Type**      | **Description**                                              | **Flags** |
| ------------- | ------------- | ------------------------------------------------------------ | --------- |
| filename      | string        | Filename of the bitmap to be loaded                          |           |
| bitmap        | Bitmap object | When creating a Bitmap texture at runtime, e.g. from Python or C++, an existing Bitmap image instance can be passed directly rather than loading it from the filesystem with filename. |           |
| data          | tensor        | Tensor array containing the texture data. Similarly to the bitmap parameter, this field can only be used at runtime. The raw parameter must also be set to true. | P, ∂      |
| filter_type   | string        | Specifies how pixel values are interpolated and filtered when queried over larger UV regions. The following options are currently available:`bilinear` (default): perform bilinear interpolation, but no filtering.`nearest`: disable filtering and interpolation. In this mode, the plugin performs nearest neighbor lookups of texture values. |           |
| wrap_mode     | string        | Controls the behavior of texture evaluations that fall outside of the [0,1] range. The following options are currently available:`repeat` (default): tile the texture infinitely.`mirror`: mirror the texture along its boundaries.`clamp`: clamp coordinates to the edge of the texture. |           |
| raw           | boolean       | Should the transformation to the stored color data (e.g. sRGB to linear, spectral upsampling) be disabled? You will want to enable this when working with bitmaps storing normal maps that use a linear encoding. (Default: false) |           |
| to_uv         | transform     | Specifies an optional 3x3 transformation matrix that will be applied to UV values. A 4x4 matrix can also be provided, in which case the extra row and column are ignored. | P         |
| accel         | boolean       | Hardware acceleration features can be used in CUDA mode. These features can cause small differences as hardware interpolation methods typically have a loss of precision (not exactly 32-bit arithmetic). (Default: true) |           |
|               |               |                                                              |           |

举例子：

```xml
<texture type="bitmap">
    <string name="filename" value="texture.png"/>
    <string name="wrap_mode" value="mirror"/>
</texture>
```



property* wo

Normalized outgoing direction in local coordinates