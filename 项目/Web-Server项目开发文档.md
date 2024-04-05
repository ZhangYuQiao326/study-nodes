2023/5/24

wbserverpdf密码：dmsxlwb0624 

防牛客论坛dmsxlluntan00

前端dmsxlqd437

薪资dmsxl666

八股文 ziyuansuixianglu

https://github.com/gaojingcome/WebServer 

![image-20230706195825453](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230706195825453.png)

![image-20230706195759845](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230706195759845.png)

![image-20230706191439545](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230706191439545.png)

# 简历写法

![image-20230619101447130](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230619101447130.png)

![image-20230619101717459](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230619101717459.png)

![image-20230619101849975](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230619101849975.png)

![image-20230619101935803](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230619101935803.png)

项目开始



![image-20230606091332324](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230606091332324.png)

#code

## pool

### threadpool.h

![image-20230525090216020](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230525090216020.png)

1. 头文件部分：
   - `#ifndef THREADPOOL_H` 和 `#define THREADPOOL_H`：这是常见的头文件保护宏，用于防止重复包含头文件。
   - `#include` 命令：包含了所需的头文件，包括 `<mutex>`、`<condition_variable>`、`<queue>`、`<thread>` 和 `<functional>`。这些头文件提供了线程、互斥锁、条件变量和函数对象等所需的功能。
   
2. 类定义部分：
   - `ThreadPool` 类：这是线程池的主要类定义。
     
     - 构造函数 `explicit ThreadPool(size_t threadCount = 8)`：创建线程池对象的构造函数。它接受一个可选参数 `threadCount`，用于指定线程池中的线程数量，默认为 8
     
     1. `explicit`: 这是一个关键字，用于指示构造函数是显式的。它禁止隐式转换和复制初始化，要求在创建对象时明确调用构造函数。
     2. `ThreadPool(size_t threadCount = 8)`: 这是构造函数的声明，接受一个 `size_t` 类型的参数 `threadCount`，并具有默认参数值 8。这意味着可以使用默认参数值调用构造函数，例如 `ThreadPool pool;`。
     3. `pool_(std::make_shared<Pool>())`: 这是构造函数的初始化列表部分，对 `pool_` 成员变量进行初始化。在这里，使用 `std::make_shared<Pool>()` 创建了一个 `Pool` 对象，并将其作为参数传递给 `std::shared_ptr` 的构造函数。然后，该 `std::shared_ptr` 对象将被用来初始化 `pool_` 成员变量。
     
     总结起来，这段代码定义了一个具有默认参数的 `ThreadPool` 类构造函数。它使用 `std::make_shared` 创建一个 `Pool` 对象，并将其封装在一个 `std::shared_ptr` 中，然后使用该 `std::shared_ptr` 初始化 `pool_` 成员变量。这样，在创建 `ThreadPool` 对象时，可以选择传递 `threadCount` 参数来指定线程池的大小，也可以使用默认参数值。
     
     - 析构函数 `~ThreadPool()`：线程池对象的析构函数。它将线程池标记为关闭状态，并通过条件变量的 `notify_all()` 方法通知所有线程退出。
     - `AddTask()` 成员函数：用于向线程池中添加任务。它接受一个可调用对象 `task`，将任务添加到任务队列中，并通过条件变量的 `notify_one()` 方法通知一个线程执行任务。
   - 内部结构体 `Pool`：打包线程池需要用到的所有成员变量
     - `std::mutex mtx`：互斥锁，用于保护共享数据的访问。
     - `std::condition_variable cond`：条件变量，用于线程的等待和唤醒。
     - `bool isClosed`：标志变量，表示线程池是否关闭。
     - `std::queue<std::function<void()>> tasks`：任务队列，用于存储待执行的任务。
   - `std::shared_ptr<Pool> pool_`：线程池对象的共享指针。它将 `Pool` 结构体包装为一个共享资源，使多个线程可以共享线程池对象。
   
