# SIMD for c++ developers

## Programming with SIMD

### Floating Point Instructions

#### Arithmetic
Most of them are available in all-lane versions, and single lane version which only compute single lane result, and copy the rest of the lanes from the first arguments.

多数arithmetic存在all-lane版，也有只计算single-lane的版本，然后复制第一个参数的其他lanes。

##### Traditional
All 4 basic operations are implemented, add, subtract, multiply, divide. Square root instruction is also there, even for 64-bit floats.

所有的四则运算都是支持的。add subtract，multiply，divide。square root instruction也是支持的，甚至对于64-bit floats。

##### Unorthodox
The CPU has minimum and maximum instructions. More often than not, modern compilers compile standard library functions `std::min<float> / std::max<double>` into `_mm_min_ss / _mm_max_sd`.

CPU有minimum和maximum instructions。而且很多现代编译器会将标准库的`std::min<float> / std::max<double>`转化为`_mm_min_ss / _mm_max_sd`

For 32-bit floats, CPUs implement faster approximate versions of $\frac{1}{x}$and $\frac{1}{\sqrt{x}}$They are `_mm_rcp_ps` and `_mm_rsqrt_ps`, respectively. For both of them the documentation says “maximum relative error for this approximation is less than 1.5*2^-12”, translates to 3.6621 × 10−4 maximum relative error.

对用户32bit float CPu对于$\frac{1}{x}$和$\frac{1}{\sqrt{x}}$有近似的快速实现，对应`_mm_rcp_ps`与`_mm_rsqrt_ps`。文档中提到两个instructions近似的最大相对误差不超过1.5*2^-12。

SSE 3 has horizontal add and subtract instructions, like `_mm_hadd_ps`, which takes two registers with [a, b, c, d] and [e, f, g, h] and returns [a+b, c+d, e+f, g+h]. The performance is not great, though.

SSE3有水平add和subtract instructions，比如`_mm_hadd_ps`。加载[a, b, c, d]和[e, f, g, h]，返回[a+b, c+d, e+f, g+h]。但是性能一半。

SSE 3 also has a couple of instructions which alternatively add and subtract values. `_mm_addsub_ps`takes two registers with [a, b, c, d] and [e, f, g, h] and returns [a-e, b+f, c-g, d+h]. `_mm_addsub_pd` does similar thing for double lanes. Handy for multiplying complex numbers and other things.

