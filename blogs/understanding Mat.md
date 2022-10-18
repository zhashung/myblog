# 翻译OpenCV->Mat->Description

## 前言
在网络上有很多浏览了众多`Mat`类的文章，但最终求真还是回到了官方文档。因此，本人特此将OpenCV文档中`Mat`类Detailed Description部分进行翻译以享，并特意添加了一些个人理解，见“**注:**”。

OpenCV文档版本为4.3.0，在线文档请点击[链接]()。

本人英文水平有限，且文档部分内容较为晦涩，因此采用一段英文原文接一段译文的方式作为对照，以免翻译有误误人子弟。如有错误，望指正。

## 翻译

n-dimensional dense array class

略

### `Mat`数据结构

The class `Mat` represents an n-dimensional dense numerical single-channel or multi-channel array. It can be used to store real or complex-valued vectors and matrices, grayscale or color images, voxel volumes, vector fields, point clouds, tensors, histograms (though, very high-dimensional histograms may be better stored in a `SparseMat` ). The data layout of the array `M` is defined by the array `M.step[]`, so that the address of element $(i_{0},...,i_{M.dims−1})$, where $0≤i_{k}<M.size[k]$, is computed as:

Mat类表示一个n维稠密单通道/多通道数组。该类可用于存储实数、复数、向量、矩阵、灰度彩色图像、voxel volumes、向量场、点云、张量、直方图（高维直方图使用`SparseMat`存储更佳）。数组`M`的结构由另一个数组`M.step[]`表达，对于坐标为$(i_{0},...,i_{M.dims−1})$的元素，$0≤i_{k}<M.size[k]$，由如下公式计算：

**注:**
    - *对于图像，相对于稀疏，稠密数组中每一个元素访问坐标直接代表其现实位置，对于稀疏数组，坐标则需要单独存储。*

$$
addr(M_{i_{0},...,i_{M.dims−1}})=M.data+M.step[0]∗i_{0}+M.step[1]∗i_{1}+...+M.step[M.dims−1]∗i+{M.dims−1}
$$

In case of a 2-dimensional array, the above formula is reduced to:

比如对于二维数组，上述表达式简化为：

$$
addr(M_{i,j})=M.data+M.step[0]∗i+M.step[1]∗j
$$

**注:**
    - *二维数组中元素地址，或称为灰度图中像素的地址。其地址可以表示为，首元素地址`M.data` + 偏移`M.step[0]∗i+M.step[1]∗j`。*

Note that $M.step[i] >= M.step[i+1]$ (in fact, $M.step[i] >= M.step[i+1]*M.size[i+1]$ ). This means that 2-dimensional matrices are stored row-by-row, 3-dimensional matrices are stored plane-by-plane, and so on. `M.step[M.dims-1]` is minimal and always equal to the element size `M.elemSize()`.

注意，$M.step[i] >= M.step[i+1]$（实际上，$M.step[i] >= M.step[i+1]*M.size[i+1]$）。这意味着，对于二维数组是逐个行存储的，对于三维数组则是逐个平面存储，以此类推。`M.step[M.dims-1]`是数组中元素跨度的最小单位，也等于数组中元素的字节大小`M.elemSize()`。

**注:**
- *row-by-row与plane-by-plane的理解：对于二维数组即灰度图，Mat结构是按照行来存储的，即数组形状为[Row, Col];对于三维数组即彩色图，Mat结构中彩色图像中每一个像素的各通道是连续存储的，即数组形状为[Row, Col, Channel]，在Pytorch中一般是[Channel, Row, Col]，这也是为何`cv::split`较为耗时的原因。*
- *对step的理解：注意step与shape的不同，step是为了方便计算数组坐标的地址，shape是为了方便获取数组形状。*

So, the data layout in `Mat` is compatible with the majority of dense array types from the standard toolkits and SDKs, such as Numpy (ndarray), Win32 (independent device bitmaps), and others, that is, with any array that uses steps (or strides) to compute the position of a pixel. Due to this compatibility, it is possible to make a `Mat` header for user-allocated data and process it in-place using OpenCV functions.

因此，`Mat`类中数据的存储结构与其他主流的稠密矩阵库是兼容的，比如Numpy，Win32（设备无关位图）等，也就是说都可以通过step（在一些库中是strides）来计算像素坐标。由于这种兼容性，只需创建`Mat`类头，即可以使用OpenCV中的函数处理该数据。

