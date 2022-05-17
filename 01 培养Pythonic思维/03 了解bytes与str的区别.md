## Python有两种类型可以表示字符序列

1. 一种是bytes，另一种是str。bytes实例包含的是原始数据，即8位的无符号值（通常按照ASCII编码标准来显示）。
2. str实例包含的是Unicode码点（code point，也叫作代码点），这些码点与人类语言之中的文本字符相对应。

str实例不一定非要用某一种固定的方案编码成二进制数据，bytes实例也不一定非要按照某一种固定的方案解码成字符串。要把Unicode数据转换成二进制数据，必须调用str的encode方法。要把二进制数据转换成Unicode数据，必须调用bytes的decode方法。调用这些方法的时候，可以明确指出自己要使用的编码方案，也可以采用系统默认的方案，通常是指UTF-8。

str(unicode)------encode()----------bytes(二进制)      **字符串编码为二进制**

bytes(二进制)----decode()---------str(unicode)          **二进制解码为字符串**

## 文件读取

> 从文件中读取二进制数据（或者把二进制数据写入文件）时，应该用'rb'（'wb'）这样的二进制模式打开文件。

## to_str

```python
def to_str(bytes_or_str):
    if isinstance(bytes_or_str, bytes):
        value = bytes_or_str.decode('utf-8')
    else:
        value = bytes_or_str
    return value  # Instance of str

print(repr(to_str(b'foo')))
print(repr(to_str('bar')))
```

## to_bytes

```python
def to_bytes(bytes_or_str):
    if isinstance(bytes_or_str, str):
        value = bytes_or_str.encode('utf-8')
    else:
        value = bytes_or_str
    return value  # Instance of bytes

print(repr(to_bytes(b'foo')))
print(repr(to_bytes('bar')))
```

> repr() 函数将对象转化为供解释器读取的形式。返回一个对象的 string 格式。