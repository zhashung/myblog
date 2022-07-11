# SIMD for c++ developers
## Introduction
...
## Introduction to SIMD
...
## Programming with SIMD
The main disadvantage of the technology is steep learning curve. It took me couple months to become somewhat familiar with the technology, and a year or so before I could say I’m comfortable using it. I had decades of prior experience programming classic C++, and years of prior experience programming GPUs. Didn’t help. CPU SIMD is very different from both of them.

主要的劣势是陡峭的学习曲线。我花了数月时间入门这项技术，一年多时间才能流程的使用它。我有着数十年的c++编程经验，多年的GPU编程经验。然而这些都没有用，CPU的SIMD与这些完全不同。

First it takes time to wrap the head around the concept. That all CPUs are actually vector ones, and every time you’re writing `float a = b + c`; you’re wasting 75% to 87.5% of CPU time, because it could have added 4 or 8 numbers with slightly different instruction, spending exactly same time.

首先，我们需要时间去理解这个概念。现在所有的CPU实际上都支持向量操作，每次你编写的`float a = b + c`，你实际浪费了75%-85%的CPU时间。因为你使用其他instruction可以同时计算4-8组数字的加法，但是与你花费相同的时间。

Then it takes much more time to get familiar with actual instructions. When I started learning the technology, I can remember few times when I spent hours trying to do something, come up with a solution that barely outperformed scalar version, only to find out later there’s that one special instruction which only takes a couple of them to do what I did in a page of code, improving performance by a factor of magnitude.

然后，我们需要花费更多的时间熟悉instruction。在我学习时，我记得有几次，在开始我花费数小时做一些事情，但是是极限与标量版本相差无几，后来发现在使用了一个特殊的instruction之后仅用了一些代码得到了数量级的性能提升。

Another thing, the documentation is not great. I hope I have fixed it to some extent, but still, that official guide is only useful when you already know which intrinsics do you need. Most developers don’t know what modern SIMD is capable of. I’m writing this article hoping to fix it.

另外，文档并不好。我希望去改进它。但是，目前官方文档仍然只对熟悉instruction的使用者有用。多数开发者并不知道SIMD的能力。这篇文章希望可以改善这种情况。

### Data Types
Most of the time you should be processing data in registers. Technically, many of these instructions can operate directly on memory, but it’s rarely a good idea to do so. The ideal pattern for SIMD code, load source data to registers, do as much as you can while it’s in registers, then store the results into memory. The best case is when you don’t have a result, or it’s very small value like bool or a single integer, there’re instructions to copy values from SIMD registers to general purpose ones.

多数情况，你使用register处理数据。技术上讲，许多instruction可以直接在内存上操作，但这并不是一个好主意。SIMD代码工作的理想模式是，加载数据到register，尽可能在register中处理这些数据，然后存入内存。最理想的情况是：但没有处理结果，或者结果是一个很小的数比如布尔或者单个整数，某些instruction可以把数据从SIMDregister复制到通用register。

In C++, the registers are exposed as variables of the 6 fundamental types. See the table on the right. In assembly code there’s no difference between registers of the same size, these types are just for C++ type checking.

在C++中，SIMDregister包括6种基本类型的变量。如右表格所示。在汇编代码中，相同大小的register没有任何区别，类型进士为了c++的类型检查。

|              | 16 bytes | 32 bytes |
| ------------ | -------- | -------- |
| 32-bit float | __m128   | __m256   |
| 640bit float | __m128d  | __m256d  |
| Integers     | __m128i  | __m256i  |

The compiler assigns variables to registers automagically, but remember there’re only 16 registers underneath the compiler. Or just 8 if you’re building a 32-bit binary. If you define too many local variables, and write code with many dependencies preventing compiler from reusing registers, the compiler will evict some variables from registers to RAM on the stack. In some edge cases, this may ruin the performance. Profile your software, and review what the compiler did to your C++ code on the performance critical paths.

编译器自动分配变量到register。但是只有16个SIMDregister，对于32bit二进制数则更是只有8个。如果你定义了过多局部变量，并且代码中变量之间依赖性较高，编译器将把部分变量从register迁移到RAM上的栈区。对于一些情况，这将损失性能。分析你的软件，并且检查在那些关乎性能的代码部分，编译器如果处理你的代码。

