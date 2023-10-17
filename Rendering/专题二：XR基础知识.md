# VR/AR基础知识

## 1.名词解释

### 1.1 FOV(视场角)

 [AR/VR 显示技术原理 (上) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/90738288) 

### 1.2 IPD

指的是瞳间距。Inter-pupillary distance

瞳距在不同的人之间，性别之间以及人种之间都不一样。错误的瞳距计算会影响眼镜的对齐，图形失真，视觉疲劳以及头晕。一般成年人的凭据瞳距是63mm，浮动范围是 50–75mm。孩子的最小瞳距约是 40 mm。 

Stereo IPD  用于描述立体眼镜或头戴式显示设备中的瞳距设置 

例子：下面是一个VR/AR相关程序的例子(Forveated-Nerf)

```python
parser.add_argument('-i', '--ipd', type=float, default=0.06,
                    help="The stereo IPD. Render mono images/video if this value is zero")
```

### 1.3 分辨率

1920x1080习惯上叫1080p，

2560x1440习惯上叫1440p或2K，

3840x2160习惯上叫2160p或4K

这里的P意思是逐行扫描，其实还有i，意思是隔行扫描。带宽要求是480i<480p<1080i<720p。

以上都是建立在16:9的屏幕基础上。