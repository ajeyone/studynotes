# 语法

## 三元表达式

Python里面没有三元运算符：`condition ? true_value : false_value`

```py
value = 'positive' if x > 0 else 'non-positive'
```

## 切割字符串
无参数默认是按空白字符分割，而且结果中不会出现空字符串，也就是连续的分隔符会当做一个。
```py
'a   b   c'.split() # ['a', 'b', 'c']
```

添加了参数就会严格按照参数中的分隔符进行分割，如果原字符串中有连续的分隔符，结果中就会出现空字符串。
```py
'a   b   c'.split(' ') # ['a', '', '', 'b', '', '', 'c']
```

分割空字符串。只要记住无参数版本不能返回空字符串，就好理解分割空字符串的返回值了。注意，分隔符是不允许空字符串的，否则会异常。
```py
''.split() # [] 无参数时结果是空数组
''.split(' ') # [''] 有参数时结果是只有一个空字符串的数组
```


## 判断None对象
```python
if x is None:
    print('x is none')
```

## 创建 list 并添加元素
```python
numbers = []
for i in range(0, 10):
    numbers.append(i)
```

## 判断当前 xxx.py 文件是否是直接 python xxx.py 执行的
```python
# 由于 python 文件可以被当做模块 import
# 被 import 与被直接执行是可以用下面的语句判断出来的
if __name__ == "__main__":
    print('run directly')
```

# 基础库

## 闭区间随机整数
```python
import random
x = random.randint(minValue, maxValue)
# minValue <= x <= maxValue
```

