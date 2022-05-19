Python内置的itertools模块里有很多函数，可以用来安排迭代器之间的交互关系（相关的基础知识，参见第30条与第31条）。

```python
import itertools
```

如果要实现比较难写的迭代逻辑，那么应该先查看itertools的文档（在Python解释器界面输入help(itertools)），说不定里面就有你能用到的函数。下面分三大类，列出其中最重要的函数。(使用help()查看某个模块之前，应该先导入这个模块。)

## 连接多个迭代器

### chain

chain可以把多个迭代器从头到尾连成一个迭代器。

```bash
>>> it = itertools.chain([1, 2, 3], [4, 5, 6])
>>> print(list(it))
[1, 2, 3, 4, 5, 6]
```

### repeat

repeat可以制作这样一个迭代器，它会不停地输出某个值。调用repeat时，也可以通过第二个参数指定迭代器最多能输出几次。

```bash
>>> it = itertools.repeat('hello', 3)
>>> print(list(it))
['hello', 'hello', 'hello']
```

### cycle

cycle可以制作这样一个迭代器，它会循环地输出某段内容之中的各项元素。

```bash
>>> it = itertools.cycle([1, 2])
>>> result = [next(it) for _ in range (10)]
>>> print(result)
[1, 2, 1, 2, 1, 2, 1, 2, 1, 2]
```

### tree

tee可以让一个迭代器分裂成多个平行的迭代器，具体个数由第二个参数指定。如果这些迭代器推进的速度不一致，那么程序可能要用大量内存做缓冲，以存放进度落后的迭代器将来会用到的元素。

```bash
>>> it1, it2, it3 = itertools.tee(['first', 'second'], 3)
>>> print(list(it1))
['first', 'second']
>>> print(list(it2))
['first', 'second']
>>> print(list(it3))
['first', 'second']
```

### zip_longest

它与Python内置的zip函数类似（参见第8条），但区别在于，如果源迭代器的长度不同，那么它会用fillvalue参数的值来填补提前耗尽的那些迭代器所留下的空缺。

```bash
>>> keys = ['one', 'two', 'three']
>>> values = [1, 2]
>>>
>>> normal = list(zip(keys, values))
>>> print('zip:        ', normal)
zip:         [('one', 1), ('two', 2)]
>>>
>>> it = itertools.zip_longest(keys, values, fillvalue='nope')
>>> longest = list(it)
>>> print('zip_longest:', longest)
zip_longest: [('one', 1), ('two', 2), ('three', 'nope')]
```

## 过滤迭代器中的元素

### islice

islice可以在不拷贝数据的前提下，按照下标切割源迭代器。**可以只给出切割的终点，也可以同时给出起点与终点，还可以指定步进值。**这种切割方式与标准的序列切片及步进机制类似（参见第11条与第12条）。

```bash
>>> values = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]            
>>>                                                     
>>> first_five = itertools.islice(values, 5)            
>>> print('First five: ', list(first_five))             
First five:  [1, 2, 3, 4, 5]                            
>>>                                                     
>>> middle_odds = itertools.islice(values, 2, 8, 2)     
>>> print('Middle odds:', list(middle_odds))            
Middle odds: [3, 5, 7]                                  
```

### takewhile

takewhile会一直从源迭代器里获取元素，直到某元素让测试函数返回False为止。

```bash
>>> values = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
>>> less_than_seven = lambda x: x < 7
>>> it = itertools.takewhile(less_than_seven, values)
>>> print(list(it))
[1, 2, 3, 4, 5, 6]
```

### dropwhile

与takewhile相反，dropwhile会一直跳过源序列里的元素，直到某元素让测试函数返回True为止，然后它会从这个地方开始逐个取值。

```bash
>>> values = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
>>> less_than_seven = lambda x: x < 7
>>> it = itertools.dropwhile(less_than_seven, values)
>>> print(list(it))
[7, 8, 9, 10]
```

### filterfalse

filterfalse和内置的filter函数相反，它会逐个输出源迭代器里使得测试函数返回False的那些元素。

```bash
>>> values = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
>>> evens = lambda x: x % 2 == 0
>>>
>>> filter_result = filter(evens, values)
>>> print('Filter:      ', list(filter_result))
Filter:       [2, 4, 6, 8, 10]
>>>
>>> filter_false_result = itertools.filterfalse(evens, values)
>>> print('Filter false:', list(filter_false_result))
Filter false: [1, 3, 5, 7, 9]
```

## 用源迭代器中的元素合成新元素

### accumulate

