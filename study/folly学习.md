

Folly库包含多个模块，每个模块都专注于不同的功能，例如：

1. **并发和同步：** Folly提供了各种并发原语，如锁、信号量、原子操作等，以帮助开发人员编写高效的多线程和并发代码。

2. **字符串处理：** Folly包含了一系列高性能的字符串处理工具，例如字符串格式化、字符串拆分、字符串查找等，能够在处理大量字符串数据时提供较好的性能。

3. **内存管理：** Folly提供了内存分配器、内存池和其他内存管理工具，旨在减少内存分配和释放的开销，从而提高应用程序的性能。

4. **IO工具：** Folly库包含了一些用于文件IO、网络IO等的工具，能够简化IO操作并提高性能。

5. **数据结构：** Folly实现了一些特定的数据结构，例如带有内存分配器支持的容器，可以在特定场景下提供更好的性能。

学习FBVector

https://github.com/facebook/folly/blob/main/folly/FBVector.h

参考资料：https://www.zhihu.com/column/c_1327270420144570368

# 迭代器

## 概念

迭代器（Iterators）和指针（Pointers）是两种不同的概念，但它们在某些方面具有相似之处。以下是它们之间的主要区别和共同点：

**区别：**

1. **概念**：
   - 迭代器是一种用于访问容器（如数组、列表、向量等）中元素的抽象概念。它们提供了一种通用的方式来遍历容器的元素，而不依赖于容器的具体类型。
   - 指针是一个指向内存地址的变量，通常用于直接访问内存中的数据。指针不仅可以指向数组元素，还可以指向任何内存位置。

2. **容器依赖**：
   - 迭代器是容器的一部分，由容器提供或支持。不同类型的容器可能提供不同类型的迭代器。
   - 指针与容器无关，可以指向任何内存地址，包括不受容器管理的内存。

3. **操作**：
   - 迭代器通常提供容器元素的安全访问，并且可以支持容器特定的操作，如插入、删除等。
   - 指针可以进行各种低级操作，包括对内存地址的算术运算，但不提供容器元素的高级访问和操作。

**共同点：**

1. **遍历容器元素**：
   - 迭代器和指针都用于遍历容器元素或访问内存中的数据。

2. **指向数据**：
   - 迭代器指向容器中的数据元素，而指针指向内存中的数据。

3. **递增和递减**：
   - 迭代器和指针都支持递增（`++`）和递减（`--`）操作，以访问下一个或前一个元素。

4. **解引用**：
   - 迭代器和指针都支持解引用操作，以访问它们指向的元素的值。使用 `*` 运算符进行解引用。

总的来说，迭代器是一种高级抽象，用于安全地遍历容器元素，而指针是一种更底层的概念，用于直接操作内存中的数据。在编程中，您通常会使用迭代器来访问容器元素，而使用指针来进行底层的内存操作。但是，需要谨慎使用指针，以避免内存错误和安全问题

## 例子

在C++中，要定义一个迭代器，通常需要遵循以下步骤：

1. **包括必要的头文件**：首先，确保包括了与迭代器相关的头文件，如 `<iterator>`。

2. **选择适当的迭代器类别**：C++标准库提供了不同类别的迭代器，包括输入迭代器、输出迭代器、正向迭代器、双向迭代器和随机访问迭代器。选择与你的数据结构或容器相匹配的迭代器类别。

3. **定义迭代器类**：创建一个类，并将其命名为你的迭代器类名。该类通常应该包含以下成员函数和类型别名：
   - `using iterator_category = YourCategory;`：指定迭代器的类别，将 `YourCategory` 替换为所选的迭代器类别。
   - `using value_type = YourValueType;`：指定迭代器所指向元素的数据类型，将 `YourValueType` 替换为实际的数据类型。
   - `using reference = YourReferenceType;`：指定迭代器的引用类型，将 `YourReferenceType` 替换为实际的引用类型。
   - `using pointer = YourPointerType;`：指定迭代器的指针类型，将 `YourPointerType` 替换为实际的指针类型。
   - `using difference_type = YourDifferenceType;`：指定迭代器之间的差异类型，将 `YourDifferenceType` 替换为实际的差异类型。
   - `YourIterator();`：默认构造函数。
   - `YourIterator(const YourIterator& other);`：拷贝构造函数。
   - `YourIterator& operator++();`：前置递增运算符，用于移动迭代器到下一个元素。
   - `YourIterator operator++(int);`：后置递增运算符，返回当前位置的迭代器，并将迭代器移动到下一个元素。
   - `YourReferenceType operator*();`：解引用运算符，用于获取当前位置的元素引用。
   - `bool operator==(const YourIterator& other) const;`：相等运算符，用于比较两个迭代器是否相等。
   - `bool operator!=(const YourIterator& other) const;`：不等运算符，用于比较两个迭代器是否不等。

