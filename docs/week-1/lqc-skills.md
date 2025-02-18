<!--
 * @Author: Sunist Chan
 * @Date: 2021-03-29 05:33:50
 * @LastEditTime: 2021-03-29 13:56:14
 * @LastEditors: Sunist Chan
 * @Description: 
 * @FilePath: \2021-LQC-Trainning\docs\week-1\lqc-skills.md
-->
# 蓝桥杯做题技巧 #

> 请注意，除非特殊说明，所有的代码片段均为C/C++代码

## 1. 递归的使用 ##

### 1.1 什么是递归 ###

递归，英文为`Recursion`，在数学与计算机科学中，是指在函数的定义中使用函数自身的方法。

这个定义主要有两层含义：

+ 递归要有一个函数
+ 这个函数要调用自己

我们举一个实际的例子，这个用例是数学领域的：

$$
\begin{aligned}
n! =& n \times(n-1)! \\
1! =& 1
\end{aligned}
$$

即：n的阶乘是n-1的阶乘乘上n。特殊的，1的阶乘为1。

我们用代码来实现它：

```cpp
int Factorial(int n) {
    if (n == 1) {
        return 1;
    }
    else {
        return n * Factorial(n-1);
    }
}
```

这样子我们可以很方便的将一些能够用递推公式表示的函数(方法)用递归代码写出来，又比如大名鼎鼎的`Fibonacci`数列：

$$
\begin{aligned}
Fib(1) =& 1 \\
Fib(2) =& 1 \\
Fib(n) =& Fib(n-1) + Fib(n-2), \quad n \geq 3
\end{aligned}
$$

用代码表示就是：

```cpp
int Fibonacci(int n) {
    if (n == 1 || n == 2) {
        return 1
    }
    else {
        return Fibonacci(n-1) + Fibonacci(n-2);
    }
}
```

我们通过观察上面两个递归函数可以发现他们的共同特点：

+ 都有一个或若干个`if/else`语句来区分特殊情况
+ 都有一个或若干个情况函数调用了它自身来计算结果

其中，我们将用于定义特殊情况的`if (case == ${exit_case})`语句称为递归出口。请注意，**如果一个递归函数没有递归出口，那么这个函数将无限运行下去直到内存溢出**。

### 1.2 递归的优势与劣势 ###

递归的优势在于我们人类可以自然地理解函数的逻辑，轻松的写出代码，并且在充分优化后，能够高效的得出结果。上文的`Factorial`函数，如果让我们用循环来写的话，如果没有见到过这类要求，普通水平的同学最少会被卡半个小时，而如果使用递归来写的话，仅仅需要几分钟甚至几十秒就可以完成这个函数了。

而递归的劣势在于如果我们没有将递归过程优化，其执行效率与内存开销会膨胀到一个非常离谱的程度。我们用上文的`Fibonacci`函数来举例。

如果我们要求$Fibonacci(10)$，那么程序在运行的时候，就会先计算$Fibonacci(9)$和$Fibonacci(8)$，而计算$Fibonacci(9)$的时候又会计算$Fibonacci(8)$和$Fibonacci(7)$。这时候我们就能够看出来，$Fibonacci(8)$其实已经算了两次了。但是我们的程序并不知道我们已经计算过了，它还是会傻傻的重新计算一次$Fibonacci(8)$，这样的后果是计算$Fibonacci(8)$所需计算的$Fibonacci(7,6,5,4,3,2,1)$又会被重复计算，如此循环，我们的程序就会将大量的时间浪费在计算已经完成的过程上，程序的执行效率就会大打折扣。

### 1.3 什么时候该使用递归 ###

从上面的分析中我们就可以看到，不是所有的情况都能够使用递归。经过我们长时间的练习与使用经验，下面这些场景可以考虑使用递归来解决问题：

+ 不同状态之间存在递推关系，并且这个递推关系只和一个其他状态有关。即`Factorial`这种和上一个数有关的情况。
+ 你需要列举所有的情况，并且已经充分考虑到了重复计算的问题。比如深度优先搜索与树的遍历。
+ 你实在想不出来使用循环或其他方法解决问题的时候。

**除非上述前两点情况，请不要将递归作为优先考虑方法。**

### 1.4 递归与循环的转换 ###

> 这个部分是练习内容，我们将在本节课程结束后公布答案。

请参训同学们将上面两个例子用循环来实现。

## 2. IO优化 ##

### 2.1 `iostream`与`cstdio`异同 ###

> 请注意，`cstdio`与`stdio.h`是同一个东西，类似的`cstdlib`与`stdlib.h`也是同一个东西。想要了解更多的同学可以自己搜索C++标准库与C标准库的联系与发展。
> 
> **在使用纯C语言时，应当使用<${header}.h>格式的头文件**

`iostream`头文件是C++的标准输入输出库，而`cstdio`是C的标注你输入输出库。在正常情况下，C++能够使用两个库而C只能使用`cstdio`。

特别的，在C++中，常见的函数都被放在`std`命名空间中，使用时需注明命名空间。

+ `iostream`的使用：

    ```cpp
    #include <iostream>

    void* func(void* args) {
        int Integer = 0;
        double Double = 0.0;
        std::cin >> Integer >> Double;

        std::cout << std::setprecision(2) << Double << std::endl;
        std::cout << Integer << "\n";
    }
    ```