**注:**
- *本人将header翻译为类头。我们可以简单的将`Mat`分解为类头和其指向的数据段，类头则是一些结构信息，即`Mat`类成员变量；数据段存储则是真正的数据，如图像像素，类头通过一些成员变量指向了该段数据。*
- *in-place，表示原位操作，即对此`Mat`的操作既是对用户数据（user-allocated data）的操作。*
- *user-allocated data，用户数据，即非使用`Mat`成员函数或构造函数创建的数据由外部用户传入的数据，比如相机采集的数据。*

### `Mat`对象的构造和析构

There are many different ways to create a `Mat` object. The most popular options are listed below:

略

- Use the `create(nrows, ncols, type)` method or the similar `Mat(nrows, ncols, type[, fillValue])` constructor. A new array of the specified size and type is allocated. type has the same meaning as in the cvCreateMat method. For example, `CV_8UC1` means a 8-bit single-channel array, `CV_32FC2` means a 2-channel (complex) floating-point array, and so on.

    使用成员函数`create(nrows, ncols, type)`或者如`Mat(nrows, ncols, type[, fillValue])`的构造函数，可以创建指定大小和数据类型的数组。数据类型（type）与cvCreateMat中的一致。比如`CV_8UC1`表示8比特单通道数组，`CV_32FC2`表示双通道（或者为复数）的浮点（32比特float）数组，等等。

    **注:**
    - *`cvCreateMat`是老式C风格数组`cvMat`的成员函数，现在已经弃用。*

    ```c++
    // make a 7x7 complex matrix filled with 1+3j.
    // 创建一个7x7大小的复数矩阵，矩阵元素值全部为1+3j。
    Mat M(7,7,CV_32FC2,Scalar(1,3));
    // and now turn M to a 100x60 15-channel 8-bit matrix.
    // The old content will be deallocated
    // 将M转变为另一个100x60有15个通道的数据类型为8比特的矩阵。
    M.create(100,60,CV_8UC(15));
    ```

    As noted in the introduction to this chapter, `create()` allocates only a new array when the shape or type of the current array are different from the specified ones.

    如本章开头所说，注意：`create()`成员函数只有指定的数组大小或数据类型与当前数组不一致时才会构造新的数组。

- Create a multi-dimensional array:

    略

    ```c++
    // create a 100x100x100 8-bit array
    // 创建一个大小为100x100x100，数据类型为8比特的数组
    int sz[] = {100, 100, 100};
    Mat bigCube(3, sz, CV_8U, Scalar::all(0));
    ```

    It passes the number of dimensions =1 to the `Mat` constructor but the created array will be 2-dimensional with the number of columns set to 1. So, Mat::dims is always >= 2 (can also be 0 when the array is empty).

    当传给`Mat`的构造函数的维度 =1时，实际创建的数组将是一个2维数组，其列数为1。因此`Mat::dims`总是>= 2（数组为空时，维度为0）。

- Use a copy constructor or assignment operator where there can be an array or expression on the right side (see below). As noted in the introduction, the array assignment is an O(1) operation because it only copies the header and increases the reference counter. The Mat::clone() method can be used to get a full (deep) copy of the array when you need it.

    当有数组或表达式在右边（如下）时，即使用拷贝构造函数或者赋值操作时，如引言中所述，数组赋值是一个O(1)操作，因为只拷贝类头并增加引用计数。如果你需要深拷贝需要使用成员函数`Mat::clone()`。

    **注:**
    - *并不清楚原文see below指的是哪里。*
    - *OpenCV使用基于引用计数的智能指针管理数组中的数据。这里涉及到深浅拷贝的问题，为了保证高效，Mat中的拷贝构造和赋值操作是浅拷贝。*