4. **实现迭代器成员函数**：实现上述成员函数的定义，确保它们按照所选迭代器类别的要求进行操作。

5. **根据需要添加其他成员函数**：根据你的需求，你可以添加其他成员函数，以便迭代器支持特定的操作。

6. **测试和验证**：确保你的迭代器类能够正确地在你的数据结构或容器中工作。可以编写测试代码来验证迭代器的行为是否符合预期。

以下是一个示例，展示了如何定义一个简单的正向迭代器：

```cpp
#include <iterator>

// 正向迭代器类
class MyIterator {
public:
    // 1 回答五个问题
    using iterator_category = std::forward_iterator_tag;
    using value_type = int;
    using reference = int&;
    using pointer = int*;
    using difference_type = std::ptrdiff_t;

    // 2 重载操作符
    MyIterator(); // 默认构造函数
    MyIterator(const MyIterator& other); // 拷贝构造函数
    MyIterator& operator++(); // 前置递增运算符
    MyIterator operator++(int); // 后置递增运算符
    int& operator*(); // 解引用运算符
    bool operator==(const MyIterator& other) const; // 相等运算符
    bool operator!=(const MyIterator& other) const; // 不等运算符

private:
    // 3 本体指针
    int* ptr; // 迭代器的内部指针
};

// 实现迭代器成员函数
MyIterator::MyIterator() : ptr(nullptr) {}

MyIterator::MyIterator(const MyIterator& other) : ptr(other.ptr) {}

MyIterator& MyIterator::operator++() {
    ++ptr;
    return *this;
}

MyIterator MyIterator::operator++(int) {
    MyIterator temp(*this);
    ++ptr;
    return temp;
}

int& MyIterator::operator*() {
    return *ptr;
}

bool MyIterator::operator==(const MyIterator& other) const {
    return ptr == other.ptr;
}

bool MyIterator::operator!=(const MyIterator& other) const {
    return ptr != other.ptr;
}
```

上述示例定义了一个简单的正向迭代器 `MyIterator`，它用于迭代整数。你可以根据需要定义自己的迭代器，以适应不同的数据结构和容器。



# 空间分配器

## 1 标准接口

| 接口                                      | 作用                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| `allocate(size_type n)`                   | 用于分配 `n` 个连续内存块的空间，并返回指向第一个内存块的指针，底层调用                                                    `operator new(sizeof(T))` |
| `deallocate(iteractor p, size_type n)`    | 用于释放先前分配的 `n` 个内存块的空间，该内存由 `p` 指向     |
| `construct( iteractor p, const T& value)` | 用于在分配的内存块上构造类型 `T` 的对象，并以 `value` 的值初始化对象，底层使用定位 `new` 运算符，在指针 `p` 指向的内存块上调用对象的构造函数，传递所需的参数。 |
| `destroy( iteractor p)`                   | 用于销毁在分配的内存块上构造的对象，但不释放内存块           |

## 2 全局函数

//  C++ 标准库中

// 传入参数均为迭代器

`#inclde <memoty>`

| 函数                                        | 作用                                                         |
| ------------------------------------------- | ------------------------------------------------------------ |
| `construct(iter， x)`                       | 构造对象，在指定内存块上，调用对象的构造函数，==针对单个内存块== |
| `destroy(begin, end)`                       | 对（begin, end）==范围==内的对象调用析构， 对单一的对象直接调用其析构函数就可 |
| `uninitialized_fill_n(iter, n, x)`          | 专注于内存的填充而不是对象的构造，==针对一系列内存块==       |
| `uninitialized_fill()`                      |                                                              |
| `uninitialized_copy(begin, end, new_begin)` | 复制容器的内容到新的容器中，参数为==迭代器==，可通过std::begin(arr)，std::end(arr) 获取迭代器 |



```cpp
#include <iostream>
#include <memory>
#include <algorithm>

int main(){
	std::allocator<int> alloc;
   	
    // 申请内存
    int *ptr = alloc.allocate(10);
    
    // (一)将9个内存，全部填充55；
    std::uninitialized_fill_n(ptr, 9, 55);
    
    ptr += 9;
    
    // (二)在最后一个内存上构造对象
    alloc.construct(ptr, 42);  
    ....
    alloc.destroy(ptr);
    
    // 释放内存
    alloc.deallocate(ptr, 10);
    
}
```

* construct 和 uninitialized_fill_n  的区别:

​	`uninitialized_fill_n()` 用于填充内存，通常不涉及对象的构造，适用于内置数据类型(int float char)或者自定义类型( struct )的内存填充。

​	`construct()` 用于在已分配的内存上构造对象( class )，会调用对象的构造函数，适用于需要对象初始化的情况。





# FBVector

##1 allocator： Impl

```cpp
struct Impl : public Allocator {
    // typedefs
    typedef typename A::pointer pointer;
    typedef typename A::size_type size_type;

    // data
    pointer b_, e_, z_;
    
    // 初始化构造
    Impl() : Allocator(), b_(nullptr), e_(nullptr), z_(nullptr) {}
    Impl(const Allocator& alloc): Allocator(alloc), b_(nullptr), e_(nullptr), z_(nullptr) {}
    Impl(Allocator&& alloc): Allocator(std::move(alloc)), b_(nullptr), e_(nullptr), z_(nullptr) {}
    Impl(size_type n, const Allocator& alloc = Allocator()): Allocator(alloc) { init(n);}

    Impl(Impl&& other) noexcept: Allocator(std::move(other)),b_(other.b_), e_(other.e_), z_(other.z_) 
    {
        other.b_ = other.e_ = other.z_ = nullptr;
    }

    ~Impl() { destroy(); }

    // allocation
    // note that 'allocate' and 'deallocate' are inherited from Allocator
    T* D_allocate(size_type n) {
        if (usingStdAllocator) {
            return static_cast<T*>(checkedMalloc(n * sizeof(T)));
        } else {
            return std::allocator_traits<Allocator>::allocate(*this, n);
        }
    }

    void D_deallocate(T* p, size_type n) noexcept {
        if (usingStdAllocator) {
            free(p);
        } else {
            std::allocator_traits<Allocator>::deallocate(*this, p, n);
        }
    }

    // helpers
    void swapData(Impl& other) {
        std::swap(b_, other.b_);
        std::swap(e_, other.e_);
        std::swap(z_, other.z_);
    }

    // data ops
    inline void destroy() noexcept {
        if (b_) {
            // THIS DISPATCH CODE IS DUPLICATED IN fbvector::D_destroy_range_a.
            // It has been inlined here for speed. It calls the static fbvector
            //  methods to perform the actual destruction.
            if (usingStdAllocator) {
                S_destroy_range(b_, e_);
            } else {
                S_destroy_range_a(*this, b_, e_);
            }

            D_deallocate(b_, size_type(z_ - b_));
        }
    }

    void init(size_type n) {
        if (n == 0) {
            b_ = e_ = z_ = nullptr;
        } else {
            size_type sz = folly::goodMallocSize(n * sizeof(T)) / sizeof(T);
            b_ = D_allocate(sz);
            e_ = b_;
            z_ = b_ + sz;
        }
    }

    void set(pointer newB, size_type newSize, size_type newCap) {
        z_ = newB + newCap;
        e_ = newB + newSize;
        b_ = newB;
    }

    void reset(size_type newCap) {
        destroy();
        auto rollback = makeGuard([&] { init(0); });
        init(newCap);
        rollback.dismiss();
    }
    void reset() { // same as reset(0)
        destroy();
        b_ = e_ = z_ = nullptr;
    }
} impl_;

static void swap(Impl& a, Impl& b) {
    using std::swap;
    if (!usingStdAllocator) {
        swap(static_cast<Allocator&>(a), static_cast<Allocator&>(b));
    }
    a.swapData(b);
}
```

```cpp
// folly::goodMallocSize()

inline size_t goodMallocSize(size_t minSize) noexcept {
  if (minSize == 0) {
    return 0;
  }

  if (!canNallocx()) {
    // No nallocx - no smarts
    return minSize;
  }
  // nallocx returns 0 if minSize can't succeed, but 0 is not actually
  // a goodMallocSize if you want minSize
  auto rv = nallocx(minSize, 0);
  return rv ? rv : minSize;
}

```

```cpp
// canNallocx()
inline bool canNallocx() noexcept {
  return detail::usingJEMallocOrTCMalloc();
}
```



