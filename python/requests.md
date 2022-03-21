# POST请求发送相同字段名的多个值

我们一般发送POST请求时一般是将字典转的key-value映射为form表单的字段名和值；但是如果一个form字段对应多个值，该怎样构建POST请求的body题呢？

1、字典中定义多个相同的key，只有后一个值是有效的；所以考虑将字典中的值改为列表形式

2、字典中的列表形式值，无法使用`urlencode`直接转换为对应请求头

3、`urlencode`除了接受字典值意外，还可以接受一个由两元素的元组组成的序列 `a sequence of two-elementes tuples`；

4、考虑编写一个中将函数将字典转换成一个两元素的元组序列，如果字典中元素的值为列表，则遍历列表拆分成多个相同key的两元素的元组序列。

```
def encode_param(data: dict):
    result = []
    for k, vs in data.items():
        if isinstance(vs, str) or not hasattr(vs, '__iter__'):
            vs = [vs]
        for v in vs:
            if v is not None:
                result.append((k.encode('utf-8') if isinstance(k, str) else k,
                               v.encode('utf8') if isinstance(v, str) else v))
    return urlencode(result, doseq=True)


if __name__ == '__main__':
    print(encode_param({'id': 1, 'status': [201, 202]}))
```

