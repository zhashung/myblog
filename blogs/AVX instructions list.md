# AVX指令集函数列表
基于Intel Intrinsics Guide 3.62，不包括AVX、AVX2中的以__mm开头的函数。本文档建议初学者学习，详细内容请查看官方文档。
## Arithmetic

### __m256i _mm256_add_epi16 (__m256i a, __m256i b)	
16位整形向量a加b	

Add packed 16-bit integers in a and b, and store the results in dst.	
### __m256i _mm256_add_epi32 (__m256i a, __m256i b)	
32位整形向量a加b	

Add packed 32-bit integers in a and b, and store the results in dst.
### __m256i _mm256_add_epi64 (__m256i a, __m256i b)	
64位整形向量a加b	

Add packed 64-bit integers in a and b, and store the results in dst.
### __m256i _mm256_add_epi8 (__m256i a, __m256i b)	
8位整形向量a加b	

Add packed 8-bit integers in a and b, and store the results in dst.
### __m256d _mm256_add_pd (__m256d a, __m256d b)	
64位双精度浮点数向量a加b	

Add packed double-precision (64-bit) floating-point elements in a and b, and store the results in dst.
### __m256 _mm256_add_ps (__m256 a, __m256 b)
32位单精度浮点数向量a加b

Add packed single-precision (32-bit) floating-point elements in a and b, and store the results in dst.
### __m256i _mm256_adds_epi16 (__m256i a, __m256i b)	
使用饱和，16位整形向量a加b	

Add packed 16-bit integers in a and b using saturation, and store the results in dst.
### __m256i _mm256_adds_epi8 (__m256i a, __m256i b)	
使用饱和，8位整形向量a加b	

Add packed 8-bit integers in a and b using saturation, and store the results in dst.
### __m256i _mm256_adds_epu16 (__m256i a, __m256i b)	
使用饱和，16位无符号整形向量a加b

Add packed unsigned 16-bit integers in a and b using saturation, and store the results in dst.
### __m256i _mm256_adds_epu8 (__m256i a, __m256i b)	
使用饱和，8位无符号整形向量a加b	

Add packed unsigned 8-bit integers in a and b using saturation, and store the results in dst.
### __m256d _mm256_addsub_pd (__m256d a, __m256d b)	
64位双精度浮点数向量a加或减b（偶数通道使用加法，奇数通道使用减法）	

Alternatively add and subtract packed double-precision (64-bit) floating-point elements in a to/from packed elements in b, and store the results in dst.
### __m256 _mm256_addsub_ps (__m256 a, __m256 b)	
32位单精度浮点数向量a加或减b（偶数通道使用加法，奇数通道使用减法）	

Alternatively add and subtract packed single-precision (32-bit) floating-point elements in a to/from packed elements in b, and store the results in dst.
### __m256d _mm256_div_pd (__m256d a, __m256d b)	
64位双精度浮点数向量a除b	

Divide packed double-precision (64-bit) floating-point elements in a by packed elements in b, and store the results in dst.
### __m256 _mm256_div_ps (__m256 a, __m256 b)	
32位单精度浮点数向量a除b	

Divide packed single-precision (32-bit) floating-point elements in a by packed elements in b, and store the results in dst.
### __m256 _mm256_dp_ps (__m256 a, __m256 b, const int imm8)	
根据imm8中的第4-7位确定32位单精度浮点数向量a、b的哪些通道执行乘法，将所有结果加起来，然后根据imm8的低四位存储到dst的各通道（对高低各128位分开计算）	

Conditionally multiply the packed single-precision (32-bit) floating-point elements in a and b using the high 4 bits in imm8, sum the four products, and conditionally store the sum in dst using the low 4 bits of imm8.
### __m256i _mm256_hadd_epi16 (__m256i a, __m256i b)	
分别对16位整形向量a、b计算相邻两个通道的加法，最后将a、b分别的计算结果交叉保存到结果dst中	

Horizontally add adjacent pairs of 16-bit integers in a and b, and pack the signed 16-bit results in dst.
### __m256i _mm256_hadd_epi32 (__m256i a, __m256i b)	
分别对32位整形向量a、b计算相邻两个通道的加法，最后将a、b分别的计算结果交叉保存到结果dst中	

Horizontally add adjacent pairs of 32-bit integers in a and b, and pack the signed 32-bit results in dst.
### __m256d _mm256_hadd_pd (__m256d a, __m256d b)	
分别对64位双精度浮点数向量a、b计算相邻两个通道的加法，最后将a、b分别的计算结果交叉保存到结果dst中	

Horizontally add adjacent pairs of double-precision (64-bit) floating-point elements in a and b, and pack the results in dst.
### __m256 _mm256_hadd_ps (__m256 a, __m256 b)	
分别对32位单精度浮点数向量a、b计算相邻两个通道的加法，最后将a、b分别的计算交叉保存到结果dst中	

Horizontally add adjacent pairs of single-precision (32-bit) floating-point elements in a and b, and pack the results in dst.
### __m256i _mm256_hadds_epi16 (__m256i a, __m256i b)	
使用饱和，分别对16位整形向量a、b计算相邻两个通道的加法，最后将a、b分别的计算结果交叉保存到结果dst中	

Horizontally add adjacent pairs of signed 16-bit integers in a and b using saturation, and pack the signed 16-bit results in dst.
### __m256i _mm256_hsub_epi16 (__m256i a, __m256i b)	
分别对16位整形向量a、b计算相邻两个通道的减法，最后将a、b分别的计算结果交叉保存到结果dst中	

Horizontally subtract adjacent pairs of 16-bit integers in a and b, and pack the signed 16-bit results in dst.
### __m256i _mm256_hsub_epi32 (__m256i a, __m256i b)	
分别对32位整形向量a、b计算相邻两个通道的减法，最后将a、b分别的计算结果交叉保存到结果dst中	

Horizontally subtract adjacent pairs of 32-bit integers in a and b, and pack the signed 32-bit results in dst.
### __m256d _mm256_hsub_pd (__m256d a, __m256d b)	
分别对64位双精度浮点数向量a、b计算相邻两个通道的加法，最后将a、b分别的计算结果交叉保存到结果dst中	

Horizontally subtract adjacent pairs of double-precision (64-bit) floating-point elements in a and b, and pack the results in dst.
### __m256 _mm256_hsub_ps (__m256 a, __m256 b)	
分别对32位单精度浮点数向量a、b计算相邻两个通道的减法，最后将a、b分别的计算结果交叉保存到结果dst中	

Horizontally subtract adjacent pairs of single-precision (32-bit) floating-point elements in a and b, and pack the results in dst.
### __m256i _mm256_hsubs_epi16 (__m256i a, __m256i b)	
使用饱和，分别对16位整形向量a、b计算相邻两个通道的减法，最后将a、b分别的计算结果交叉保存到结果dst中	

Horizontally subtract adjacent pairs of signed 16-bit integers in a and b using saturation, and pack the signed 16-bit results in dst.
### __m256i _mm256_madd_epi16 (__m256i a, __m256i b)	
16位整形向量a乘b，结果为32位整形向量，将该中间结果的临近的通道相加，最后将结果保存到dst。	

Multiply packed signed 16-bit integers in a and b, producing intermediate signed 32-bit integers. Horizontally add adjacent pairs of intermediate 32-bit integers, and pack the results in dst.
### __m256i _mm256_maddubs_epi16 (__m256i a, __m256i b)	
8位无符号整形向量a乘8位有符号整形向量b，结果为16位有符号整形向量。使用饱和，将该中间结果的临近的通道相加，最后将结果保存到dst。	

Vertically multiply each unsigned 8-bit integer from a with the corresponding signed 8-bit integer from b, producing intermediate signed 16-bit integers. Horizontally add adjacent pairs of intermediate signed 16-bit integers, and pack the saturated results in dst.
### __m256i _mm256_mul_epi32 (__m256i a, __m256i b)	
使用64位整形向量a和b的各低32位有符号整形相乘，结果为64位有符号向量	

Multiply the low signed 32-bit integers from each packed 64-bit element in a and b, and store the signed 64-bit results in dst.
### __m256i _mm256_mul_epu32 (__m256i a, __m256i b)	
使用64位整形向量a和b的各低32位无符号整形相乘，结果为64位无符号向量	

Multiply the low unsigned 32-bit integers from each packed 64-bit element in a and b, and store the unsigned 64-bit results in dst.
### __m256d _mm256_mul_pd (__m256d a, __m256d b)	
64位双精度浮点数向量a乘b	

Multiply packed double-precision (64-bit) floating-point elements in a and b, and store the results in dst.
### __m256 _mm256_mul_ps (__m256 a, __m256 b)	
32位单精度浮点数向量a乘b	

Multiply packed single-precision (32-bit) floating-point elements in a and b, and store the results in dst.
### __m256i _mm256_mulhi_epi16 (__m256i a, __m256i b)	
16位有符号整形向量a乘b，结果为32位整形向量，然后保存该中间结果每个通道高16位至dst中	

Multiply the packed signed 16-bit integers in a and b, producing intermediate 32-bit integers, and store the high 16 bits of the intermediate integers in dst.
### __m256i _mm256_mulhi_epu16 (__m256i a, __m256i b)	
16位无符号整形向量a乘b，结果为32位整形向量，然后保存该中间结果每个通道高16位至dst中	

Multiply the packed unsigned 16-bit integers in a and b, producing intermediate 32-bit integers, and store the high 16 bits of the intermediate integers in dst.
### __m256i _mm256_mulhrs_epi16 (__m256i a, __m256i b)	
16位有符号整形向量a乘b，结果为32位整形向量，保留该中间结果高18位有效数字再加1，最后保留低16位

Multiply packed signed 16-bit integers in a and b, producing intermediate signed 32-bit integers. Truncate each intermediate integer to the 18 most significant bits, round by adding 1, and store bits [16:1] to dst.
### __m256i _mm256_mullo_epi16 (__m256i a, __m256i b)	
16位有符号整形向量a乘b，结果为32位整形向量，然后保存该中间结果的每个通道低16位至dst中	

Multiply the packed signed 16-bit integers in a and b, producing intermediate 32-bit integers, and store the low 16 bits of the intermediate integers in dst.
### __m256i _mm256_mullo_epi32 (__m256i a, __m256i b)	
32位有符号整形向量a乘b，结果为64位整形向量，然后保存该中间结果的每个通道低32位至dst中	

Multiply the packed signed 32-bit integers in a and b, producing intermediate 64-bit integers, and store the low 32 bits of the intermediate integers in dst.
### __m256i _mm256_sad_epu8 (__m256i a, __m256i b)	
计算8位无符号整形向量a和b的差的绝对值。每8个通道将这些差值取和，共生成4个16位无符号整形向量，将该向量放在dst每64位元素的低16位。	

Compute the absolute differences of packed unsigned 8-bit integers in a and b, then horizontally sum each consecutive 8 differences to produce four unsigned 16-bit integers, and pack these unsigned 16-bit integers in the low 16 bits of 64-bit elements in dst.
### __m256i _mm256_sign_epi16 (__m256i a, __m256i b)	
当16位有符号整形向量b中通道的值为负数时，反转16位有符号整形向量a中对应位置的值的符号，并存储在dst中，当b中通道的值为0时，dst中对应位置的值设为0	

Negate packed signed 16-bit integers in a when the corresponding signed 16-bit integer in b is negative, and store the results in dst. Element in dst are zeroed out when the corresponding element in b is zero.
### __m256i _mm256_sign_epi32 (__m256i a, __m256i b)	
当32位有符号整形向量b中通道的值为负数时，反转32位有符号整形向量a中对应位置的值的符号，并存储在dst中，当b中通道的值为0时，dst中对应位置的值设为0	

Negate packed signed 32-bit integers in a when the corresponding signed 32-bit integer in b is negative, and store the results in dst. Element in dst are zeroed out when the corresponding element in b is zero.
### __m256i _mm256_sign_epi8 (__m256i a, __m256i b)	
当8位有符号整形向量b中通道的值为负数时，反转8位有符号整形向量a中对应位置的值的符号，并存储在dst中，当b中通道的值为0时，dst中对应位置的值设为0	