```cpp
namespace detail {

#if FOLLY_CPLUSPLUS >= 202002L
// Faster "static bool" using a tri-state atomic. The flag is identified by the
// Initializer functor argument.
template <class Initializer>
class FastStaticBool {
 public:
  // std::memory_order_relaxed can be used if it is not necessary to synchronize
  // with the invocation of the initializer, only the result is used.
  FOLLY_ALWAYS_INLINE static bool get(
      std::memory_order mo = std::memory_order_acquire) noexcept {
    auto f = flag_.load(mo);
    if (FOLLY_LIKELY(f != 0)) {
      return f > 0;
    }
    return getSlow(); // Tail call.
  }

 private:
  FOLLY_NOINLINE FOLLY_COLD FOLLY_EXPORT static bool getSlow() noexcept {
    static bool rv = [] {
      auto v = Initializer{}();
      flag_.store(v ? 1 : -1, std::memory_order_release);
      return v;
    }();
    return rv;
  }

  static std::atomic<signed char> flag_;
};

template <class Initializer>
constinit std::atomic<signed char> FastStaticBool<Initializer>::flag_{};
#else // FOLLY_CPLUSPLUS >= 202002L
// Fallback on native static if std::atomic does not have a constexpr
// constructor.
template <class Initializer>
class FastStaticBool {
 public:
  FOLLY_ALWAYS_INLINE static bool get(
      std::memory_order = std::memory_order_acquire) noexcept {
    static const bool rv = Initializer{}();
    return rv;
  }
};
#endif

} // namespace detail

namespace detail {
FOLLY_EXPORT inline bool usingJEMallocOrTCMalloc() noexcept {
  usingJEMalloc
  struct Initializer {
    bool operator()() const { return usingJEMalloc() || usingTCMalloc(); }
  };
#if FOLLY_CONSTANT_USING_JE_MALLOC && FOLLY_CONSTANT_USING_TC_MALLOC
  return Initializer{}();
#else
  return detail::FastStaticBool<Initializer>::get(std::memory_order_relaxed);
#endif
}
} 
```

```cpp
FOLLY_EXPORT inline bool usingJEMalloc() noexcept {
  struct Initializer {
    bool operator()() const {
      // Checking for rallocx != nullptr is not sufficient; we may be in a
      // dlopen()ed module that depends on libjemalloc, so rallocx is resolved,
      // but the main program might be using a different memory allocator. How
      // do we determine that we're using jemalloc? In the hackiest way
      // possible. We allocate memory using malloc() and see if the per-thread
      // counter of allocated memory increases. This makes me feel dirty inside.
      // Also note that this requires jemalloc to have been compiled with
      // --enable-stats.

      // Some platforms (*cough* OSX *cough*) require weak symbol checks to be
      // in the form if (mallctl != nullptr). Not if (mallctl) or if (!mallctl)
      // (!!). http://goo.gl/xpmctm
      if (mallocx == nullptr || rallocx == nullptr || xallocx == nullptr ||
          sallocx == nullptr || dallocx == nullptr || sdallocx == nullptr ||
          nallocx == nullptr || mallctl == nullptr ||
          mallctlnametomib == nullptr || mallctlbymib == nullptr) {
        return false;
      }

      // "volatile" because gcc optimizes out the reads from *counter, because
      // it "knows" malloc doesn't modify global state...
      /* nolint */ volatile uint64_t* counter;
      size_t counterLen = sizeof(uint64_t*);

      if (mallctl(
              "thread.allocatedp",
              static_cast<void*>(&counter),
              &counterLen,
              nullptr,
              0) != 0) {
        return false;
      }

      if (counterLen != sizeof(uint64_t*)) {
        return false;
      }

      uint64_t origAllocated = *counter;

      static void* volatile ptr = malloc(1);
      if (!ptr) {
        // wtf, failing to allocate 1 byte
        return false;
      }

      free(ptr);

      return (origAllocated != *counter);
    }
  };
  return detail::FastStaticBool<Initializer>::get(std::memory_order_relaxed);
}
```



## 2 初始化

```cpp
fbvector() = default;

explicit fbvector(const Allocator& a) : impl_(a) {}
```

















结构：



```cpp
private:
	1 traits 底层alloc调用工具
    2 impl_ 分配器 
    3 M_allocate()、M_deallocate()、M_construct()、M_destroy()分配、释放内存函数
```



## 内存

```cpp
// 迭代器
iterator begin() noexcept  { return impl_.b_; }
const_iterator begin() const noexcept { return impl_.b_; }
iterator end() noexcept { return impl_.e_; }

size_type size() const noexcept { return size_type(impl_.e_ - impl_.b_); }
size_type capacity() const noexcept { return size_type(impl_.z_ - impl_.b_); }
bool empty() const noexcept { return impl_.b_ == impl_.e_; }原地扩充容量
```

### 3.2 原地扩充

`if (xallocx(p, newCapacityBytes, 0, 0) == newCapacityBytes)`：这是一个调用 `xallocx` 函数的条件语句，用于尝试扩展内存块的容量。`xallocx` 是jemalloc库中的一个函数，用于扩展内存块。如果成功扩展，返回值将等于 `newCapacityBytes`，表示扩展成功