3. 构造函数：
   - 构造函数通过接受线程数量参数，并使用 `std::make_shared<Pool>()` 创建 `Pool` 结构体的共享指针。
   
   1. `explicit`: 这是一个关键字，用于指示构造函数是显式的。它禁止隐式转换和复制初始化，要求在创建对象时明确调用构造函数。
   2. `ThreadPool(size_t threadCount = 8)`: 这是构造函数的声明，接受一个 `size_t` 类型的参数 `threadCount`，并具有默认参数值 8。这意味着可以使用默认参数值调用构造函数，例如 `ThreadPool pool;`。
   3. `pool_(std::make_shared<Pool>())`: 这是构造函数的初始化列表部分，对 `pool_` 成员变量进行初始化。在这里，使用 `std::make_shared<Pool>()` 创建了一个 `Pool` 对象，并将其作为参数传递给 `std::shared_ptr` 的构造函数。然后，该 `std::shared_ptr` 对象将被用来初始化 `pool_` 成员变量。
   
   - `assert(threadCount > 0);` 用于在 `ThreadPool` 构造函数中检查传递的线程数参数是否大于 0。如果不满足条件，将触发断言失败，终止程序的执行，并输出相应的错误消息。
   - 然后，使用 `std::thread` 创建指定数量的子线程，并在每个子线程中执行一个匿名函数。
   - 匿名函数中，首先获得互斥锁的独占锁（`std::unique_lock<std::mutex> locker(pool->mtx)`）。
   - 在循环中，判断任务队列是否为空，如果不为空，则取出队首任务并执行，然后继续循环。
   - 如果任务队列为空且线程池未关闭，则使用条件变量的 `wait()` 方法等待条件满足（有新任务到来或线程池关闭）。
   - 如果线程池关闭，则跳出循环，子线程结束。
   
   4.析构函数：

- 析构函数首先检查线程池对象是否存在（`static_cast<bool>(pool_)`），如果存在则执行析构操作。
- 首先使用互斥锁的 `lock_guard` 对互斥锁进行上锁，确保线程安全。
- 将线程池的关闭标志设为 true（`pool_->isClosed = true`），表示线程池要关闭。
- 最后，通过条件变量的 `notify_all()` 方法通知所有等待的线程，以便它们退出等待状态。

5. AddTask()成员函数：

- `AddTask()` 函数用于向线程池中添加任务。
- 首先使用互斥锁的 `lock_guard` 对互斥锁进行上锁，确保线程安全。
- 将任务（通过右值引用）加入到任务队列中（`pool_->tasks.emplace(std::forward<F>(task))`）。
- 最后，通过条件变量的 `notify_one()` 方法通知一个等待的线程，以便它执行任务。

整体流程如下：

1. 在主函数中创建 `ThreadPool` 对象，并指定线程数量（默认为 8）。
2. 调用 `AddTask()` 函数添加需要执行的任务到线程池中。
3. 线程池的子线程会循环等待任务的到来。
4. 当调用 `AddTask()` 函数添加任务时，一个子线程会被唤醒并执行任务。
5. 线程池关闭时，子线程会退出循环并结束执行。
6. 在程序结束时，线程池对象的析构函数会自动被调用，释放资源。

这个线程池的实现利用了互斥锁和条件变量实现了线程之间的同步和协调，确保任务的安全执行。它可以用于管理多个并发任务，提高程序的并发性和性能。通过灵活的任务添加和自动管理线程的方式，简化了多线程编程的复杂性。

# main.cpp



# server

## webserver.cpp

* webserver

```cpp
// 1、获取当前工作目录绝对路径，nullptr自动分配缓存区，大小256
srcDir_ = getcwd(nullptr, 256);
assert(srcDir_); // 确保路径获取成功，不成功srcDir为nullptr
strncat(srcDir_, "/resources/", 16); // 拼接到末尾，最大16个字符

// 2、初始化客户端连接数、工作目录（httpconn下）
HttpConn::userCount = 0;
HttpConn::srcDir = srcDir_;
SqlConnPool::Instance()->Init("localhost", sqlPort, sqlUser, sqlPwd, dbName, connPoolNum);
```

![image-20230601095903016](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230601095903016.png)