Negate packed signed 8-bit integers in a when the corresponding signed 8-bit integer in b is negative, and store the results in dst. Element in dst are zeroed out when the corresponding element in b is zero.
### __m256i _mm256_sub_epi16 (__m256i a, __m256i b)	
16位整形向量a减b	

Subtract packed 16-bit integers in b from packed 16-bit integers in a, and store the results in dst.
### __m256i _mm256_sub_epi32 (__m256i a, __m256i b)	
32位整形向量a减b	

Subtract packed 32-bit integers in b from packed 32-bit integers in a, and store the results in dst.
### __m256i _mm256_sub_epi64 (__m256i a, __m256i b)	
64位整形向量a减b	

Subtract packed 64-bit integers in b from packed 64-bit integers in a, and store the results in dst.
### __m256i _mm256_sub_epi8 (__m256i a, __m256i b)	
8位整形向量a减b	

Subtract packed 8-bit integers in b from packed 8-bit integers in a, and store the results in dst.
### __m256d _mm256_sub_pd (__m256d a, __m256d b)	
64位双精度浮点数向量a减b	

Subtract packed double-precision (64-bit) floating-point elements in b from packed double-precision (64-bit) floating-point elements in a, and store the results in dst.
### __m256 _mm256_sub_ps (__m256 a, __m256 b)	
32位单精度浮点数向量a减b	

Subtract packed single-precision (32-bit) floating-point elements in b from packed single-precision (32-bit) floating-point elements in a, and store the results in dst.
### __m256i _mm256_subs_epi16 (__m256i a, __m256i b)	
使用饱和，16位整形向量a减b	

Subtract packed signed 16-bit integers in b from packed 16-bit integers in a using saturation, and store the results in dst.
### __m256i _mm256_subs_epi8 (__m256i a, __m256i b)	
使用饱和，8位整形向量a减b	

Subtract packed signed 8-bit integers in b from packed 8-bit integers in a using saturation, and store the results in dst.
### __m256i _mm256_subs_epu16 (__m256i a, __m256i b)	
使用饱和，16位无符号整形向量a减b	

Subtract packed unsigned 16-bit integers in b from packed unsigned 16-bit integers in a using saturation, and store the results in dst.
### __m256i _mm256_subs_epu8 (__m256i a, __m256i b)	
使用饱和，8位无符号整形向量a减b	

Subtract packed unsigned 8-bit integers in b from packed unsigned 8-bit integers in a using saturation, and store the results in dst.

## Compare
### __m256d _mm256_cmp_pd (__m256d a, __m256d b, const int imm8)	
根据imm8的条件比较64位双精度浮点数向量a和b，每个通道当满足条件时返回64位全1否则为64位全0	

Compare packed double-precision (64-bit) floating-point elements in a and b based on the comparison operand specified by imm8, and store the results in dst.
### __m256 _mm256_cmp_ps (__m256 a, __m256 b, const int imm8)	
根据imm8的条件比较32位单精度浮点数向量a和b，每个通道当满足条件时返回32位全1否则为32位全0	

Compare packed single-precision (32-bit) floating-point elements in a and b based on the comparison operand specified by imm8, and store the results in dst.
### __m256i _mm256_cmpeq_epi16 (__m256i a, __m256i b)	
比较16位整形向量a和b是否相等，每个通道当满足条件时返回16位全1否则为16位全0	

Compare packed 16-bit integers in a and b for equality, and store the results in dst.
### __m256i _mm256_cmpeq_epi32 (__m256i a, __m256i b)	
比较32位整形向量a和b是否相等，每个通道当满足条件时返回32位全1否则为32位全0	

Compare packed 32-bit integers in a and b for equality, and store the results in dst.
### __m256i _mm256_cmpeq_epi64 (__m256i a, __m256i b)	
比较64位整形向量a和b是否相等，每个通道当满足条件时返回64位全1否则为64位全0	

Compare packed 64-bit integers in a and b for equality, and store the results in dst.
### __m256i _mm256_cmpeq_epi8 (__m256i a, __m256i b)	
比较8位整形向量a和b是否相等，每个通道当满足条件时返回8位全1否则为8位全0	

Compare packed 8-bit integers in a and b for equality, and store the results in dst.
### __m256i _mm256_cmpgt_epi16 (__m256i a, __m256i b)	
比较16位整形向量a是否大于（不含等于）b，每个通道当满足条件时返回16位全1否则为16位全0	

Compare packed signed 16-bit integers in a and b for greater-than, and store the results in dst.
### __m256i _mm256_cmpgt_epi32 (__m256i a, __m256i b)	
比较32位整形向量a和b是否大于（不含等于），每个通道当满足条件时返回32位全1否则为32位全0	

Compare packed signed 32-bit integers in a and b for greater-than, and store the results in dst.
### __m256i _mm256_cmpgt_epi64 (__m256i a, __m256i b)	
比较64位整形向量a和b是否大于（不含等于），每个通道当满足条件时返回64位全1否则为64位全0	

Compare packed signed 64-bit integers in a and b for greater-than, and store the results in dst.
### __m256i _mm256_cmpgt_epi8 (__m256i a, __m256i b)	
比较8位整形向量a和b是否大于（不含等于），每个通道当满足条件时返回8位全1否则为8位全0

Compare packed signed 8-bit integers in a and b for greater-than, and store the results in dst.

## Convert
### __m256i _mm256_cvtepi16_epi32 (__m128i a)	
将16位有符号整形向量a扩展（SignExtend）为32位有符号整形向量dst	

Sign extend packed 16-bit integers in a to packed 32-bit integers, and store the results in dst.
### __m256i _mm256_cvtepi16_epi64 (__m128i a)	
将16位有符号整形向量a的低4个通道扩展（SignExtend）为64bit有符号整形向量dst	

Sign extend packed 16-bit integers in a to packed 64-bit integers, and store the results in dst.
### __m256i _mm256_cvtepi32_epi64 (__m128i a)	
将32位有符号整形向量a扩展（SignExtend）为64位有符号整形向量dst	

Sign extend packed 32-bit integers in a to packed 64-bit integers, and store the results in dst.
### __m256d _mm256_cvtepi32_pd (__m128i a)	
将32位有符号整形向量a扩展（SignExtend）为64位双精度浮点向量dst	

Convert packed signed 32-bit integers in a to packed double-precision (64-bit) floating-point elements, and store the results in dst.
### __m256 _mm256_cvtepi32_ps (__m256i a)	
将32位有符号整形向量a扩展（SignExtend）为32位单精度浮点向量dst	

Convert packed signed 32-bit integers in a to packed single-precision (32-bit) floating-point elements, and store the results in dst.
### __m256i _mm256_cvtepi8_epi16 (__m128i a)	
将8位有符号整形向量a扩展（SignExtend）为16位有符号整形向量dst	

Sign extend packed 8-bit integers in a to packed 16-bit integers, and store the results in dst.
### __m256i _mm256_cvtepi8_epi32 (__m128i a)	
将8位有符号整形向量a的低8个通道扩展（SignExtend）为32位有符号整形向量dst	

Sign extend packed 8-bit integers in a to packed 32-bit integers, and store the results in dst.
### __m256i _mm256_cvtepi8_epi64 (__m128i a)	
将8位有符号整形向量a的低4个通道扩展（SignExtend）为64位有符号整形向量dst	

Sign extend packed 8-bit integers in the low 8 bytes of a to packed 64-bit integers, and store the results in dst.
### __m256i _mm256_cvtepu16_epi32 (__m128i a)	
将16位无符号整形向量a扩展（ZeroExtend）为32位整形向量dst	

Zero extend packed unsigned 16-bit integers in a to packed 32-bit integers, and store the results in dst.
### __m256i _mm256_cvtepu16_epi64 (__m128i a)	
将16位无符号整形向量a的低4个通道扩展（ZeroExtend）为64位有符号整形向量dst	

Zero extend packed unsigned 16-bit integers in a to packed 64-bit integers, and store the results in dst.
### __m256i _mm256_cvtepu32_epi64 (__m128i a)	
将32位无符号整形向量a扩展（SignExtend）为64位整形向量dst	

Zero extend packed unsigned 32-bit integers in a to packed 64-bit integers, and store the results in dst.
### __m256i _mm256_cvtepu8_epi16 (__m128i a)	
将8位无符号整形向量a的低16个通道扩展扩展（SignExtend）为16位整形向量dst	

Zero extend packed unsigned 8-bit integers in a to packed 16-bit integers, and store the results in dst.
### _m256i _mm256_cvtepu8_epi32 (__m128i a)	
将8位无符号整形向量a的低8个通道扩展（SignExtend）为32位整形向量dst	

Zero extend packed unsigned 8-bit integers in a to packed 32-bit integers, and store the results in dst.
### __m256i _mm256_cvtepu8_epi64 (__m128i a)	
将8位无符号整形向量a的低4个通道扩展（SignExtend）为64位整形向量dst	

Zero extend packed unsigned 8-bit integers in the low 8 bytes of a to packed 64-bit integers, and store the results in dst.
### __m128i _mm256_cvtpd_epi32 (__m256d a)	
将64位双精度浮点数向量a转换为32位整形向量dst	

Convert packed double-precision (64-bit) floating-point elements in a to packed 32-bit integers, and store the results in dst.
### __m128 _mm256_cvtpd_ps (__m256d a)	
将64位双精度浮点数向量a转换为32位单精度浮点数向量dst	

Convert packed double-precision (64-bit) floating-point elements in a to packed single-precision (32-bit) floating-point elements, and store the results in dst.
### __m256i _mm256_cvtps_epi32 (__m256 a)	
将32位单精度浮点数向量a转换为32位整形向量dst	

Convert packed single-precision (32-bit) floating-point elements in a to packed 32-bit integers, and store the results in dst.
### __m256d _mm256_cvtps_pd (__m128 a)	
将32位单精度浮点数向量a转换为64位双精度浮点数向量dst	

Convert packed single-precision (32-bit) floating-point elements in a to packed double-precision (64-bit) floating-point elements, and store the results in dst.
### double _mm256_cvtsd_f64 (__m256d a)	
复制64位双精度浮点数向量的最低通道至dst	

Copy the lower double-precision (64-bit) floating-point element of a to dst.
### int _mm256_cvtsi256_si32 (__m256i a)	
复制32位整形向量的最低通道至dst	

Copy the lower 32-bit integer in a to dst.
### float _mm256_cvtss_f32 (__m256 a)	
复制32位单精度浮点数向量的最低通道至dst	

Copy the lower single-precision (32-bit) floating-point element of a to dst.
### __m128i _mm256_cvttpd_epi32 (__m256d a)	
使用截断，转换64位双精度浮点数向量a至32位整形向量dst	

Convert packed double-precision (64-bit) floating-point elements in a to packed 32-bit integers with truncation, and store the results in dst.
### __m256i _mm256_cvttps_epi32 (__m256 a)	
使用截断，转换32位单精度浮点数向量a至32位整形向量dst	

Convert packed single-precision (32-bit) floating-point elements in a to packed 32-bit integers with truncation, and store the results in dst.

## Elementary Math Functions
### __m256 _mm256_rcp_ps (__m256 a)	
近似计算32位单精度浮点数向量a的倒数，结果的最大误差不大于1.5*2^-12	

Compute the approximate reciprocal of packed single-precision (32-bit) floating-point elements in a, and store the results in dst. The maximum relative error for this approximation is less than 1.5*2^-12.
### __m256 _mm256_rsqrt_ps (__m256 a)	
近似计算32位单精度浮点数向量a的平方根的倒数，结果的最大误差不大于1.5*2^-12	

Compute the approximate reciprocal square root of packed single-precision (32-bit) floating-point elements in a, and store the results in dst. The maximum relative error for this approximation is less than 1.5*2^-12.
### __m256d _mm256_sqrt_pd (__m256d a)	
计算64位双精度浮点数向量a的平方根	

Compute the square root of packed double-precision (64-bit) floating-point elements in a, and store the results in dst.
### __m256 _mm256_sqrt_ps (__m256 a)	
计算32位单精度浮点数向量a的平方根	