- Construct a header for a part of another array. It can be a single row, single column, several rows, several columns, rectangular region in the array (called a minor in algebra) or a diagonal. Such operations are also O(1) because the new header references the same data. You can actually modify a part of the array using this feature, for example:

    构造一个类头如果是另一个数组的一部分，比如一行、一列、几行、几列、数组中的矩形区域（在代数中称为余子式）或对角元素（diagonal）。该类操作仍然是O(1)操作，因为新创建的类头仍然指向原数据，可以通过这一特性编辑数组中的一块区域，例如：
    ```c++
    // add the 5-th row, multiplied by 3 to the 3rd row
    // 第5行乘3，加到第三行。
    M.row(3) = M.row(3) + M.row(5)*3;
    // now copy the 7-th column to the 1-st column
    // M.col(1) = M.col(7); // this will not work
    // 拷贝第七列至第1列。
    // M.col(1) = M.col(7); 该方法不正确
    Mat M1 = M.col(1);
    M.col(7).copyTo(M1);
    // create a new 320x240 image
    // 创建一个新的大小为320x240的图像
    Mat img(Size(320,240),CV_8UC3);
    // select a ROI
    // 选择一个ROI
    Mat roi(img, Rect(10,10,100,100));
    // fill the ROI with (0,255,0) (which is green in RGB space);
    // the original 320x240 image will be modified
    // 使用像素值(0,255,0)（在RBG空间指绿色）填充ROI。
    // 则原始的320x240图像也将被改变。
    roi = Scalar(0,255,0);
    ```

    **注:**
    - *本段中header类头，可理解为`Mat`对象。*

    Due to the additional `datastart` and `dataend` members, it is possible to compute a relative sub-array position in the main container array using `locateROI()`:

    根据成员变量`datastart`和`dataend`，使用成员函数`locateROI()`可获取子数组相对于整个数组的位置。
    
    ```c++
    Mat A = Mat::eye(10, 10, CV_32S);
    // extracts A columns, 1 (inclusive) to 3 (exclusive).
    // 提取A第1列（包含第1列）到第3列（不包含第3列）
    Mat B = A(Range::all(), Range(1, 3));
    // extracts B rows, 5 (inclusive) to 9 (exclusive).
    // that is, C \~ A(Range(5, 9), Range(1, 3))
    // 提取B第5行（包含第5行）到第9行（不包含第9行）
    Mat C = B(Range(5, 9), Range::all());
    Size size; Point ofs;
    C.locateROI(size, ofs);
    // size will be (width=10,height=10) and the ofs will be (x=1, y=5) 
    // size是（width=10,height=10），ofs是(x=1, y=5) 。
    ``` 

    As in case of whole matrices, if you need a deep copy, use the `clone()` method of the extracted sub-matrices.

    跟整个矩阵一样，如果需要深拷贝提取子矩阵，需要使用成员函数`clone()`。

    **注:**
    - *原文中并没有详细区分 matrices、arrays、header或pixel、element等，本人着实翻译并没有统一。*

- Make a header for user-allocated data. It can be useful to do the following:
        
    使用用户数据创建类头，帮助如下：

    1. Process "foreign" data using OpenCV (for example, when you implement a DirectShow* filter or a processing module for gstreamer, and so on). For example:

        使用OpenCV处理外部数据（比如，为gstreamer实现一个DirectShow*过滤器或处理模块），如下：

        ```c++
        void process_video_frame(const unsigned char* pixels,
                            int width, int height, int step)
        {
            Mat img(height, width, CV_8UC3, pixels, step);
            GaussianBlur(img, img, Size(7,7), 1.5, 1.5);
        }
        ```

    2. Quickly initialize small matrices and/or get a super-fast element access.

        快速初始化一个小矩阵并快速访问其中元素。

        ```c++
        double m[3][3] = {{a, b, c}, {d, e, f}, {g, h, i}};
        Mat M = Mat(3, 3, CV_64F, m).inv();
        ```

- Use MATLAB-style array initializers, `zeros()`, `ones()`, `eye()`, for example:

    使用类似MARLAB风格的数组初始化方法，例如`zeros()`、`ones()`、`eye()`：

    ```c++
    // create a double-precision identity matrix and add it to M.
    // 创建一个双精度单位矩阵并加至M
    M += Mat::eye(M.rows, M.cols, CV_64F);
    ```

- Use a comma-separated initializer

    使用逗号分隔的初始化方法

    ```c++
    // create a 3x3 double-precision identity matrix
    // 创建一个 3x3 的双精度单位矩阵
    Mat M = (Mat_<double>(3,3) << 1, 0, 0, 0, 1, 0, 0, 0, 1);
    ```
    
    With this approach, you first call a constructor of the Mat class with the proper parameters, and then you just put << operator followed by comma-separated values that can be constants, variables, expressions, and so on. Also, note the extra parentheses required to avoid compilation errors.

    使用这种办法，你首先要调用`Mat`类中对应参数的构造函数，之后在`<<`符号后输入常量、变量、或表达式等，这些值需要使用逗号分隔。同时，需要注意最外面一层括号防止编译错误。