One caveat about these vector types, compilers define them as a structure or union with arrays inside. Technically you can access individual lanes of the vectors by using these arrays inside, the code compiles and works. The performance is not great, though. Compilers usually implement such code as a roundtrip from register to memory, and then back. Don’t treat SIMD vectors that way, use proper shuffle, insert or extract intrinsics instead.

对于这些向量类型，需要注意：编译器定义该变量作为一个structure或者union，内部则是数组。技术上，你可以获取向量中单独的lane通过访问数组的方式，代码是可以正常编译并工作的，虽然性能不佳。编译器通常会导致这些代码数据往返于内存和register。因此不要用这种方式使用SIMD，使用shuffle，insert或者exrtact的intrinsics更为合适。

### General Purpose Intrinsics
#### Casting Types
Assembly only knows about 2 vector data types, 16-bytes registers and 32-bytes ones. There’re no separate set of 16/32 byte registers, 16-byte ones are lower halves of the corresponding 32-bytes.

汇编只识别两种向量类型16字节register和32字节register。16字节register和32字节register不是独立的，16字节register是32字节register的低16字节部分。

But they are different in C++, and there’re intrinsics to re-interpret them to different types. In the table below, rows correspond to source types, and columns represent destination type.

但是在C++中，两者是不同的，因此存在intrinsics用来转换两种类型。如下表，行表示原始类型，列表示目标类型。

|         | __m128                 | __m128d                | __m128i                | __m256              | __m256d                | __m256i                |
| ------- | ---------------------- | ---------------------- | ---------------------- | ------------------- | ---------------------- | ---------------------- |
| __m128  | =                      | _mm_castps_pd          | _mm_castps_si128       | _mm256_castps12     |                        |                        |
| __m128d | _mm_castpd_ps          | =                      | _mm_castpd_si128       |                     | _mm256_castpd128_pd256 |                        |
| __m128i | _mm_castsi128_ps       | _mm_castsi128_pd       | =                      |                     |                        | _mm256_castsi128_si256 |
| __m256  | _mm256_castps256_ps128 |                        |                        | =                   | _mm256_castps_pd       | _mm256_castps_si256    |
| __m256d |                        | _mm256_castpd256_pd128 |                        | _mm256_castpd_ps    | =                      | _mm256_castpd_si256    |
| __m256i |                        |                        | _mm256_castsi256_si128 | _mm256_castsi256_ps | _mm256_castsi256_pd    | =                      |

All these intrinsics compile into no instructions, so they’re practically free performance wise. They don’t change bits in the registers, so 32-bit `1.0f` float becomes `0x3f800000` in 32-bit lanes of the
destination integer register. When casting 16-byte values into 32 bytes, the upper half is undefined.

所有这些intrinsics不会编译成任何instructions，因此是不耗费任何性能的。这些intrinsics不改变register中任一bit，因此，32bit的`1.0f`将转换成`0x3f800000`对于整型register的32bitlanes。当cast 16字节到32字节时，高字节部分是未定义的（undefined）。

#### Converting Types

There’re also instructions to do proper type conversion. They only support signed 32-bit integer lanes. `_mm_cvtepi32_ps` converts 4 integer 32-bit lanes into 32-bit floats, `_mm_cvtepi32_pd` converts first 2 integers into 64-bit floats, `_mm256_cvtpd_epi32` converts 4 doubles into 4 integers, setting higher 4 lanes of the output to zero. When converting from floats to integer, these instructions use rounding mode specified in MXCSR control and status register. See `_MM_SET_ROUNDING_MODE` macro for how to change that mode. That value is a part of thread state, so the OS persists the value across context switches.

有一些instuctions可以执行类型conversion。目前只支持有符号的32bitinteger lanes。`_mm_cvtepi32_ps`，convert 4个32bitinterger lanes至32bitfloat。`_mm_cvtepi32_pd`convert 4个double至interger，高4个lanes置为0。当从float convert至interger时，instructions取整采用MXCSR或status register中指定的模式。通过`_MM_SET_ROUNDING_MODE`宏修改取整模式。That value is a part of thread state, so the OS persists the value across context switches.