accumulate会从源迭代器里取出一个元素，并把已经累计的结果与这个元素一起传给表示累加逻辑的函数，然后输出那个函数的计算结果，并把结果当成新的累计值。

```bash
>>> values = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
>>> sum_reduce = itertools.accumulate(values)
>>> print('Sum:   ', list(sum_reduce))
Sum:    [1, 3, 6, 10, 15, 21, 28, 36, 45, 55]
>>>
>>> def sum_modulo_20(first, second):
...     output = first + second
...     return output % 20
...
>>> modulo_reduce = itertools.accumulate(values, sum_modulo_20)
>>> print('Modulo:', list(modulo_reduce))
Modulo: [1, 3, 6, 10, 15, 1, 8, 16, 5, 15]
```

这与内置的functools模块中reduce函数，实际上是一样的，只不过这个函数每次只给出一项累计值。如果调用者没有指定表示累加逻辑的双参数函数（binary function），那么默认的逻辑就是两值相加。

> **reduce()** 函数会对参数序列中元素进行累积。
>
> 函数将一个数据集合（链表，元组等）中的所有数据进行下列操作：用传给 reduce 中的函数 function（有两个参数）先对集合中的第 1、2 个元素进行操作，得到的结果再与第三个数据用 function 函数运算，最后得到一个结果。
>
> `reduce(function, iterable[, initializer])`
>
> ```python
> from functools import reduce
> 
> def add(x, y) :            # 两数相加
>     return x + y
> sum1 = reduce(add, [1,2,3,4,5])   # 计算列表和：1+2+3+4+5
> sum2 = reduce(lambda x, y: x+y, [1,2,3,4,5])  # 使用 lambda 匿名函数
> print(sum1)
> print(sum2)
> 
> # 以上实例输出结果为：
> 15
> 15
> ```

### product

product会从一个或多个源迭代器里获取元素，并计算笛卡尔积（Cartesian product），它可以取代那种多层嵌套的列表推导代码（参见第28条）。

> 笛卡尔乘积是指在数学中，两个[集合](https://baike.baidu.com/item/集合)*X*和*Y*的笛卡尔积（Cartesian product），又称[直积](https://baike.baidu.com/item/直积)，表示为*X*×*Y*，第一个对象是*X*的成员而第二个对象是*Y*的所有可能[有序对](https://baike.baidu.com/item/有序对)的其中一个成员 。
>
> 假设集合A={a, b}，集合B={0, 1, 2}，则两个集合的笛卡尔积为{(a, 0), (a, 1), (a, 2), (b, 0), (b, 1), (b, 2)}。
>
> 类似的例子有，如果A表示某学校学生的集合，B表示该学校所有课程的集合，则A与B的笛卡尔积表示所有可能的选课情况。A表示所有声母的集合，B表示所有韵母的集合，那么A和B的笛卡尔积就为所有可能的汉字全拼。

```bash
>>> single = itertools.product([1, 2], repeat=2)
>>> print('Single:  ', list(single))
Single:   [(1, 1), (1, 2), (2, 1), (2, 2)]
>>>
>>> multiple = itertools.product([1, 2], ['a', 'b'])
>>> print('Multiple:', list(multiple))
Multiple: [(1, 'a'), (1, 'b'), (2, 'a'), (2, 'b')]
```

### permutations

permutations会考虑源迭代器所能给出的全部元素，并逐个输出由其中N个元素形成的每种有序排列（permutation）方式，元素相同但顺序不同，算作两种排列。

```bash
>>> from pprint import pprint
>>> it = itertools.permutations([1, 2, 3, 4], 2)
>>> pprint(list(it))
[(1, 2),
 (1, 3),
 (1, 4),
 (2, 1),
 (2, 3),
 (2, 4),
 (3, 1),
 (3, 2),
 (3, 4),
 (4, 1),
 (4, 2),
 (4, 3)]
```

### combinations

combinations会考虑源迭代器所能给出的全部元素，并逐个输出由其中N个元素形成的每种无序组合（combination）方式，元素相同但顺序不同，算作同一种组合。

```bash
>>> it = itertools.combinations([1, 2, 3, 4], 2)
>>> print(list(it))
[(1, 2), (1, 3), (1, 4), (2, 3), (2, 4), (3, 4)]
```

### combinations_with_replacement

combinations_with_replacement与combinations类似，但它允许同一个元素在组合里多次出现。

```bash
>>> it = itertools.combinations_with_replacement([1, 2, 3, 4], 2)
>>> print(list(it))
[(1, 1), (1, 2), (1, 3), (1, 4), (2, 2), (2, 3), (2, 4), (3, 3), (3, 4), (4, 4)]
```