```cpp
bool reserve_in_place(size_type n) {
    if (!usingStdAllocator || !usingJEMalloc()) {
      return false;
    }

    // jemalloc can never grow in place blocks smaller than 4096 bytes.
    // jemalloc分配器不能原地增长小于4096bytes内存块
    // 当前内存块的大小小于这个阈值
    if ((impl_.z_ - impl_.b_) * sizeof(T) <
        folly::jemallocMinInPlaceExpandable) {
      return false;
    }

    // 确定分配的最终内存块
    auto const newCapacityBytes = folly::goodMallocSize(n * sizeof(T));
    void* p = impl_.b_;

    // xallocx 是jemalloc库中的一个函数，用于扩展内存块。如果成功扩展，返回值将等于 newCapacityBytes
    if (xallocx(p, newCapacityBytes, 0, 0) == newCapacityBytes) {
      impl_.z_ = impl_.b_ + newCapacityBytes / sizeof(T);
      return true;
    }
    return false;
  }
```

### 3.3 异地扩充+迁移数据

用于在无法原地扩展内存的情况下重新分配内存：

- `auto newCap = folly::goodMallocSize(n * sizeof(T)) / sizeof(T);`：这一行计算了要分配的新内存块的容量。它根据 `n` 个元素的大小计算出需要的字节数，然后使用 `folly::goodMallocSize` 函数来获取一个合适的内存块大小，最后将其除以 `sizeof(T)` 得到新容量。
- `auto newB = M_allocate(newCap);`：这一行分配了新的内存块，返回一个指向新内存块起始位置的指针 `newB`。
- 接下来使用了一个 RAII（资源获取即初始化）技巧，通过 `makeGuard` 创建了一个对象 `rollback`，该对象在退出作用域时会调用指定的函数，用于在分配失败时回滚内存操作。
- `M_relocate(newB);`：这一行调用了 `M_relocate` 函数，将已有的元素从旧内存块迁移到新内存块。
- `rollback.dismiss();`：如果 `M_relocate` 成功，手动调用 `dismiss` 来防止 `rollback` 对象在函数退出时回滚内存分配。

```cpp
void reserve(size_type n) {
    if (n <= capacity()) {
      return;
    }
    if (impl_.b_ && reserve_in_place(n)) {
      return;
    }

    // 计算真正分配的内存块数
    auto newCap = folly::goodMallocSize(n * sizeof(T)) / sizeof(T);
    // 分配内存，返回指向内存开始的位置的指针
    auto newB = M_allocate(newCap);
    {
      // 内存分配失败，作用域结束后删除新内存退出
      auto rollback = makeGuard([&] { M_deallocate(newB, newCap); });
      // 迁移数据
      M_relocate(newB);
      // 迁移成功，终止作用域结束后退出
      rollback.dismiss();
    }

    // 释放原内存块
    if (impl_.b_) {
      M_deallocate(impl_.b_, size_type(impl_.z_ - impl_.b_));
    }

    impl_.b_ = newB;
    impl_.e_ = newB + (impl_.e_ - impl_.b_);
    impl_.z_ = newB + newCap;
    
    
  }
```



### 3.4 容量扩充策略

```cpp
private:
  // std::vector implements a similar function with a different growth
  //  strategy: empty() ? 1 : capacity() * 2.
  //
  // fbvector grows differently on two counts:
  //
  // (1) initial size
  //     Instead of growing to size 1 from empty, fbvector allocates at least
  //     64 bytes. You may still use reserve to reserve a lesser amount of
  //     memory.
  // (2) 1.5x
  //     For medium-sized vectors, the growth strategy is 1.5x. See the docs
  //     for details.
  //     This does not apply to very small or very large fbvectors. This is a
  //     heuristic.
  //     A nice addition to fbvector would be the capability of having a user-
  //     defined growth strategy, probably as part of the allocator.
  //

  size_type computePushBackCapacity() const {
      // 初始化策略，最小64bit，64bit能装多少个元素就装多少个，>=1， 如果装不下一个元素，就申请一个元素大小的空间
    if (capacity() == 0) {
      return std::max(64 / sizeof(T), size_type(1));
    }
    // 容量小的策略
    if (capacity() < folly::jemallocMinInPlaceExpandable / sizeof(T)) {
      return capacity() * 2;
    }
    // 容量大的策略
    if (capacity() > 4096 * 32 / sizeof(T)) {
      return capacity() * 2;
    }

    // 寻常的策略，+1目的：额外添加一个缓冲区
    return (capacity() * 3 + 1) / 2;
  }
```

```cpp
不支持重定位：：：单纯的拷贝数据，原来的数据没有释放
重定位：：：：拷贝数据同时，释放掉原来的数据块
```





