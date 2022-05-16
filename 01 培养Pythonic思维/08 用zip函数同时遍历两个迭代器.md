1. 内置的zip函数可以同时遍历多个迭代器。
2. zip会创建惰性生成器，让它每次只生成一个元组，所以无论输入的数据有多长，它都是一个一个处理的。
3. 如果提供的迭代器的长度不一致，那么只要其中任何一个迭代完毕，zip就会停止。
4. 如果想按最长的那个迭代器来遍历，那就改用**内置的itertools模块中的zip_longest函数**。

```python
import itertools

for name, count in itertools.zip_longest(names, counts):
    print(f'{name}: {count}')
```

