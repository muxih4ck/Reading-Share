# 向量类

```py
from array import array
import math

class Vector2d:
    typecode = 'd'          # 类属性

    def __init__(self, x, y):           # 构造器
        self.__x = float(x)          # 实例属性
        self.__y = float(y)

    @property
    def x(self):                        # 不支持属性修改，保证数据的可散列性
        return self.__x

    @property
    def y(self):                        # 不支持属性修改，保证数据的可散列性
        return self.__y

    def __iter__(self):                 # 迭代器，用于拆包等
        return (i for i in (self.x, self.y))

    def __hash__(self):             # 哈希  让示例变为可散列的（于__eq__一起）
        return hash(self.x) ^ hash(self.y)

    def __repr__(self):             # 面向程序员的显示
        class_name = type(self).__name__
        return '{}({!r}, {!r})'.format(class_name, *self)

    def __str__(self):              # 面向用户的显示
        return str(tuple(self))

    def __bytes__(self):            # 生成字节序列，而且要为支持用字节序列生成向量做准备
        return (bytes([ord(self.typecode)]) + bytes(array(self.typecode, self)))

    def __eq__(self, other):        # 用于 == 运算
        return tuple(self) == tuple(other)

    def __abs__(self):              # 用于 abs()
        return math.hypot(self.x, self.y)

    def __bool__(self):             # 用于 bool()
        return bool(abs(self))

    @classmethod                # 类方法
    def frombytes(cls, octets):             # 备选构造器，通过字节序列来生成一个实例
        typecode = chr(octets[0])
        memv = memoryview(octets[1:]).cast(typecode)
        return cls(*memv)

    def __format__(self, fmt_sepc=''):          # 格式化显示
        if fmt_sepc.endswith('p'):
            fmt_sepc = fmt_sepc[:-1]
            coords = (abs(self), self.angle())
            outer_fmt = '<{}, {}>'
        else:
            coords = self
            outer_fmt = '({}, {})'
        components = (format(c, fmt_sepc) for c in coords)
        return outer_fmt.format(*components)

    def angle(self):
        return math.atan2(self.x, self.y)  
```

# `@classmethod`与`@staticmethod`

```py
class demo:
    @classmethod
    def func_class(cls, *args):
        print(cls)
        print(*args)
    
    @staticmethod
    def func_static(*args):
        print(*args)
```

## `@classmethod`
第一个参数接受类本身，而不是实例本身。所以被 `@classmethod`装饰的函数都是类方法，只能对类进行操作，不能对实例进行操作。  
具体可以参照第一个代码的使用。   
## `@staticmethod`
与普通的方法没有太大的区别，唯一的区别就是定义的地方不一样而已。

# 格式化显示
内置的`format()`函数和`str.format()`方法都会调用`.__format__(format_spec)`方法。在没有实现`__format__`方法时，会返回`str(my_object)`。具体的`format()`使用方法在此不具体讲解。  
![](https://ww1.sinaimg.cn/large/005YhI8igy1fvn8v82t6vj315e0nxak8)   

# `__slots__`类属性
+ 使用`__slots__`将所有的实例属性存储到一个可迭代对象中，从而避免使用消耗内存的`__dict__`属性。   
+ 但是缺点是在类中定义了`__slots__`属性之后，实例不能拥有`__slots__`之外的属性。另外，不要把`__dict__`属性也放到`__slots__`中，原因太明显了。  
+ 用户自定义的类中默认有`__weakref__`属性，但是如果使用了`__slots__`记得将其放进去，否则该类将不再支持弱引用。  
+ `__slots__`属性不可以继承。

# 覆盖类属性
```py
Vector2d.typecode = 'b'
v1.typecode = 'b'   # v1是Vector2d的一个实例
```

第一种方式修改类属性，第二种方式用实例属性去覆盖类属性。