# myVector

## 空间分配器

```cpp
template <class T>
class allocator
{
public:
  typedef T            value_type;
  typedef T*           pointer;
  typedef const T*     const_pointer;
  typedef T&           reference;
  typedef const T&     const_reference;
  typedef size_t       size_type;
  typedef ptrdiff_t    difference_type;

public:
  static T*   allocate();
  static T*   allocate(size_type n);

  static void deallocate(T* ptr);
  static void deallocate(T* ptr, size_type n);

  static void construct(T* ptr);
  static void construct(T* ptr, const T& value);
  static void construct(T* ptr, T&& value);

  template <class... Args>
  static void construct(T* ptr, Args&& ...args);

  static void destroy(T* ptr);
  static void destroy(T* first, T* last);
};
```

* 底层调用new分配内存

### 1 new的分类和作用

1. ```cpp
   //::operator new
   // 仅动态分配内存，不构造对象，返回void*
   int* ptr = static_cast<int*>(::operator new(sizeof(int)));
   ```

2. 在C++中，`new` 运算符的行为取决于它所用于的上下文和使用方式。它可以用于两种不同的操作：分配内存和构造对象。以下是判断 `new` 是仅构造对象还是分配内存和构造对象的方法：

1. **只构造对象**：
   - 如果你使用 `new` 运算符来构造对象，并且提供了一个已分配内存的指针作为参数，则 `new` 只会构造对象，而不会分配新的内存。这通常称为 "定位 `new`"。
   - 语法示例：`new (pointer) Type;`

2. **分配内存和构造对象**：
   - 如果你使用 `new` 运算符来创建对象，并且提供的是一个类型的构造参数，则 `new` 将首先分配足够的内存以容纳对象，然后使用构造参数来构造对象。
   - 语法示例：`new Type(constructor_arguments);`

判断 `new` 的行为通常取决于括号中的内容和上下文。在 "只构造对象" 的情况下，你必须提供一个指向已分配内存的指针，并且不需要提供构造参数。在 "分配内存和构造对象" 的情况下，你提供了构造参数，告诉 `new` 如何构造对象，并且不需要提前分配内存。

以下是示例，演示了这两种用法：

```cpp
// 只构造对象，使用已分配内存的指针
int* ptr = static_cast<int*>(::operator new(sizeof(int)));
int* newPtr = new (ptr) int;  // 构造 int 对象，不分配新内存

T* ptr = static_cast<T*>(::operator new(sizeof(T)));
T* newPtr = new (ptr) T();  // 构造 int 对象，不分配新内存


// 分配内存并构造对象，提供构造参数
int* newObj = new int(42);  // 构造 int 对象，并分配新内存
```

在上述示例中，第一个 `new` 是 "只构造对象" 的用法，第二个 `new` 是 "分配内存和构造对象" 的用法。

### 2 fill 填充函数

`std::memset` 是 C++ 标准库中的一个函数，用于将一段内存区域的内容设置为指定的值。它通常用于将内存初始化为特定的值，例如将数组或缓冲区中的所有字节设置为零。`memset` 的函数原型如下：

```cpp
void* memset(void* ptr, int value, size_t num);
```

参数解释如下：
- `ptr`：指向要设置的内存区域的指针，通常是一个数组或缓冲区的首地址。
- `value`：要设置的值，通常是一个整数。`value` 参数会被转换为无符号字符类型 `unsigned char`，然后用作填充内存区域的字节值。
- `num`：要设置的字节数，即要设置的内存区域的大小。

`std::memset` 函数将 `ptr` 指向的内存区域的前 `num` 个字节都设置为 `value` 的值。这个函数通常用于在初始化数组、缓冲区或数据结构时，将它们的内容清零或设置为某个特定的值。

以下是一个示例，演示如何使用 `std::memset` 将一个整数数组的所有元素设置为零：

```cpp
#include <iostream>
#include <cstring>

int main() {
    int arr[5];
    std::memset(arr, 0, sizeof(arr)); // 将整数数组的所有元素设置为零
    for (int i = 0; i < 5; ++i) {
        std::cout << arr[i] << " ";
    }
    return 0;
}
```

在这个示例中，`std::memset` 用于将 `arr` 数组的所有元素设置为零。然后，我们使用循环打印数组的元素，它们应该都是零。

### 3 erase()和destroy()

这两种操作都会从 `vector` 中移除元素，但在处理底层资源时有所不同。让我们深入了解这两个操作：

