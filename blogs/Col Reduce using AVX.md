# 图像沿列方向降维的AVX2实现讨论

对于二维图像，取每一列像素序列的和或均值是一种基础算子。

本文主要介绍本人的优化思路，因此本文中要求图像高度不能超过256，这样就可以保证对于常见的8bit图像，每列求和的结果使用`uint16_t`保存即可保证不溢出，同时要求图像宽度是32或64的倍数等，省略对每行尾部无法并行的部分的计算。

## 普通指针实现
```C++
void ReduceColSum(const uint8_t* src, uint16_t* dst, uint32_t height,
                   uint32_t width) {
  for (size_t y = 0; y < height; ++y) {
    for (size_t x = 0; x < width; ++x) {
      dst[x] += (uint16_t)src[y * width + x];
    }
  }
}
```
当前OpenCV在4.6.0中的`cv::Reduce`代码中使用的便是使用通用寄存器执行for循环，不过对不同的算术运算封装成了模板，并使用了×4的循环展开。

## AVX指令集实现

### 两种基本实现
第一种AVX指令集的实现如下函数`ReduceColSum1`所示。循环内部avx指令相关的操作主要包括load256->permute->unpacklo/unpackhi->adds->storeu。
```C++
void ReduceColSum1(const uint8_t* src, uint16_t* dst, uint32_t height,
                   uint32_t width) {
  assert(width % 32 == 0);
  assert(height <= 256);
  __m256i mSrc, mSum, mSum2;
  int nCount = (width / 32);
  const uint8_t* psrc = src;
  const __m256i m0 = _mm256_setzero_si256();
  for (size_t y = 0; y < height; ++y) {
    uint16_t* pdst = dst;
    for (size_t x = 0; x < nCount; ++x) {
      mSrc = _mm256_loadu_si256((__m256i*)(psrc));
      mSrc = _mm256_permute4x64_epi64(mSrc, 0xD8);

      mSum = _mm256_loadu_si256((__m256i*)(pdst));
      mSum2 = _mm256_loadu_si256((__m256i*)(pdst + 16));

      mSum = _mm256_adds_epu16(mSum, _mm256_unpacklo_epi8(mSrc, m0));
      _mm256_store_si256((__m256i*)(pdst), mSum);
      mSum2 = _mm256_adds_epu16(mSum2, _mm256_unpackhi_epi8(mSrc, m0));
      _mm256_storeu_si256((__m256i*)(pdst + 16), mSum2);
      pdst += 32;
      psrc += 32;
    }
  }
}
```

第二种实现如下`ReduceColSum2`函数所示，实现是将permute->unpacklo/unpackhi替换为AVX2指令集中的convert指令。可以代码更简洁易懂。但每个内层循环只能读取并处理16字节。经测试两种实现耗时大体相当。

```C++
void ReduceColSum2(const uint8_t* src, uint16_t* dst, uint32_t height,
                   uint32_t width) {
  assert(width % 32 == 0);
  assert(height <= 256);
  __m128i mSrc;
  __m256i mSum;
  const uint8_t* psrc = src;
  const int nCount = (width / 16);
  for (size_t y = 0; y < height; ++y) {
    uint16_t* pdst = dst;
    for (size_t x = 0; x < nCount; ++x) {
      mSum = _mm256_loadu_si256((__m256i*)(pdst));
      mSrc = _mm_loadu_si128((const __m128i*)(psrc));
      mSum = _mm256_adds_epu16(mSum, _mm256_cvtepu8_epi16(mSrc));
      _mm256_storeu_si256((__m256i*)(pdst), mSum);
      pdst += 16;
      psrc += 16;
    }
  }
}
```

### 测试`_mm256_cvtepu8_epi32`
将`ReduceColSum2`中`_mm256_cvtepu8_epi16`替换为`_mm256_cvtepu8_epi32`，取消图像高度不大于256的限制，经测试耗时增加了一倍左右。

```C++
void ReduceColSum3(const uint8_t* src, uint16_t* dst, uint32_t height,
                   uint32_t width) {
  assert(width % 32 == 0);
  assert(height <= 256);
  __m128i mSrc;
  __m256i mSum;
  const uint8_t* psrc = src;
  const int nCount = (width / 8);
  for (size_t y = 0; y < height; ++y) {
    uint16_t* pdst = dst;
    for (size_t x = 0; x < nCount; ++x) {
      mSum = _mm256_loadu_si256((__m256i*)(pdst));
      mSrc = _mm_loadu_si128((const __m128i*)(psrc));
      mSum = _mm256_add_epi32(mSum, _mm256_cvtepu8_epi32(mSrc));
      _mm256_storeu_si256((__m256i*)(pdst), mSum);
      pdst += 8;
      psrc += 8;
    }
  }
}
```

