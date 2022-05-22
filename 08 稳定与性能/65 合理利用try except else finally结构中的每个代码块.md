## try/finally形式

如果我们想确保，无论某段代码有没有出现异常，与它配套的清理代码都必须得到执行，同时还想在出现异常的时候，把这个异常向上传播，那么可以将这两段代码分别放在try/finally结构的两个代码块里面。

```python
def try_finally_example(filename):
    print('* Opening file')
    handle = open(filename, encoding='utf-8') # May raise OSError
    try:
        print('* Reading data')
        return handle.read()  # May raise UnicodeDecodeError
    finally:
        print('* Calling close()')
        handle.close()        # Always runs after try block
```

## try/except/else形式

如果你想在某段代码发生特定类型的异常时，把这种异常向上传播，同时又要在代码没有发生异常的情况下，执行另一段代码，那么可以使用try/except/else结构表达这个意思。如果try块代码没有发生异常，那么else块就会运行。try里面应该尽量少写一些代码，这样阅读起来比较清晰，而且即便出现异常，我们也能很快找到它是由哪一行代码引发的。例如，我们实现这样一个load_json_key函数，让它把data参数所表示的字符串加载成JSON字典，然后把key参数所对应的键值返回给调用方。

```python
import json

def load_json_key(data, key):
    try:
        print('* Loading JSON data')
        result_dict = json.loads(data)  # May raise ValueError
    except ValueError as e:
        print('* Handling ValueError')
        raise KeyError(key) from e
    else:
        print('* Looking up key')
        return result_dict[key]         # May raise KeyError
```

## 完整的try/except/else/finally形式

如果这四个代码块的功能全都要用到，那么可以编写完整的try/except/else/finally结构。例如，我们要把待处理的数据从文件里读出来，然后加以处理，最后把结果写回文件之中。在实现这个功能时，可以把读取文件并处理数据的那段代码写在try块里面，并用except块来捕获try块有可能抛出的某些异常。如果try块正常结束，那就在else块中把处理结果写回原来的文件（这个过程中抛出的异常会直接向上传播）。最后，程序无论是进入了except块，还是进入了else块，它都会在即将返回之前，先执行finally块以清理文件句柄。

## 要点

1. try/finally形式的复合语句可以确保，无论try块是否抛出异常，finally块都会得到运行。
2. 如果某段代码应该在前一段代码顺利执行之后加以运行，那么可以把它放到else块里面，而不要把这两段代码全都写在try块之中。这样可以让try块更加专注，同时也能够跟except块形成明确对照：except块写的是try块没有顺利执行时所要运行的代码。
3. 如果你要在某段代码顺利执行之后多做一些处理，然后再清理资源，那么通常可以考虑把这三段代码分别放在try、else与finally块里。

