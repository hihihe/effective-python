## 要点

1. 可以把try/finally逻辑封装到情境管理器里面，这样就能通过with结构反复运用这套逻辑，而不需要每次用到的时候，都手工打一遍代码。
2. Python内置的contextlib模块提供了contextmanager修饰器，让我们可以很方便地修饰某个函数，从而制作出相对应的情境管理器，使得这个函数能够运用在with语句里面。
3. 情境管理器通过yield语句所产生的值，可以由with语句之中位于as右侧的那个变量所接收，这样的话，我们就可以通过该变量与当前情境相交互了。

如果想让其他的对象跟函数，也能像Lock这样用在with语句里面，那么可以通过**内置的contextlib模块来实现**。这个模块**提供了contextmanager修饰器**（参见第26条），它可以使没有经过特别处理的普通函数也能受到with语句支持。这要比标准做法简单得多，因为那种做法必须定义新类并实现名为\_\_enter\_\_与\_\_exit\_\_的特殊方法。

```python
from contextlib import contextmanager
import logging

logging.getLogger().setLevel(logging.WARNING)

def my_function():
    logging.debug('Some debug data')
    logging.error('Error log here')
    logging.debug('More debug data')
    
    
@contextmanager
def debug_logging(level):
    logger = logging.getLogger()
    old_level = logger.getEffectiveLevel()
    logger.setLevel(level)
    try:
        yield
    finally:
        logger.setLevel(old_level)


with debug_logging(logging.DEBUG):
    print('* Inside:')
    my_function()

print('* After:')
my_function()
```

