# 概速览C++多线程库

本文大量摘抄了blog[C++ 并发编程（从C++11到C++17）](https://paul.pub/cpp-concurrency/)，可能就算原文加了一些注释。

## 进程与线程

进程（英语：process），是指计算机中已运行的程序。进程为曾经是分时系统的基本运作单位。在面向进程设计的系统（如早期的UNIX，Linux 2.4及更早的版本）中，进程是程序的基本执行实体；

线程（英语：thread）是操作系统能够进行运算调度的最小单位。它被包含在进程之中，是进程中的实际运作单位。

更多的内容就是操作系统里面讲的内容了就不再多涉及了。 作为机器视觉工作者来说多线程对算法速度的提升是巨大的，对于软件开发人员来说就更大了。

## std::thread
```c++
// 01_hello_thread.cpp

#include <iostream>
#include <thread> // 为了使用多线程的接口，我们需要#include <thread>头文件

void hello() { // 新建线程的入口是一个普通的函数，它并没有什么特别的地方
  std::cout << "Hello World from new thread." << std::endl;
}

int main() {
   //创建线程的方式就是构造一个thread对象，并指定入口函数。
  // 与普通对象不一样的是，此时编译器便会为我们创建一个新的操作系统线程，并在新的线程中执行我们的入口函数。
  std::thread t(hello);
  t.join();

  return 0;
}
```
一旦启动线程之后，我们必须决定是要等待直接它结束（通过join），还是让它独立运行（通过detach），我们必须二者选其一。如果在thread对象销毁的时候我们还没有做决定，则thread对象在析构函数出将调用std::terminate()从而导致我们的进程异常退出。

- join：调用此接口时，当前线程会一直阻塞，直到目标线程执行完成（当然，很可能目标线程在此处调用之前就已经执行完成了，不过这不要紧）。因此，如果目标线程的任务非常耗时，你就要考虑好是否需要在主线程上等待它了，因此这很可能会导致主线程卡住。
- detach：detach是让目标线程成为守护线程（daemon threads）。一旦detach之后，目标线程将独立执行，即便其对应的thread对象销毁也不影响线程的执行。并且，你无法再与之通信。

对于这两个接口，都必须是可执行的线程才有意义。你可以通过joinable()接口查询是否可以对它们进行join或者detach。

## 一次调用
主要API
| API       | C++标准 | 说明                                           |
| --------- | ------- | ---------------------------------------------- |
| call_once | C++11   | 即便在多线程环境下，也能保证只调用某个函数一次 |
| once_flag | C++11   | 与call_once配合使用                            |

在一些情况下，我们有些任务需要执行一次，并且我们只希望它执行一次，例如资源的初始化任务。这个时候就可以用到上面的接口。这个接口会保证，即便在多线程的环境下，相应的函数也只会调用一次。

下面就是一个示例：有三个线程都会使用init函数，但是只会有一个线程真正执行它。
```c++
// 05_call_once.cpp

void init() {
  cout << "Initialing..." << endl;
  // Do something...
}

void worker(once_flag* flag) {
  call_once(*flag, init);
}

int main() {
  once_flag flag;

  thread t1(worker, &flag);
  thread t2(worker, &flag);
  thread t3(worker, &flag);

  t1.join();
  t2.join();
  t3.join();

  return 0;
}
```
我们无法确定具体是哪一个线程会执行init。而事实上，我们也不关心，因为只要有某个线程完成这个初始化工作就可以了。

## std::mutex
开发并发系统的目的主要是为了提升性能：将任务分散到多个线程，然后在不同的处理器上同时执行。这些分散开来的线程通常会包含两类任务：
- 独立的对于划分给自己的数据的处理
- 对于处理结果的汇总

其中第1项任务因为每个线程是独立的，不存在竞争条件的问题。而第2项任务，由于所有线程都可能往总结果汇总，这就需要做保护了。在某一个具体的时刻，只应当有一个线程更新总结果，即：保证每个线程对于共享数据的访问是“互斥”的。mutex 就提供了这样的功能。mutex是mutual exclusion（互斥）的简写，主要API：
| API                   | C++标准 | 说明                                             |
| --------------------- | ------- | ------------------------------------------------ |
| mutex                 | C++11   | 提供基本互斥设施                                 |
| timed_mutex           | C++11   | 提供互斥设施，带有超时功能                       |
| recursive_mutex       | C++11   | 提供能被同一线程递归锁定的互斥设施               |
| recursive_timed_mutex | C++11   | 提供能被同一线程递归锁定的互斥设施，带有超时功能 |
| shared_timed_mutex    | C++14   | 提供共享互斥设施并带有超时功能                   |
| shared_mutex          | C++17   | 提供共享互斥设施                                 |

很明显，在这些类中，mutex是最基础的API。其他类都是在它的基础上的改进。所以这些类都提供了下面三个方法，并且它们的功能是一样的：

| 方法     | 说明                                 |
| -------- | ------------------------------------ |
| lock     | 锁定互斥体，如果不可用，则阻塞       |
| try_lock | 尝试锁定互斥体，如果不可用，直接返回 |
| unlock   | 解锁互斥体                           |

这三个方法提供了基础的锁定和解除锁定的功能。使用lock意味着你有很强的意愿一定要获取到互斥体，而使用try_lock则是进行一次尝试。这意味着如果失败了，你通常还有其他的路径可以走。

在这些基础功能之上，其他的类分别在下面三个方面进行了扩展：

- 超时：timed_mutex，recursive_timed_mutex，shared_timed_mutex的名称都带有timed，这意味着它们都支持超时功能。它们都提供了try_lock_for和try_lock_until方法，这两个方法分别可以指定超时的时间长度和时间点。如果在超时的时间范围内没有能获取到锁，则直接返回，不再继续等待。
  | 方法           | 说明                                                                    |
  | -------------- | ----------------------------------------------------------------------- |
  | try_lock_for   | 尝试锁定互斥，若在**指定的时限内**，互斥不可用则返回false, 否则返回true |
  | try_lock_until | 尝试锁定互斥，若到**指定时间点**，互斥不可用则返回false, 否则返回true。 |

- 可重入：recursive_mutex和recursive_timed_mutex的名称都带有recursive。可重入或者叫做可递归，是指在同一个线程中，同一把锁可以锁定多次。这就避免了一些不必要的死锁。
- 共享：shared_timed_mutex和shared_mutex提供了共享功能。对于这类互斥体，实际上是提供了两把锁：一把是共享锁，一把是互斥锁。一旦某个线程获取了互斥锁，任何其他线程都无法再获取互斥锁和共享锁；但是如果有某个线程获取到了共享锁，其他线程无法再获取到互斥锁，但是还有获取到共享锁。这里互斥锁的使用和其他的互斥体接口和功能一样。而共享锁可以同时被多个线程同时获取到（使用共享锁的接口见下面的表格）。共享锁通常用在读者写者模型上。
使用共享锁的接口如下：

  | 方法            | 说明                                   |
  | --------------- | -------------------------------------- |
  | lock_shared     | 获取互斥体的共享锁，如果无法获取则阻塞 |
  | try_lock_shared | 尝试获取共享锁，如果不可用，直接返回   |
  | unlock_shared   | 解锁共享锁                             |

接下来，我们就借助刚学到的mutex来改造我们的并发系统，改造后的程序如下：
```c++
// 07_mutex_lock.cpp

static const int MAX = 10e8;
static double sum = 0;

static mutex exclusive;

oid concurrent_worker(int min, int max) {
  double tmp_sum = 0; // 增加临时局部变量，减少锁次数
  for (int i = min; i <= max; i++) {
    tmp_sum += sqrt(i);
  }
  exclusive.lock(); // 在访问共享数据之前加锁。
  sum += tmp_sum;
  exclusive.unlock(); // 访问完成之后解锁。
}

void concurrent_task(int min, int max) {
  auto start_time = chrono::steady_clock::now();

  unsigned concurrent_count = thread::hardware_concurrency();
  cout << "hardware_concurrency: " << concurrent_count << endl;
  vector<thread> threads;
  min = 0;
  sum = 0;
  for (int t = 0; t < concurrent_count; t++) {
    int range = max / concurrent_count * (t + 1);
    threads.push_back(thread(concurrent_worker, min, range)); // 在多线程中使用带锁的版本。
    min = range + 1;
  }
  for (int i = 0; i < threads.size(); i++) {
    threads[i].join();
  }

  auto end_time = chrono::steady_clock::now();
  auto ms = chrono::duration_cast<chrono::milliseconds>(end_time - start_time).count();
  cout << "Concurrent task finish, " << ms << " ms consumed, Result: " << sum << endl;
}
```

执行之后结果输出如下：
```
hardware_concurrency: 16
Concurrent task finish, 74232 ms consumed, Result: 2.10819e+13
```
我们用锁的粒度（granularity）来描述锁的范围。细粒度（fine-grained）是指锁保护较小的范围，粗粒度（coarse-grained）是指锁保护较大的范围。出于性能的考虑，我们应该保证锁的粒度尽可能的细。并且，不应该在获取锁的范围内执行耗时的操作，例如执行IO。如果是耗时的运算，也应该尽可能的移到锁的外面。

## 通用互斥管理
主要API:
| API         | C++标准 | 说明                                   |
| ----------- | ------- | -------------------------------------- |
| lock_guard  | C++11   | 实现严格基于作用域的互斥体所有权包装器 |
| unique_lock | C++11   | 实现可移动的互斥体所有权包装器         |
| shared_lock | C++14   | 实现可移动的共享互斥体所有权封装器     |
| scoped_lock | C++17   | 用于多个互斥体的免死锁 RAII 封装器     |

| 锁定策略    | C++标准 | 说明                                              |
| ----------- | ------- | ------------------------------------------------- |
| defer_lock  | C++11   | 类型为 defer_lock_t，不获得互斥的所有权           |
| try_to_lock | C++11   | 类型为try_to_lock_t，尝试获得互斥的所有权而不阻塞 |
| adopt_lock  | C++11   | 类型为adopt_lock_t，假设调用方已拥有互斥的所有权  |

互斥体（mutex相关类）提供了对于资源的保护功能，但是手动的锁定（调用lock或者try_lock）和解锁（调用unlock）互斥体是要耗费比较大的精力的，我们需要精心考虑和设计代码才行。因为我们需要保证，在任何情况下，解锁要和加锁配对，因为假设出现一条路径导致获取锁之后没有正常释放，就会影响整个系统。如果考虑方法还可以会抛出异常，这样的代码写起来会很费劲。

鉴于这个原因，标准库就提供了上面的这些API。它们都使用了叫做RAII的编程技巧，来简化我们手动加锁和解锁的“体力活”。

请看下面的例子：
```c++
// https://en.cppreference.com/w/cpp/thread/lock_guard

#include <thread>
#include <mutex>
#include <iostream>
 
int g_i = 0;
std::mutex g_i_mutex;  // 全局的互斥体g_i_mutex用来保护全局变量g_i。
 
void safe_increment()
{
  // 这是一个设计为可以被多线程环境使用的方法。因此需要通过互斥体来进行保护。
  // 这里没有调用lock方法，而是直接使用lock_guard来锁定互斥体
  std::lock_guard<std::mutex> lock(g_i_mutex); 
  ++g_i;

  std::cout << std::this_thread::get_id() << ": " << g_i << '\n';
  // 在方法结束的时候，局部变量std::lock_guard<std::mutex> lock会被销毁，它对互斥体的锁定也就解除了
}
 
int main()
{
  std::cout << "main: " << g_i << '\n';
 
  std::thread t1(safe_increment); // 在多个线程中使用这个方法
  std::thread t2(safe_increment);
 
  t1.join();
  t2.join();
 
  std::cout << "main: " << g_i << '\n';
}
```

## 条件变量

条件变量
| API                       | C++标准 | 说明                                         |
| ------------------------- | ------- | -------------------------------------------- |
| condition_variable        | C++ 11  | 提供与 std::unique_lock 关联的条件变量       |
| condition_variable_any    | C++ 11  | 提供与任何锁类型关联的条件变量               |
| notify_all_at_thread_exit | C++ 11  | 安排到在此线程完全结束时对 notify_all 的调用 |
| cv_status                 | C++ 11  | 列出条件变量上定时等待的可能结果             |

这在很多业务中是很常见的一个需求：每一次操作都要正确执行，如果条件不满足就停下来等待，直到条件满足之后再继续。而不是直接返回。

条件变量提供了一个可以让多个线程间同步协作的功能。这对于生产者-消费者模型很有意义。在这个模型下：

- 生产者和消费者共享一个工作区。这个区间的大小是有限的。

- 生产者总是产生数据放入工作区中，当工作区满了。它就停下来等消费者消费一部分数据，然后继续工作。

- 消费者总是从工作区中拿出数据使用。当工作区中的数据全部被消费空了之后，它也会停下来等待生产者往工作区中放入新的数据。

从上面可以看到，无论是生产者还是消费者，当它们工作的条件不满足时，它们并不是直接报错返回，而是停下来等待，直到条件满足。

```c++
// 11_bank_transfer_wait_notify.cpp

class Account {
public:
  Account(string name, double money): mName(name), mMoney(money) {};

public:
  void changeMoney(double amount) {
    // 这里使用的是unique_lock，这是为了与条件变量相配合。因为条件变量会解锁和重新锁定互斥体
    unique_lock lock(mMoneyLock); 
    //这里是比较重要的一个地方：通过条件变量进行等待。此时：会通过后面的lambda表达式判断条件是否满足。
    // 如果满足则继续；如果不满足，则此处会解锁互斥体，并让当前线程等待。
    // 解锁这一点非常重要，因为只有这样，才能让其他线程获取互斥体
    mConditionVar.wait(lock, [this, amount] { 
      return mMoney + amount > 0; // 这里是条件变量等待的条件。
    });
    mMoney += amount;
    // 此处也很重要。当金额发生变动之后，我们需要通知所有在条件变量上等待的其他线程。
    // 此时所有调用wait线程都会再次唤醒，然后尝试获取锁（当然，只有一个能获取到）并再次判断条件是否满足。
    // 除了notify_all还有notify_one，它只通知一个等待的线程。wait和notify就构成了线程间互相协作的工具.    
    mConditionVar.notify_all(); 

  }

  string getName() {
    return mName;
  }

  double getMoney() {
    return mMoney;
  }

private:
  string mName;
  double mMoney;
  mutex mMoneyLock;
  condition_variable mConditionVar; // 这里声明了一个条件变量，用来在多个线程之间协作。
};
```
这几处改动说明如下：

请注意：wait和notify_all虽然是写在一个函数中的，但是在运行时它们是在多线程环境中执行的，因此对于这段代码，需要能够从不同线程的角度去思考代码的逻辑。这也是开发并发系统比较难的地方。

## future
| API           | C++标准 | 说明                                            |
| ------------- | ------- | ----------------------------------------------- |
| async         | C++11   | 异步运行一个函数，并返回保有其结果的std::future |
| future        | C++11   | 等待被异步设置的值                              |
| packaged_task | C++11   | 打包一个函数，存储其返回值以进行异步获取        |
| promise       | C++11   | 存储一个值以进行异步获取                        |
| shared_future | C++11   | 等待被异步设置的值（可能为其他 future 所引用）  |

这一小节中，我们来熟悉更多的可以在并发环境中使用的工具，它们都位于<future>头文件中。

### async
很多语言都提供了异步的机制。异步使得耗时的操作不影响当前主线程的执行流。

在C++11中，async便是完成这样的功能的。下面是一个代码示例：
```c
// 12_async_task.cpp

static const int MAX = 10e8;
static double sum = 0;

void worker(int min, int max) {
  for (int i = min; i <= max; i++) {
    sum += sqrt(i);
  }
}

int main() {
  sum = 0;
  auto f1 = async(worker, 0, MAX);
  cout << "Async task triggered" << endl;
  f1.wait();
  cout << "Async task finish, result: " << sum << endl << endl;
}
```
这仍然是我们之前熟悉的例子。这里有两个地方需要说明：
1. 这里以异步的方式启动了任务。它会返回一个future对象。future用来存储异步任务的执行结果，关于future我们在后面packaged_task的例子中再详细说明。在这个例子中我们仅仅用它来等待任务执行完成。
2. 此处是等待异步任务执行完成。

需要注意的是，默认情况下，async是启动一个新的线程，还是以同步的方式（不启动新的线程）运行任务，这一点标准是没有指定的，由具体的编译器决定。如果希望一定要以新的线程来异步执行任务，可以通过launch::async来明确说明。launch中有两个常量：

async：运行新线程，以异步执行任务。
deferred：调用方线程上第一次请求其结果时才执行任务，即惰性求值。
除了通过函数来指定异步任务，还可以lambda表达式的方式来指定。如下所示：
```c
// 12_async_task.cpp

int main() {

  double result = 0;
  cout << "Async task with lambda triggered, thread: " << this_thread::get_id() << endl;
  auto f2 = async(launch::async, [&result]() {
    cout << "Lambda task in thread: " << this_thread::get_id() << endl;
    for (int i = 0; i <= MAX; i++) {
      result += sqrt(i);
    }
  });
  f2.wait();
  cout << "Async task with lambda finish, result: " << result << endl << endl;
  
  return 0;
}
```
在上面这段代码中，我们使用一个lambda表达式来编写异步任务的逻辑，并通过launch::async明确指定要通过独立的线程来执行任务，同时我们打印出了线程的id。

这段代码输出如下：
```
Async task with lambda triggered, thread: 0x11290d5c0
Lambda task in thread: 0x700007aa1000
Async task with lambda finish, result: 2.10819e+13
```
对于面向对象编程来说，很多时候肯定希望以对象的方法来指定异步任务。下面是一个示例：
```c++
// 12_async_task.cpp

class Worker {
public:
  // 这里通过一个类来描述任务。这个类是对前面提到的任务的封装。它包含了任务的输入参数，和输出结果。
  Worker(int min, int max): mMin(min), mMax(max) {} 
  double work() { // work函数是任务的主体逻辑
    mResult = 0;
    for (int i = mMin; i <= mMax; i++) {
      mResult += sqrt(i);
    }
    return mResult;
  }
  double getResult() {
    return mResult;
  }

private:
  int mMin;
  int mMax;
  double mResult;
};

int main() {
  Worker w(0, MAX);
  cout << "Task in class triggered" << endl;
  // 通过async执行任务：这里指定了具体的任务函数以及相应的对象。
  // 请注意这里是&w，因此传递的是对象的指针。如果不写&将传入w对象的临时复制。
  auto f3 = async(&Worker::work, &w); 
  f3.wait();
  cout << "Task in class finish, result: " << w.getResult() << endl << endl;

  return 0;
}
```
