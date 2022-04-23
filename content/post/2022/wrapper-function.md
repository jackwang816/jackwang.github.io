---
title: 利用 --wrap 链接选项包装函数的技巧
slug: wrap-function-by-linker-option
date: 2022-03-28
categories:
- 2022-03
tags:
- C
- 编译
# thumbnailImagePosition: left
# thumbnailImage: //d1u9biwaxjngwg.cloudfront.net/chinese-test-post/vintage-140.jpg
# comments: false
---

最近在公司项目中看到一个链接技巧，可以通过链接器的链接选项 --wrap 来包装一个函数，使得在程序链接的时候链接器自动将对目标函数的引用解析到我们定义的包装函数。通过这个小技巧可以很方便的在不改变代码结构的前提下，通过链接选项来控制具体环境下的函数具体实现方法，替换掉原项目中某些函数的实现。这个技巧在debug以及一些嵌入式项目的单元测试中比较好用。

话不多说，来看看--wrap这个链接选项具体的用法吧。
<!--more-->
<!-- toc -->

## 链接选项描述

以下是GCC linker option --wrap的描述：

    Use a wrapper function for symbol. Any undefined reference to symbol will be resolved to
    __wrap_symbol. Any undefined reference to __real_symbol will be resolved to symbol. This 
    can be used to provide a wrapper for a system function. The wrapper function should be 
    called __wrap_symbol. If it wishes to call the system function, it should call __real_symbol.
    Here is a trivial example:
    void *
    __wrap_malloc (int c)
    {
        printf ("malloc called with %ld\n", c);
        return __real_malloc (c);
    }
    If you link other code with this file using --wrap malloc, then all calls to malloc will
    call the function __wrap_malloc instead. The call to __real_malloc in __wrap_malloc will
    call the real malloc function. You may wish to provide a __real_malloc function as well, 
    so that links without the --wrap option will succeed. If you do this, you should not put 
    the definition of __real_malloc in the same file as __wrap_malloc; if you do, the assembler 
    may resolve the call before the linker has a chance to wrap it to malloc.

从描述可以看出，这个链接选项可以为符号进行包装，所有对这个符号未定义的引用（undefined reference）都会被链接到__wrap_symbol这个定义，而任何对未定义的__real_symbol的引用都会被链接到原始的symbol这个定义。换句话说，如果使用了--wrap这个选项，程序中所有对symbol的调用都会被链接器解析到__wrap_symbol，实际调用的就是__wrap_symbol这个符号，如果想调用原始的symbol定义，就要调用__real_symbol这个符号。

***Talk is cheap, show me the code!!***

## 测试代码

main.c
```
int main(int args, char**argv)
{
    test_func();
    __real_test_func();
}

```

wrapper.c
```
#include<stdio.h>

void __wrap_test_func()
{
    printf("wrap test func\n");
}
```

orgLib.c
```
#include<stdio.h>

void test_func()
{
    printf("test func\n");
}
```

通过下面的命令行编译：
```
gcc main.c wrapper.c orgLib.c -Wl,--wrap=test_func -o test_wrap
```

我们来看看运行的结果吧：
```
$./test_wrap
wrap test func
test func
```

可以看到当调用test_func时，程序实际调用的是__wrap_test_func, 而调用__real_test_func才是调用原始的test_func函数。到此我们就完成了简单的wrap操作，通过这个链接选项，我们可以很方便的替换掉某些函数的实现而不改变源代码。

这里有一点需要注意，来看一下gcc文档中说明：
```
Any undefined reference to symbol will be resolved to __wrap_symbol.
```
是针对所有 "undefined reference to symbol", 在引用symbol的源文件里面不能有symbol的定义，否则汇编器在处理源文件的时候就会解析这个symbol，链接器也就没有机会wrap这个symbol了。这么说起来有点抽象，我们还是用一个例子来讲一下这点。

main.c
```
#include<stdio.h>

void test_func()
{
    printf("test func\n");
}

int main(int args, char**argv)
{
    test_func();
    __real_test_func();
}
```
wrapper.c
```
#include<stdio.h>

void __wrap_test_func()
{
    printf("wrap test func\n");
}
```
编译:
    gcc main.c wrapper.c -Wl,--wrap=test_func -o test_wrap

运行一下看结果:
```
$ ./test_wrap
test func
test func
```
结果可以看出来，这次并没有被链接器test_func并没有被链接包装。原因就像前面所说的，编译main.c的时候，汇编器已经解析了test_func这个符号，也就是说main.c汇编结束以后的目标文件已经没有test_func这个符号的引用，链接器也就没有机会再去打包这个符号了。

## 应用

原理已经讲完了，那这个wrap链接选项要怎么用呢？这里介绍两种简单的应用。

一种用法是在调试程序的时候，希望在每次调用某个库函数的时候加上一些调试信息，或者是在产品发布以后，希望对某个库函数打一个补丁，增加一些逻辑。这时就可以通过简单的链接选项替换原来的库函数，如下程序替换malloc库函数：
```
void *__wrap_malloc(size_t size)
{
    printf("size: %d\n", size);
    return __real_malloc(size);
}
```

还有一种用法就是给单元测试打桩，在很多嵌入式项目中涉及很多对硬件的操作，在写单元测试的时候就需要屏蔽掉硬件操作，重新定义一个接口替换掉原先的接口。在整个项目中一一替换，效率太低也容易出错，通过这个链接选项可以很方便的将一些函数用重新定义的桩函数替换。例如：
HAL_Dma.c
```
void HAL_DmaCopyBlock(uint8_t *src, uint8_t *dst, uint32_t len)
{
    // DMA copy block until copy done
    ...
}
```
Wrap_HAL_Dma.c
```
void __wrap_HAL_DmaCopyBlock(uint8_t* src, uint8_t* dst, uint32_t len)
{
    memcpy(src, dst, len);
}
```

如上所示，当使用--wrap链接选项对单元测试的代码进行链接时，原程序中的DMA相关的拷贝操作就被替换成了c库函数 memcpy，而不需要在原程序中大面积的替换，简单方便。

## 总结
gcc提供的这个--wrap链接选项可以很方便的对某个函数做替换操作，在调试、打补丁以及单元测试中挺有用的，文章简单介绍了一下这个选项的使用，仅供参考。