![image-20230605162109083](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230605162109083.png)

# log

* 利用单例模式与阻塞队列实现异步的日志系统，记录服务器的运行状态

## log.h

一、write

```cpp
// 默认日志是打开的
void Log::write(int level, const char *format, ...)
{
    // 1、获取当前时间，保存在t指向的结构体中
    struct timeval now = {0, 0};
    gettimeofday(&now, nullptr);
    time_t tSec = now.tv_sec;
    struct tm *sysTime = localtime(&tSec);
    struct tm t = *sysTime;
    
    va_list vaList;
	
    // 2、检查是否需要切换日志文件，需要则创建新文件
    //（当前日期与上次写入日期是否相同/行数非0且达到了最大行数的倍数）
    if (toDay_ != t.tm_mday || (lineCount_ && (lineCount_ % MAX_LINES == 0)))
    {
        unique_lock<mutex> locker(mtx_);
        locker.unlock();

        char newFile[LOG_NAME_LEN]; // 存放新的文件名
        char tail[36] = {0}; 		// 存储日期的部分字符串
        // 写入当前的时间
        snprintf(tail, 36, "%04d_%02d_%02d", t.tm_year + 1900, t.tm_mon + 1, t.tm_mday);
		// 
        if (toDay_ != t.tm_mday)
        {
            snprintf(newFile, LOG_NAME_LEN - 72, "%s/%s%s", path_, tail, suffix_);
            toDay_ = t.tm_mday;
            lineCount_ = 0;
        }
        else
        // 日志文件已满，修改文件名为第几个文件
        {
            snprintf(newFile, LOG_NAME_LEN - 72, "%s/%s-%d%s", path_, tail, (lineCount_ / MAX_LINES), suffix_);
        }
		
        
        // 上锁、关闭当前日志，打开新建的日志
        locker.lock();
        flush();
        fclose(fp_);
        fp_ = fopen(newFile, "a");
        assert(fp_ != nullptr);
    }

    {
        unique_lock<mutex> locker(mtx_);
        lineCount_++;
        int n = snprintf(buff_.BeginWrite(), 128, "%d-%02d-%02d %02d:%02d:%02d.%06ld ",
                         t.tm_year + 1900, t.tm_mon + 1, t.tm_mday,
                         t.tm_hour, t.tm_min, t.tm_sec, now.tv_usec);

        buff_.HasWritten(n);
        AppendLogLevelTitle_(level);

        va_start(vaList, format);
        int m = vsnprintf(buff_.BeginWrite(), buff_.WritableBytes(), format, vaList);
        va_end(vaList);

        buff_.HasWritten(m);
        buff_.Append("\n\0", 2);

        if (isAsync_ && deque_ && !deque_->full())
        {
            deque_->push_back(buff_.RetrieveAllToStr());
        }
        else
        {
            fputs(buff_.Peek(), fp_);
        }
        buff_.RetrieveAll();
    }
}
```



思考：

正常 字符串与文件的交互

字符串-> 缓冲区 -> 文件



加入异步的功能

字符串-> 缓冲区 -> 阻塞队列 -> 文件





## blockDeque.h

一、自定义封装的deque容器（本质是一个模板类）----> 共享文件，至此一个，日志线程间同步访问

二、功能：

* 读写操作，均需mutex，其中，阻塞队列的加入和弹出，是一个生产者消费者模型
* 重载操作：front、back、size、clear、push_back、push_front、empty、capacity、pop（阻塞、从队头弹出）
* 自定义操作：close、flush、full

三、技术点

* 生产者-消费者模型

  1. push_back、push_front:

  productor.wait   consumer.notify_one（执行pop）

  

  2. pop:

  consumer.wait   productor.notify_one(执行push)

  

  3. flush:

  consumer.notify_one (执行pop)

  

# buffer

## buffer.cpp

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230711180145693.png" alt="image-20230711180145693" style="zoom:67%;" />

一、对外功能：