There’re other ones with extra ‘t’ in the name which ignore MXCSR and always use truncation towards zero, like `_mm_cvttpd_epi32` and `_mm_cvttps_epi32`.

使用带有额外“t”字符的instructions，可以忽略MXCSR，直接使用向下取整的方式，比如`_mm_cvttpd_epi32`和`_mm_cvttps_epi32`。

There’re also instructions to convert floats between 32- and 64-bit.

有一些instructions可以转换32bit与64bit的floats。

#### Memory Access

There’re many ways to load data from memory into registers.

一些从内存load数据到register的instructions。

- All types support regular loads, like` _mm_load_si128` or `_mm256_load_ps`. They require source 
address to be aligned by 16 or 32 bytes. They may crash otherwise but it’s not guaranteed, 
depends on compiler settings.

- 所有的类型都可以load，比如`_mm_load_si128`和`_mm256_load_ps`。但是这些instruction要求原始数据的地址时16bit或32bit aligned的，可能会因为编译器设置造成崩溃。
  
- All types support unaligned loads, like `_mm_loadu_si128` or `_mm256_loadu_ps`. They’re fine with unaligned pointers but aligned loads are faster.

- 所有数据类型支持 unaligned loads，比如`_mm_loadu_si128`和`_mm256_loadu_ps`，比如unaligned 指针可以正常加载，但是使用aligned loads比unaligned更快。

- `__m128` and `__m128d` types support a few extra. They support single-lane loads, to load just the first lane and set the rest of them to `0.0`, they are `_mm_load_ss` and `_mm_load_sd`. They support intrinsics to load them from memory in reverse order (requires aligned source address).

- `__m128`和`__m128d`支持一些额外的load方式.`_mm_load_ss`和`_mm_load_sd`支持single-lane load，即只load第一个lane，并设置其他lanes为`0.0`。对于aligned的数据，一些intrinsics支持倒序加载。

- In AVX, `__m128`, `__m256` and `__m256d` have broadcast load instructions, to load single float or double value from RAM into all lanes of the destination variable. Also `__m256` and `__m256d` types have broadcast loads which read 16 bytes from RAM and duplicate the value in 2 halves of the destination register.

- 在AVX指令集中`__m128`、`__m256`、`__m256d` 有broadcast load instructions，可以从内存中load single float或者double到目标的所有lanes中。有些instructions可以从内存中读取16字节到`__m256`或`__m256d`，然后复制到register的另一半。

- AVX 1 introduced masked load instructions, they load some lanes and zero out others.

- AVX1中引入了masked load instruction是，可以只load一部分lanes，其他的置为0。

- AVX 2 introduced gather load instruction, like `_mm_i32gather_ps`, they take a base pointer, integer register with offsets, and also integer value to scale the offsets. Handy at times, unfortunately rather slow.

- AVX2中进入了gather load instruction，如`_mm_i32gather_ps`，可以load base pointer，带偏移的interger，带缩放的interger。方便的同时，比较慢。

- Finally, in many cases you don’t need to do anything special to load values. If your source data is aligned, you can just write code like `__m128i value = *pointer`; and it’ll compile into an equivalent of regular load.

- 最后，在很多情况，你在load时不需要做任何特殊的操作。如果你的数据时aligned，你可以直接写做`__m128i value = *pointer`；编译器会将其编译为常规的加载方式。

Similarly, there’s many ways to store stuff: aligned, unaligned, single-lane, masked in AVX 1.

同样，在AVX1中，针对aligned、unaligned、single-lane、masked有多种方式storeregister，

Couple general notes on RAM access.

1. On modern PCs, RAM delivers at least 8 bytes per load. If you have dual-channel RAM, it delivers 16 bytes. This makes it much faster to access memory in aligned 16- or 32-bytes blocks. If you look at the source code of a modern version of memcpy or memmove library routine, you’ll usually find a manually vectorized version which uses SSE 2 instructions to copy these bytes around. Similarly, when you need something transposed, it’s often much faster to load continuously in blocks of 16 or 32 bytes, then transpose in registers with shuffle instructions.