+ `cstdio`的使用：

    ```cpp
    #include <stdio.h>

    void* func(void* args) {
        int Integer = 0;
        double Double = 0.0;
        scanf("%d%lf", &Integer, &Double);
        
        printf("%.2lf", Double);
        printf("%d", Integer);
    }
    ```
### 2.2 关闭流同步 ###

相信同学们都了解且掌握了上面的示例用法，但是同学们是否清楚他们之间的区别呢？

在C++中，输入输出引入了"流操作"这一运算，为了与原来C语言的`printf`兼容，C++使用了流同步这一策略，输入输出的内容会先保存到缓存区里，然后再从缓存区里读写。而这一策略导致了其读取、输出的效率极为低下，能够达到十倍乃至百倍的差距。

**所以在竞赛情况下，请优先使用`printf`与`scanf`进行读写操作**，如果真的需要使用`cin`或者`cout`，**请先关闭流同步**。

```cpp
std::ios::sync_with_stdio(false);
```

这一段代码请在`main()`函数的一开始运行，并且在`cout`换行的时候请优先使用`\n`而不是`endl`。

但是，关闭了流同步以后，会导致同时使用`cin/cout`和`scanf/printf`时现顺序不一致，即在语句上先输出的在实际上可能会后输出。

所以在关闭流同步以后，请不要同时使用`cin/cout`和`scanf/printf`。

### 2.3 快速读写 ###

在有的IO密集型题目里，可能连`scanf/printf`都无法满足需要的读写速度，这个时候就需要我们手动进行IO优化，即快速读写。

一般情况下，以下情景可以考虑使用快速读写：

+ 需要进行极高频率(百万数量级)输入与输出的场景
+ 读写内容极其单一的场景，即只读取数字或字符的场景
+ 读写内容在存储结构上较为简单

> 但是就蓝桥杯而言，从其开始举办到现在为止，没有一道题目使用到了该技巧，所以此技巧我们不重点讲解。

### 2.4 快速读写的模版 ###

**快速读取数字**

```cpp
inline long long read()
{
    long long s = 0, w = 1;
    char ch = getchar();
    while (ch < '0' || ch > '9')
    {
        if (ch == '-')
            w = -1;
        ch = getchar();
    }
    while (ch >= '0' && ch <= '9')
        s = s * 10 + ch - '0', ch = getchar();
    return s * w;
}
```

## 3. 吸氧(开启$O_2$优化) ##

### 3.1 什么是$O_2$优化，有什么作用 ###

一般来说，如果不指定优化标识的话，gcc就会产生可调试代码，每条指令之间将是独立的：可以在指令之间设置断点，使用gdb中的`p`命令查看变量的值，改变变量的值等。并且把获取最快的编译速度作为它的目标。

当优化标识被启用之后，gcc编译器将会试图改变程序的结构（当然会在保证变换之后的程序与源程序语义等价的前提之下），以满足某些目标，如：代码大小最小或运行速度更快（只不过通常来说，这两个目标是矛盾的，二者不可兼得）。

在不同的gcc配置和目标平台下，同一个标识所采用的优化种类也是不一样的，这可以使用`-Q --help =optimizers`来获取每个优化标识所启用的优化选项。

**$O_1$优化**

目的都是在不影响编译速度的前提下，尽量采用一些优化算法降低代码大小和可执行代码的运行速度。

**$O_2$优化**

该优化选项会牺牲部分编译速度，除了执行-O1所执行的所有优化之外，还会采用几乎所有的目标配置支持的优化算法，用以提高目标代码的运行速度。

**$O_3$优化**

该选项除了执行-O2所有的优化选项之外，一般都是采取很多向量化算法，提高代码的并行执行程度，利用现代CPU中的流水线，Cache等。

而在竞赛中，允许我们使用$O_2$优化。

### 3.2 如何开启$O_2$优化 ###

> 在蓝桥杯大赛中，比赛所用的电脑默认给我们开启了$O_2$优化，所以不需要我们手动开启，我们直接使用就可以了。

```cpp
#pragma GCC optimize(2)
```

同样的，在`main()`函数的开头写上该代码段就可以了。

## 4. 分治 ##

<!--这部分wyj负责一下-->

## 5. 萌新三法 ##

> 这一部分将在直播中展示，请关注公开课所使用的[直播间](https://live.bilibili.com/5184681)，且直播的录屏会在[西南大学商贸学院ACM](https://space.bilibili.com/1599858052)上传。

### 5.1 打表法 ###

### 5.2 暴搜法 ###

### 5.3 骗分法 ### 

## 6. 做填空题的方法 ##

## 7. Excel和Python的高级应用 ##

### 7.1 使用Excel计算日期 ###

> 在比赛环境与自己的电脑上，一般都会装有Excel软件，我们能够用它来辅助我们完成题目。

1. 新建一个工作表并打开
2. 将其中的若干个使用到的单元格格式设置为`日期/时间`
3. 用Excel内置的函数与运算计算我们需要的内容

### 7.2 使用Python进行数学运算 ###

> 在比赛环境与自己的电脑上，Python基本也是必装的，我们能够用Python强大的数学库来帮助我们进行数学运算，简化我们的做题过程。

**比赛时只要能做出题目，用什么语言是根本不管的。**

1. 打开Python
2. 使用Python

## 8. RE/TLE/MLE的常见解决思路 ##