Compute the square root of packed single-precision (32-bit) floating-point elements in a, and store the results in dst.

## General Support
### __m256d _mm256_undefined_pd (void)	
返回一个未定义的__m256d变量	

Return vector of type __m256d with undefined elements.
### __m256 _mm256_undefined_ps (void)	
返回一个未定义的__m256变量	

Return vector of type __m256 with undefined elements.
### __m256i _mm256_undefined_si256 (void)	
返回一个未定义的__m256i变量	

Return vector of type __m256i with undefined elements.
### void _mm256_zeroall (void)	
将所有XMM或YMM寄存器置零	

Zero the contents of all XMM or YMM registers.
### void _mm256_zeroupper (void)	
将所有YMM寄存器的高128位置零，低128位不变	

Zero the upper 128 bits of all YMM registers; the lower 128-bits of the registers are unmodified.

## Load
### __m256d _mm256_broadcast_pd (__m128d const * mem_addr)	
从内存中读取128位（2个64位双精度浮点数）并广播至dst	

Broadcast 128 bits from memory (composed of 2 packed double-precision (64-bit) floating-point elements) to all elements of dst.
### __m256 _mm256_broadcast_ps (__m128 const * mem_addr)	
从内存中读取128位（4个64位双精度浮点数）并广播至dst	

Broadcast 128 bits from memory (composed of 4 packed single-precision (32-bit) floating-point elements) to all elements of dst.
### __m256d _mm256_broadcast_sd (double const * mem_addr)	
从内存中读取1个64位双精度浮点数并广播至dst所有通道	

Broadcast a double-precision (64-bit) floating-point element from memory to all elements of dst.
### __m256 _mm256_broadcast_ss (float const * mem_addr)	
从内存中读取1个32位单精度浮点数并广播至dst所有通道	

Broadcast a single-precision (32-bit) floating-point element from memory to all elements of dst.
### __m256i _mm256_i32gather_epi32 (int const* base_addr, __m256i vindex, const int scale)	
从内存中聚集32位整形向量。读取的起始地址为base_addr，偏移量为32位向量vindex乘scale个字节。scale必须为1、2、4、8

Gather 32-bit integers from memory using 32-bit indices. 32-bit elements are loaded from addresses starting at base_addr and offset by each 32-bit element in vindex (each index is scaled by the factor in scale). Gathered elements are merged into dst. scale should be 1, 2, 4 or 8.
### __m256i _mm256_mask_i32gather_epi32 (__m256i src, int const* base_addr, __m256i vindex, __m256i mask, const int scale)	
从内存中聚集32位整形向量。读取的起始地址为base_addr，偏移量为32位vindex向量乘scale个字节。scale必须为1、2、4、8。
如果32位向量mask对应通道的最高位为1时，dst使用聚集的数据，否则使用src中对应的通道的值

Gather 32-bit integers from memory using 32-bit indices. 32-bit elements are loaded from addresses starting at base_addr and offset by each 32-bit element in vindex (each index is scaled by the factor in scale). Gathered elements are merged into dst using mask (elements are copied from src when the highest bit is not set in the corresponding element). scale should be 1, 2, 4 or 8.
### __m256i _mm256_i32gather_epi64 (__int64 const* base_addr, __m128i vindex, const int scale)	
从内存中聚集64位整形向量。读取的起始地址为base_addr，偏移量为32位vindex向量乘scale个字节。scale必须为1、2、4、8

Gather 64-bit integers from memory using 32-bit indices. 64-bit elements are loaded from addresses starting at base_addr and offset by each 32-bit element in vindex (each index is scaled by the factor in scale). Gathered elements are merged into dst. scale should be 1, 2, 4 or 8.
### __m256i _mm256_mask_i32gather_epi64 (__m256i src, __int64 const* base_addr, __m128i vindex, __m256i mask, const int scale)	
从内存中聚集64位整形向量。读取的起始地址为base_addr，偏移量为32位vindex向量乘scale个字节。scale必须为1、2、4、8。
如果对应mask中32位向量通道的最高位为1时，dst使用聚集的数据，否则使用src中对应的通道的值

Gather 64-bit integers from memory using 32-bit indices. 64-bit elements are loaded from addresses starting at base_addr and offset by each 32-bit element in vindex (each index is scaled by the factor in scale). Gathered elements are merged into dst using mask (elements are copied from src when the highest bit is not set in the corresponding element). scale should be 1, 2, 4 or 8.
	
### __m256d _mm256_i32gather_pd (double const* base_addr, __m128i vindex, const int scale)	
从内存中聚集64位双精度浮点数向量。读取的起始地址为base_addr，偏移量为32位vindex向量乘scale个字节。scale必须为1、2、4、8

Gather double-precision (64-bit) floating-point elements from memory using 32-bit indices. 64-bit elements are loaded from addresses starting at base_addr and offset by each 32-bit element in vindex (each index is scaled by the factor in scale). Gathered elements are merged into dst. scale should be 1, 2, 4 or 8.
### __m256d _mm256_mask_i32gather_pd (__m256d src, double const* base_addr, __m128i vindex, __m256d mask, const int scale)	
从内存中聚集64位双精度浮点数向量。读取的起始地址为base_addr，偏移量为32位vindex向量乘scale个字节。scale必须为1、2、4、8。
如果32位向量mask对应通道的最高位为1时，dst使用聚集的数据，否则使用src中对应的通道的值

Gather double-precision (64-bit) floating-point elements from memory using 32-bit indices. 64-bit elements are loaded from addresses starting at base_addr and offset by each 32-bit element in vindex (each index is scaled by the factor in scale). Gathered elements are merged into dst using mask (elements are copied from src when the highest bit is not set in the corresponding element). scale should be 1, 2, 4 or 8.
	
### __m256 _mm256_i32gather_ps (float const* base_addr, __m256i vindex, const int scale)	
从内存中聚集32位单精度浮点数向量。读取的起始地址为base_addr，偏移量为32位vindex向量乘scale个字节。scale必须为1、2、4、8

Gather single-precision (32-bit) floating-point elements from memory using 32-bit indices. 32-bit elements are loaded from addresses starting at base_addr and offset by each 32-bit element in vindex (each index is scaled by the factor in scale). Gathered elements are merged into dst. scale should be 1, 2, 4 or 8.
### __m256 _mm256_mask_i32gather_ps (__m256 src, float const* base_addr, __m256i vindex, __m256 mask, const int scale)	

从内存中聚集32位单精度浮点数向量。读取的起始地址为base_addr，偏移量为32位vindex向量乘scale个字节。scale必须为1、2、4、8。
如果32位向量mask对应通道的最高位为1时，dst使用聚集的数据，否则使用src中对应的通道的值	

Gather single-precision (32-bit) floating-point elements from memory using 32-bit indices. 32-bit elements are loaded from addresses starting at base_addr and offset by each 32-bit element in vindex (each index is scaled by the factor in scale). Gathered elements are merged into dst using mask (elements are copied from src when the highest bit is not set in the corresponding element). scale should be 1, 2, 4 or 8.
### __m128i _mm256_i64gather_epi32 (int const* base_addr, __m256i vindex, const int scale)	
从内存中聚集32位整形向量。读取的起始地址为base_addr，偏移量为32位vindex向量乘scale字节。scale必须为1、2、4、8	

Gather 32-bit integers from memory using 64-bit indices. 32-bit elements are loaded from addresses starting at base_addr and offset by each 64-bit element in vindex (each index is scaled by the factor in scale). Gathered elements are merged into dst. scale should be 1, 2, 4 or 8.
### __m128i _mm256_mask_i64gather_epi32 (__m128i src, int const* base_addr, __m256i vindex, __m128i mask, const int scale)
从内存中聚集32位整形向量。读取的起始地址为base_addr，偏移量为64位向量vindex乘scale个字节。scale必须为1、2、4、8。
如果32位向量mask对应通道的最高位为1时，dst使用聚集的数据，否则使用src中对应的通道的值  

Gather 32-bit integers from memory using 64-bit indices. 32-bit elements are loaded from addresses starting at base_addr and offset by each 64-bit element in vindex (each index is scaled by the factor in scale). Gathered elements are merged into dst using mask (elements are copied from src when the highest bit is not set in the corresponding element). scale should be 1, 2, 4 or 8.
	
### __m256i _mm256_i64gather_epi64 (__int64 const* base_addr, __m256i vindex, const int scale)	
从内存中聚集64位整形向量，读取的起始地址为base_addr，偏移量为64位vindex向量乘以scale个字节。scale必须为1、2、4、8	

Gather 64-bit integers from memory using 64-bit indices. 64-bit elements are loaded from addresses starting at base_addr and offset by each 64-bit element in vindex (each index is scaled by the factor in scale). Gathered elements are merged into dst. scale should be 1, 2, 4 or 8.
### __m256i _mm256_mask_i64gather_epi64 (__m256i src, __int64 const* base_addr, __m256i vindex, __m256i mask, const int scale)	
从内存中聚集64位整形向量，读取的起始地址为base_addr，偏移量为64位vindex向量乘scale个字节。scale必须为1、2、4、8。
如果64位向量mask对应通道的最高位为1时，dst使用聚集的数据，否则使用src中对应的通道的值

Gather 64-bit integers from memory using 64-bit indices. 64-bit elements are loaded from addresses starting at base_addr and offset by each 64-bit element in vindex (each index is scaled by the factor in scale). Gathered elements are merged into dst using mask (elements are copied from src when the highest bit is not set in the corresponding element). scale should be 1, 2, 4 or 8.
### __m256d _mm256_i64gather_pd (double const* base_addr, __m256i vindex, const int scale)	

从内存中聚集64位双精度浮点数向量，读取的起始地址为base_addr，偏移量为64位vindex向量乘scale个字节。scale必须为1、2、4、8

Gather double-precision (64-bit) floating-point elements from memory using 64-bit indices. 64-bit elements are loaded from addresses starting at base_addr and offset by each 64-bit element in vindex (each index is scaled by the factor in scale). Gathered elements are merged into dst. scale should be 1, 2, 4 or 8.
### __m256d _mm256_mask_i64gather_pd (__m256d src, double const* base_addr, __m256i vindex, __m256d mask, const int scale)
从内存中聚集64位双精度浮点数向量，读取的起始地址为base_addr，偏移量为64位vindex向量乘scale个字节。scale必须为1、2、4、8。
如果64位向量mask对应通道的最高位为1时，dst使用聚集的数据，否则使用src中对应的通道的值  

Gather double-precision (64-bit) floating-point elements from memory using 64-bit indices. 64-bit elements are loaded from addresses starting at base_addr and offset by each 64-bit element in vindex (each index is scaled by the factor in scale). Gathered elements are merged into dst using mask (elements are copied from src when the highest bit is not set in the corresponding element). scale should be 1, 2, 4 or 8.
		
### __m128 _mm256_i64gather_ps (float const* base_addr, __m256i vindex, const int scale)	
从内存中聚集32位单精度浮点数向量，读取的起始地址为base_addr，偏移量为64位vindex向量乘scale个字节。scale必须为1、2、4、8

Gather single-precision (32-bit) floating-point elements from memory using 64-bit indices. 32-bit elements are loaded from addresses starting at base_addr and offset by each 64-bit element in vindex (each index is scaled by the factor in scale). Gathered elements are merged into dst. scale should be 1, 2, 4 or 8.
### __m128 _mm256_mask_i64gather_ps (__m128 src, float const* base_addr, __m256i vindex, __m128 mask, const int scale)	
从内存中聚集32位单精度浮点数向量，读取的起始地址为base_addr，偏移量为64位vindex向量乘以scale个字节。scale必须为1、2、4、8。
如果64位向量mask对应通道的最高位为1时，dst使用聚集的数据，否则使用src中对应的通道的值