现代计算机，RAM 每次load至少加载8字节。dual-channel内存，每次传递16字节。因此读取aligned的16或者32字节块是很快的。如果你查看现代memcpy或memmove函数在现代版本的源代码，你可以看到使用sse2手动向量化复制字节。同样当你transpose时，load连续的16或32字节块通常更快，然后使用shuffle instructions在register中transpose。

2. Many instructions support memory source for one of the operands. When compiling for SSE-only targets, this requires aligned loads. When compiling for AVX targets, this works for both aligned and unaligned loads. Code like `_mm_add_ps( v, _mm_loadu_ps( ptr ) )` only merges the load when compiling for AVX, while `_mm_add_ps( v, _mm_load_ps( ptr ) )` merges for all targets because the load is aligned (but might crash in runtime if the passed address is not actually amultiple of 16 bytes).

很多instrutions支持operands的一个数据。当编译sse目标时，要求aligned load。当编译AVX时，aligned或unaligned load均可。`_mm_add_ps( v, _mm_loadu_ps( ptr ) )`只当编译为AVX时load编译并merge成功。`_mm_add_ps( v, _mm_load_ps( ptr ) )`因为load是aligned的所以支持所有merge（但是如果运行时传入的并不是16字节aligned的，则会发生crash）。

3. Most single-lane loads/store can only use lowest lane of destination/source register. If you’re implementing a reduction like sum or average, try to do so you have result in the first lane. The exceptions are `_mm256_insertf128_ps`, `_mm256_insertf128_pd`, `_mm256_inserti128_si256` AVX instructions, they can load 16-byte vectors into higher half of the destination register with little to no performance penalty compared to regular 16-byte load.

多数single-lane的loads或store只支持操作最低lene。如果你想要实现reduction比如sum或average，将结果存放在第一个lane。除了一些AVX instructions `_mm256_insertf128_ps`, `_mm256_insertf128_pd`, `_mm256_inserti128_si256`，这些instructions load16字节的向量到寄存器的高一半部分，而且相对常规的16字节load没有性能损失。

4. There’re instructions to load or store bypassing CPU caches, `_mm[256]_stream_something`. Can be useful for cases like video processing, when you know you’re writing stuff that will only be loaded much later, after you’ve processed the complete frame, and probably by another CPU core (L1 and L2 caches are per-core, only L3 is shared).

有一些instructions如`_mm[256]_stream_something`可以绕过cache load store 数据。在视频处理时会有用。when you know you’re writing stuff that will only be loaded much later, after you’ve processed the complete frame, and probably by another CPU core (L1 and L2 caches are per-core, only L3 is shared).

5. Most current CPUs can do twice as many loads per cycle, compared to stores.

多数现代CPU每cycle load速度是store的两倍。

### Initializing Vector Registers
#### Initializing with Zeros
All the vector types have intrinsics like `_mm_setzero_ps` and `_mm256_setzero_si256` to initialize a register with all zeros. It compiles into code like xorps xmm0, xmm0, xmm0. Zero initialization is very efficient because that XOR instruction has no dependencies, the CPU just renames a new register.

所有向量类型都有对应的intrinsics比如`_mm_setzero_ps`和`_mm256_setzero_si256`，来将register全部初始化为0。代码编写的形式如xorps xmm0, xmm0, xmm0。初始化为0是很快的因为不依赖于XORinstruction，CPU只需要rename一个新的register。

#### Initializing with Values

CPUs can’t initialize registers with constants apart from 0, but compilers pretend they can, and expose intrinsics like `_mm[256]_set_something` to initialize lanes with different values, and `_mm[256]_set_something` to initialize all lanes with the same value. If the arguments are compile time constants, they usually compile into read-only data in the compiled binary. Compilers emit the complete 16/32 bytes value, and they make sure it’s aligned. if the arguments aren’t known at compile time, the compilers will do something else reasonable, e.g. if the register is mostly 0 and you only setting one lane they’ll probably `emit _mm_insert_something` instruction. If the values come from variables, the compiler may emit shuffles, or scalar stores followed by vector load.

