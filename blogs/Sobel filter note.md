# Sobel算子杂谈

Sobel算子是一种数字图像处理中常用的边缘提取算子，更多基础内容可以参考冈萨雷斯版《数字图像处理》。下文主要介绍OpenCV中实现该算法的三种形式及其区别。

## 基本概念 
Sobel算子包括检测水平变化的卷积核$G_x$和检测垂直方向变化的卷积核$G_y$。比如当选择大小为3的核时：

$G_x=$$\left[\begin{matrix}
-1 & 0 & 1 \\
-2 & 0 & 2 \\
-1 & 0 & 1 \\
\end{matrix}\right]$,
$G_y=$$\left[\begin{matrix}
-1 & -2 & -1 \\
0 & 0 & 0 \\
1 & 2 & 1 \\
\end{matrix}\right]$

## 核分离
Sobel算子是一种典型的可分核，比如：

$G_x=$$\left[\begin{matrix}
-1 & 0 & 1 \\
-2 & 0 & 2 \\
-1 & 0 & 1 \\
\end{matrix}\right]=$$\left[\begin{matrix}
1\\
2\\
1\\
\end{matrix}\right]\times$$\left[\begin{matrix}
-1 & 0 & 1 \\
\end{matrix}\right]$

$G_y=$$\left[\begin{matrix}
-1 & -2 & -1 \\
0 & 0 & 0 \\
1 & 2 & 1 \\
\end{matrix}\right]=$$\left[\begin{matrix}
-1\\
0\\
1\\
\end{matrix}\right]\times$$\left[\begin{matrix}
1 & 2 & 1 \\
\end{matrix}\right]$

以下为可分离核耗时对比测试代码，其中省略了耗时分析部分:
```python
import cv2
import numpy as np


org_img = cv2.imread("Lena.jpg", 0)
sobel_y = np.mat([[-1, -2, -1], [0, 0, 0], [1, 2, 1]], dtype=np.float32)
sobel_col = np.mat([[-1], [0], [1]], dtype=np.float32)
sobel_row = np.mat([1, 2, 1], dtype=np.float32)
sobel_img1 = cv2.filter2D(org_img, cv2.CV_16S, sobel_y)
soble_img2 = cv2.sepFilter2D(org_img, cv2.CV_16S, sobel_row, sobel_col)
# sobel_img3 = cv2.Sobel(org_img, cv2.CV_16S, 0, 1, 3)
```
经1000次循环测试，使用完整核并通过`filter2D()`函数计算的平均耗时为0.24ms，使用分离核通过`sepfilter2D()`函数计算的平均耗时为0.16ms，与理论值3/2符合较好。
## Sobel()函数
在OpenCV中有单独使用Sobel滤波的函数，其函数签名为：

C++:
```c++
void cv::Sobel(InputArray src, OutputArray dst, int ddepth, int dx, int dy, int ksize = 3, double scale = 1, double	delta = 0, int borderType = BORDER_DEFAULT 
)
```		
Python:
```python
dst	= cv.Sobel(src, ddepth, dx, dy[, dst[, ksize[, scale[, delta[, borderType]]]]])
```
需要注意的参数：当`ksize = 1`时表示使用$3\times1$或$1\times3$大小的核，当`ksize = FILTER_SCHARR (-1)`时表示使用$3\times3$的Scharr核，该核比$3\times3$的Sobel核表现更佳。
## 性能测试
综上，目前实现Sobel滤波有`filter2D()`、`sepFilter2D()`、`Sobel()`三个函数，其实现方式略有不同。因此下面对性能进行测试。
### Python性能表现
经测试发现在Python环境中，三个函数的表现受输出图像的ddepth影响很大，具体测试结果如下表格，单位为毫秒。

| ddepth | `filter2D()` | `sepFilter2D()` | `Sobel()` |
| ------ | ------------ | --------------- | --------- |
| CV_8U  | 0.17         | 1.1             | 1.1       |
| CV_16S | 0.26         | 0.22            | 0.34      |
| CV_32F | 0.98         | 0.68            | 0.68      |

### C++实现
C++实现代码如下，同样省去了测试部分代码:
```c++
#include "opencv2\opencv.hpp"
using namespace cv;

int main()
{
	Mat img = imread("Lena.jpg", 0);
	Mat sobel_img1 = Mat::zeros(img.size(), img.type());
	Mat sobel_img2 = Mat::zeros(img.size(), img.type());
	Mat sobel_img3 = Mat::zeros(img.size(), img.type());
	
    Mat sobel_y= (Mat_<float>(3, 3) << -1,-2,-1,0,0,0,1,2,1);
	float data1[3][1] = { {-1},{0},{1} };
	Mat col = Mat(3, 1, CV_32F, data1);
	float data2[3] = { 1,2,1 };
	Mat row = Mat(1, 3, CV_32F, &data2);

	filter2D(img, sobel_img1, CV_16S, sobel_y);
	Sobel(img, sobel_img2, CV_16S, 0, 1, 3);
	sepFilter2D(img, sobel_img3, CV_16S, row, col);
	return 0;
}
```
在Release模式默认-O2优化,对三种函数的耗时统计如下:

| ddepth | `filter2D()` | `sepFilter2D()` | `Sobel()` |
| ------ | ------------ | --------------- | --------- |
| CV_8U  | 0.17         | 1.07            | 1.17      |
| CV_16S | 0.16         | 0.09            | 0.09      |
| CV_32F | 0.86         | 0.45            | 0.49      |

可以看到在C++环境中，与Python环境相比结果比较微妙。

## `Sobel()`函数源码分析
以下浅尝辄止地探讨一下`Sobel()`函数的具体实现，OpenCV版本号为4.3.0，函数目录为modules>imgproc>src>deriv.cpp，这里推荐一篇详细的[介绍文章](https://blog.csdn.net/weixin_42171170/article/details/89890325)

源码贴在下面，以供参考。
```c++
void cv::Sobel( InputArray _src, OutputArray _dst, int ddepth, int dx, int dy,
                int ksize, double scale, double delta, int borderType )
{
    CV_INSTRUMENT_REGION();

    CV_Assert(!_src.empty());

    int stype = _src.type(), sdepth = CV_MAT_DEPTH(stype), cn = CV_MAT_CN(stype);
    if (ddepth < 0)
        ddepth = sdepth;
    int dtype = CV_MAKE_TYPE(ddepth, cn);
    _dst.create( _src.size(), dtype );

    int ktype = std::max(CV_32F, std::max(ddepth, sdepth));

    Mat kx, ky;
    getDerivKernels( kx, ky, dx, dy, ksize, false, ktype );
    if( scale != 1 )
    {
        // usually the smoothing part is the slowest to compute,
        // so try to scale it instead of the faster differentiating part
        if( dx == 0 )
            kx *= scale;
        else
            ky *= scale;
    }

    CV_OCL_RUN(ocl::isOpenCLActivated() && _dst.isUMat() && _src.dims() <= 2 && ksize == 3 &&
               (size_t)_src.rows() > ky.total() && (size_t)_src.cols() > kx.total(),
               ocl_sepFilter3x3_8UC1(_src, _dst, ddepth, kx, ky, delta, borderType));

    CV_OCL_RUN(ocl::isOpenCLActivated() && _dst.isUMat() && _src.dims() <= 2 && (size_t)_src.rows() > kx.total() && (size_t)_src.cols() > kx.total(),
               ocl_sepFilter2D(_src, _dst, ddepth, kx, ky, Point(-1, -1), delta, borderType))

    Mat src = _src.getMat();
    Mat dst = _dst.getMat();

    Point ofs;
    Size wsz(src.cols, src.rows);
    if(!(borderType & BORDER_ISOLATED))
        src.locateROI( wsz, ofs );

    CALL_HAL(sobel, cv_hal_sobel, src.ptr(), src.step, dst.ptr(), dst.step, src.cols, src.rows, sdepth, ddepth, cn,
             ofs.x, ofs.y, wsz.width - src.cols - ofs.x, wsz.height - src.rows - ofs.y, dx, dy, ksize, scale, delta, borderType&~BORDER_ISOLATED);

    CV_OVX_RUN(true,
               openvx_sobel(src, dst, dx, dy, ksize, scale, delta, borderType))

    //CV_IPP_RUN_FAST(ipp_Deriv(src, dst, dx, dy, ksize, scale, delta, borderType));

    sepFilter2D(src, dst, ddepth, kx, ky, Point(-1, -1), delta, borderType );
}
```
这里不做逐行分析，从上到下可以依次看到：异常判断->创建dst->创建滤波核->判断缩放系数scale->判断是否使用OpenCL加速->Array转换Mat->根据borderType调整图像大小->调用`sepFilter2D()`。

从源码中可以简单看出两点：
- `Sobel()`会最终调用`sepFilter2D()`执行滤波操作，从C++的耗时统计中也可以看到两者的耗时几乎一致。
- 生成卷积核的ddepth使用浮点数，可以看出OpenCV卷积滤波针对浮点数的优化，应避免使用CV_8U。
  
而`sepFilter2D()`最终会调用`hal::sepFilter2D()`，这样就进入了openCV的HAL部分，因此就不再班门弄斧深入探讨了。