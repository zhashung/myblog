# 基于NCC的模板匹配算法的一些补充

## 基于灰度差值的相似度计算方法SAD和SSD
在模板匹配算法中，灰度值差值的相似度计算方法SAD和SSD是最基础的，相关公式如下，摘自《机器视觉算法与应用》：
$$
SAD(r,c)=\frac{1}{n}\sum_{(u,v)\in T}|t(u,v)-f(r+u,c+v)|
$$

$$
SSD(r,c)=\frac{1}{n}\sum_{(u,v)\in T}({t(u,v)-f(r+u,c+v)})^{2}
$$

在上述公式中$f(r,c)$表示匹配图像坐标为(r,c)的像素的灰度值，$t(u,v)$表示模板图像坐标为$(u,v)$的像素的灰度值，$T$表示需要计算相似度的区域，$n$表示$T$区域内像素的数量。
## 基于归一化互相关系数的相似度计算方法NCC
NCC（归一化互相关）是图像模板匹配中更为常用的一种匹配算法。SAD和SSD只有在光照条件不发生变换的情况下使用，NCC匹配计算获得的相似度不随光照线性变换而变化。
### NCC与ZNCC
在OpenCV的模板匹配算法`cv::matchTemplate`，其中一种模板匹配方法就是NCC，其计算方法如下公式所示：
$$
R(x,y)= \frac{\sum_{x',y'} (T(x',y') \cdot I(x+x',y+y'))}{\sqrt{\sum_{x',y'}T(x',y')^2 \cdot \sum_{x',y'} I(x+x',y+y')^2}}
$$

在《机器视觉算法与应用》中同样提到的NCC公式如下所示：
$$
NCC(r,c)=\frac{1}{n}\sum_{(u,v)\in T}\frac{t(u,v)-m_{t}}{\sqrt{s_{t}^{2}}} \cdot \frac{f(r+u,c+v)-m_{f}(r,c)}{\sqrt{s_{f}^{2}(r,c)}}
$$
其中
$$
m_{t}=\frac{1}{n}\sum_{(u,v)\in T}t(u,v)
$$
$$
s_{t}^{2}=\frac{1}{n}\sum_{(u,v)\in T}(t(u,v)-m_{t})^{2}
$$
$$
m_{f}(r,c)=\frac{1}{n}\sum_{(u,v)\in T}f(r+u,c+u)
$$
$$
s_{f}^{2}(r,c)=\frac{1}{n}\sum_{(u,v)\in T}(f(r+u,c+v)-m_{f}(r,c))^{2}
$$

对比OpenCV和《机器视觉算法与应用》中的公式我看可以看到两种的不同。根据互相关最基本的数学计算公式$\rho(X,Y)=\frac{COV(X,Y)}{\sqrt{D(X)}\sqrt{D(Y)}}$。我们可以看到两者公式的不同在于在《机器视觉算法与应用》书中将$X$替换为$X-\overline{X}$,$Y$替换为$X-\overline{Y}$，该公式被称为ZNCC公式。ZNCC相比NCC，对光照变化的干扰更小。