Gather single-precision (32-bit) floating-point elements from memory using 64-bit indices. 32-bit elements are loaded from addresses starting at base_addr and offset by each 64-bit element in vindex (each index is scaled by the factor in scale). Gathered elements are merged into dst using mask (elements are copied from src when the highest bit is not set in the corresponding element). scale should be 1, 2, 4 or 8.
### __m256i _mm256_lddqu_si256 (__m256i const * mem_addr)	
从非对齐的内存加载256位整形数据。当数据跨越一个cache line时，该命令性能可能比_mm256_loadu_si256更好	

Load 256-bits of integer data from unaligned memory into dst. This intrinsic may perform better than _mm256_loadu_si256 when the data crosses a cache line boundary.
### __m256d _mm256_load_pd (double const * mem_addr)	
从内存中加载256位数据（由4个64位双精度浮点数组成）， mem_addr必须32字节对齐，否则会产生通用保护异常	

Load 256-bits (composed of 4 packed double-precision (64-bit) floating-point elements) from memory into dst. mem_addr must be aligned on a 32-byte boundary or a general-protection exception may be generated.
### __m256 _mm256_load_ps (float const * mem_addr)	
从内存中加载256位数据（由8个32位单精度浮点数组成）， mem_addr必须32字节对齐，否则会产生通用保护异常	

Load 256-bits (composed of 8 packed single-precision (32-bit) floating-point elements) from memory into dst. mem_addr must be aligned on a 32-byte boundary or a general-protection exception may be generated.
### __m256i _mm256_load_si256 (__m256i const * mem_addr)	
从内存中加载256位整形数据， mem_addr必须32字节对齐，否则会产生通用保护异常	

Load 256-bits of integer data from memory into dst. mem_addr must be aligned on a 32-byte boundary or a general-protection exception may be generated.
### __m256d _mm256_loadu_pd (double const * mem_addr)	
从内存中加载256位数据（由4个64位双精度浮点数组成）， mem_addr无需对齐	

Load 256-bits (composed of 4 packed double-precision (64-bit) floating-point elements) from memory into dst. mem_addr does not need to be aligned on any particular boundary.
### __m256 _mm256_loadu_ps (float const * mem_addr)	
从内存中加载256位数据（由8个32位单精度浮点数组成）， mem_addr无需对齐	

Load 256-bits (composed of 8 packed single-precision (32-bit) floating-point elements) from memory into dst. mem_addr does not need to be aligned on any particular boundary.
### __m256i _mm256_loadu_si256 (__m256i const * mem_addr)	
从内存中加载256位整形数据， mem_addr无需对齐	

Load 256-bits of integer data from memory into dst. mem_addr does not need to be aligned on any particular boundary.
### __m256 _mm256_loadu2_m128 (float const* hiaddr, float const* loaddr)	
从内存中加载两个128位数据（分别由4个32位单精度浮点数组成），并拼接为一个256位数据，hiaddr和loaddr不需要对齐	

Load two 128-bit values (composed of 4 packed single-precision (32-bit) floating-point elements) from memory, and combine them into a 256-bit value in dst. hiaddr and loaddr do not need to be aligned on any particular boundary.
### __m256d _mm256_loadu2_m128d (double const* hiaddr, double const* loaddr)	
从内存中加载两个128位数据（分别由2个64位双精度浮点数组成），并拼接为一个256位数据，hiaddr和loaddr不需要对齐	

Load two 128-bit values (composed of 2 packed double-precision (64-bit) floating-point elements) from memory, and combine them into a 256-bit value in dst. hiaddr and loaddr do not need to be aligned on any particular boundary.
### __m256i _mm256_loadu2_m128i (__m128i const* hiaddr, __m128i const* loaddr)	
从内存中加载两个128位数据（分别由整形数据组成），并拼接为一个256位数据，hiaddr和loaddr不需要对齐	

Load two 128-bit values (composed of integer data) from memory, and combine them into a 256-bit value in dst. hiaddr and loaddr do not need to be aligned on any particular boundary.
### __m256i _mm256_maskload_epi32 (int const* mem_addr, __m256i mask)	
从内存中加载32位整形向量，当mask对应通道的最高位是0时，将dst对应通道置零	

Load packed 32-bit integers from memory into dst using mask (elements are zeroed out when the highest bit is not set in the corresponding element).
### __m256i _mm256_maskload_epi64 (__int64 const* mem_addr, __m256i mask)	
从内存中加载64位整形向量，当mask对应通道的最高位是0时，将dst对应通道置零	

Load packed 64-bit integers from memory into dst using mask (elements are zeroed out when the highest bit is not set in the corresponding element).
### __m256d _mm256_maskload_pd (double const * mem_addr, __m256i mask)	
从内存中加载64位双精度浮点数向量，当mask对应通道的最高位是0时，将dst对应通道置零	

Load packed double-precision (64-bit) floating-point elements from memory into dst using mask (elements are zeroed out when the high bit of the corresponding element is not set).
### __m256 _mm256_maskload_ps (float const * mem_addr, __m256i mask)	
从内存中加载32位单精度浮点数向量，当mask对应通道的最高位是0时，将dst对应通道置零	

Load packed single-precision (32-bit) floating-point elements from memory into dst using mask (elements are zeroed out when the high bit of the corresponding element is not set).
### __m256i _mm256_stream_load_si256 (__m256i const* mem_addr)	
使用non-temporal memory hint，从内存中加载256位数据，mem_addr必须32字节对齐，否则会产生通用保护异常

Load 256-bits of integer data from memory into dst using a non-temporal memory hint. mem_addr must be aligned on a 32-byte boundary or a general-protection exception may be generated.

## Logical

### __m256d _mm256_and_pd (__m256d a, __m256d b)	
64位双精度浮点数向量a逻辑与b	

Compute the bitwise AND of packed double-precision (64-bit) floating-point elements in a and b, and store the results in dst.
### __m256 _mm256_and_ps (__m256 a, __m256 b)	
32位单精度浮点数向量a逻辑与b	

Compute the bitwise AND of packed single-precision (32-bit) floating-point elements in a and b, and store the results in dst.
### __m256i _mm256_and_si256 (__m256i a, __m256i b)	
256位向量a逻辑与b	

Compute the bitwise AND of 256 bits (representing integer data) in a and b, and store the result in dst.
### __m256d _mm256_andnot_pd (__m256d a, __m256d b)	
先计算64位双精度浮点数向量a的逻辑非，再与向量b做逻辑与	

Compute the bitwise NOT of packed double-precision (64-bit) floating-point elements in a and then AND with b, and store the results in dst.
### __m256 _mm256_andnot_ps (__m256 a, __m256 b)	
先计算32位单精度浮点数向量a的逻辑非，再与向量b做逻辑与	

Compute the bitwise NOT of packed single-precision (32-bit) floating-point elements in a and then AND with b, and store the results in dst.
### __m256i _mm256_andnot_si256 (__m256i a, __m256i b)	
先计算256位向量a的逻辑非，再与向量b做逻辑与	

Compute the bitwise NOT of 256 bits (representing integer data) in a and then AND with b, and store the result in dst.
### __m256d _mm256_or_pd (__m256d a, __m256d b)	
64位双精度浮点数向量a逻辑或b	

Compute the bitwise OR of packed double-precision (64-bit) floating-point elements in a and b, and store the results in dst.
### __m256 _mm256_or_ps (__m256 a, __m256 b)	
32位单精度浮点数向量a逻辑或b	

Compute the bitwise OR of packed single-precision (32-bit) floating-point elements in a and b, and store the results in dst.
### __m256i _mm256_or_si256 (__m256i a, __m256i b)	
256位向量a逻辑或b	

Compute the bitwise OR of 256 bits (representing integer data) in a and b, and store the result in dst.
### int _mm256_testc_pd (__m256d a, __m256d b)	
首先计算64位双精度浮点数向量a逻辑与b，如果中间结果4个通道的符号位全为0则设置ZF为1，否则ZF为0.然后先计算64位双精度浮点数向量a的逻辑非，再与向量b做逻辑与，如果该中间结果4个通道的的符号位全为0则设置CF位1，否则CF为1，返回CF	

Compute the bitwise AND of 256 bits (representing double-precision (64-bit) floating-point elements) in a and b, producing an intermediate 256-bit value, and set ZF to 1 if the sign bit of each 64-bit element in the intermediate value is zero, otherwise set ZF to 0. Compute the bitwise NOT of a and then AND with b, producing an intermediate value, and set CF to 1 if the sign bit of each 64-bit element in the intermediate value is zero, otherwise set CF to 0. Return the CF value.
### int _mm256_testc_ps (__m256 a, __m256 b)	
首先计算32位单精度浮点数向量a逻辑与b，如果中间结果的符号位全为0则设置ZF为1，否则ZF为0.然后先计算32位单精度浮点数向量a的逻辑否，再与向量b做逻辑与，如果该中间结果8的符号位全为0则设置CF位1，否则CF为1，返回CF

Compute the bitwise AND of 256 bits (representing single-precision (32-bit) floating-point elements) in a and b, producing an intermediate 256-bit value, and set ZF to 1 if the sign bit of each 32-bit element in the intermediate value is zero, otherwise set ZF to 0. Compute the bitwise NOT of a and then AND with b, producing an intermediate value, and set CF to 1 if the sign bit of each 32-bit element in the intermediate value is zero, otherwise set CF to 0. Return the CF value.
### int _mm256_testc_si256 (__m256i a, __m256i b)	
首先计算256位数据a逻辑与b，如果中间结果所有位全为0则设置ZF为1，否则ZF为0.然后先计算256位向量a的逻辑非，再与向量b做逻辑与，如果该中间结果所有位全为0则设置CF位1，否则CF为1，返回CF	

Compute the bitwise AND of 256 bits (representing integer data) in a and b, and set ZF to 1 if the result is zero, otherwise set ZF to 0. Compute the bitwise NOT of a and then AND with b, and set CF to 1 if the result is zero, otherwise set CF to 0. Return the CF value.
### int _mm256_testnzc_pd (__m256d a, __m256d b)	
首先计算64位双精度浮点数向量a逻辑与b，如果中间结果4个通道的符号位全为0则设置ZF为1，否则ZF为0。然后先计算64位双精度浮点数向量a的逻辑否，再与向量b做逻辑与，如果该中间结果4个通道的的符号位全为0则设置CF位1，否则CF为1。如果ZF与CF全为0则返回1，否则返回0"	

Compute the bitwise AND of 256 bits (representing double-precision (64-bit) floating-point elements) in a and b, producing an intermediate 256-bit value, and set ZF to 1 if the sign bit of each 64-bit element in the intermediate value is zero, otherwise set ZF to 0. Compute the bitwise NOT of a and then AND with b, producing an intermediate value, and set CF to 1 if the sign bit of each 64-bit element in the intermediate value is zero, otherwise set CF to 0. Return 1 if both the ZF and CF values are zero, otherwise return 0.
### int _mm256_testnzc_ps (__m256 a, __m256 b)	
首先计算32位单精度浮点数向量a逻辑与b，如果中间结果8个通道的符号位全为0则设置ZF为1，否则ZF为0。然后先计算32位单精度浮点数向量a的逻辑非，再与向量b做逻辑与，如果该中间结果8个通道的的符号位全为0则设置CF位1，否则CF为1。如果ZF与CF全为0则返回1，否则返回0		

Compute the bitwise AND of 256 bits (representing single-precision (32-bit) floating-point elements) in a and b, producing an intermediate 256-bit value, and set ZF to 1 if the sign bit of each 32-bit element in the intermediate value is zero, otherwise set ZF to 0. Compute the bitwise NOT of a and then AND with b, producing an intermediate value, and set CF to 1 if the sign bit of each 32-bit element in the intermediate value is zero, otherwise set CF to 0. Return 1 if both the ZF and CF values are zero, otherwise return 0.

### int _mm256_testnzc_si256 (__m256i a, __m256i b)	
首先计算256位向量a逻辑与b，如果中间结果所有位全为0则设置ZF为1，否则ZF为0。然后先计算256位向量a的逻辑非，再与向量b做逻辑与，如果该中间结果所有位全为0则设置CF位1，否则CF为1，返回CF。如果ZF与CF全为0则返回1，否则返回0

