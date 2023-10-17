# 9：Signal Processing

从相机捕获的图像信息（光）是连续的，而生成的照片是离散的。这中间经过了采样过程。

连续<——>离散；采样和重建（sampling and reconstrunction)是本章的主题。

## 9.1 数字音频信号：一维采样

![]( https://box.nju.edu.cn/f/cc5cd962d8ea4db19438/?dl=1 )

麦克风将声波转化为时间连续的电信号。时间连续的电信号再经过A/D转换器（analog-digital converter，ADC).ADC每秒测量数千次电压，生成整数流，而后者可以被存储在大多数媒体上。

在播放时，数据被以一定速率读出并传给DAC（digital-analog converter)，DAC将接收的数字信号转换为电压。如果我们采样足够，能够忠实地反映原电压的变化，那么重建后的模拟信号基本和原信号一样。

A/D前的Lowpass filter: 过滤高频信号，避免高频信号低采样带来的artifact，且高频信号很多是杂音

D/A后的Lowpass filter: 这是为了去除由D/A特性造成的artifact。D/A在获得采样信号时立即重建电压，但是当没有新的数字信号传来时，D/A保持不变。这就会造成“阶梯型”变化，这突变会带来高频噪音，因此需要一个filter进行过滤。

## 9.2 卷积 Convolution