1. 从文件中读取数据
   * 双缓冲区读取（buffer+临时缓冲区），将临时缓冲区的内容放入到新开辟的缓冲区中
   * <img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230711180244666.png" alt="image-20230711180244666" style="zoom:67%;" />
   
   ````cpp
   //`iovec` 是一个结构体，定义在 `<sys/uio.h>` 头文件中，用于进行分散（Scatter）和聚集（Gather）操作。它用于描述一组数据块的地址和长度。
   
   //`iovec` 结构体的定义如下：
   
   ```c
   struct iovec {
       void *iov_base; // 数据块的起始地址
       size_t iov_len; // 数据块的长度
   };
   ```
   
   `iovec` 结构体包含两个字段：
   
   1. `iov_base`：指向数据块的起始地址的指针。数据块可以是任何类型的内存缓冲区，例如字符数组、字节流等。
   
   2. `iov_len`：数据块的长度，以字节为单位。它表示数据块的大小或可读写的字节数。
   
   `iovec` 结构体通常用于与系统调用函数一起使用，例如 `readv()` 和 `writev()`。这些系统调用函数可以一次性读取或写入多个数据块，而不需要进行多次的系统调用，从而提高效率。对于读取操作，每个数据块中的数据将被依次填充；对于写入操作，每个数据块中的数据将被依次写入到目标缓冲区。
   
   通过使用 `iovec` 结构体数组，可以描述多个数据块的地址和长度，从而进行分散读取和聚集写入的操作。这对于处理网络 I/O、文件 I/O 和内存映射等场景非常有用。
   ````
   
2. 向文件中写入数据
   * 将一个缓冲区的内容写到文件中

二、对内功能：

1. 利用标准库容器vector封装char数组，实现自动增长的缓冲区
   * 代码中的`Buffer`类使用了一个`std::vector<char>`对象来存储数据，即`buffer_`。该`buffer_`对象可以自动扩展以容纳更多的数据。

```cpp
std::vector<char> buffer_;
void Buffer::MakeSpace_(size_t len)
{
    if (WritableBytes() + PrependableBytes() < len)
    {
        buffer_.resize(writePos_ + len + 1); // 已读+未写 < len,增加vector长度，+1为字符串结尾空字符\0
    }
    else
    {
        size_t readable = ReadableBytes();
        // 将未读的数据，往前移动，覆盖掉前面已经读过的数据
        std::copy(BeginPtr_() + readPos_, BeginPtr_() + writePos_, BeginPtr_());
        readPos_ = 0;
        writePos_ = readPos_ + readable;
        assert(readable == ReadableBytes());
    }
}
```

三、缺陷

实现的缓冲区是可以动态增长的，但并没有提供回收或缩小容量的功能。在当前的实现中，一旦缓冲区扩大，它将一直保持相同的大小，即使在后续的操作中数据被读取完毕或不再需要那么大的缓冲区。

如果需要回收或缩小缓冲区的容量，可以在适当的时机添加相应的逻辑来实现。以下是一些可能的方法：

1. 手动收缩：在确定不再需要较大容量的缓冲区时，可以显式调用`resize()`函数将缓冲区的大小缩小到实际需要的大小。例如，当缓冲区中的数据被处理完毕或传输完成后，可以根据需要调用`resize()`函数将缓冲区的大小调整为实际需要的大小。
2. 自动回收：可以根据一些策略或条件自动回收缓冲区的容量。例如，可以根据缓冲区中的数据量、空闲空间的比例或其他业务需求来判断是否需要回收容量。当满足一定条件时，自动将缓冲区的容量缩小到合适的大小。

请注意，缓冲区的容量回收可能会带来额外的开销，因为它涉及到内存的重新分配和数据的复制。因此，在设计和实现回收功能时，需要权衡资源利用和性能开销之间的平衡。

根据具体的使用场景和需求，可以选择适当的方法来回收或调整缓冲区的容量，以确保资源的有效利用和系统的性能。



# timer

## heaptimer.cpp

`ref_` 是 `HeapTimer` 类中的一个成员变量，它的类型是 `std::unordered_map<int, size_t>`。

它的作用是用于快速查找给定节点标识（id）在堆（`heap_`）中的下表位置（`size_t` 类型的值）。