CPU不能初始化register除了0以外的constants，但是编译器假装可以。`_mm[256]_set_something`可以为lanes initialize不同的值。`_mm[256]_set_something`可以initialize全部lanes一个值。如果变量时编译器常量，该变量将编译入二进制文件的read-only data区域。编译器emit完整的16/32bytes数值，因此保证数据是aligned。如果变量在编译期是未知的，编译器会做一些合理性动作，比如register除了1个lane娶她全是0，编译器会使用`emit _mm_insert_something` instruction。如果值从变量中拷贝而来，编译器会在load之后使用shuffles、scalar stores等。

For all their `_mm[256]_set_something` intrinsics, Intel screwed up the order. To make a register with integer values `[ 1, 2, 3, 4 ]`, you have to write either `_mm_set_epi32( 4, 3, 2, 1 )` or `_mm_setr_epi32( 1, 2, 3, 4 )`。

对于所有`_mm[256]_set_something`，Intel有一些问题。当往register中存入`[ 1, 2, 3, 4 ]`时，你需要使用`_mm_set_epi32( 4, 3, 2, 1 )`而不是`_mm_setr_epi32( 1, 2, 3, 4 )`。

#### Bitwise Instructions
Both floats and integers have a fairly complete set of bitwise instructions. All of them have AND, OR, XOR, and ANDNOT instructions. If you need bitwise NOT, the fastest way is probably XOR with all ones. Example for 16-byte values:

floats和interger都有几乎完整的bitwise instructions。均有 AND，OR，XOR和ANDNOT instructions。如果你需要bitwise NOT，最快的方式是与1做XOR，比如对于16byte：

```c++
__m128i bitwiseNot( __m128i x ) {
const __m128i zero = _mm_setzero_si128();
const __m128i one = _mm_cmpeq_epi32( zero, zero );
return _mm_xor_si128( x, one );
}
```

Whenever you need a register value with all bits set to 1, it’s often a good idea to do what I did here, setzero then compare zeroes with zeroes.

当你需要全1的register，最好的方式如上所示，setzero然后0与0比较。

## Floating Point Instructions
### Arithmetic
Most of them are available in all-lane versions, and single lane version which only compute single lane result, and copy the rest of the lanes from the first arguments.

多数arithmetic存在all-lane版，也有只计算single-lane的版本，然后复制第一个参数的其他lanes。

#### Traditional
All 4 basic operations are implemented, add, subtract, multiply, divide. Square root instruction is also there, even for 64-bit floats.

所有的四则运算都是支持的。add subtract，multiply，divide。square root instruction也是支持的，甚至对于64-bit floats。

#### Unorthodox
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

#### Missing
Unlike NEON, there’s no SSE instructions for unary minus or absolute value. The fastest way is bitwise tricks, specifically `_mm_xor_ps`( x, `_mm_set1_ps`( -0.0f ) ) for unary minus, `_mm_andnot_ps`( `_mm_set1_ps( -0.0f )`, x ) for absolute value. The reason these tricks work, -0.0f float value only has the sign bit set, the rest of the bits are 0, so `_mm_xor_ps` flips the sign and `_mm_andnot_ps` clears the sign bit making the value non-negative.

与NEON不同，没有unary minus或absolute value对应的SSE instructions 。最快的方法是使用bitwise，`_mm_xor_ps`( x, `_mm_set1_ps`( -0.0f ) )表示unary minus。`_mm_andnot_ps`( `_mm_set1_ps( -0.0f )`, x ) 表示absolute value。该方法的奥妙在于：-0.0f float只把sign bit 设为1，其他bits设为0，因此`_mm_xor_ps`反转了sign，`_mm_andnot_ps`清除sign bit使结果non-negative。

There’s no logarithm nor exponent, and no trigonometry either. Intel’s documentation says otherwise, because Intel were writing their documentation for their C++ compiler, which implements them in its standard library. You can fall back to scalar code, or better search the web for the implementation. It’s not terribly complex, e.g. trigonometric functions are usually implemented as high-degree minmax polynomial approximation. For single-precision floats you can use XMVectorSin/XMVectorCos/etc. from DirectXMath, for FP64 use the coefficients from GeometricTools.

