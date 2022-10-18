# Python性能分析工具

在工业视觉检测中，对图像算法的执行效率要求严格，因此代码耗时分析是很重要的一环。除了使用`time.time()`计算时间差，`datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S.%f')`记录当前系统时间外，我们更需要对单个函数、单行代码进行耗时分析。

尤其是面对Opencv、Numpy这种成熟的封装库，通过对其性能分析，能够更深入地理解其库函数性能，以及各参数对其影响。

## cProfile-函数调用耗时分析工具

cProfile是Python内置的工具，使用C语言编写，时间开销较少。使用方法如下例:
```python
import cProfile


def test():
    print("hello world")

# 直接把分析结果打印到控制台
cProfile.run("test()")
# 把分析结果保存到result.out文件中
cProfile.run("test()", filename="result.out")
# 增加排序方式（建议使用该方法）
cProfile.run("test()", filename = None, sort="cumulative")
```
&emsp;&emsp;<small>tip：保存的分析文件并不能通过记事本直接显示，需要使用pstats分析工具。</small>

其输出结果如下格式：
```shell
        2312262 function calls (2282114 primitive calls) in 9.583 seconds

Ordered by: cumulative time

ncalls  tottime  percall  cumtime  percall filename:lineno(function)
    60    0.335    0.006    9.467    0.158 index.py:19(main)  
    55    0.348    0.006    2.030    0.037 imgproc.py:182(filter_spot)
    232    0.099    0.000    1.799    0.008 imgproc.py:130(isnot_contour_smooth)
    512    1.652    0.003    1.652    0.003 {findContours}     
```
每一列的含义为：
- ncalls：函数调用的次数。
- tottime：函数总运行时间，不含函数中子函数的时间。
- percall：函数的平均耗时，= tottime/ncalls。
- cumtime：函数包含所有子函数的总运行时间。
- percall：函数的平均耗时，= cumtime/ncalls。
- filename:lineno(function)：函数具体信息。
  
## line_profiler-代码行耗时分析工具

仅仅使用cProfile定位耗时函数往往是不够的，我们还需要这些函数的每行代码进行耗时分析，定位到具体耗时代码。这时就需要使用line_profilter工具。

该工具库非Python内置，需要提前安装line_profilter包。

&emsp;&emsp;<small>tip：直接从pypl下载安装会报错，请下载[line_profiler-3.0.2-cp38-cp38-win_amd64.whl](https://sigmasquarescom.sharepoint.com/sites/ss-technical/Shared%20Documents/Forms/AllItems.aspx?RootFolder=%2fsites%2fss%2dtechnical%2fShared%20Documents%2fGeneral%2f5%2e%20Software&FolderCTID=0x0120003C17CAFD4E62424F9D3D07185CEB0F28)，再使用pip工具安装。</small>

使用方法如下例：
```python
import line_profiler
import sys


def test():
    print("hello world")

prof = line_profiler.LineProfiler(test)
prof.enable()  # 开始性能分析
test()
prof.disable()  # 停止性能分析
prof.print_stats(sys.stdout)
```
由于我们更多情况下，是对子函数进行分析，因此下面介绍其命令行工具,以实现该目的。其代码实例如下，需要注意的是我们并不需要import任意库，只需要对待分析函数添加装饰器`@profile`，并在代码中运行该函数或其上层函数。
```python
@profile
def test():
    print("hello world")


def main():
    test()


if __name__ == "__main__" :
    main()
```
该代码文件并不能直接使用python解释器直接运行，很显然因为我们并未import该装饰器。此时我们需要在命令行工具中执行如下命令，便会在所有代码执行完毕后显示函数的分析结果：
```shell
PS C:\Users\xzhang>kernprof -l -v test.py
Wrote profile results to test.py.lprof
Timer unit: 1e-07 s

Total time: 0.0020232 s
File: test.py
Function: test at line 296

Line #      Hits         Time  Per Hit   % Time  Line Contents
==============================================================
   296                                           @profile
   297                                           def test():
   298         1       2303.0   2303.0     11.4      img = np.zeros((1000, 1000), dtype=np.uint8)      
   299         1        571.0    571.0      2.8      cv2.circle(img, (500, 500), 100, 255, -1, lineType=cv2.LINE_AA)
   300         1      16505.0  16505.0     81.6      np_out = np.count_nonzero(img)
   301         1        853.0    853.0      4.2      cv_out = cv2.countNonZero(img)
```
命令行代码最后一个参数为该代码所属`.py`的相对路径，同时运行结束后会在代码文件同级生成一个`.lprof`文件。其输出如上格式。每一列的含义为：
- Line： 该行代码行号
- Hits： 该行代码运行次数
- Time： 该行代码运行总时间
- Time Per Hit： 该行代码运行平均耗时，= Time/Hits
- Line Contents： 该行代码内容
  

因此针对我们视觉算法耗时优化的一般流程为：首先使用cProfile对所有函数进行耗时统计，找到其中耗时严重的函数，再使用line_profiler对该段代码逐行进行耗时分析，找到其中耗时严重的代码进行重构或者参数调整。 