Compute the bitwise AND of 256 bits (representing integer data) in a and b, and set ZF to 1 if the result is zero, otherwise set ZF to 0. Compute the bitwise NOT of a and then AND with b, and set CF to 1 if the result is zero, otherwise set CF to 0. Return 1 if both the ZF and CF values are zero, otherwise return 0.
### int _mm256_testz_pd (__m256d a, __m256d b)	
首先计算64位双精度浮点数向量a逻辑与b，如果中间结果4个通道的符号位全为0则设置ZF为1，否则ZF为0。然后先计算64位双精度浮点数向量a的逻辑非，再与向量b做逻辑与，如果该中间结果4个通道的的符号位全为0则设置CF位1，否则CF为1，返回ZF

Compute the bitwise AND of 256 bits (representing double-precision (64-bit) floating-point elements) in a and b, producing an intermediate 256-bit value, and set ZF to 1 if the sign bit of each 64-bit element in the intermediate value is zero, otherwise set ZF to 0. Compute the bitwise NOT of a and then AND with b, producing an intermediate value, and set CF to 1 if the sign bit of each 64-bit element in the intermediate value is zero, otherwise set CF to 0. Return the ZF value.
### int _mm256_testz_ps (__m256 a, __m256 b)	
首先计算32位单精度浮点数向量a逻辑与b，如果中间结果的符号位全为0则设置ZF为1，否则ZF为0。然后先计算32位单精度浮点数向量a的逻辑非，再与向量b做逻辑与，如果该中间结果8个通道的符号位全为0则设置CF位1，否则CF为1，返回ZF	

Compute the bitwise AND of 256 bits (representing single-precision (32-bit) floating-point elements) in a and b, producing an intermediate 256-bit value, and set ZF to 1 if the sign bit of each 32-bit element in the intermediate value is zero, otherwise set ZF to 0. Compute the bitwise NOT of a and then AND with b, producing an intermediate value, and set CF to 1 if the sign bit of each 32-bit element in the intermediate value is zero, otherwise set CF to 0. Return the ZF value.
### int _mm256_testz_si256 (__m256i a, __m256i b)	
首先计算256位向量a逻辑与b，如果中间结果所有位全为0则设置ZF为1，否则ZF为0。然后先计算256位向量a的逻辑非，再与向量b做逻辑与，如果该中间结果所有位全为0则设置CF位1，否则CF为1，返回ZF

Compute the bitwise AND of 256 bits (representing integer data) in a and b, and set ZF to 1 if the result is zero, otherwise set ZF to 0. Compute the bitwise NOT of a and then AND with b, and set CF to 1 if the result is zero, otherwise set CF to 0. Return the ZF value.
### __m256d _mm256_xor_pd (__m256d a, __m256d b)	
64位双精度浮点数向量a逻辑与或b	

Compute the bitwise XOR of packed double-precision (64-bit) floating-point elements in a and b, and store the results in dst.
### __m256 _mm256_xor_ps (__m256 a, __m256 b)	
32位单精度浮点数向量a逻辑与或b	

Compute the bitwise XOR of packed single-precision (32-bit) floating-point elements in a and b, and store the results in dst.
### __m256i _mm256_xor_si256 (__m256i a, __m256i b)	
256位向量a逻辑与或b	

Compute the bitwise XOR of 256 bits (representing integer data) in a and b, and store the result in dst.

## Miscellaneous

### __m256i _mm256_alignr_epi8 (__m256i a, __m256i b, const int imm8)	
将16字节向量a与b相拼接为32字节向量，将该中间结果右移imm8个字节，然后存储低16字节	

Concatenate pairs of 16-byte blocks in a and b into a 32-byte temporary result, shift the result right by imm8 bytes, and store the low 16 bytes in dst.
### int _mm256_movemask_epi8 (__m256i a)	
提取8位向量a的最高位组成一个32位dst	

Create mask from the most significant bit of each 8-bit element in a, and store the result in dst.
### int _mm256_movemask_pd (__m256d a)	
提取64位双精度浮点数向量a的最高位组成一个32位dst	

Set each bit of mask dst based on the most significant bit of the corresponding packed double-precision (64-bit) floating-point element in a.
### int _mm256_movemask_ps (__m256 a)	
提取32位单精度浮点数向量a的最高位组成一个32位dst	

Set each bit of mask dst based on the most significant bit of the corresponding packed single-precision (32-bit) floating-point element in a.
### __m256i _mm256_mpsadbw_epu8 (__m256i a, __m256i b, const int imm8)	
Compute the sum of absolute differences (SADs) of quadruplets of unsigned 8-bit integers in a compared to those in b, and store the 16-bit results in dst. Eight SADs are performed for each 128-bit lane using one quadruplet from b and eight quadruplets from a. One quadruplet is selected from b starting at on the offset specified in imm8. Eight quadruplets are formed from sequential 8-bit integers selected from a starting at the offset specified in imm8.
### __m256i _mm256_packs_epi16 (__m256i a, __m256i b)	
使用饱和，将有符号16位整形向量a和b缩小，并交叉保存为一个8位整形向量dst	

Convert packed signed 16-bit integers from a and b to packed 8-bit integers using signed saturation, and store the results in dst.
### __m256i _mm256_packs_epi32 (__m256i a, __m256i b)	
使用饱和，将有符号32位整形向量a和b缩小，并交叉保存为一个16位整形向量dst	

Convert packed signed 32-bit integers from a and b to packed 16-bit integers using signed saturation, and store the results in dst.
### __m256i _mm256_packus_epi16 (__m256i a, __m256i b)	
使用饱和，将有符号16位整形向量a和b缩小，并交叉保存为一个8位无符号整形向量dst	

Convert packed signed 16-bit integers from a and b to packed 8-bit integers using unsigned saturation, and store the results in dst.
### __m256i _mm256_packus_epi32 (__m256i a, __m256i b)	
使用饱和，将有符号32位整形向量a和b缩小，并交叉保存为一个16位无符号整形向量dst	

Convert packed signed 32-bit integers from a and b to packed 16-bit integers using unsigned saturation, and store the results in dst.

## Move
### __m256d _mm256_movedup_pd (__m256d a)	
复制64位双精度浮点数向量的偶数索引的元素到相邻奇数索引的元素	

Duplicate even-indexed double-precision (64-bit) floating-point elements from a, and store the results in dst.
### __m256 _mm256_movehdup_ps (__m256 a)	
复制32位单精度浮点数向量的奇数索引的元素到相邻偶数索引的元素	

Duplicate odd-indexed single-precision (32-bit) floating-point elements from a, and store the results in dst.
### __m256 _mm256_moveldup_ps (__m256 a)	
复制32位单精度浮点数向量的偶数索引的元素到相邻奇数索引的元素	

Duplicate even-indexed single-precision (32-bit) floating-point elements from a, and store the results in dst.

## Probability/Statistics
### __m256i _mm256_avg_epu16 (__m256i a, __m256i b)	
计算16位无符号整形向量a和b的均值	

Average packed unsigned 16-bit integers in a and b, and store the results in dst.
### __m256i _mm256_avg_epu8 (__m256i a, __m256i b)	
计算8位无符号整形向量a和b的均值	

Average packed unsigned 8-bit integers in a and b, and store the results in dst.

## Set
### __m256i _mm256_set_epi16 (short e15, short e14, short e13, short e12, short e11, short e10, short e9, short e8, short e7, short e6, short e5, short e4, short e3, short e2, short e1, short e0)	
使用指定值分别设置16位整形向量	

Set packed 16-bit integers in dst with the supplied values.
### __m256i _mm256_set_epi32 (int e7, int e6, int e5, int e4, int e3, int e2, int e1, int e0)	
使用指定值分别设置32位整形向量	

Set packed 32-bit integers in dst with the supplied values.
### __m256i _mm256_set_epi64x (__int64 e3, __int64 e2, __int64 e1, __int64 e0)	
使用指定值分别设置64位整形向量	

Set packed 64-bit integers in dst with the supplied values.
### __m256i _mm256_set_epi8 (char e31, char e30, char e29, char e28, char e27, char e26, char e25, char e24, char e23, char e22, char e21, char e20, char e19, char e18, char e17, char e16, char e15, char e14, char e13, char e12, char e11, char e10, char e9, char e8, char e7, char e6, char e5, char e4, char e3, char e2, char e1, char e0)	
使用指定值分别设置8位整形向量	

Set packed 8-bit integers in dst with the supplied values.
### __m256 _mm256_set_m128 (__m128 hi, __m128 lo)	
使用两个__m128设置__m256	

Set packed __m256 vector dst with the supplied values.
### __m256d _mm256_set_m128d (__m128d hi, __m128d lo)	
使用两个__m128d设置__m256d	

Set packed __m256d vector dst with the supplied values.
### __m256i _mm256_set_m128i (__m128i hi, __m128i lo)	
使用两个__m128i设置__m256i	

Set packed __m256i vector dst with the supplied values.
### __m256d _mm256_set_pd (double e3, double e2, double e1, double e0)	
使用指定值分别设置64位双精度浮点数向量	

Set packed double-precision (64-bit) floating-point elements in dst with the supplied values.
### __m256 _mm256_set_ps (float e7, float e6, float e5, float e4, float e3, float e2, float e1, float e0)	
使用指定值分别设置32位单精度浮点数向量	

Set packed single-precision (32-bit) floating-point elements in dst with the supplied values.
### __m256i _mm256_set1_epi16 (short a)	
将一个16位整形广播为向量	

Broadcast 16-bit integer a to all all elements of dst. This intrinsic may generate the vpbroadcastw.
### __m256i _mm256_set1_epi32 (int a)	
将一个32位整形广播为向量	

Broadcast 32-bit integer a to all elements of dst. This intrinsic may generate the vpbroadcastd.
### __m256i _mm256_set1_epi64x (long long a)	
将一个64位整形广播为向量	

Broadcast 64-bit integer a to all elements of dst. This intrinsic may generate the vpbroadcastq.
### __m256i _mm256_set1_epi8 (char a)	
将一个8位整形广播为向量	

Broadcast 8-bit integer a to all elements of dst. This intrinsic may generate the vpbroadcastb.
### __m256d _mm256_set1_pd (double a)	
将一个64位双精度浮点数广播为向量	

Broadcast double-precision (64-bit) floating-point value a to all elements of dst.
### __m256 _mm256_set1_ps (float a)	
将一个32位单精度浮点数广播为向量	

Broadcast single-precision (32-bit) floating-point value a to all elements of dst.
### __m256i _mm256_setr_epi16 (short e15, short e14, short e13, short e12, short e11, short e10, short e9, short e8, short e7, short e6, short e5, short e4, short e3, short e2, short e1, short e0)	
反向使用指定值分别设置16位整形向量	

Set packed 16-bit integers in dst with the supplied values in reverse order.
### __m256i _mm256_setr_epi32 (int e7, int e6, int e5, int e4, int e3, int e2, int e1, int e0)	
反向使用指定值分别设置32位整形向量	

Set packed 32-bit integers in dst with the supplied values in reverse order.
### __m256i _mm256_setr_epi64x (__int64 e3, __int64 e2, __int64 e1, __int64 e0)	
反向使用指定值分别设置64位整形向量	

Set packed 64-bit integers in dst with the supplied values in reverse order.
### __m256i _mm256_setr_epi8 (char e31, char e30, char e29, char e28, char e27, char e26, char e25, char e24, char e23, char e22, char e21, char e20, char e19, char e18, char e17, char e16, char e15, char e14, char e13, char e12, char e11, char e10, char e9, char e8, char e7, char e6, char e5, char e4, char e3, char e2, char e1, char e0)	反向使用指定值分别设置8位整形向量	Set packed 8-bit integers in dst with the supplied values in reverse order.
### __m256 _mm256_setr_m128 (__m128 lo, __m128 hi)	
反向使用两个__m128设置__m256	

Set packed __m256 vector dst with the supplied values.
### __m256d _mm256_setr_m128d (__m128d lo, __m128d hi)	
反向使用两个__m128d设置__m256d	

Set packed __m256d vector dst with the supplied values.
### __m256i _mm256_setr_m128i (__m128i lo, __m128i hi)	
反向使用两个__m128i设置__m256i	