没有logarithm、exponent和trigonometry的instructions。但是Intel自己的c++编译器中将这些集成到了标准库中。你可以使用scalar code，或者在网络上找到更好的实现。这并不复杂，比如trigonometric函数经常使用high-degree minmax polynomial approximation来实现。对于单精度floats可以使用XMVectorSin/XMVectorCos。对于FP64可以使用DirectXMath中的GeometricTools。

### Comparisons
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

### Shuffles

These are instructions which reorder lanes. They are probably the most complicated topic about the subject. Here’s an example code which transposes a 4x4 matrix. To me it looks a bit scary, despite I understand pretty well what that code does.

We’ll start with shuffle instructions processing 32-bit float lanes in 16-byte registers. On the images below, the left boxes represent what’s on input, the right one what’s on output. The A B C D values are lanes in these registers, the first one is on top.
#### Fixed Function Shuffles
|                      |       |                                                                                                                                                                                        |
| -------------------- | ----- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `_mm_movehl_ps`      | SSE 1 | Move the upper 2 single-precision (32-bit) floating-point elements from "b" to the lower 2 elements of "dst", and copy the upper 2 elements from "a" to the upper 2 elements of "dst". |
| `_mm_movelh_ps`      | SSE 1 | Move the lower 2 single-precision (32-bit) floating-point elements from "b" to the upper 2 elements of "dst", and copy the lower 2 elements from "a" to the lower 2 elements of "dst". |
| `_mm_unpacklo_ps`    | SSE 1 | Unpack and interleave single-precision (32-bit) floating-point elements from the low half of "a" and "b", and store the results in"dst".                                               |
| `_mm_unpackhi_ps`    | SSE 1 | Unpack and interleave single-precision (32-bit) floating-point elements from the high half "a" and "b", and store the results in "dst".                                                |
| `_mm_movehdup_ps`    | SSE 3 | Duplicate odd-indexed single-precision (32-bit) floating-point elements from "a", and store the results in "dst".                                                                      |
| `_mm_moveldup_ps`    | SSE 3 | Duplicate even-indexed single-precision (32-bit) floating-point elements from "a", and store the results in "dst".                                                                     |
| `_mm_broadcastss_ps` | AVX 2 | Broadcast the low single-precision (32-bit) floating-point element from "a" to all elements of "dst".                                                                                  |

#### Variable Compile Time Shuffles
These instructions include the shuffle constant. This means you can’t change that constant
dynamically, it has to be a compile-time constant, like C++ constexpr or a template argument. The 
following C++ code fails to compile:
```c++
const __m128 zero = _mm_setzero_ps();
return _mm_shuffle_ps( zero, zero, rand() );
```
VC++ says “error C2057: expected constant expression”

In the pictures below, blue arrows show what has been selected by the control value used for the 
illustration. Dashed gray arrows show what could have been selected by different control constant.
|                  |         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ---------------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `_mm_shuffle_ps` | SSE 1   | Shuffle single-precision (32-bit) floating-point elements. On the image on the right, the control constant was 10 01 10 00 = 0x98. The first 2 lanes come from the first argument, second 2 lanes from the second one. If you want to permute a single vector, pass it into both arguments.                                                                                                                                                                                                                                                                                                                                                                               |
| `_mm_blend_ps`   | SSE 4.1 | Blend packed single-precision (32-bit) floating-point elements from "a" and "b" using control mask.On the illustration on the right, the control was 1, it’s 0b0001 in binary, so only the first lane was taken from the second argument.                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| `_mm_insert_ps`  | SSE 4.1 | Insert single float lane, and optionally zero out some lanes.The image on the right shows what happens when the control value is  `0b01100001 = 0x61`: the source index is 1. that’s why the F value is being inserted, the destination index is 2 so that value is inserted into lane #2, and the lowest 4 bits are 0001 so the output lane #0 is zeroedout.You can abuse this instruction to selectively zero out some lanes without inserting: pass same register in both arguments, and e.g. `0b00001001` control value to zero out lanes #0 and #3.The equivalent is `_mm_blend_ps` with `_mm_setzero_ps` second argument, but that’s 2 instructions instead of one. |
| `mm_permute_ps`  | AVX 1   | An equivalent of _mm_shuffle_ps with the same value in both arguments. Machine code of _mm_shuffle_ps is 1 byte shorter.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |

