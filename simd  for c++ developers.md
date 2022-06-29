# SIMD for c++ developers

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

多数情况，你使用寄存器处理数据。技术上讲，许多instruction可以直接在内存上操作，但这并不是一个好主意。SIMD代码工作的理想模式是，加载数据到寄存器，尽可能在寄存器中处理这些数据，然后存入内存。最理想的情况是：但没有处理结果，或者结果是一个很小的数比如布尔或者单个整数，某些instruction可以把数据从SIMD寄存器复制到通用寄存器。

In C++, the registers are exposed as variables of the 6 fundamental types. See the table on the right. In assembly code there’s no difference between registers of the same size, these types are just for C++ type checking.

在C++中，SIMD寄存器包括6种基本类型的变量。如右表格所示。在汇编代码中，相同大小的寄存器没有任何区别，类型进士为了c++的类型检查。

||16 bytes|32 bytes|
|---|---|---|
|32-bit float|__m128|__m256|
|640bit float|__m128d|__m256d|
|Integers|__m128i|__m256i|

The compiler assigns variables to registers automagically, but remember there’re only 16 registers underneath the compiler. Or just 8 if you’re building a 32-bit binary. If you define too many local variables, and write code with many dependencies preventing compiler from reusing registers, the compiler will evict some variables from registers to RAM on the stack. In some edge cases, this may ruin the performance. Profile your software, and review what the compiler did to your C++ code on the performance critical paths.

编译器自动分配变量到寄存器。但是只有16个SIMD寄存器，对于32bit二进制数则更是只有8个。如果你定义了过多局部变量，并且代码中变量之间依赖性较高，编译器将把部分变量从寄存器迁移到RAM上的栈区。对于一些情况，这将损失性能。分析你的软件，并且检查在那些关乎性能的代码部分，编译器如果处理你的代码。

One caveat about these vector types, compilers define them as a structure or union with arrays inside. Technically you can access individual lanes of the vectors by using these arrays inside, the code compiles and works. The performance is not great, though. Compilers usually implement such code as a roundtrip from register to memory, and then back. Don’t treat SIMD vectors that way, use proper shuffle, insert or extract intrinsics instead.

对于这些向量类型，需要注意：编译器定义该变量作为一个structure或者union，内部则是数组。技术上，你可以获取向量中单独的lane通过访问数组的方式，代码是可以正常编译并工作的，虽然性能不佳。编译器通常会导致这些代码数据往返于内存和寄存器。因此不要用这种方式使用SIMD，使用shuffle，insert或者exrtact的intrinsics更为合适。

### General Purpose Intrinsics

#### Casting Types

Assembly only knows about 2 vector data types, 16-bytes registers and 32-bytes ones. There’re no separate set of 16/32 byte registers, 16-byte ones are lower halves of the corresponding 32-bytes.

汇编只识别两种向量类型16字节寄存器和32字节寄存器。16字节寄存器和32字节寄存器不是独立的，16字节寄存器是32字节寄存器的低16字节部分。

But they are different in C++, and there’re intrinsics to re-interpret them to different types. In the table below, rows correspond to source types, and columns represent destination type.

但是在C++中，两者是不同的，因此存在intrinsics用来转换两种类型。如下表，行表示原始类型，列表示目标类型。

||__m128| __m128d |__m128i |__m256 |__m256d |__m256i|
|-|-|-|-|-|-|-|
|__m128| = |_mm_castps_pd| _mm_castps_si128|_mm256_castps12|||
|__m128d|_mm_castpd_ps|=|_mm_castpd_si128||_mm256_castpd128_pd256||
|__m128i|_mm_castsi128_ps|_mm_castsi128_pd| =|||_mm256_castsi128_si256|
|__m256| _mm256_castps256_ps128||| =|_mm256_castps_pd|_mm256_castps_si256|
|__m256d| |_mm256_castpd256_pd128||_mm256_castpd_ps| =|_mm256_castpd_si256|
|__m256i||| _mm256_castsi256_si128|_mm256_castsi256_ps|_mm256_castsi256_pd| =|

All these intrinsics compile into no instructions, so they’re practically free performance wise. They don’t change bits in the registers, so 32-bit `1.0f` float becomes `0x3f800000` in 32-bit lanes of the
destination integer register. When casting 16-byte values into 32 bytes, the upper half is undefined.

所有这些intrinsics不会编译成任何instructions，因此是不耗费任何性能的。这些intrinsics不改变寄存器中任一比特，因此，32比特的`1.0f`将转换成`0x3f800000`对于整型寄存器的32比特lanes。当cast 16字节到32字节时，高字节部分是未定义的（undefined）。

#### Converting Types

There’re also instructions to do proper type conversion. They only support signed 32-bit integer lanes. `_mm_cvtepi32_ps` converts 4 integer 32-bit lanes into 32-bit floats, `_mm_cvtepi32_pd` converts first 2 integers into 64-bit floats, `_mm256_cvtpd_epi32` converts 4 doubles into 4 integers, setting higher 4 lanes of the output to zero. When converting from floats to integer, these instructions use rounding mode specified in MXCSR control and status register. See `_MM_SET_ROUNDING_MODE` macro for how to change that mode. That value is a part of thread state, so the OS persists the value across context switches.
