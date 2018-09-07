# collections
`collections`是Python内建的一个集合模块，提供了许多有用的集合类。
## namedtuple
具名元组，`tuple`的一个子类。能够用来创建类似于`tuple`的数据类型，除了能够用索引来访问数据，能够迭代，更能够方便的通过属性名来访问数据。类似于`tuple`，`namedtuple`的属性也是不可改变的。

```py
from collections import namedtuple
	
Card = collections.namedtuple('Card', ['rand', 'suit'])
	
beer_card = Card('7', 'diamonds')
print(beer_card.rand, beer_card[0])
```

## deque
`deque`是高效实现了插入和删除操作的双向列表，适合用于队列和栈。相较而言`list`可以按索引快速访问元素，但是插入和删除元素则很慢，因为`list`是现行存储，数据量大的时候，插入和删除效率很低。

```py
from collections import deque

sq = deque([str(x) for x in range(10)])

sq.append('x')		//入栈 or 入队
sq.pop()				//出栈
sq.appendleft('y')	
sq.popleft()			//出队
```

## defaultdict
安全的字典。使用`dict`时，如果引用的`key`不存在，则会抛出`KeyError`，如果希望`key`不存在时不抛出`Error`而是返回一个默认值，则可以使用`deafultdict`

```py
from collections import defaultdict

dedict = defaultdict({'key':'value'},lambda: 'not found')

dedict['key'] = 'value'
dedict['key']
dedict['nokey']
```

## OrderedDict
在`dict`中，`key`是无序的，所以当我们对`dict`做迭代时顺序是不确定的。当我们需要一个确定的顺序时可以使用`OrderedDict`。`OrderedDict`中`key`的排序是按照插入顺序排序。   

## Counter
`Counter`是一个简单的计数器，并且只存储次数大于一的元素，否则可以访问，但是不存储。

```py
from collections import Counter

c = Counter()
c['d']

for str in 'mycounter':  
	c[str] += 1
c
```

# 特殊方法
>通常我们无需直接使用特殊方法，直接调用特殊方法的频率应该远远低于我们去实现他们的次数。通过内置函数（例如`len`、`iter`、`str`，等等）来使用特殊方法是最好的选择。这些内置函数不仅会调用特殊方法，通常还提供额外的好处，而且对于内置的类来说，他们的速度更快。	————《Fluent Python》

## `__getitem__`
`__getitem__`方法的实现使得我们可以像`p[key]`这样来取值。并且我们通过索引来访问元素时也是使用了`__getitem__`方法。
## `__len__`
当我们使用`len(obj)`函数来获取一个对象的长度时，便是调用了`__len__`方法。

## `__repr__` or `__str__`
简单来说这两个方法都是用于显示的，但是`__str__`是面向用户的，`__repr__`是面向程序员的。   
在使用`str()`或者是`print()`时候会首先调用`__str__`方法，并且这种方法返回的字符串对终端用户更友好。在没有实现`__str__`方法时，两个函数回去调用`__repr__`方法。如果两个方法二选一去实现，显而易见后者是我们的选择。

## `__enter__` and `__exit__`
用于上下文管理，简单讲解见[这里](https://darren2017.github.io/2018/09/05/Flask-个人进阶/#上下文)。

## Others
其他的特殊方法还有很多，与以上集中特殊方法各有异同，能力有限，时间有限不做过度描述。
