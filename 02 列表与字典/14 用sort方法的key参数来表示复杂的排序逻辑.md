## 要点

- 可以把辅助函数传给sort方法的key参数，让sort根据这个函数所返回的值来排列元素顺序，而不是根据元素本身来排列。
- 如果排序时要依据的指标有很多项，可以把它们放在一个元组中，让key函数返回这样的元组。对于支持一元减操作符的类型来说，可以单独给这项指标取反，让排序算法在这项指标上按照相反的方向处理。
- 排序要依据多项指标，并且每项的顺序不一致(如按照重量从大到小、名称从小到大排序)。如果这些指标不支持一元减操作符，可以**多次调用sort方法**，并在每次调用时分别指定key函数与reverse参数。**最次要的指标放在第一轮处理**，然后逐步处理更为重要的指标，**首要指标放在最后一轮处理**。

我们可以把排序逻辑定义成函数，然后将这个**函数传给sort方法的key参数**。**key所表示的函数本身应该带有一个参数，这个参数指代列表中有待排序的对象，函数返回的应该是个可比较的值（也就是具备自然顺序的值），以便sort方法以该值为标准给这些对象排序。**

```python
# d
power_tools.sort(key=lambda x: x.name)   # Name ascending

power_tools.sort(key=lambda x: x.weight, # Weight descending
                 reverse=True)

print(power_tools)
```

## 利用元组的特性排序

元组是一种不可变的序列，能够存放任意的Python值。两个元组之间是可以比较的，因为这种类型本身已经定义了自然顺序，也就是说，sort方法所要求的特殊方法（例如\_\_lt\_\_方法），它都已经定义好了。元组在实现这些特殊方法时会依次比较每个位置的那两个对应元素，直到能够确定大小为止。

利用元组的这项特性，我们可以用工具的weight与name构造一个元组。下面就定义这样一个lambda函数，让它返回这种元组，把首要指标（也就是weight）写在前面，把次要指标（也就是name）写在后面。

```python
power_tools = [
    Tool('drill', 4),
    Tool('circular saw', 5),
    Tool('jackhammer', 40),
    Tool('sander', 4),
]

power_tools.sort(key=lambda x: (x.weight, x.name),
                 reverse=True)  # Makes all criteria descending
print(power_tools)
```

