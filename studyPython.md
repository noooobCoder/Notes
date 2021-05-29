# Python

## 可能会用到Django中的一些内容

### 在函数内定义函数

比如现在有一个求和函数:

    def calc_sum(*args):
        ax = 0
        for n in args:
            ax = ax + n
        return ax
但我们不希望立刻求和，而是在后面的代码中根据需要再计算，因此我们需要使另一个函数返回求和的函数

    def lazy_sum(*args):
        def sum():
            ax=0
            for n in args:
                ax += n
            return ax
        return sum
当调用lazy_sum()时，返回的就是求和函数:

    >>> f = lazy_sum(1, 3, 5, 7, 9)
    >>> f
    <function lazy_sum.<locals>.sum at 0x101c6ed90>
再调用函数f时，才真正计算求和的结果:

    >>> f()
    25
需要注意的是，内部函数可以引用外部函数的参数和局部变量，当lazy_sum返回函数sum时，相关参数和变量都保存在返回的函数中，成为“闭包”的程序结构。

因此，即使传入相同的参数：

    >>> f1 = lazy_sum(1, 3, 5, 7, 9)
    >>> f2 = lazy_sum(1, 3, 5, 7, 9)
    >>> f1==f2
    False
**另外**：返回闭包时牢记返回函数不要引用任何循环变量，或者后续会发生变化的变量！

举个例子：

    def count():
        fs = []
        for i in range(1, 4):
            def f():
                return i*i
            fs.append(f)
        return fs

    f1, f2, f3 = count()
当你查看f1(),f2(),f3()时：

    >>> f1()
    9
    >>> f2()
    9
    >>> f3()
    9
原因就在于返回的函数引用了变量i，但它并非立刻执行，而是等到3个函数都返回时，它们所引用的变量i已经变成了3，所以最终结果都是9

如果一定要引用循环变量，就需要再创建一个函数，用该函数的参数绑定循环变量当前的值：

    def count():
        def f(j):
            def g():
                return j*j
            return g
        fs = []
        for i in range(1, 4):
            fs.append(f(i)) # f(i)立刻被执行，因此i的当前值被传入f()
        return fs
