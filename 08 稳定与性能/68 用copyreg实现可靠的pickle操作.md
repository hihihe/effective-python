Python内置的pickle模块可以将对象序列化成字节流，也可以把字节流反序列化（还原）成对象。经过pickle处理的字节流，只应该在彼此信任的双方之间传输，而不应该随意传给别人，或者随意接受别人发来的这种数据，因为pickle的本意只是提供一种数据传输手段，让你在自己可以控制的程序之间传递二进制形式的Python对象。



pickle模块所使用的这种序列化格式本身就没有考虑过安全问题。这种格式会把原有的Python对象记录下来，让系统可以在稍后予以重建。这意味着，假如记录的这个对象本身含有恶意行为，那么通过反序列化还原出来之后，就有可能破坏整个程序。
跟pickle不同，json模块考虑到了安全问题。序列化之后的JSON数据表示的只不过是一套对象体系而已，把这样的数据反序列化不会给程序带来风险。如果要在彼此不信任的两个人或两个程序之间传递数据，那么应该使用JSON这样的格式。

## json

### JSON基本语法
- JSON 规定字符集是UTF-8，字符串必须使用""，数据的名称也必须使用""，**不能使用单引号 ''**
- **数组或对象的最后一个成员，不能加逗号**

### 序列化与反序列化

1. **序列化：将python的值转换为json格式的字符串**。
2. 反序列化：将json格式的字符串转换成python的数据类型

### 把json文本转换为python可以操作的数据结构

```python
import json

# 转换文件
fp = file('test.txt', 'r')
s1 = json.load(f)

#　转换字符串
s2 = json.loads(str, strict=False)
```

### 转换为json数据存储

dump需要一个类似于文件指针的参数（并不是真的指针，可称之为类文件对象），可以与文件操作结合，也就是说可以将dict转成str然后存入文件中；而dumps直接给的是dict，也就是将字典转成str。

```python
import json

# 存储为json文件
fp = file('test.txt', 'w')
json.dump(dict, fp)


#　将字典序列化为json格式数据
res = json.dumps(dict, separators=(',', ':'), ensure_ascii=False)
```

## 要点

1. Python内置的pickle模块，只适合用来在彼此信任的程序之间传递数据，以实现对象的序列化与反序列化功能。
2. 如果对象所在的这个类发生了变化（例如增加或删除了某些属性），那么程序在还原旧版数据的时候，可能会出现错误。
3. 把内置的copyreg模块与pickle模块搭配起来使用，可以让新版的程序兼容旧版的序列化数据。