SSE3有一对instructions，实现了交替add subtract。`_mm_addsub_ps`加载[a, b, c, d]和[e, f, g, h] 返回[a-e, b+f, c-g, d+h]。`_mm_addsub_pd对两个double lanes做相似操作。对于复数的乘法等操作十分方便。

SSE 4.1 includes dot product instruction, which take 2 vector registers and also 8-bit constant. It uses higher 4 bits of the constant to compute dot product of some lanes of the inputs, then lower 4 bits of the constant to broadcast the result.

SSE4.1 包含了dot product instructions，加载2个vector registers，和一个8bit的constant。使用constant的高4bits计算部分lanes的dot product，使用constant的低4bit broadcast结果。

For instance, when SSE 4.1 is enabled, `XMVector3Dot` library function compiles into single instruction, this one: `_mm_dp_ps`( a, b, 0b01111111 ) The bits in the constant mean “compute dot product of the first 3 lanes ignoring what’s in the highest ones, and broadcast the resulting scalar value into all 4 lanes of the output register”. The lanes for which the store bit is zero will be set to 0.0f.

当SSE4.1 enabled时，`XMVector3Dot`库将函数编译至单个的instruction。对于`_mm_dp_ps`( a, b, 0b01111111 )，constant参数表示：计算前三个lanes的dot product，忽略最高lanes。constant中被置为0的bit其对应的lanes也将被置为0.0f。

SSE 4.1 has introduced rounding instructions for both sizes of floats. For 32-bit floats, the all-lanes version is exposed in C++ as `_mm_round_ps`, `_mm_ceil_ps`, and `_mm_floor_ps` intrinsics. For AVX, Intel neglected to implement proper ceil/round intrinsics, they only exposed `_mm256_round_ps` and `_mm256_round_pd` which take extra integer constant specifying rounding mode. Use `_MM_FROUND_NINT` constant to round to nearest integer, `_MM_FROUND_FLOOR` to round towards negative infinity, `_MM_FROUND_CEIL` to round towards positive infinity, or `_MM_FROUND_TRUNC` to round towards zero.

SSE4.1 为16/32bits的floats引入了rounding instructions。对于32bit floats，c++中all-lanes版本对应的instrution为`_mm_round_ps`、`_mm_ceil_ps`和`_mm_floor_ps`。在AVX中，Intel不区分ceil/round intrinsics，只有`_mm256_round_ps`和`_mm256_round_pd`，使用interger constant指定rounding模式。`_MM_FROUND_NINT`表示round到最近的interger，`_MM_FROUND_FLOOR`表示round到负无穷. `  _MM_FROUND_CEIL`表示round到正无穷，`_MM_FROUND_TRUNC`表示round到0。

##### Missing
Unlike NEON, there’s no SSE instructions for unary minus or absolute value. The fastest way is bitwise tricks, specifically `_mm_xor_ps`( x, `_mm_set1_ps`( -0.0f ) ) for unary minus, `_mm_andnot_ps`( `_mm_set1_ps( -0.0f )`, x ) for absolute value. The reason these tricks work, -0.0f float value only has the sign bit set, the rest of the bits are 0, so `_mm_xor_ps` flips the sign and `_mm_andnot_ps` clears the sign bit making the value non-negative.

与NEON不同，没有unary minus或absolute value对应的SSE instructions 。最快的方法是使用bitwise，`_mm_xor_ps`( x, `_mm_set1_ps`( -0.0f ) )表示unary minus。`_mm_andnot_ps`( `_mm_set1_ps( -0.0f )`, x ) 表示absolute value。该方法的奥妙在于：-0.0f float只把sign bit 设为1，其他bits设为0，因此`_mm_xor_ps`反转了sign，`_mm_andnot_ps`清除sign bit使结果non-negative。

There’s no logarithm nor exponent, and no trigonometry either. Intel’s documentation says otherwise, because Intel were writing their documentation for their C++ compiler, which implements them in its standard library. You can fall back to scalar code, or better search the web for the implementation. It’s not terribly complex, e.g. trigonometric functions are usually implemented as high-degree minmax polynomial approximation. For single-precision floats you can use XMVectorSin/XMVectorCos/etc. from DirectXMath, for FP64 use the coefficients from GeometricTools.

没有logarithm、exponent和trigonometry的instructions。但是Intel自己的c++编译器中将这些集成到了标准库中。你可以使用scalar code，或者在网络上找到更好的实现。这并不复杂，比如trigonometric函数经常使用high-degree minmax polynomial approximation来实现。对于单精度floats可以使用XMVectorSin/XMVectorCos。对于FP64可以使用DirectXMath中的GeometricTools。

#### Comparisons
There’re all-lanes versions of equality comparison and the rest of them, <, >, ≤, ≥, ≠. These versions return another float register, that’s either all zeros 0.0f, or all ones. A float with all ones is a `NAN`.

对于<, >, ≤, ≥, ≠等equality comparison，有all-lanes的版本。返回另一个float register，数值为全零0.0f或者全1。全1的float是`NAN`。

You can send the result from SIMD to a general-purpose CPU register with `_mm_movemask_ps`, `_mm_movemask_pd` or the corresponding AVX 1 equivalents. These instructions gather most significant bits of each float/double lane (that bit happen to be the sign bit, by the way), pack these bits into a scalar, and copy to a general-purpose CPU register. The following code prints 15:

你可以将`_mm_movemask_ps`, `_mm_movemask_pd`或者对应的AVX1版的instructions的结果传递给一般register。这些instructions gather 每一个float/double lane的most significant bit（通常是sign bit），将bits传递给一个一般register的scalar。如下代码将print 15：

```c++
const __m128 zero = _mm_setzero_ps();
const __m128 eq = _mm_cmpeq_ps( zero, zero );
const int mask = _mm_movemask_ps( eq );
printf( "%i\n", mask );
```

The `0 == 0` comparison is true for all 4 lanes of `__m128`, the `eq` variable has all 128 bits set to 1, then `_mm_movemask_ps` gathers and returns sign bits of all 4 float lanes. `0b1111` becomes 15 in decimal.

对于`__m128`的所有四个lanes comparison `0 == 0`的结果是true，变量`eq`的所有128bits均为1，`_mm_movemask_ps`gathers4个float lanes的sign bits，`0b1111`在decimal表示为15。

Another thing you can do with comparison results, use them as arguments to bitwise instructions to combine lanes somehow. Or pass them into blendv_something. Or cast to integer vectors and do something else entirely.

另一个使用comparison结果的情景是，将结果作为bitwise instructions的参数来combine lanes，或传给blendv_something，或者cast 为interger。

Also, both 32- and 64-bit floats have instructions to compare just the lowest lanes of 2 registers, and return the comparison result as bits in the flags register accessible to general-purpose instructions:

通用，一些instructions可以compare 32/64bit floats的lowest lanes，返回comparison结果as bits in the flags register accessible to general-purpose instructions。

```c++
// Compare the first lane for `x > 0.0f`
bool isFirstLanePositive( __m128 x ) {
return (bool)_mm_comigt_ss( x, _mm_setzero_ps() );
}
```

For AVX, Intel only specified two comparison intrinsics, `_mm256_cmp_ps` and `_mm256_cmp_pd`. To compare 32-byte vectors, you need to supply an integer constant for the comparison predicate, such as `_CMP_EQ_OQ` or other from that header. See this stackoverflow answer for more info on predicates.

对于AVX，Intel只提供了两种comparison intrinsics，`_mm256_cmp_ps` and `_mm256_cmp_pd`。为了比较32byte的vectors，你需要设置一个integer constant作为comparison predicate。比如`_CMP_EQ_OQ`等。可以在stackoverflow找到更多相关的predicates。

#### Shuffles

These are instructions which reorder lanes. They are probably the most complicated topic about the subject. Here’s an example code which transposes a 4x4 matrix. To me it looks a bit scary, despite I understand pretty well what that code does.

以下介绍reorder lanes的instructions。这可能是最复杂的部分。点击示例代码链接，该段代码用于transpose 一个4×4矩阵。它开启了优点可怕，不过我很清楚这段代码的作用。

We’ll start with shuffle instructions processing 32-bit float lanes in 16-byte registers. On the images below, the left boxes represent what’s on input, the right one what’s on output. The A B C D values are lanes in these registers, the first one is on top.

我们使用16byte register处理32bit float lanes来介绍shuffle instructions。如下图，左边空表示输入，右边表示输出。ABCD表示register中的lanes。

##### Fixed Function Shuffles
|                      |       |                                                                                                                                                                                        |
| -------------------- | ----- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `_mm_movehl_ps`      | SSE 1 | Move the upper 2 single-precision (32-bit) floating-point elements from "b" to the lower 2 elements of "dst", and copy the upper 2 elements from "a" to the upper 2 elements of "dst". |
| `_mm_movelh_ps`      | SSE 1 | Move the lower 2 single-precision (32-bit) floating-point elements from "b" to the upper 2 elements of "dst", and copy the lower 2 elements from "a" to the lower 2 elements of "dst". |
| `_mm_unpacklo_ps`    | SSE 1 | Unpack and interleave single-precision (32-bit) floating-point elements from the low half of "a" and "b", and store the results in"dst".                                               |
| `_mm_unpackhi_ps`    | SSE 1 | Unpack and interleave single-precision (32-bit) floating-point elements from the high half "a" and "b", and store the results in "dst".                                                |
| `_mm_movehdup_ps`    | SSE 3 | Duplicate odd-indexed single-precision (32-bit) floating-point elements from "a", and store the results in "dst".                                                                      |
| `_mm_moveldup_ps`    | SSE 3 | Duplicate even-indexed single-precision (32-bit) floating-point elements from "a", and store the results in "dst".                                                                     |
| `_mm_broadcastss_ps` | AVX 2 | Broadcast the low single-precision (32-bit) floating-point element from "a" to all elements of "dst".                                                                                  |

##### Variable Compile Time Shuffles
These instructions include the shuffle constant. This means you can’t change that constant dynamically, it has to be a compile-time constant, like C++ constexpr or a template argument. The following C++ code fails to compile:

这些instructions包含shuffle constant。这意味着你无法动态的改变该constant，它是一个 compile-time constant，类似c++ constexpr或者template argument。以下C++代码将无法完成编译。
```c++
const __m128 zero = _mm_setzero_ps();
return _mm_shuffle_ps( zero, zero, rand() );
```
VC++ says “error C2057: expected constant expression”.

VC++提示“error C2057: expected constant expression”。

In the pictures below, blue arrows show what has been selected by the control value used for the illustration. Dashed gray arrows show what could have been selected by different control constant. 

在下图中，使用蓝色箭头表示control value选中的内容。灰色虚线箭头显示不同control constant指向的内容。

|                  |         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ---------------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `_mm_shuffle_ps` | SSE 1   | Shuffle single-precision (32-bit) floating-point elements. On the image on the right, the control constant was 10 01 10 00 = 0x98. The first 2 lanes come from the first argument, second 2 lanes from the second one. If you want to permute a single vector, pass it into both arguments.                                                                                                                                                                                                                                                                                                                                                                               |
| `_mm_blend_ps`   | SSE 4.1 | Blend packed single-precision (32-bit) floating-point elements from "a" and "b" using control mask.On the illustration on the right, the control was 1, it’s 0b0001 in binary, so only the first lane was taken from the second argument.                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| `_mm_insert_ps`  | SSE 4.1 | Insert single float lane, and optionally zero out some lanes.The image on the right shows what happens when the control value is  `0b01100001 = 0x61`: the source index is 1. that’s why the F value is being inserted, the destination index is 2 so that value is inserted into lane #2, and the lowest 4 bits are 0001 so the output lane #0 is zeroedout.You can abuse this instruction to selectively zero out some lanes without inserting: pass same register in both arguments, and e.g. `0b00001001` control value to zero out lanes #0 and #3.The equivalent is `_mm_blend_ps` with `_mm_setzero_ps` second argument, but that’s 2 instructions instead of one. |
| `mm_permute_ps`  | AVX 1   | An equivalent of _mm_shuffle_ps with the same value in both arguments. Machine code of _mm_shuffle_ps is 1 byte shorter.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |

I’m too lazy to draw similar block diagrams for `__m128d`, `__m256`, and `__m256d` shuffles. Fortunately, Intel was lazy as well. The 64-bit float versions are very similar to 32-bit floats. The shuffle constants have only half of the bits for 64-bit floats, the idea is the same.

`__m128d`, `__m256`和 `__m256d` 对应的shuffles是相似的。64bit float和32bit float是十分相似的。shuffle constants have only half of the bits for 64-bit floats。

As for the 32-byte AVX versions, the corresponding AVX instructions only shuffle/permute within 16-bit halves of the register. Here’s from documentation of `_mm256_shuffle_ps`: Shuffle single-precision (32-bit) floating-point elements in "a" within 128-bit lanes.

对于32byte的AVX版本，the corresponding AVX instructions only shuffle/permute within 16-bit halves of the register。`_mm256_shuffle_ps`文档中写道：shuffle “a”中128bit lanes的32bit float。

However, there’re also some new AVX2 shuffle instruction which can cross 16-byte lanes.

然而AVX2的shuffle instructions可以cross全部16byte lanes。

`_mm256_broadcastss_ps` broadcasts the lowest lane into all 8 destination ones. Same applies to `_mm256_broadcastsd_pd`, it broadcasts a 64-bit float into all laned of the output.

`_mm256_broadcastss_ps` broadcast最低lane到全部8个lanes。`_mm256_broadcastsd_pd`类似，broadcast 64bit float到全部lanes。

`_mm256_permute2f128_ps`, `_mm256_permute2f128_pd`, `_mm256_permute2x128_si256` shuffle 16-byte lanes from two 32-byte registers, it can also selectively zero out 16-byte lanes.

`_mm256_permute2f128_ps`, `_mm256_permute2f128_pd`, `_mm256_permute2x128_si256` shuffle 两个32byte registers的16byte lanes，也可以选择性的将其中16byte lanes置零。

`_mm256_permute4x64_pd` and `_mm256_permute4x64_epi64` permute 8-byte lanes within 32-byte registers.

`_mm256_permute4x64_pd` and `_mm256_permute4x64_epi64`permute两个32byte registers的8byte lanes。

##### Variable Run Time Shuffles
SSE 4.1 introduced `_mm_blendv_ps` instruction. It takes 3 arguments, uses sign bit of the mask to select lanes from A or B.

SSE4.1引入了`_mm_blendv_ps` instruction。它有三个arguments，根据mask中的sign bit从A或者B中的lanes选择。

No version of SSE has float shuffling instructions controllable in runtime. The closest one is `_mm_shuffle_epi8` from SSSE3, see the corresponding section under integer instructions. If you really need that, you can cast registers to integer type, use `_mm_shuffle_epi8`, then cast back.

在SSE中没有tuntime float shuffing instructions。 比较接近的是SSE3中的`_mm_shuffle_epi8`，见下面integer instructions对应的章节。如果你确实需要，你可以先cast到integer类型，使用`_mm_shuffle_epi8`再转换回来。

AVX 1 finally introduced one, `_mm_permutevar_ps`. It takes 2 arguments, float register with source values, and integer vector register with source indices. It treats integer register as 32-bit lanes, uses lower 2 bits of each integer lane to select source value from the float input vector.

在AVX1中引入了`_mm_permutevar_ps`，该函数需要两个参数，float register表示数据的values，integer vector register表示数据的indices。这些interger registers是32bit lanes的，使用每个integer lane的lower 2 bit来表示从float vector的哪个value选择。

`_mm256_permutevar8x32_ps` from AVX 2 can move data across the complete 32-byte register.

AVX2中的`_mm256_permutevar8x32_ps`可以移动数据across 全部32byte register。

#### Fused Multiply-Add
When processors vendors brag about how many FLOPs they can do per cycle, almost universally, they count FLOPs of their FMA instructions.

当处理器供应商吹牛每cycle多少FLOPs时，多数情况，他们使用FMA instructions计算FLOPs。

FMA instructions take 3 arguments, each being either 32- or 64-bit floating point numbers, a, b and c, and compute $( a · b ) + c$. For marketing reasons, that single operation counts as 2 FLOPs.

FMA instuctions 需要3个参数a、b、c，每一个参数是32或64bit的float，然后计算$( a · b ) + c$。因为市场原因，这一个操作会被记为2FLOIPs。

Modern CPUs implement 8-wide FMA for 32-bit floats, `_mm256_fmadd_ps` intrinsic in C++, and 4-wide FMA for 64-bit floats, `_mm256_fmadd_pd` intrinsics. There’re versions which subtract instead of adding, flip sign of the product before adding. There’re even versions which alternately add or subtract, computing $( a · b ) + c$ for even lanes $( a · b ) − c$ for odd ones, or doing the opposite. It was probably added for complex numbers multiplication.

现代CPU在C++中添加`_mm256_fmadd_ps`intrinsics实现了对32bitfloat的8-wide FMA，`_mm256_fmadd_pd` intrinsics对64bit floats 4-wide FMA。有相应的intrinsics在add之前，flip符号位，即表示subtract。有的intrinsics在even lanes计算$( a · b ) + c$在odd lanes计算$( a · b ) − c$，或者相反。这大概是为了complex numbers multiplication添加的。

There’re versions twice as narrow operating on 16-byte registers, 4-wide 32-bit floats FMA, and 2-wide 64-bit floats FMA.

有些intrinsics可以用在16byte registers，如针对4wide 32bit floats 或2wide 64bit floats的FMA。

These instructions process the same SIMD registers, `__m128`, `__m128d`, `__m256` or `__m256d` C++ types.Besides performance, FMA is also more precise. These instructions only round the value once, after they have computed both the product and the sum. The intermediate value has twice as many bits.

这些instructions处理相同的SIMD registers，对于C++来说包括`__m128`、 `__m128d`、 `__m256`、 `__m256d`。除了性能，FMA同样计算精确。这些instructions只在计算为product和sum之后取一次round，对于中间值的计算通常是两倍bit。

The support for these instructions is wide but not universal. Both Intel and AMD support the compatible version of FMA, called FMA 3, in their CPUs released since 2012-2013. See hardware support section for more info.

对于这些FMA instructions的支持是很广的但不普及。从2012-2013年起，Intel和AMD都支持兼容版本的FMA，称为FMA3。请参阅硬件支持部分获得更多信息。

Another caveat, the latency of FMA is not great, 4-5 CPU cycles on modern CPUs. If you’re computing dot product or similar, have an inner loop which updates the accumulator, the loop will throttle to 4-5 cycles per iteration due to data dependency chain. To resolve, unroll the loop by a small factor like 4, use 4 independent accumulators, and sum them after the loop. This way each iteration of the loop handles 4 vectors independently, and the code should saturate the throughput instead of stalling on latency. See this stackoverflow answer for the sample code which computes dot product of two FP32 vectors.

另外警告，FMA的latency不佳，现代CPU需要4-5和cycles。如果你是计算dot product，或者更新accumulator的一个循环，由于data dependency chain，每次迭代将减少到4-5个cycles。为了解决这个问题，使用一个小的因子比如4，unroll 迭代，使用4个单独的accumulators，在循环完之后求和。这样。每次迭代处理4个vector，这样的代码可以使throughput saturate而不是因为latency而stall。参照stackoverflow中计算两个FP32 float的product的回答中的代码样例。