1. **erase()**:
   - `erase()` 是 `std::vector` 提供的成员函数，用于移除一个或一段元素。
   - 它不仅销毁了对象，还确保了 `vector` 的其他元素进行了适当的重新排列，以保持其连续的特性。在移除元素后，`erase()` 还会调整 `vector` 的 `size`。
   - 使用 `erase()` 移除元素后，任何指向已移除元素之后的迭代器都可能失效。

2. **destroy()**:
   - `destroy()` 通常是一个底层的操作，用于直接销毁对象，但不涉及内存的释放或重新排列。
   - 它是为 `allocator` 而设计的，它仅仅调用对象的析构函数。
   - 当你知道确切的区域或范围要被销毁，并且不需要对 `vector` 的其他部分进行重新排列时，可以使用这个操作。

在上面的代码中：

- 在输入迭代器版本中，当 `first` 达到 `last` 时，使用 `erase(cur, end_)` 来移除从 `cur` 到 `end_` 的所有元素。这是因为在这种情况下，可能还有其他元素在 `cur` 之后，它们也需要被适当地移动到更前面的位置。因此，`erase()` 是合适的选择。

- 在前向迭代器版本中，当 `size() >= len` 时，知道 `new_end` 后的所有元素都是多余的。这些元素可以直接被销毁，而不需要重新排列或调整大小，因此可以直接使用 `destroy()`。

总的来说，选择 `erase()` 还是 `destroy()` 取决于具体的场景和需要完成的任务。在 `vector` 的实现中，这种选择使得代码在各种情况下都能高效地工作。

好的，我会通过一个简单的例子来解释 `erase()` 和 `destroy()` 的区别，并说明为什么在某些情况下使用 `erase()` 是有意义的。

假设我们有一个 `std::vector<int>` 如下：

```
vector = [10, 20, 30, 40, 50]
```

现在，我们要基于另一个序列来重新赋值这个 `vector`。假设这个新的序列只包含两个元素 `[11, 22]`。

1. **使用输入迭代器**

我们会同时遍历当前 `vector` 和新序列。在这个过程中，前两个元素 `10` 和 `20` 会被替换为 `11` 和 `22`。当新序列遍历完后，当前 `vector` 仍然包含三个元素 `[30, 40, 50]`。

此时，我们需要删除这三个元素并确保 `vector` 的大小减小到2。同时，为了保持 `vector` 的连续性，必须确保后面的元素（如果有的话）向前移动以填补空缺。在这种情况下，我们使用 `erase()`。

使用 `erase()` 后，`vector` 变为：

```
vector = [11, 22]
```

2. **使用前向迭代器**

假设我们已经知道新序列的长度为2。首先检查 `vector` 的容量。如果新序列的长度大于 `vector` 的容量，我们需要重新分配内存。但在这个例子中，我们的容量足够大。

接下来，由于 `vector` 的当前大小（5）大于新序列的长度（2），我们只需将新序列的元素拷贝到 `vector`，然后销毁多余的元素。

此时，我们不需要移动 `vector` 的任何元素或调整它的大小，因为新序列的长度小于当前 `vector` 的大小。我们只需要销毁多余的元素 `[30, 40, 50]`。为了这个目的，我们可以使用 `destroy()` 来直接销毁这三个元素。

结果还是：

```
vector = [11, 22]
```

总结：
- 使用 `erase()` 时，如果有必要，它会处理元素的移动并调整 `vector` 的大小。
- 使用 `destroy()` 只会销毁元素，不会涉及其他操作。

在输入迭代器的情境中，我们不知道新序列的长度，因此可能需要处理元素的移动和调整 `vector` 的大小，所以使用 `erase()` 是有意义的。

好的，我会更详细地解释这一部分。

首先，`std::vector` 保证它的元素在内存中是连续存储的。这意味着如果你删除了某个元素，为了保持连续性，其后面的所有元素必须前移一个位置来填补这个空缺。

现在，让我们再看一下前面的例子：

```
vector = [10, 20, 30, 40, 50]
```

如果我们基于一个新的序列 `[11, 22]` 来重新赋值这个 `vector`，然后直接使用 `destroy()` 来销毁第三个元素 `30`，情况会是这样的：

```
vector = [11, 22, ???, 40, 50]
```

这里的 `???` 表示这个位置的元素已经被销毁，但它的值是未定义的。更重要的是，我们不能仅仅通过销毁 `30` 来保证 `vector` 的大小是2。还需要确保元素 `40` 和 `50` 也被移除，这样 `vector` 的结尾才是在元素 `22` 后面。

如果我们使用 `destroy()` 来仅仅销毁元素而不处理后面的元素，那么 `vector` 的状态就会变得不一致。这就是为什么在这种情况下使用 `erase()` 是有意义的，因为 `erase()` 不仅会销毁元素，还会确保后续的元素前移来保持 `vector` 的连续性，并调整 `vector` 的大小。