在定时器的实现中，通常需要对定时器进行插入、删除和调整等操作。为了能够高效地定位到特定的定时器节点，可以使用 `ref_` 容器来建立节点标识和节点在堆中位置的映射关系。

具体地说，当向堆中插入一个新的定时器节点时，会在 `ref_` 容器中添加一个键值对，其中键是节点的标识，值是节点在堆中的索引。这样，通过访问 `ref_` 容器，可以根据节点的标识快速找到节点在堆中的位置，从而进行相应的操作。

这种设计可以提高定时器的操作效率，避免了线性搜索整个堆来查找特定节点的开销，而是通过 `ref_` 容器的快速查找特性，在常数时间复杂度内定位到节点的位置。



* siftdown_

这段代码是 `HeapTimer` 类的一个私有成员函数 `siftdown_` 的实现，用于向下调整堆中的元素，以维护堆的性质。

函数的主要目的是将位于索引 `index` 的元素向下移动，直到它满足堆的性质（通常是小顶堆，即父节点的值小于等于子节点的值）或达到堆底。

代码解释如下：

1. `assert(index >= 0 && index < heap_.size())` 和 `assert(n >= 0 && n <= heap_.size())`：使用断言确保索引 `index` 和 `n` 在合法范围内，即不小于 0，且小于等于堆的大小。

2. `size_t i = index;`：将变量 `i` 初始化为 `index`，表示当前节点的索引。

3. `size_t j = i * 2 + 1;`：计算当前节点的左子节点的索引。在通常的 0-based 数组表示中，左子节点的索引为 `i * 2 + 1`。

4. `while (j < n)`：进入一个循环，条件为左子节点的索引小于 `n`，即仍然存在子节点。

5. `if (j + 1 < n && heap_[j + 1] < heap_[j]) j++;`：比较左子节点和右子节点的值，如果右子节点的值小于左子节点的值，则将 `j` 更新为右子节点的索引。

6. `if (heap_[i] < heap_[j]) break;`：检查当前节点和子节点的值的大小关系。如果当前节点的值小于子节点的值，则说明满足堆的性质，退出循环。

7. `SwapNode_(i, j);`：交换当前节点和子节点的位置，以保持堆的性质。

8. `i = j; j = i * 2 + 1;`：更新当前节点的索引为子节点的索引，继续向下移动。

通过循环迭代，函数将当前节点不断与其较小的子节点进行比较并交换，直到满足堆的性质或到达堆底。这样可以确保堆的局部性质被维护，以实现堆数据结构的有效性和有序性。

最后，函数返回一个布尔值，表示是否发生了节点的移动（即节点是否向下移动了）。这个返回值在堆的调整过程中通常用于判断是否需要继续进行后续的调整操作。





* add

这段代码是 `HeapTimer` 类的一个公有成员函数 `add` 的实现，用于向堆中添加定时器节点。

函数的主要功能是根据定时器的标识 `id` 进行判断，如果堆中不存在相应的定时器节点，则将新的定时器节点插入堆中；如果已存在相应的定时器节点，则更新该节点的超时时间和回调函数，并进行堆的调整。

代码解释如下：

1. `assert(id >= 0)`: 使用断言确保定时器的标识 `id` 大于等于 0。

2. `if (ref_.count(id) == 0)`: 检查堆中是否存在标识为 `id` 的定时器节点。使用 `ref_.count(id)` 可以判断 `ref_` 容器中是否包含键为 `id` 的元素，返回值为 0 表示不存在，非 0 表示存在。

3. 如果不存在相应的定时器节点，则执行以下操作：
   - `i = heap_.size()`: 获取新节点在堆中的索引，即当前堆的大小。
   - `ref_[id] = i`: 在 `ref_` 容器中添加一个键值对，将定时器的标识 `id` 映射为新节点的索引 `i`。
   - `heap_.push_back({id, Clock::now() + MS(timeout), cb})`: 向堆中插入一个新的定时器节点，包括标识 `id`、超时时间（当前时间加上 `timeout` 毫秒）和回调函数 `cb`。
   - `siftup_(i)`: 对插入的新节点进行向上调整，以维护堆的性质。

