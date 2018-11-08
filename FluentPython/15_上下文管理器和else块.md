其实是不想写这篇分享的，感觉第十五章没有讲多少东西，而且之前分享过关于上下文的东西（[这里](https://darren2017.github.io/2018/09/05/Flask-个人进阶/#上下文)），所以就少写一点新的东西吧。

# `else`
+ `for`: 仅当`for`循环运行完毕时（即`for`循环没有被`break`语句终止）才运行`else`块
+ `while`:仅当`while`循环条件因为假值而退出时（即`while`循环没有被`break`语句终止）才运行`else`块
+ `try`:仅当`try`块中没有异常抛出时才运行`else`块

# 两种风格
+ `EAFP`: 取得原谅比获得许可容易
+ `LBYL`: 三思而后行

# 使用`@contextmanager`
使用`@contextmanager`来代替上下文管理器，与上下文管理器的区别是异常默认被处理，而不是继续向上层抛出。

```py
@contextlib.contextmanager
def looking_glass():
    import sys
    original_write = sys.stdout.write

    def reverse_write(text):
        original_write(text[::-1])

    sys.stdout.write = reverse_write
    yield 'JABBERWOCKY'
    sys.stdout = original_write
``` 

# 取出面包
> 假如有一系列操作，如A-B-C和P-B-Q，那么可以把B拿出来，变成子程序，这就好比把三明治的馅取出来，这样我们就能使用金枪鱼搭配不同的面包。如果我们想把面包取出来，使用小麦面包夹不同的馅，这就是`with`语句实现的功能。
