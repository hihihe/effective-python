除yield from表达式（参见第33条）与send方法（参见第34条）外，生成器还有一项高级功能，就是可以把调用者通过throw方法传来的Exception实例重新抛出。这个throw方法用起来很简单：如果调用了这个方法，那么生成器下次推进时，就不会像平常那样，直接走到下一条yield表达式那里，而是会把通过throw方法传入的异常重新抛出。下面用代码演示这种效果。

```python
try:
    class MyError(Exception):
        pass
    
    def my_generator():
        yield 1
        yield 2
        yield 3
    
    it = my_generator()
    print(next(it))  # Yield 1
    print(next(it))  # Yield 2
    print(it.throw(MyError('test error')))
except:
    logging.exception('Expected')
else:
    assert False
```

## 可重置计数器

编写一个偶尔可以重置的计时器程序。笔者定义下面的Reset异常与timer生成器方法，让调用者可以在timer给出的迭代器上通过throw方法注入Reset异常，令计时器重置。

用可迭代的容器对象（参见第31条）定义一个有状态的闭包（参见第38条）。下面的代码就写了这样一个Timer类，并通过它重新实现刚才的timer生成器。

```python
class Timer:
    def __init__(self, period):
        self.current = period
        self.period = period

    def reset(self):
        self.current = self.period

    def __iter__(self):
        while self.current:
            self.current -= 1
            yield self.current


RESETS = [
    False, False, True, False, True, False,
    False, False, False, False, False, False, False]
    
def check_for_reset():
    # Poll for external event
    return RESETS.pop(0)

def run():
    timer = Timer(4)
    for current in timer:
        if check_for_reset():
            timer.reset()
        announce(current)
```

凡是想用生成器与异常来实现的功能，通常都可以改用异步机制去做（参见第60条），那样会更好。如果确实遇到了这里讲到的这种需求，那么应该通过可迭代的类来实现生成器，而不要用throw方法注入异常。

## 要点

1. throw方法可以把异常发送到生成器刚执行过的那条yield表达式那里，让这个异常在生成器下次推进时重新抛出。
2. 通过throw方法注入异常，会让代码变得难懂，因为需要用多层嵌套的模板结构来抛出并捕获这种异常。
3. 如果确实遇到了这样的特殊情况，那么应该通过类的\_\_iter\_\_方法实现生成器，并且专门提供一个方法，让调用者通过这个方法来触发这种特殊的状态变换逻辑。

