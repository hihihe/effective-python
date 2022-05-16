```python
for i, flavor in enumerate(flavor_list):
    print(f'{i + 1}: {flavor}')

# 另外，还可以通过enumerate的第二个参数指定起始序号，这样就不用在每次打印的时候去调整了。例如，本例可以从1开始计算。
for i, flavor in enumerate(flavor_list, 1):
    print(f'{i}: {flavor}')
```