4. 如果已存在相应的定时器节点，则执行以下操作：
   - `i = ref_[id]`: 获取已存在定时器节点在堆中的索引。
   - `heap_[i].expires = Clock::now() + MS(timeout)`: 更新该节点的超时时间为当前时间加上 `timeout` 毫秒。
   - `heap_[i].cb = cb`: 更新该节点的回调函数为给定的回调函数 `cb`。
   - `if (!siftdown_(i, heap_.size())) siftup_(i)`: 对已存在的节点进行向下调整，以维护堆的性质。如果节点没有向下移动，则进行向上调整。

通过以上操作，函数可以将新的定时器节点插入堆中，或者更新已存在的定时器节点的超时时间和回调函数，并保持堆的性质。





今日未解决：

阻塞队列 -> 文件的流程

flush的作用

Init

日志功能打开、设置级别

使用异步写、新建阻塞队列、写日志线程（从阻塞队列往文件中写-> AsyncWrite_()  -> pop(str) 阻塞等待唤醒

创建日志文件

清空缓存区（内容已经写入队列）

队列内容写入文件（flush）、清空队列

打开日志文件（fp）



write:

是否创建新文件？ fp更换

日志写入缓冲区 -> 阻塞队列（push_back）-> 唤醒子线程执行（pop）写入文件



# 服务器框架

## 1、主线程与工作线程的分工

服务器端为了能流畅处理多个客户端链接，一般在某个线程A里面accept新的客户端连接并生成新连接的socket fd，然后将这些新连接的socketfd给另外开的数个工作线程B1、B2、B3、B4，这些工作线程处理这些新连接上的网络IO事件（即收发数据），同时，还处理系统中的另外一些事务。这里我们将线程A称为主线程，B1、B2、B3、B4等称为工作线程。工作线程的代码框架一般如下：

```
while (!m_bQuit) 
{  
    epoll_or_select_func();  
  
    handle_io_events();  
  
    handle_other_things();
}  
```

在epoll_or_select_func()中通过select()或者poll/epoll()去检测socket fd上的io事件，若存在这些事件则下一步handle_io_events()来处理这些事件（收发数据），做完之后可能还要做一些系统其他的任务，即调用handle_other_things()。

这样做有三个好处：

1. ==线程A只需要处理新连接的到来即可，不用处理网络IO事件==。由于网络IO事件处理一般相对比较慢，如果在线程A里面既处理新连接又处理网络IO，则可能由于线程忙于处理IO事件，而无法及时处理客户端的新连接，这是很不好的。

2. 线程A接收的新连接，可以==根据一定的负载均衡原则将新的socket fd分配给工作线程==。常用的算法，比如round robin，即轮询机制，即，假设不考虑中途有连接断开的情况，一个新连接来了分配给B1，又来一个分配给B2，再来一个分配给B3，再来一个分配给B4。如此反复，也就是说线程A记录了各个工作线程上的socket fd数量，这样可以最大化地来平衡资源，避免一些工作线程“忙死”，另外一些工作线程“闲死”的现象。

3. 即使工作线程不满载的情况下，也可以让工作线程做其他的事情。比如现在有四个工作线程，但只有三个连接。那么线程B4就可以在handle_other_thing()做一些其他事情。



下面讨论处理other事件的效率问题：

在上述while循环里面，epoll_or_selec_func()中的epoll_wait/poll/select等函数一般设置了一个超时时间。

两种情况，处理events事件均能即使处理，但是处理ohter事件效率低

如果设置**超时时间==0**，那么在没有任何网络IO时间和其他任务处理的情况下，这些工作线程实际上会空转，白白地浪费cpu时间片。

如果设置的**超时时间>0**，在没有网络IO时间的情况，epoll_wait/poll/select仍然要等待指定时间才能返回，导致handle_other_thing()不能及时执行，影响其他任务不能及时处理，也就是说其他任务一旦产生，其处理起来具有一定的延时性。