Once the array is created, it is automatically managed via a reference-counting mechanism. If the array header is built on top of user-allocated data, you should handle the data by yourself. The array data is deallocated when no one points to it. If you want to release the data pointed by a array header before the array destructor is called, use `Mat::release()`.

一旦数组被创建，它将通过引用计数机制自动管理。当数组类头由用户以上数据（top of user-allocated data）创建时，你需要自行管理数据。当没有类头指向该数据时将被析构。如果你想在析构函数被调用前手动释放数组类头指向的数据，使用`Mat::release()`。

**注:**
- *top of user-allocated data，本人理解为用户数据或者其他非通过`Mat`类创建的数据。如果Mat将此数据析构，将可能带来野指针问题。*

### 元素访问

The next important thing to learn about the array class is element access. This manual already described how to compute an address of each array element. Normally, you are not required to use the formula directly in the code. If you know the array element type (which can be retrieved using the method `Mat::type()` ), you can access the element $M_{ij}$ of a 2-dimensional array as:

下一个重要的事情则是学习在数组类如何访问元素。本文上面已经表述了如何计算每一个元素的地址。一般来说，你并不需要在代码中使用上面的公式。如果你想知道数组元素的类型（可以使用成员函数`Mat::type()`），在二维数组中，你可以使用如下公式获取元素$M_{ij}$。

```c++
M.at<double>(i,j) += 1.f;
```

assuming that `M` is a double-precision floating-point array. There are several variants of the method at for a different number of dimensions.

假如`M`是一个双精度浮点数数组，对于不同维度的数组，以下有几种不同的成员函数的变体。

If you need to process a whole row of a 2D array, the most efficient way is to get the pointer to the row first, and then just use the plain C operator [] :

如果你需要处理二维数组的所有行，最有效的方式是首先获取行首元素的指针，然后使用C的操作符[]:

```c++
// compute sum of positive matrix elements
// 计算元素中所有正数元素的和
// (assuming that M is a double-precision matrix)
// 假设 M是一个双精度浮点矩阵
double sum=0;
for(int i = 0; i < M.rows; i++)
{
    const double* Mi = M.ptr<double>(i);
    for(int j = 0; j < M.cols; j++)
        sum += std::max(Mi[j], 0.);
}
```

Some operations, like the one above, do not actually depend on the array shape. They just process elements of an array one by one (or elements from multiple arrays that have the same coordinates, for example, array addition). Such operations are called element-wise. It makes sense to check whether all the input/output arrays are continuous, namely, have no gaps at the end of each row. If yes, process them as a long single row:

上述一些操作，实际上并不依赖于数组形状。这些操作只是逐一对数组中的元素进行操作（或者对多个数组在同一个坐标的元素操作，比如数组  加法）。这些操作叫做基于元素的操作（element-wise）。这些操作要求所有的输入输出数组是连续的，即行与行之间没有间隔。如果是连续的，运算将他们视为一个长的整行。

**注:**
- *对于连续的（continuous）的n维数组，由于真实内存是一维的，因此在内存中可以视为一维数组。可以逐一快速进行计算，因此判断数组是否是continuous的是十分重要的，在其他矩阵库中也有类似判断。*

```c++
// compute the sum of positive matrix elements, optimized variant
// 计算矩阵中正数元素的和，优化变体
double sum=0;
int cols = M.cols, rows = M.rows;
if(M.isContinuous())
{
    cols *= rows;
    rows = 1;
}
for(int i = 0; i < rows; i++)
{
    const double* Mi = M.ptr<double>(i);
    for(int j = 0; j < cols; j++)
        sum += std::max(Mi[j], 0.);
}
```

In case of the continuous matrix, the outer loop body is executed just once. So, the overhead is smaller, which is especially noticeable in case of small matrices.

对于连续矩阵，外部循环体只执行了一次，因此开销是更小的，尤其是对于小矩阵更显著。

Finally, there are STL-style iterators that are smart enough to skip gaps between successive rows:

最后，使用STL风格的迭代器可以智能跳过连续行之间的间隔。

```c++
// compute sum of positive matrix elements, iterator-based variant
// 计算矩阵中正数元素的和，基于迭代器的变体
double sum=0;
MatConstIterator_<double> it = M.begin<double>(), it_end = M.end<double>();
for(; it != it_end; ++it)
    sum += std::max(*it, 0.);
```

The matrix iterators are random-access iterators, so they can be passed to any STL algorithm, including `std::sort()`.

矩阵的迭代器是随机访问迭代器，因此可以传递给任一STL算法，包括`std::sort()`。

