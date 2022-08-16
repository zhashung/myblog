# SIMD for c++ developers

## Programming with SIMD

### Interger Instructions

#### Arithmetic 

##### Traditional
Additions and subtractions are totally fine and do what you would expect, e.g. `_mm_add_epi32` adds 32-bit lanes of two integer registers.

addition和subtractions如你所愿是没有问题的。比如`_mm_add_epi32`使两个interger registers的32bit lanes相加。

Moreover, for 8 and 16 bits, there’re saturated versions of them, which don’t overflow but instead stick to minimum or maximum values. For example, `_mm_adds_epi8( _mm_set1_epi8( 100 )`, `_mm_set1_epi8( 100 ) )` will return a vector with all 8-bit lanes set to +127, because the sum is 200 but maximum value for signed bytes is +127. Here’s more in-depth article on saturation arithmetic.

进一步，对于8、16bit的integers，有对应的saturated版本，即不会发生overflow而是固定在最大或最小值。比如，`_mm_adds_epi8( _mm_set1_epi8( 100 )`和`_mm_set1_epi8( 100 ) )`将返回一个全部8bit lanes为+127的vector，因为虽然和是200但是对于8 bit signed bytes的最大值是+127，这里提供更多关于saturation arithmetic的深入的文章。

There’s no integer divide instruction. Intel has integer divide implemented in the standard library of their proprietary compiler. If you’re dividing by a compile-time constant that’s the same for all lanes, you can write a function which divides same sized scalar by that constant, compile with https://godbolt.org/ and port to SIMD. For example, here’s what it compiled when asked to divide uint16_t scalar by 11, and here’s how to translate that assembly into SIMD intrinsics:

没有integer divide instruction。Intel在自己编译器的标准库中有integer divide的实现。如果对所有lanes divide一个编译期常量。你可以自己写一个函数每一个lanes divide相同大小的常量。 使用https://godbolt.org/编译，导出到SIMD。如下，对于uint16_t大小的lane，divide 11，如下是对应的SIMD intrinsics。

```c++
// Divide uint16_t lanes by 11, all 8 of them in just two SSE 2 instructions.
__m128i div_by_11_epu16( __m128i x ) 
{ 
    x = _mm_mulhi_epu16( x, _mm_set1_epi16( (short)47663 ) );
    return _mm_srli_epi16( x, 3 );
}
```

Multiplication is tricky as well. `_mm_mul_epi32` takes 2 values [a, b, c, d] [e, f, g, h], ignores half of the values, and returns register with 2 64-bit values [ a * e, c * g ] The normal version is `_mm_mullo_epi32`. Oh, and both are SSE 4.1. If you only have SSE 2, you’ll need workarounds.

multiplication也是很棘手的。`_mm_mul_epi32`忽略[a, b, c, d]和[e, f, g, h]的一半数值，返回由两个64bit数值的[ a * e, c * g ]register。，一般的版本是`_mm_mullo_epi32`，两个instructions都在SSE 4.1中。SSE需要适当变通。

##### Unorthodox
For most lane sizes there’re integer minimum and maximum instructions, e.g. `_mm_max_epu8` (SSE 2) is maximum for uint8_t lanes, `_mm_min_epi32` (SSE 4.1) is minimum for 32-bit integer lanes.

对大多数大小的integer 都有对应的minimum和maximun instructions。比如SSE2中的`_mm_max_epu8`计算uint8_t lanes的maximum，SSE4.1中的`_mm_min_epi32`计算32bit interger lanes的最小值。

SSE 3 has absolute value instructions for signed integers. It also has horizontal addition and subtraction instructions, e.g. `_mm_hsub_epi32` takes [a, b, c, d] and [e, f, g, h] and returns [a-b, c-d, e-f, g-h]. For signed int16 lanes, saturated version of horizontal addition and subtraction is also available.

SSE3中对于signed integers有absoluter value instructions。也有horizontal addition和subractions instrutions，比如`_mm_hsub_epi32`输入[a, b, c, d]减[e, f, g, h] ，返回[a-b, c-d, e-f, g-h]。对于signed int16 lanes，horizontal addition 和 subtraction 有对应的saturated版本。

For uint8_t and uint16_t lanes, SSE 2 has instructions to compute average of two inputs.

对于uint8_t和uint16_t lanes，在SSE2中有计算两个输入的average的instuctions。

The weirdest of them is probably `_mm_sad_epu8` (SSE2) / `_mm256_sad_epu8` (AVX2). The single instruction, which can run once per cycle on Intel Skylake, is an equivalent of the following code:

有点不可思议的是SSE中的`_mm_sad_epu8`和AVX2中的`_mm256_sad_epu8`。每个instuction在Intel Skylake上每次运行只需要1个cycle，与以下代码等效。

```c++
array<uint64_t, 4> avx2_sad_epu8( array<uint8_t, 32> a, array<uint8_t, 32> b ) 
{
    array<uint64_t, 4> result;
    for( int i = 0; i < 4; i++ )
    {
        uint16_t totalAbsDiff = 0;
        for( int j = 0; j < 8; j++ )
        {
            const uint8_t va = a[ i * 8 + j ];
            const uint8_t vb = b[ i * 8 + j ];
            const int absDiff = abs( (int)va - (int)vb );
            totalAbsDiff += (uint16_t)absDiff; 
        }
        result[ i ] = totalAbsDiff; 
    }
    return result; 
}
```
I think it was added for video encoders. Apparently, they treat AVX 2 register as a block of 8×4 grayscale 8-bit pixels, and want to estimate compression errors. That instruction computes sum of errors over each row of the block. I’ve used it couple times for things unrelated to video codecs, it’s the fastest ways to compute sum of bytes: use zero vector for the second argument of `_mm_sad_epu8`, then `_mm_add_epi64` to accumulate.

我想这是为视频解码设计的。很显然，设计者将AVX2 register视为8*4大小的8bit像素，然后计算compression errors。这个instruction 计算block每行error的和。我已经多次将其用于视频编解码以外的事情。这是最快的方式计算和，将`_mm_sad_epu8`的第二个参数设为0，然后使用`_mm_add_epi64`累加。

#### Comparisons
Only all-lane versions are implemented. The results are similar to float comparisons, they set complete lane to all zeroes or all ones. For example, `_mm_cmpgt_epi8` sets 8-bit lanes of the output to either 0 or 0xFF, depending on whether the corresponding signed 8-bit values are greater or not。
...
comparisons只实现了all-lane版本。结果与float comparision相似，将结果的所有lane设为0或者1。比如，`_mm_cmpgt_epi8`根据对应signed 8bit数值是否大，将结果对应的8-bit lane设为0或者0xFF。

Protip: a signed integer with all bits set is equal to -1. A useful pattern to count matching numbers is integer subtract after the comparison:

```c++
const __m128i cmp = _mm_cmpgt_epi32( val, threshold ); // Compare val > threshold
acc = _mm_sub_epi32( acc, cmp ); // Increment accumulator where comparison was true
```
#### Shifts

##### Whole Register Shift

##### Individual Lanes Shift

##### Variable Shifts

#### Pack and Unpack

#### Shuffles