### ZNCC公式简化
如果直接使用ZNNC公式其计算量是相当大的，尤其是分子的计算量是比较大的。但该公式是可以简化的，我本人最初在ImageShop的[blog](https://www.cnblogs.com/Imageshop/p/14559685.html#!comments)中看到简化公式。在后续调研中，发现在该领域2001年的一篇经典相关论文[《Template matching using fast normalized cross correlation》](https://isas.iar.kit.edu/pdf/SPIE01_BriechleHanebeck_CrossCorr.pdf)中就提出了对原始ZNCC公式的简化。以下对简化步骤进行介绍：

对于分子的简化如下所示，将公式展开
$$
\sum_{(u,v)\in T}(t(u,v)-m_{t})\cdot(f(r+u,c+v)-m_{f}(r,c))
$$
$$
=\sum_{(u,v)\in T}t(u,v)\cdot f(r+u,c+v)-\sum_{(u,v)\in T}t(u,v)\cdot m_{f}(r,c)-\sum_{(u,v)\in T}m_{t}\cdot f(r+u,c+v)+\sum_{(u,v)\in T}m_{t}\cdot m_{f}(r,c) 
$$
$$
=\sum_{(u,v)\in T}t(u,v)\cdot f(r+u,c+v)-n\cdot m_{t}\cdot m_{f}(r,c)-\sum_{(u,v)\in T}m_{t}\cdot f(r+u,c+v)+n\cdot m_{t}\cdot m_{f}(r,c) 
$$
$$
=\sum_{(u,v)\in T}t(u,v)\cdot f(r+u,c+v)-\sum_{(u,v)\in T}m_{t}\cdot f(r+u,c+v)
$$
$$
=\sum_{(u,v)\in T}t(u,v)\cdot f(r+u,c+v)-m_{t}\sum_{(u,v)\in T}f(r+u,c+v)
$$
$$
=\sum_{(u,v)\in T}t(u,v)\cdot f(r+u,c+v)-n\cdot m_{t}\cdot m_{f}(r,c)
$$

对于分母的简化如下所示，直接参考方差计算公式$Var(X)=E[(X-E[X])^{2}]=E[X^{2}]-E[X]^{2}$。
将公式中的$\frac{1}{n}$拆分为两个$\frac{1}{\sqrt{n}}$，分别带入$\sqrt{s_{t}^{2}}$和$\sqrt{s_{f}^{2}(r,c)}$。则：
$$
\sqrt{n}\cdot \sqrt{s_{t}^{2}} = \sqrt{n}\cdot (\frac{1}{n}\sum_{(u,v)\in T}(t(u,v)-m_{t})^{2})=\sqrt{n}\sqrt{\frac{1}{n}\sum_{(u,v)\in T}(t(u,v)^{2})-m_{t}^{2}}=\sqrt{\sum_{(u,v)\in T}t(u,v)^{2}-n\cdot m_{t}^{2}}
$$
$$
\sqrt{n}\cdot \sqrt{s_{f}^{2}(r,c)} = \sqrt{n}\cdot (\frac{1}{n}\sum_{(u,v)\in T}(f(r+u,c+v)-m_{f}(r,c))^{2})=\sqrt{n}\sqrt{\frac{1}{n}\sum_{(u,v)\in T}f(r+u,c+v)^{2}-m_{f}(r,c)^{2}}=\sqrt{\sum_{(u,v)\in T}f(r+u,c+v)^{2}-n\cdot m_{f}(r,c)^{2}}
$$

最后ZNCC的计算公式可化简为
$$
NCC(r,c)=\frac{\sum_{(u,v)\in T}t(u,v)\cdot f(r+u,c+v)-m_{t}\cdot n\cdot m_{f}(r,c)}{\sqrt{\sum_{(u,v)\in T}t(u,v)^{2}-n\cdot m_{t}^{2}}\cdot \sqrt{\sum_{(u,v)\in T}f(r+u,c+v)^{2}-n\cdot m_{f}(r,c)^{2}}}
$$
经过化简后的公式，其中与模板相关的计算可以提前计算获得。在匹配过程中需要计算的内容包括:

- $\sum_{(u,v)\in T}t(u,v)\cdot f(r+u,c+v)$，即模板与匹配图像的互相关（卷积），当模板较大时可想而知，由于无法使用可分离卷积或递归卷积，其计算耗时是很严重的。
- $n\cdot m_{f}(r,c) = \sum_{(u,v)\in T}f(r+u,c+u)$ ，即匹配图像的局部求和。由于可以使用递归卷积，其计算耗时较小。
- $\sum_{(u,v)\in T}f(r+u,c+v)^{2}$，该步骤计算当模板较大时，为了防止溢出风险，需要一个大字节数值存储。