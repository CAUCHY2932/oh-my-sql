# python问题50

> 吕阳阳 于 2019-10-01

## 前言

python是存在最佳实践的，我们规定语法，规范，和某些常用的写法，是有利于我们进行学习的。

## 基础

### Q01.打印一句话？

```python
print("hello")
```

### Q02.打开文件

```python
# encoding指定编码，mode指定模式
with open(filename, encoding='utf-8', mode='r')as f:
  fr = f.read()
```

### Q03.处理字符串



### Q04.函数带不带括号

当函数带括号，我们是在进行调用，而不带括号时，我们只是将它当作了变量名，传递给函数或是赋值给其他变量使用，当我们调用时，在后面加上括号和变量列表（如果有），就可以实现调用

### Q05.返回一个列表后n位？

```python
lst = list("hello world!")
back = lst[-9:]
```

### Q06.使用列表推导式

```python
lst = [x for x in range(10)]
```

### Q07.map的使用

```python

```