总之，如果你只是想销毁一个元素而不调整后面的元素或 `vector` 的大小，可以使用 `destroy()`。但在上述的场景中，我们不仅要销毁元素，还要保持 `vector` 的连续性和正确的大小，因此 `erase()` 是更合适的选择。

## 处理错误

```
// 通过自定义函数验证是否出错
MYSTL_DEBUG()
```

## 返回值

```cpp
// 返回的指针为迭代器类型  iterator
// 返回的内容为引用，方便修改返回值  reference
```

## 迭代器

```cpp
struct input_iterator_tag {};
struct output_iterator_tag {};
struct forward_iterator_tag : public input_iterator_tag {};
struct bidirectional_iterator_tag : public forward_iterator_tag {};
struct random_access_iterator_tag : public bidirectional_iterator_tag {};
```

1. **输入迭代器（Input Iterators）**
   - 支持读取序列中的元素（不支持写入）。
   - 支持递增操作。
   - 通常用于单遍扫描的算法中。
   - 不能计算begin_ 到 end_ 的距离
   - 删除使用 erase() ,  插入使用insert()
   
2. **输出迭代器（Output Iterators）**
   - 支持写入序列中的元素（不支持多次读取）。
   - 支持递增操作。
   - 通常用于单遍扫描的输出操作中。

3. **前向迭代器（Forward Iterators）**
   - 既是输入迭代器又是输出迭代器。
   - 支持多次读取同一个元素。
   - 通常用于需要多遍扫描的算法中。
   - 可以计算两个迭代器的距离 `const size_type len = mystl::distance(first, last);`
   - 删除使用 destory()
   
4. **双向迭代器（Bidirectional Iterators）**
   - 支持前向迭代器的所有操作。
   - 还支持递减操作。
   - 例如，`std::list` 和 `std::set` 的迭代器就是双向迭代器。

5. **随机访问迭代器（Random Access Iterators）**
   - 支持双向迭代器的所有操作。
   - 还支持跳过多个元素的直接访问。
   - 支持算术运算和比较操作。
   - 例如，`std::vector` 和 `std::deque` 的迭代器就是随机访问迭代器。

6. **连续迭代器（Contiguous Iterators）**（C++17之后）
   - 是随机访问迭代器的一个子集。
   - 它们所指向的元素在内存中是连续的，如`std::vector`。
   
7. **反向迭代器（Reverse Iterators）**
   - 不是一个独立的迭代器类别，但是很多容器为了支持逆序遍历提供了反向迭代器，如 `rbegin()` 和 `rend()`。

除了这些主要的迭代器类型，C++还为迭代器提供了各种适配器和工具函数，如`std::back_inserter`、`std::front_inserter` 和 `std::inserter`等，以便在算法中方便地使用它们。

以下是关于前五种迭代器类型在具体容器使用中的一些说明：

1. 输入迭代器（Input Iterator）：
   - 适用容器：适用于任何支持单向遍历的容器，如单向链表。
   - 使用示例：适用于从容器中读取元素，但不能修改容器。例如，使用输入迭代器可以遍历链表并读取其中的数据。

2. 输出迭代器（Output Iterator）：
   - 适用容器：适用于任何支持单向遍历的容器，用于向容器中写入元素。
   - 使用示例：适用于向容器中添加元素，但不支持读取元素。例如，可以使用输出迭代器将数据逐个写入文件。

3. 前向迭代器（Forward Iterator）：
   - 适用容器：适用于支持单向遍历的容器，如单向链表。
   - 使用示例：可以用于遍历单向链表、单链表、以及某些顺序容器（如 `std::forward_list`），可以进行逐个读取和写入元素的操作。

4. 双向迭代器（Bidirectional Iterator）：
   - 适用容器：适用于支持双向遍历的容器，如双向链表、某些序列容器（如 `std::list`）。
   - 使用示例：可以用于遍历双向链表和某些序列容器，支持逆向遍历以及逐个读取和写入元素。

5. 随机访问迭代器（Random Access Iterator）：
   - 适用容器：适用于支持随机访问的容器，如数组、向量、deque 等。
   - 使用示例：可以用于高效地访问容器中的元素，支持随机访问、跳跃式遍历、计算迭代器距离等操作。可用于数组、向量等连续内存存储的容器，具有最灵活和高效的迭代器功能。

每种迭代器类型在特定容器上都有其优点和限制，因此在选择迭代器类型时，需要考虑容器类型、遍历方式、性能需求等因素。不同的容器和迭代器类型组合可以满足不同的应用需求。