Set packed __m256i vector dst with the supplied values.
### __m256d _mm256_setr_pd (double e3, double e2, double e1, double e0)	
反向使用指定值分别，设置64位双精度浮点数向量	

Set packed double-precision (64-bit) floating-point elements in dst with the supplied values in reverse order.
### __m256 _mm256_setr_ps (float e7, float e6, float e5, float e4, float e3, float e2, float e1, float e0)	
反向使用指定值分别，设置32位单精度浮点数向量	

Set packed single-precision (32-bit) floating-point elements in dst with the supplied values in reverse order.
### __m256d _mm256_setzero_pd (void)	

返回一个全为零的__m256d	

Return vector of type __m256d with all elements set to zero.
### __m256 _mm256_setzero_ps (void)	
返回一个全为零的__m256	

Return vector of type __m256 with all elements set to zero.
### __m256i _mm256_setzero_si256 (void)	
返回一个全为零的__m256i	

Return vector of type __m256i with all elements set to zero.

## Shift
### __m256i _mm256_bslli_epi128 (__m256i a, const int imm8)	
使用0填充，左移128位向量imm8个字节	

Shift 128-bit lanes in a left by imm8 bytes while shifting in zeros, and store the results in dst.
### __m256i _mm256_bsrli_epi128 (__m256i a, const int imm8)	
使用0填充，右移128位向量imm8个字节	

Shift 128-bit lanes in a right by imm8 bytes while shifting in zeros, and store the results in dst.
### __m256i _mm256_sll_epi16 (__m256i a, __m128i count)	
使用0填充，左移16位整形向量count位	

Shift packed 16-bit integers in a left by count while shifting in zeros, and store the results in dst
### __m256i _mm256_sll_epi32 (__m256i a, __m128i count)	
使用0填充，左移32位整形向量count位	

Shift packed 32-bit integers in a left by count while shifting in zeros, and store the results in dst.
### __m256i _mm256_sll_epi64 (__m256i a, __m128i count)	

使用0填充，左移64位整形向量count位	

Shift packed 64-bit integers in a left by count while shifting in zeros, and store the results in dst.
### __m256i _mm256_slli_epi16 (__m256i a, int imm8)	
使用0填充，左移16位整形向量imm8位	

Shift packed 16-bit integers in a left by imm8 while shifting in zeros, and store the results in dst.
### __m256i _mm256_slli_epi32 (__m256i a, int imm8)	
使用0填充，左移32位整形向量imm8位	

Shift packed 32-bit integers in a left by imm8 while shifting in zeros, and store the results in dst.
### __m256i _mm256_slli_epi64 (__m256i a, int imm8)	
使用0填充，左移64位整形向量imm8位	

Shift packed 64-bit integers in a left by imm8 while shifting in zeros, and store the results in dst.
### __m256i _mm256_slli_si256 (__m256i a, const int imm8)	
使用0填充，左移128位向量imm8个字节	

Shift 128-bit lanes in a left by imm8 bytes while shifting in zeros, and store the results in dst.
### __m256i _mm256_sllv_epi32 (__m256i a, __m256i count)	
使用0填充，根据count中对应通道的数值左移32位整形向量	

Shift packed 32-bit integers in a left by the amount specified by the corresponding element in count while shifting in zeros, and store the results in dst.
### __m256i _mm256_sllv_epi64 (__m256i a, __m256i count)	
使用0填充，根据count中对应通道的数值左移64位整形向量	

Shift packed 64-bit integers in a left by the amount specified by the corresponding element in count while shifting in zeros, and store the results in dst.
### __m256i _mm256_sra_epi16 (__m256i a, __m128i count)	
使用算术右移，右移16位整形向量count位	

Shift packed 16-bit integers in a right by count while shifting in sign bits, and store the results in dst.
### __m256i _mm256_sra_epi32 (__m256i a, __m128i count)	
使用算术右移，右移32位整形向量count位	

Shift packed 32-bit integers in a right by count while shifting in sign bits, and store the results in dst.
### __m256i _mm256_srai_epi16 (__m256i a, int imm8)	
使用算术右移，右移16位整形向量imm8位	

Shift packed 16-bit integers in a right by imm8 while shifting in sign bits, and store the results in dst.
### __m256i _mm256_srai_epi32 (__m256i a, int imm8)	
使用算术右移，右移32位整形向量imm8位	

Shift packed 32-bit integers in a right by imm8 while shifting in sign bits, and store the results in dst.
### __m256i _mm256_srav_epi32 (__m256i a, __m256i count)	
使用算术右移，根据count中对应通道的数值右移32位整形向量	

Shift packed 32-bit integers in a right by the amount specified by the corresponding element in count while shifting in sign bits, and store the results in dst.
### __m256i _mm256_srl_epi16 (__m256i a, __m128i count)	
使用逻辑右移，右移16位整形向量count位	

Shift packed 16-bit integers in a right by count while shifting in zeros, and store the results in dst.
### __m256i _mm256_srl_epi32 (__m256i a, __m128i count)	
使用逻辑右移，右移32位整形向量count位	

Shift packed 32-bit integers in a right by count while shifting in zeros, and store the results in dst.
### __m256i _mm256_srl_epi64 (__m256i a, __m128i count)	
使用逻辑右移，右移64位整形向量count位	

Shift packed 64-bit integers in a right by count while shifting in zeros, and store the results in dst.
### __m256i _mm256_srli_epi16 (__m256i a, int imm8)	
使用逻辑右移，右移16位整形向量 imm8位	

Shift packed 16-bit integers in a right by imm8 while shifting in zeros, and store the results in dst.
### __m256i _mm256_srli_epi32 (__m256i a, int imm8)	
使用逻辑右移，右移32位整形向量 imm8位	

Shift packed 32-bit integers in a right by imm8 while shifting in zeros, and store the results in dst.
### __m256i _mm256_srli_epi64 (__m256i a, int imm8)	
使用逻辑右移，右移64位整形向量 imm8位	

Shift packed 64-bit integers in a right by imm8 while shifting in zeros, and store the results in dst.
### __m256i _mm256_srli_si256 (__m256i a, const int imm8)	
使用逻辑右移，右移128位向量 imm8位	

Shift 128-bit lanes in a right by imm8 bytes while shifting in zeros, and store the results in dst.
### __m256i _mm256_srlv_epi32 (__m256i a, __m256i count)	
使用逻辑右移，根据count中对应通道的数值右移32位整形向量	

Shift packed 32-bit integers in a right by the amount specified by the corresponding element in count while shifting in zeros, and store the results in dst.
### __m256i _mm256_srlv_epi64 (__m256i a, __m256i count)	
使用逻辑右移，根据count中对应通道的数值右移64位整形向量	

Shift packed 64-bit integers in a right by the amount specified by the corresponding element in count while shifting in zeros, and store the results in dst.

## Special Math Functions
### __m256i _mm256_abs_epi16 (__m256i a)	
计算16位有符号整形向量a的绝对值	

Compute the absolute value of packed signed 16-bit integers in a, and store the unsigned results in dst.
### __m256i _mm256_abs_epi32 (__m256i a)	

计算32位有符号整形向量a的绝对值	

Compute the absolute value of packed signed 32-bit integers in a, and store the unsigned results in dst.
### __m256i _mm256_abs_epi8 (__m256i a)	
计算8位有符号整形向量a的绝对值	

Compute the absolute value of packed signed 8-bit integers in a, and store the unsigned results in dst.
### __m256d _mm256_ceil_pd (__m256d a)	
将64位双精度浮点数向量a向上取整，并返回64位双精度浮点数向量	

Round the packed double-precision (64-bit) floating-point elements in a up to an integer value, and store the results as packed double-precision floating-point elements in dst.
### __m256 _mm256_ceil_ps (__m256 a)	
将32位单精度浮点数向量a向上取整，并返回32位单精度浮点数向量	

Round the packed single-precision (32-bit) floating-point elements in a up to an integer value, and store the results as packed single-precision floating-point elements in dst.
### __m256d _mm256_floor_pd (__m256d a)	
将64位双精度浮点数向量a向下取整，并返回64位双精度浮点数向量	

Round the packed double-precision (64-bit) floating-point elements in a down to an integer value, and store the results as packed double-precision floating-point elements in dst.
### __m256 _mm256_floor_ps (__m256 a)	
将32位单精度浮点数向量a向下取整，并返回32位单精度浮点数向量	

Round the packed single-precision (32-bit) floating-point elements in a down to an integer value, and store the results as packed single-precision floating-point elements in dst.
### __m256i _mm256_max_epi16 (__m256i a, __m256i b)	
计算16位有符号整形向量a与b的最大值	

Compare packed signed 16-bit integers in a and b, and store packed maximum values in dst.
### __m256i _mm256_max_epi32 (__m256i a, __m256i b)	
计算32位有符号整形向量a与b的最大值	

Compare packed signed 32-bit integers in a and b, and store packed maximum values in dst.
### __m256i _mm256_max_epi8 (__m256i a, __m256i b)	
计算8位有符号整形向量a与b的最大值	

Compare packed signed 8-bit integers in a and b, and store packed maximum values in dst.
### __m256i _mm256_max_epu16 (__m256i a, __m256i b)	
计算16位无符号整形向量a与b的最大值	

Compare packed unsigned 16-bit integers in a and b, and store packed maximum values in dst.
### __m256i _mm256_max_epu32 (__m256i a, __m256i b)	
计算32位无符号整形向量a与b的最大值	

Compare packed unsigned 32-bit integers in a and b, and store packed maximum values in dst.
### __m256i _mm256_max_epu8 (__m256i a, __m256i b)	
计算8位无符号整形向量a与b的最大值	

Compare packed unsigned 8-bit integers in a and b, and store packed maximum values in dst.
### __m256d _mm256_max_pd (__m256d a, __m256d b)	
计算64位双精度浮点数向量a与b的最大值。当a、b为NaN或+0时不符合IEEE754标准	

Compare packed double-precision (64-bit) floating-point elements in a and b, and store packed maximum values in dst.dst does not follow the IEEE Standard for Floating-Point Arithmetic (IEEE 754) maximum value when inputs are NaN or signed-zero values.
### __m256 _mm256_max_ps (__m256 a, __m256 b)	
计算32位单精度浮点数向量a与b的最大值。当a、b为NaN或+0时不符合IEEE754标准	

Compare packed single-precision (32-bit) floating-point elements in a and b, and store packed maximum values in dst.dst does not follow the IEEE Standard for Floating-Point Arithmetic (IEEE 754) maximum value when inputs are NaN or signed-zero values.
### __m256i _mm256_min_epi16 (__m256i a, __m256i b)	
计算16位有符号整形向量a与b的最小值	

Compare packed signed 16-bit integers in a and b, and store packed minimum values in dst.
### __m256i _mm256_min_epi32 (__m256i a, __m256i b)	
计算32位有符号整形向量a与b的最小值	

Compare packed signed 32-bit integers in a and b, and store packed minimum values in dst.
### __m256i _mm256_min_epi8 (__m256i a, __m256i b)	
计算8位有符号整形向量a与b的最小值	

Compare packed signed 8-bit integers in a and b, and store packed minimum values in dst.
### __m256i _mm256_min_epu16 (__m256i a, __m256i b)	
计算16位无符号整形向量a与b的最小值	

Compare packed unsigned 16-bit integers in a and b, and store packed minimum values in dst.
### __m256i _mm256_min_epu32 (__m256i a, __m256i b)	
计算32位无符号整形向量a与b的最小值	

Compare packed unsigned 32-bit integers in a and b, and store packed minimum values in dst.
### __m256i _mm256_min_epu8 (__m256i a, __m256i b)	
计算8位无符号整形向量a与b的最小值	

Compare packed unsigned 8-bit integers in a and b, and store packed minimum values in dst.
### __m256d _mm256_min_pd (__m256d a, __m256d b)	
计算64位双精度浮点数向量a与b的最小值，当a、b为NaN或+0时不符合IEEE754标准	

