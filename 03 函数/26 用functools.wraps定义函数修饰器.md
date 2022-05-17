## 要点
1. 修饰器是Python中的一种写法，能够把一个函数封装在另一个函数里面，这样程序在执行原函数之前与执行完毕之后，就有机会执行其他一些逻辑了。
2. 修饰器可能会让那些利用introspection机制运作的工具（例如调试器）产生奇怪的行为。
3. Python内置的functools模块里有个叫作wraps的修饰器，可以帮助我们正确定义自己的修饰器，从而避开相关的问题。

## 异常处理装饰器

```python
def catch_exceptions(job_func):
    @functools.wraps(job_func)
    def wrapper(*args, **kwargs):
        try:
            return job_func(*args, **kwargs)
        except:
            import traceback
            print(traceback.format_exc())
    return wrapper
```