其实我们想达到的效果是，如果没有网络IO时间和其他任务要处理，那么这些工作线程最好直接挂起而不是空转；如果有其他任务要处理，这些工作线程要立刻能处理这些任务而不是在epoll_wait/poll/selec挂起指定时间后才开始处理这些任务。

* 解决方案

给监听树绑定一个默认的fd，这个fd被称为唤醒fd。

当我们需要处理其他任务的时候，向这个唤醒fd上随便写入1个字节的，这样这个fd立即就变成可读的了，epoll_wait()/poll()/select()函数立即被唤醒，并返回，接下来马上就能执行handle_other_thing()，其他任务得到处理。

反之，没有其他任务也没有网络IO事件时，epoll_or_select_func()就挂在那里什么也不做。

这个唤醒fd，在linux平台上可以通过以下几种方法实现：

1. 管道pipe，创建一个管道，将管道绑定到epoll_fd上。需要时，向管道一端写入一个字节，工作线程立即被唤醒。

2. linux 2.6新增的eventfd：

```
int eventfd(unsigned int initval, int flags); 
```

步骤也是一样，将生成的eventfd绑定到epoll_fd上。需要时，向这个eventfd上写入一个字节，工作线程立即被唤醒。



3. 第三种方法最方便。即linux特有的==socketpair==，将处理other事件  --->  处理event事件

```
int socketpair(int domain, int type, int protocol, int sv[2]);

domain：套接字的协议族，通常设置为AF_UNIX表示本地套接字。
type：套接字的类型，通常设置为SOCK_STREAM表示面向连接的套接字。
protocol：套接字的协议，通常设置为0，表示使用默认协议。
sv[2]：用于存储套接字文件描述符的数组，创建成功后，sv[0]和sv[1]分别表示套接字对的两端。
// 使用
int pair[2];
socketpair(AF_UNIX, SOCK_STREAM, 0, pair)
```



调用这个函数返回的两个socket句柄就是sv[0]，和sv[1]，在一个其中任何一个写入字节，在另外一个收取字节。

将收取的字节的pair[0]添加到监听树。

需要时，向另外一个写入的socket上写入一个字节，wait立即返回，继而执行other工作。

```cpp
// 例子
#include <sys/types.h>
#include <sys/socket.h>
#include <sys/epoll.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>

#define MAX_EVENTS 10

int main() {
    int epoll_fd, sockpair[2];
    struct epoll_event events[MAX_EVENTS];

    // 创建 epoll 实例
    epoll_fd = epoll_create1(0); // 根节点
    if (epoll_fd == -1) {
        perror("epoll_create1");
        exit(EXIT_FAILURE);
    }

    // 创建 socketpair
    if (socketpair(AF_UNIX, SOCK_STREAM, 0, sockpair) == -1) {
        perror("socketpair");
        exit(EXIT_FAILURE);
    }

    // 将 sockpair[1] 添加到 epoll 实例中
    struct epoll_event event;
    event.events = EPOLLIN;
    event.data.fd = sockpair[1];
    if (epoll_ctl(epoll_fd, EPOLL_CTL_ADD, sockpair[1], &event) == -1) { // 阻塞等待
        perror("epoll_ctl");
        exit(EXIT_FAILURE);
    }

    // 模拟需要唤醒 epoll_wait 的情况
    char wake_byte = 'W';
    if (write(sockpair[0], &wake_byte, sizeof(char)) == -1) { // 写入数据，wait监听到读事件，立刻返回
        perror("write");
        exit(EXIT_FAILURE);
    }

    // 使用 epoll_wait 等待事件
    int num_events = epoll_wait(epoll_fd, events, MAX_EVENTS, -1);
    if (num_events == -1) {
        perror("epoll_wait");
        exit(EXIT_FAILURE);
    }

    printf("Number of events: %d\n", num_events);

    // 处理事件
    for (int i = 0; i < num_events; i++) {
        if (events[i].data.fd == sockpair[1]) {
            printf("Wakeup event received.\n");
            // 可以在此处处理其他事件
        }
    }

    // 关闭套接字和 epoll 实例
    close(sockpair[0]);
    close(sockpair[1]);
    close(epoll_fd);

    return 0;
}

```

