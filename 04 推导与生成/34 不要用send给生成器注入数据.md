## 要点

1. send方法可以把数据注入生成器，让它成为上一条yield表达式的求值结果，生成器可以把这个结果赋给变量。
2. 把send方法与yield from表达式搭配起来使用，可能导致奇怪的结果，例如会让程序在本该输出有效值的地方输出None。
3. 通过迭代器向组合起来的生成器输入数据，要比采用send方法的那种方案好，所以尽量避免使用send方法。

[Python生成器中的send()与next()方法解析](https://blog.csdn.net/qq_39521554/article/details/79864889)
