## 要点

1. **参数的默认值只会计算一次，也就是在系统把定义函数的那个模块加载进来的时候**。所以，如果**默认值将来可能由调用方修改（例如{}、[]）或者要随着调用时的情况变化（例如datetime.now()），那么程序就会出现奇怪的效果**。
2. 如果关键字参数的默认值属于这种会发生变化的值，那就应该写成None，并且要在docstring里面描述函数此时的默认行为。
3. 默认值为None的关键字参数，也可以添加类型注解。

```python
def log(message, when=None):
    """Log a message with a timestamp.

    Args:
        message: Message to print.
        when: datetime of when the message occurred.
            Defaults to the present time.
    """
    if when is None:
        when = datetime.now()
    print(f'{when}: {message}')


log('Hi there!')
sleep(0.1)
log('Hello again!')
```