### 使用循环展开优化
可以看到列求和操作计算部分包括cvt和add两步骤，两条指令的Latency是比较低的，反之读写操作loadu和storeu的Latency相对较高，因此可以使用循环展开进行优化。`ReduceColSum3`和`ReduceColSum4`分别是展开2倍和4倍的代码，经测试耗时约是不使用循环展开的70%和50%。
```c++
void ReduceColSum3(const uint8_t* src, uint16_t* dst, uint32_t height,
                   uint32_t width) {
  assert(width % 32 == 0);
  assert(height <= 256);
 __m128i mSrc, mSrc1;
  __m256i mSum, mSum1;
  const uint8_t* psrc = src;
  const int nCount = (width / 32);
  for (size_t y = 0; y < height; ++y) {
    uint16_t* pdst = dst;
    for (size_t x = 0; x < nCount; ++x) {
      mSum = _mm256_loadu_si256((__m256i*)(pdst));
      mSum1 = _mm256_loadu_si256((__m256i*)(pdst+16));
      mSrc = _mm_loadu_si128((const __m128i*)(psrc));
      mSrc1 = _mm_loadu_si128((const __m128i*)(psrc + 16));

      mSum = _mm256_adds_epu16(mSum, _mm256_cvtepu8_epi16(mSrc));
      _mm256_storeu_si256((__m256i*)(pdst), mSum);
      mSum1 = _mm256_adds_epu16(mSum1, _mm256_cvtepu8_epi16(mSrc1));
      _mm256_storeu_si256((__m256i*)(pdst + 16), mSum1);
      pdst += 32;
      psrc += 32;
    }
  }
}
```

```c++
void ReduceColSum4(const uint8_t* src, uint16_t* dst, uint16_t height,
                   uint16_t width) {
  assert(width % 64 == 0);
  assert(height <= 256);
  __m128i mSrc, mSrc1, mSrc2, mSrc3;
  __m256i mSum, mSum1, mSum2, mSum3;
  const uint8_t* psrc = src;
  const int nCount = (width / 64);
  for (size_t y = 0; y < height; ++y) {
    uint16_t* pdst = dst;
    for (size_t x = 0; x < nCount; ++x) {
      mSum = _mm256_loadu_si256((__m256i*)(pdst));
      mSum1 = _mm256_loadu_si256((__m256i*)(pdst+16));
      mSum2 = _mm256_loadu_si256((__m256i*)(pdst+32));
      mSum3 = _mm256_loadu_si256((__m256i*)(pdst+48));

      mSrc = _mm_loadu_si128((const __m128i*)(pdst));
      mSrc1 = _mm_loadu_si128((const __m128i*)(pdst+16));
      mSrc2 = _mm_loadu_si128((const __m128i*)(pdst+32));
      mSrc3 = _mm_loadu_si128((const __m128i*)(pdst+48));

      mSum = _mm256_adds_epu16(mSum, _mm256_cvtepu8_epi16(mSrc));
      _mm256_storeu_si256((__m256i*)(pdst), mSum);

      mSum1 = _mm256_adds_epu16(mSum1, _mm256_cvtepu8_epi16(mSrc1));
      _mm256_storeu_si256((__m256i*)(pdst + 16), mSum1);

      mSum2 = _mm256_adds_epu16(mSum2, _mm256_cvtepu8_epi16(mSrc2));
      _mm256_storeu_si256((__m256i*)(pdst + 32), mSum2);

      mSum3 = _mm256_adds_epu16(mSum3, _mm256_cvtepu8_epi16(mSrc3));
      _mm256_storeu_si256((__m256i*)(pdst + 48), mSum3);
      pdst += 64;
      psrc += 64;
    }
  }
}
```

## 总结
除了本文之外，我实际进行了更多测试，总体由于不同指令的测试效果并不稳定，在本文并未体现。总体上可以看到使用AVX指令集并行只不过是第一步，尤其是对于简单的图像计算也是最简单的一步，全部依赖编译器优化也是不合理，比如使用的MSVC并不会使用循环展开。只有深入理解计算机系统，理解汇编代码、清楚计算机工作原理，才能更好的优化代码执行。