# 自适应阈值（adaptiveThreshold）杂谈

本文主要介绍我对OpenCV自适应阈值函数-adaptiveThreshold的理解，包括如何合理设置参数大小、解释参数含义和一些注意事项等，最后对OpenCV中该部分源码浅尝辄止地一些分析。

## 基础原理
本节简单介绍自适应阈值的原理，建议直接阅读冈萨雷斯《数字图像处理 第四版》10.3.7.1节-“基于局部图像性质的可变阈值处理”，以下内容大致摘自于此。  

令$m_{xy}$和$\sigma_{xy}$是图像中以$(x,y)$为中心的邻域$S_{xy}$所包含像素集合的均值和标准差。

$$
T_{xy}=a\times\sigma_{xy}+b\times m_{xy}
$$

式中，$a$和$b$是非负常数，计算得到的二值图像$g(x,y)$为：

$$ 
g(x,y)=\left\{\begin{aligned}1&,f(x,y)>T_{xy} \\0&,f(x,y)\le T_{xy}\end{aligned}\right.
$$

式中，$f(x,y)$是输入图像，对图像中所有像素应用该公式，则获取分割后的二值图。

## adaptiveThreshold函数
OpenCV中对应的自适应阈值函数声明如下：
``` c++
cv::adaptiveThreshold (InputArray src, OutputArray dst, double maxValue, int adaptiveMethod, int thresholdType, int blockSize, double C)
```
关于各参数的解释**强烈**建议直接查看[官方文档](https://docs.opencv.org/4.5.2/d7/d1b/group__imgproc__misc.html#ga72b913f352e4a1b1b397736707afcde3)，本文重点不在于此，这里不再赘述。

### 对函数的理解
官方文档中对$T(x,y)$的计算方式如下[链接](https://docs.opencv.org/4.5.2/d7/d1b/group__imgproc__misc.html#gaa42a3e6ef26247da787bf34030ed772c)。这里给出公式：

$$
T(x,y)=F(x,y)-C
$$

式中$F$代表`adaptiveMethod`方法，F(x,y)的计算结果为对图像使用大小为$blockSize\times blockSize$的盒状滤波核(使用`ADAPTIVE_THRESH_MEAN_C`)或者高斯滤波核(使用`ADAPTIVE_THRESH_GAUSSIAN_C`)卷积在像素点$(x,y)$的值。$C$为函数中参数`C`。
### 对函数的理论理解
可以看到上节中$T(x,y)$与理论书中$T_{xy}$在$a=0$、$b=1$时的表示基本一致。

从原始图像$f(x,y)$到二值图像$g(x,y)$，可以从空间域和频率域理解：
- 从空间域理解：自适应阈值只跟该像素点的邻域相关。当该局部区域偏亮时，$T(x,y)$变大；反之当该局部区域偏暗时，$T(x,y)$变小。
- 从频率域理解：盒装滤波和高斯滤波均为低通滤波器，原始图像$f(x,y)$与低通图像$F(x,y)$做差分相当于高通滤波，获得的是高频图像，去除了图像中光线不均匀等低通信息。

可以看到自适应阈值比全局阈值针通过对局部图像进行计算实现可变阈值，有更强的适应能力，因此可以针对光照不均匀的图像有更好的分割效果。相关案例可参考[OpenCV官方文档](https://docs.opencv.org/4.5.2/d7/d4d/tutorial_py_thresholding.html)或《数字图像处理》中相关章节。
### 函数参数选择
这里只介绍`blockSize`和`C`两个数值的设置，可以配合公司开发的“Sirius Studio”软件更好的理解和掌握。准则总结如下：

1. 当想过滤掉更多的噪声时（当然有可能同时过滤掉有用信息），可以降低`blockSize`值或增大`C`值（当选择`THRESH_BINARY_INV`时增大`C`值，当选择`THRESH_BINARY`时降低`C`值，甚至将其置为负值）。
   
    <small>

    - 从频率域解释：当降低`blockSize`值时，低通图像将含有更多的高频信息，因此差分后，将剩下更少更高频的信息；当增大`C`值时（当选择`THRESH_BINARY_INV`时），表示选择更高频的信息。
  
    - 从空间域考虑：当降低`blockSize`数值时，像素点$f(x,y)$的权重更大，只有当$f(x,y)$与邻域$S_{xy}$差异更大时才会被提取；当增大`C`值时（当选择`THRESH_BINARY_INV`时），表示只提取$f(x,y)$与邻域$S_{xy}$差异更大的信息。

    </small>

2. 当有用目标面积较大时，为了分割出完整的结构，可以增大`blockSize`数值，同时适当调整`C`值。
    
    <small>

    从获得的二值图中可以看出，当增大`blockSize`时，图像中线条更为粗、长，反之图像中线条更为细、小。

    - 从频率域解释：目标区域只有在边缘才是高频信息，中心往往是低频信息（图像灰度变化相对比较平坦）；
    
    - 从空间域解释：当`blockSize`较小时，对于目标区域中心的部分，$f(x,y)$与其邻域$S_{xy}$差异不明显。
    
    </small>

## 注意事项
- `ADAPTIVE_THRESH_MEAN_C`与`ADAPTIVE_THRESH_GAUSSIAN_C`的选择。使用高斯滤波核心可能会获取更好的效果，但是当`blockSize`较大时，耗时相较盒状滤波核极为严重。

    以下对2000*2000分辨率随机生成的图像进行测试，结果仅供参考，其耗时如下表所示，单位ms。

    | `blockSize` | `ADAPTIVE_THRESH_GAUSSIAN_C ` | `ADAPTIVE_THRESH_MEAN_C` |
    | ----------- | ----------------------------- | ------------------------ |
    | 3           | 11                            | 7                        |
    | 31          | 28                            | 7                        |
    | 131         | 119                           | 7                        |

- 自适应阈值可能会引入除目标信息之外的信息，当图像光照较为复杂时尤为明显，必要时可以配合固定阈值共同使用。
    
    <small>

    可以想象：自适应阈值只计算$f(x,y)$与$T(x,y)$的差值并不考虑其绝对值大小，比如对于$f(x,y)=100$，$T(x,y)=80$与$f(x,y)=200$，$T(x,y)=180$的两个像素点，自适应阈值是无法区分的。
    
    </small>
  
## 源码分析

这里简单的介绍一下`adaptiveThreshold`函数的源码实现，OpenCV的版本号为4.3.0，函数所在目录为sources/modules/imgproc/src/thresh.cpp。该部分代码不太复杂，于是我将全部代码贴在下方。
```c++
void cv::adaptiveThreshold( InputArray _src, OutputArray _dst, double maxValue,
                            int method, int type, int blockSize, double delta )
{
    CV_INSTRUMENT_REGION();

    Mat src = _src.getMat();
    CV_Assert( src.type() == CV_8UC1 );
    CV_Assert( blockSize % 2 == 1 && blockSize > 1 );
    Size size = src.size();

    _dst.create( size, src.type() );
    Mat dst = _dst.getMat();

    if( maxValue < 0 )
    {
        dst = Scalar(0);
        return;
    }

    CALL_HAL(adaptiveThreshold, cv_hal_adaptiveThreshold, src.data, src.step, dst.data, dst.step, src.cols, src.rows,
             maxValue, method, type, blockSize, delta);

    Mat mean;

    if( src.data != dst.data )
        mean = dst;

    if (method == ADAPTIVE_THRESH_MEAN_C)
        boxFilter( src, mean, src.type(), Size(blockSize, blockSize),
                   Point(-1,-1), true, BORDER_REPLICATE|BORDER_ISOLATED );
    else if (method == ADAPTIVE_THRESH_GAUSSIAN_C)
    {
        Mat srcfloat,meanfloat;
        src.convertTo(srcfloat,CV_32F);
        meanfloat=srcfloat;
        GaussianBlur(srcfloat, meanfloat, Size(blockSize, blockSize), 0, 0, BORDER_REPLICATE|BORDER_ISOLATED);
        meanfloat.convertTo(mean, src.type());
    }
    else
        CV_Error( CV_StsBadFlag, "Unknown/unsupported adaptive threshold method" );

    int i, j;
    uchar imaxval = saturate_cast<uchar>(maxValue);
    int idelta = type == THRESH_BINARY ? cvCeil(delta) : cvFloor(delta);
    uchar tab[768];

    if( type == CV_THRESH_BINARY )
        for( i = 0; i < 768; i++ )
            tab[i] = (uchar)(i - 255 > -idelta ? imaxval : 0);
    else if( type == CV_THRESH_BINARY_INV )
        for( i = 0; i < 768; i++ )
            tab[i] = (uchar)(i - 255 <= -idelta ? imaxval : 0);
    else
        CV_Error( CV_StsBadFlag, "Unknown/unsupported threshold type" );

    if( src.isContinuous() && mean.isContinuous() && dst.isContinuous() )
    {
        size.width *= size.height;
        size.height = 1;
    }

    for( i = 0; i < size.height; i++ )
    {
        const uchar* sdata = src.ptr(i);
        const uchar* mdata = mean.ptr(i);
        uchar* ddata = dst.ptr(i);

        for( j = 0; j < size.width; j++ )
            ddata[j] = tab[sdata[j] - mdata[j] + 255];
    }
}
```
我们暂时忽略`CALL_HAL`，函数依次可以看到：参数异常判断->创建`dst`->使用`boxFilter`或`GuassianBlur`->生成映射表`tab`->根据映射表修改`dst`。可以看到其实现思路与预想基本是一致的。

源码中有以下三点需要注意：
- 两种`adaptiveMethod`的耗时差距与`boxFilter`和`GuassianBlur`相关。
- 源码中使用`GaussianBlur`时，先将src转换为`CV_32F`，再将求得的`dst`转换为`src.type()`。
- 使用指针循环图像行、列依映射表`tab`更新`dst`。