## 2、线程池

![image-20230706190926432](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230706190926432.png)

```cpp
// 头文件防卫式声明
#ifndef THREADPOOL_H
#define THREADPOOL_H

#include <mutex>
#include <condition_variable>
#include <queue>
#include <thread>
#include <functional>
class ThreadPool
{
public:
    // 1、构造多个线程
    explicit ThreadPool(size_t threadCount = 8) : pool_(std::make_shared<Pool>()) // 创建pool结构体的共享指针
    {
        // 检查传入的threadCount是否合法，不合法程序终止
        assert(threadCount > 0);

        // 创建指定数量的子线程，并在每个子线程中执行一个匿名函数。
        for (size_t i = 0; i < threadCount; i++)
        {
            std::thread([pool = pool_]
                        {
                    std::unique_lock<std::mutex> locker(pool->mtx); // 上锁开始访问队列

                    // 每个线程循环从队列里取数据
                    while(true) {
                        if(pool->isClosed) break;  // 若线程池已关闭，则终止该线程任务

                        // 所有线程在无任务下阻塞在这一步
                        else if(pool->tasks.empty()) pool->cond.wait(locker); // // 任务队列为空且线程池未关闭，阻塞等有新任务到来或线程池关闭
                        else{
                            // 队列不为空，取出任务并执行
                            auto task = std::move(pool->tasks.front());
                            pool->tasks.pop();

                            // 解锁，执行任务
                            locker.unlock();
                            task();  

                            // 重新上上锁继续取任务
                            locker.lock();

                        }
                        
                        // if(!pool->tasks.empty()) {
                        //     auto task = std::move(pool->tasks.front());
                        //     pool->tasks.pop();
                        //     locker.unlock(); // 立即解锁
                        //     task();
                        //     locker.lock(); //上锁
                        // } 
                        // else if(pool->isClosed) break; 
                        // else pool->cond.wait(locker); 
                    } })
                .detach(); // 线程分离
        }
    }

    ThreadPool() = default; // 默认构造函数

    ThreadPool(ThreadPool &&) = default; // 移动构造函数

    // 2、析构函数
    ~ThreadPool()
    {
        if (static_cast<bool>(pool_)) // 类型转换为bool型，判断pool_是否为空，若不为空，则
        {
            {
                std::lock_guard<std::mutex> locker(pool_->mtx); // 上锁
                pool_->isClosed = true;                         // 将线程池标记为关闭状态
            }
            pool_->cond.notify_all(); // 通过条件变量通知所有线程退出
        }
    }

    template <class F>
    // 3、向线程池中添加任务
    // 4、task将任务添加到任务队列中
    void AddTask(F &&task)
    {
        {
            std::lock_guard<std::mutex> locker(pool_->mtx); // 上锁
            pool_->tasks.emplace(std::forward<F>(task));    // 将任务（通过右值引用）加入到任务队列中
        }
        pool_->cond.notify_one(); // 唤醒一个等待的线程，执行任务
    }

private:
    // 定义结构体（打包需要用到的所有成员变量）
    struct Pool
    {
        std::mutex mtx;                          // 互斥锁
        std::condition_variable cond;            // 条件变量
        bool isClosed;                           // 线程池状态
        std::queue<std::function<void()>> tasks; // 队列（保存任务），std::function<void()>一个通用函数包装器，可以用于存储任意可调用对象（函数、函数指针、lambda表达式等），该对象接受无参数并返回 void
    };
    // 属性1
    std::shared_ptr<Pool> pool_; // 声明指针指向pool
};

#endif // THREADPOOL_H
```

未解决问题：

tasks队列，存储的task本质上是函数，执行该任务，task（），执行这个函数就可以了

技术点：

* 单例模式
* 互斥锁、条件变量、生产者消费者模型
* 



计算器：540+1080+580=2200

住：612+1080+550 = 2242 15 + 250 = 85

房租：1000-500=500 15 +15