I’m too lazy to draw similar block diagrams for `__m128d, `__m256`, and `__m256d` shuffles. Fortunately, Intel was lazy as well. The 64-bit float versions are very similar to 32-bit floats. The shuffle constants have only half of the bits for 64-bit floats, the idea is the same.

As for the 32-byte AVX versions, the corresponding AVX instructions only shuffle/permute within 16-bit halves of the register. Here’s from documentation of `_mm256_shuffle_ps`: Shuffle single-precision (32-bit) floating-point elements in "a" within 128-bit lanes.


However, there’re also some new AVX2 shuffle instruction which can cross 16-byte lanes.

`_mm256_broadcastss_ps` broadcasts the lowest lane into all 8 destination ones. Same applies to `_mm256_broadcastsd_pd`, it broadcasts a 64-bit float into all laned of the output.

`_mm256_permute2f128_ps`, `_mm256_permute2f128_pd`, `_mm256_permute2x128_si256` shuffle 16-byte lanes from two 32-byte registers, it can also selectively zero out 16-byte lanes.

`_mm256_permute4x64_pd` and `_mm256_permute4x64_epi64` permute 8-byte lanes within 32-byte registers.

#### Variable Run Time Shuffles
SSE 4.1 introduced _mm_blendv_ps instruction. It takes 3 arguments, uses sign bit of the mask to select lanes from A or B.

No version of SSE has float shuffling instructions controllable in runtime. The closest one is `_mm_shuffle_epi8` from SSSE3, see the corresponding section under integer instructions. If you really need that, you can cast registers to integer type, use `_mm_shuffle_epi8`, then cast back.

AVX 1 finally introduced one, `_mm_permutevar_ps`. It takes 2 arguments, float register with source values, and integer vector register with source indices. It treats integer register as 32-bit lanes, uses lower 2 bits of each integer lane to select source value from the float input vector.

`_mm256_permutevar8x32_ps` from AVX 2 can move data across the complete 32-byte register.

### Fused Multiply-Add
When processors vendors brag about how many FLOPs they can do per cycle, almost universally, they count FLOPs of their FMA instructions.

FMA instructions take 3 arguments, each being either 32- or 64-bit floating point numbers, a, b and c, and compute ( a · b ) + c. For marketing reasons, that single operation counts as 2 FLOPs.

Modern CPUs implement 8-wide FMA for 32-bit floats, `_mm256_fmadd_ps` intrinsic in C++, and 4-wide FMA for 64-bit floats, `_mm256_fmadd_pd` intrinsics. There’re versions which subtract instead of adding, flip sign of the product before adding. There’re even versions which alternately add or subtract, computing ( a · b ) + c for even lanes ( a · b ) − c for odd ones, or doing the opposite. It was probably added for complex numbers multiplication.

There’re versions twice as narrow operating on 16-byte registers, 4-wide 32-bit floats FMA, and 2-wide 64-bit floats FMA.

These instructions process the same SIMD registers, `__m128`, `__m128d`, `__m256` or `__m256d` C++ types.Besides performance, FMA is also more precise. These instructions only round the value once, after they have computed both the product and the sum. The intermediate value has twice as many bits.

The support for these instructions is wide but not universal. Both Intel and AMD support the compatible version of FMA, called FMA 3, in their CPUs released since 2012-2013. See hardware support section for more info.

Another caveat, the latency of FMA is not great, 4-5 CPU cycles on modern CPUs. If you’re computing dot product or similar, have an inner loop which updates the accumulator, the loop will throttle to 4-5 cycles per iteration due to data dependency chain. To resolve, unroll the loop by a small factor like 4, use 4 independent accumulators, and sum them after the loop. This way each iteration of the loop handles 4 vectors independently, and the code should saturate the throughput instead of stalling on latency. See this stackoverflow answer for the sample code which computes dot product of two FP32 vectors.