Compare packed double-precision (64-bit) floating-point elements in a and b, and store packed minimum values in dst.dst does not follow the IEEE Standard for Floating-Point Arithmetic (IEEE 754) minimum value when inputs are NaN or signed-zero values.
### __m256 _mm256_min_ps (__m256 a, __m256 b)	
计算32位单精度浮点数向量a与b的最小值，当a、b为NaN或+0时不符合IEEE754标准	

Compare packed single-precision (32-bit) floating-point elements in a and b, and store packed minimum values in dst.dst does not follow the IEEE Standard for Floating-Point Arithmetic (IEEE 754) minimum value when inputs are NaN or signed-zero values.
### __m256d _mm256_round_pd (__m256d a, int rounding)	
计算64位双精度浮点数向量a取整，并返回64位双精度浮点数向量，取整方法根据参数rounding。	

"Round the packed double-precision (64-bit) floating-point elements in a using the rounding parameter, and store the results as packed double-precision floating-point elements in dst.Rounding is done according to the rounding[3:0] parameter, which can be one of:"
### __m256 _mm256_round_ps (__m256 a, int rounding)	
计算32位单精度浮点数向量a取整，并返回32位单精度浮点数向量，取整方法根据参数rounding。	

"Round the packed single-precision (32-bit) floating-point elements in a using the rounding parameter, and store the results as packed single-precision floating-point elements in dst.Rounding is done according to the rounding[3:0] parameter, which can be one of:"

## Store
### void _mm256_maskstore_epi32 (int* mem_addr, __m256i mask, __m256i a)	
保存32位整形向量至内存，当mask对应通道的最高位是零时不保存	

Store packed 32-bit integers from a into memory using mask (elements are not stored when the highest bit is not set in the corresponding element).
### void _mm256_maskstore_epi64 (__int64* mem_addr, __m256i mask, __m256i a)	
保存64位整形向量至内存，当mask对应通道的最高位是零时不保存	

Store packed 64-bit integers from a into memory using mask (elements are not stored when the highest bit is not set in the corresponding element).
### void _mm256_maskstore_pd (double * mem_addr, __m256i mask, __m256d a)	
保存64位双精度浮点数向量至内存，当mask对应通道的最高位是零时不保存	

Store packed double-precision (64-bit) floating-point elements from a into memory using mask.
### void _mm256_maskstore_ps (float * mem_addr, __m256i mask, __m256 a)	
保存32位单精度浮点数向量至内存，当mask对应通道的最高位是零时不保存	

Store packed single-precision (32-bit) floating-point elements from a into memory using mask.
### void _mm256_store_pd (double * mem_addr, __m256d a)	
保存256位（由4个64位双精度浮点数组成）至内存， mem_addr必须32字节对齐，否则会产生通用保护异常	

Store 256-bits (composed of 4 packed double-precision (64-bit) floating-point elements) from a into memory. mem_addr must be aligned on a 32-byte boundary or a general-protection exception may be generated.
### void _mm256_store_ps (float * mem_addr, __m256 a)	
保存256位（由8个32位单精度浮点数组成）至内存， mem_addr必须32字节对齐，否则会产生通用保护异常	

Store 256-bits (composed of 8 packed single-precision (32-bit) floating-point elements) from a into memory. mem_addr must be aligned on a 32-byte boundary or a general-protection exception may be generated.
### void _mm256_store_si256 (__m256i * mem_addr, __m256i a)	
保存256位整形数据至内存， mem_addr必须32字节对齐，否则会产生通用保护异常	

Store 256-bits of integer data from a into memory. mem_addr must be aligned on a 32-byte boundary or a general-protection exception may be generated.
### void _mm256_storeu_pd (double * mem_addr, __m256d a)	
保存256位（由4个64位双精度浮点数组成）至内存， mem_addr无需32字节对齐	

Store 256-bits (composed of 4 packed double-precision (64-bit) floating-point elements) from a into memory. mem_addr does not need to be aligned on any particular boundary.
### void _mm256_storeu_ps (float * mem_addr, __m256 a)	
保存256位（由8个32位单精度浮点数组成）至内存， mem_addr无需32字节对齐	

Store 256-bits (composed of 8 packed single-precision (32-bit) floating-point elements) from a into memory. mem_addr does not need to be aligned on any particular boundary.
### void _mm256_storeu_si256 (__m256i * mem_addr, __m256i a)	
保存256位整形数据至内存， mem_addr无需32字节对齐	

Store 256-bits of integer data from a into memory. mem_addr does not need to be aligned on any particular boundary.
### void _mm256_storeu2_m128 (float* hiaddr, float* loaddr, __m256 a)	
将a拆分为两个128位数据（每个由4个32位单精度浮点数向量组成），分别存储至 hiaddr和loaddr。 hiaddr和loaddr无需对齐

Store the high and low 128-bit halves (each composed of 4 packed single-precision (32-bit) floating-point elements) from a into memory two different 128-bit locations. hiaddr and loaddr do not need to be aligned on any particular boundary.
### void _mm256_storeu2_m128d (double* hiaddr, double* loaddr, __m256d a)	
将a拆分为两个128位数据（每个由2个64位双精度浮点数向量组成），分别存储至 hiaddr和loaddr。 hiaddr和loaddr无需对齐	

Store the high and low 128-bit halves (each composed of 2 packed double-precision (64-bit) floating-point elements) from a into memory two different 128-bit locations. hiaddr and loaddr do not need to be aligned on any particular boundary.
### void _mm256_storeu2_m128i (__m128i* hiaddr, __m128i* loaddr, __m256i a)	
将a拆分为两个128位数据（每个由128位整形数据组成），分别存储至 hiaddr和loaddr。 hiaddr和loaddr无需对齐	

Store the high and low 128-bit halves (each composed of integer data) from a into memory two different 128-bit locations. hiaddr and loaddr do not need to be aligned on any particular boundary.
### void _mm256_stream_pd (double * mem_addr, __m256d a)	
使用non-temporal memory hint，保存256位（由4个64位双精度浮点数组成）至内存， mem_addr必须32字节对齐，否则会产生通用保护异常

Store 256-bits (composed of 4 packed double-precision (64-bit) floating-point elements) from a into memory using a non-temporal memory hint. mem_addr must be aligned on a 32-byte boundary or a general-protection exception may be generated.
### void _mm256_stream_ps (float * mem_addr, __m256 a)	
使用non-temporal memory hint，保存256位（由8个32位单精度浮点数组成）至内存， mem_addr必须32字节对齐，否则会产生通用保护异常

Store 256-bits (composed of 8 packed single-precision (32-bit) floating-point elements) from a into memory using a non-temporal memory hint. mem_addr must be aligned on a 32-byte boundary or a general-protection exception may be generated.
### void _mm256_stream_si256 (__m256i * mem_addr, __m256i a)
使用non-temporal memory hint，保存256位整形数据至内存， mem_addr必须32字节对齐，否则会产生通用保护异常

Store 256-bits of integer data from a into memory using a non-temporal memory hint. mem_addr must be aligned on a 32-byte boundary or a general-protection exception may be generated.

## Swizzle
### __m256i _mm256_blend_epi16 (__m256i a, __m256i b, const int imm8)	
根据imm8的低8位混合（二选一）16位整形向量a和b的高低128位通道（即第0个16位通道和第8个16位通道使用相同的混合规则）	

Blend packed 16-bit integers from a and b within 128-bit lanes using control mask imm8, and store the results in dst.
### __m256i _mm256_blend_epi32 (__m256i a, __m256i b, const int imm8)	
根据imm8的低8位混合（二选一）32位整形向量a和b	

Blend packed 32-bit integers from a and b using control mask imm8, and store the results in dst.
### __m256d _mm256_blend_pd (__m256d a, __m256d b, const int imm8)	
根据imm8的低4位混合（二选一）64位双精度浮点数向量a和b	

Blend packed double-precision (64-bit) floating-point elements from a and b using control mask imm8, and store the results in dst.
### __m256 _mm256_blend_ps (__m256 a, __m256 b, const int imm8)	
根据imm8的低8位混合（二选一）32位单精度浮点数向量a和b	

Blend packed single-precision (32-bit) floating-point elements from a and b using control mask imm8, and store the results in dst.
### __m256i _mm256_blendv_epi8 (__m256i a, __m256i b, __m256i mask)	
根据mask对应通道的最高位混合（二选一）8位整形向量a和b	

Blend packed 8-bit integers from a and b using mask, and store the results in dst.
### __m256d _mm256_blendv_pd (__m256d a, __m256d b, __m256d mask)	
根据mask对应通道的最高位混合（二选一）64位双精度浮点数向量a和b	

Blend packed double-precision (64-bit) floating-point elements from a and b using mask, and store the results in dst.
### __m256 _mm256_blendv_ps (__m256 a, __m256 b, __m256 mask)	
根据mask对应通道的最高位混合（二选一）32位单精度浮点数向量a和b	

Blend packed single-precision (32-bit) floating-point elements from a and b using mask, and store the results in dst.
### __m256d _mm256_broadcast_pd (__m128d const * mem_addr)	
广播内存中的128位数据（由2个64位双精度浮点数组成）	

Broadcast 128 bits from memory (composed of 2 packed double-precision (64-bit) floating-point elements) to all elements of dst.
### __m256 _mm256_broadcast_ps (__m128 const * mem_addr)	
广播内存中的128位数据（由4个32位单精度浮点数成）	

Broadcast 128 bits from memory (composed of 4 packed single-precision (32-bit) floating-point elements) to all elements of dst.
### __m256d _mm256_broadcast_sd (double const * mem_addr)	
广播内存中的一个64位双精度浮点数至dst所有通道	

Broadcast a double-precision (64-bit) floating-point element from memory to all elements of dst.
### __m256i _mm256_broadcastb_epi8 (__m128i a)	
广播8位整形向量最低通道至dst所有通道	

Broadcast the low packed 8-bit integer from a to all elements of dst.
### __m256i _mm256_broadcastd_epi32 (__m128i a)	
广播32位整形向量最低通道至dst所有通道	

Broadcast the low packed 32-bit integer from a to all elements of dst.
### __m256i _mm256_broadcastq_epi64 (__m128i a)	
广播64位整形向量最低通道至dst所有通道	

Broadcast the low packed 64-bit integer from a to all elements of dst.
### __m256d _mm256_broadcastsd_pd (__m128d a)	
广播64位双精度浮点数向量最低通道至dst所有通道	

Broadcast the low double-precision (64-bit) floating-point element from a to all elements of dst.
### __m256i _mm256_broadcastsi128_si256 (__m128i a)	
广播128位整形数据至dst所有通道	

Broadcast 128 bits of integer data from a to all 128-bit lanes in dst.
### __m256 _mm256_broadcastss_ps (__m128 a)	
广播32位单精度浮点数向量最低通道至dst所有通道	

Broadcast the low single-precision (32-bit) floating-point element from a to all elements of dst.
### __m256i _mm256_broadcastw_epi16 (__m128i a)	
广播16位整形向量最低通道至dst所有通道	

Broadcast the low packed 16-bit integer from a to all elements of dst.
### int _mm256_extract_epi16 (__m256i a, const int index)	
根据索引index从a中提取16位整形	

Extract a 16-bit integer from a, selected with index, and store the result in dst.
### __int32 _mm256_extract_epi32 (__m256i a, const int index)	
根据索引index从a中提取32位整形	

Extract a 32-bit integer from a, selected with index, and store the result in dst.
### __int64 _mm256_extract_epi64 (__m256i a, const int index)	
根据索引index从a中提取64位整形	

Extract a 64-bit integer from a, selected with index, and store the result in dst.
### int _mm256_extract_epi8 (__m256i a, const int index)	
根据索引index从a中提取8位整形	

Extract an 8-bit integer from a, selected with index, and store the result in dst.
### __m128d _mm256_extractf128_pd (__m256d a, const int imm8)	
根据索引imm8提取128位数据（由2个64位双精度浮点数组成）	

Extract 128 bits (composed of 2 packed double-precision (64-bit) floating-point elements) from a, selected with imm8, and store the result in dst.
### __m128 _mm256_extractf128_ps (__m256 a, const int imm8)	
根据索引imm8提取128位数据（由4个32位单精度浮点数组成）	

Extract 128 bits (composed of 4 packed single-precision (32-bit) floating-point elements) from a, selected with imm8, and store the result in dst.
### __m128i _mm256_extractf128_si256 (__m256i a, const int imm8)	
根据索引imm8提取128位数据（由整形数据组成）	

Extract 128 bits (composed of integer data) from a, selected with imm8, and store the result in dst.
### __m128i _mm256_extracti128_si256 (__m256i a, const int imm8)	
根据索引imm8提取128位数据（由整形数据组成）	

Extract 128 bits (composed of integer data) from a, selected with imm8, and store the result in dst.
### __m256i _mm256_insert_epi16 (__m256i a, __int16 i, const int index)	
复制a至dst，然后根据索引index在相应位置插入16位整形i	

Copy a to dst, and insert the 16-bit integer i into dst at the location specified by index.
### __m256i _mm256_insert_epi32 (__m256i a, __int32 i, const int index)	
复制a至dst，然后根据索引index在相应位置插入32位整形i	

Copy a to dst, and insert the 32-bit integer i into dst at the location specified by index.
### __m256i _mm256_insert_epi64 (__m256i a, __int64 i, const int index)	
复制a至dst，然后根据索引index在相应位置插入64位整形i	

Copy a to dst, and insert the 64-bit integer i into dst at the location specified by index.
### __m256i _mm256_insert_epi8 (__m256i a, __int8 i, const int index)	
复制a至dst，然后根据索引index在相应位置插入8位整形i	

Copy a to dst, and insert the 8-bit integer i into dst at the location specified by index.
### __m256d _mm256_insertf128_pd (__m256d a, __m128d b, int imm8)	
复制a至dst，然后根据索引imm8在相应位置插入128位数据（由2个64位双精度浮点数组成）	

Copy a to dst, then insert 128 bits (composed of 2 packed double-precision (64-bit) floating-point elements) from b into dst at the location specified by imm8.
### __m256 _mm256_insertf128_ps (__m256 a, __m128 b, int imm8)	
复制a至dst，然后根据索引imm8在相应位置插入128位数据（由4个32位单精度浮点数组成）	

Copy a to dst, then insert 128 bits (composed of 4 packed single-precision (32-bit) floating-point elements) from b into dst at the location specified by imm8.
### __m256i _mm256_insertf128_si256 (__m256i a, __m128i b, int imm8)	
复制a至dst，然后根据索引imm8在相应位置插入128位数据	

Copy a to dst, then insert 128 bits from b into dst at the location specified by imm8.
### __m256i _mm256_inserti128_si256 (__m256i a, __m128i b, const int imm8)	
复制a至dst，然后根据索引imm8在相应位置插入128位数据（由整形数据组成）	

Copy a to dst, then insert 128 bits (composed of integer data) from b into dst at the location specified by imm8.
### __m256d _mm256_permute_pd (__m256d a, int imm8)	
根据 imm8（低4位每1位控制1个通道）重排64位双精度浮点数向量（只能在高低各128位内重排）	

Shuffle double-precision (64-bit) floating-point elements in a within 128-bit lanes using the control in imm8, and store the results in dst.
### __m256 _mm256_permute_ps (__m256 a, int imm8)	
根据 imm8（低8位每2位控制1个通道）重排32位单精度浮点数向量（只能在高低各128位内重排）	

Shuffle single-precision (32-bit) floating-point elements in a within 128-bit lanes using the control in imm8, and store the results in dst.
### __m256d _mm256_permute2f128_pd (__m256d a, __m256d b, int imm8)	
根据imm8（低8位每4位控制1个通道，每4位的最高位可控制是否输出0）混合128位（由2个64位双精度浮点数组成）向量a和b

Shuffle 128-bits (composed of 2 packed double-precision (64-bit) floating-point elements) selected by imm8 from a and b, and store the results in dst.
### __m256 _mm256_permute2f128_ps (__m256 a, __m256 b, int imm8)	
根据imm8（低8位每4位控制1个通道，每4位最高位可控制是否输出0）混合128位（由4个32位单精度浮点数组成）向量a和b	

Shuffle 128-bits (composed of 4 packed single-precision (32-bit) floating-point elements) selected by imm8 from a and b, and store the results in dst.
### __m256i _mm256_permute2f128_si256 (__m256i a, __m256i b, int imm8)	
根据imm8（低8位每4位控制1个通道，每4位最高位可控制是否输出0）混合128位（由整形数据组成）向量a和b

Shuffle 128-bits (composed of integer data) selected by imm8 from a and b, and store the results in dst.
### __m256i _mm256_permute2x128_si256 (__m256i a, __m256i b, const int imm8)	
根据imm8（低8位每4位控制1个通道，每4位最高位可控制是否输出0）混合128位（由整形数据组成）向量a和b

Shuffle 128-bits (composed of integer data) selected by imm8 from a and b, and store the results in dst.
### __m256i _mm256_permute4x64_epi64 (__m256i a, const int imm8)	
根据 imm8（低8位每2位控制1个通道）重排64位整形向量	

Shuffle 64-bit integers in a across lanes using the control in imm8, and store the results in dst.
### __m256d _mm256_permute4x64_pd (__m256d a, const int imm8)	
根据 imm8（低8位每2位控制1个通道）重排64位双精度浮点数向量

Shuffle double-precision (64-bit) floating-point elements in a across lanes using the control in imm8, and store the results in dst.
### __m256d _mm256_permutevar_pd (__m256d a, __m256i b)	
根据b（每64位的第2位控制1个通道）重排64位双精度浮点数向量（只能在高低各128位内重排）

Shuffle double-precision (64-bit) floating-point elements in a within 128-bit lanes using the control in b, and store the results in dst.
### __m256 _mm256_permutevar_ps (__m256 a, __m256i b)	
根据b（每32位的低2位控制1个通道）重排32位单精度浮点数向量（只能在高低各128位内重排）	

Shuffle single-precision (32-bit) floating-point elements in a within 128-bit lanes using the control in b, and store the results in dst.
### __m256i _mm256_permutevar8x32_epi32 (__m256i a, __m256i idx)	
根据 idx（每32位的低3位控制1个通道）重排32位整形向量	

Shuffle 32-bit integers in a across lanes using the corresponding index in idx, and store the results in dst.
### __m256 _mm256_permutevar8x32_ps (__m256 a, __m256i idx)	
根据 idx（每32位的低3位控制1个通道）重排32位单精度浮点数向量	

Shuffle single-precision (32-bit) floating-point elements in a across lanes using the corresponding index in idx.
### __m256i _mm256_shuffle_epi32 (__m256i a, const int imm8)	
根据 imm8（低8位的每2位控制1个通道）重排32位整形向量（只能在高低各128位内重排）	

Shuffle 32-bit integers in a within 128-bit lanes using the control in imm8, and store the results in dst.
### __m256i _mm256_shuffle_epi8 (__m256i a, __m256i b)	
根据b（每8位的低4位控制1个通道，每4位最高位可控制是否输出0）重排8位整形向量（只能在高低各128位内重排，可将通道置零）

Shuffle 8-bit integers in a within 128-bit lanes according to shuffle control mask in the corresponding 8-bit element of b, and store the results in dst.
### __m256d _mm256_shuffle_pd (__m256d a, __m256d b, const int imm8)	
根据 imm8（低4位的每1位控制1个通道）混合64位双精度浮点数向量a和b（只能在高低各128位内混合）	

Shuffle double-precision (64-bit) floating-point elements within 128-bit lanes using the control in imm8, and store the results in dst.
### __m256 _mm256_shuffle_ps (__m256 a, __m256 b, const int imm8)	
根据 imm8（低4位的每1位控制1个通道）混合64位双精度浮点数向量a和b（只能在高低各128位内混合）

Shuffle single-precision (32-bit) floating-point elements in a within 128-bit lanes using the control in imm8, and store the results in dst.
### __m256i _mm256_shufflehi_epi16 (__m256i a, const int imm8)	
根据imm8（低8位的每2位控制1个通道）重排128位向量的高64位，并保存到dst对应128位通道的高64位，将a中的128位通道的低64位直接拷贝到dst的对应通道的低64位

Shuffle 16-bit integers in the high 64 bits of 128-bit lanes of a using the control in imm8. Store the results in the high 64 bits of 128-bit lanes of dst, with the low 64 bits of 128-bit lanes being copied from from a to dst.
### __m256i _mm256_shufflelo_epi16 (__m256i a, const int imm8)	
根据imm8（低8位的每2位控制1个通道）重排128位向量a的低64位，并保存到dst对应128位通道的低64位，将128位向量的高64位直接拷贝到dst的对应通道的高64位	

Shuffle 16-bit integers in the low 64 bits of 128-bit lanes of a using the control in imm8. Store the results in the low 64 bits of 128-bit lanes of dst, with the high 64 bits of 128-bit lanes being copied from from a to dst.
### __m256i _mm256_unpackhi_epi16 (__m256i a, __m256i b)	
以16位整形为单位交叉拼接128位向量a和b的各高64位	

Unpack and interleave 16-bit integers from the high half of each 128-bit lane in a and b, and store the results in dst.
### __m256i _mm256_unpackhi_epi32 (__m256i a, __m256i b)	
以32位整形为单位交叉拼接128位向量a和b的各高64位	

Unpack and interleave 32-bit integers from the high half of each 128-bit lane in a and b, and store the results in dst.
### __m256i _mm256_unpackhi_epi64 (__m256i a, __m256i b)	
以64位整形为单位交叉拼接128位向量a和b的各高64位	

Unpack and interleave 64-bit integers from the high half of each 128-bit lane in a and b, and store the results in dst.
### __m256i _mm256_unpackhi_epi8 (__m256i a, __m256i b)	
以8位整形为单位交叉拼接128位向量a和b的各高64位	

Unpack and interleave 8-bit integers from the high half of each 128-bit lane in a and b, and store the results in dst.
### __m256d _mm256_unpackhi_pd (__m256d a, __m256d b)	
以64位双精度浮点数为单位交叉拼接128位向量a和b的各高64位	

Unpack and interleave double-precision (64-bit) floating-point elements from the high half of each 128-bit lane in a and b, and store the results in dst.
### __m256 _mm256_unpackhi_ps (__m256 a, __m256 b)	
以32位单精度浮点数为单位交叉拼接128位向量a和b的各高64位	

Unpack and interleave single-precision (32-bit) floating-point elements from the high half of each 128-bit lane in a and b, and store the results in dst.
### __m256i _mm256_unpacklo_epi16 (__m256i a, __m256i b)	
以16位整形为单位交叉拼接128位向量a和b的各低64位	

Unpack and interleave 16-bit integers from the low half of each 128-bit lane in a and b, and store the results in dst.
### __m256i _mm256_unpacklo_epi32 (__m256i a, __m256i b)	
以32位整形为单位交叉拼接128位向量a和b的各低64位	

Unpack and interleave 32-bit integers from the low half of each 128-bit lane in a and b, and store the results in dst.
### __m256i _mm256_unpacklo_epi64 (__m256i a, __m256i b)	
以64位整形为单位交叉拼接128位向量a和b的各低64位	

Unpack and interleave 64-bit integers from the low half of each 128-bit lane in a and b, and store the results in dst.
### __m256i _mm256_unpacklo_epi8 (__m256i a, __m256i b)	
以8位整形为单位交叉拼接128位向量a和b的各低64位	

Unpack and interleave 8-bit integers from the low half of each 128-bit lane in a and b, and store the results in dst.
### __m256d _mm256_unpacklo_pd (__m256d a, __m256d b)	
以64位双精度浮点数为单位交叉拼接128位向量a和b的各低64位	

Unpack and interleave double-precision (64-bit) floating-point elements from the low half of each 128-bit lane in a and b, and store the results in dst.
### __m256 _mm256_unpacklo_ps (__m256 a, __m256 b)	
以32位单精度浮点数为单位交叉拼接128位向量a和b的各低64位	

Unpack and interleave single-precision (32-bit) floating-point elements from the low half of each 128-bit lane in a and b, and store the results in dst.
