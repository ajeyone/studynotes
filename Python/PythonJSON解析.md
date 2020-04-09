Python 自带的json库，可以转换json格式的字符串和dict类型的数据。

## import
```py
import json
```

## 从字符串解析
```py
jsonData = '{"a":1,"b":2,"c":3,"d":4,"e":5}'
d = json.loads(jsonData)
print(d) # {'a': 1, 'b': 2, 'c': 3, 'd': 4, 'e': 5}
print(type(d)) # <class 'dict'>
```

## 从文件解析
保存一个 json.txt 文件，内容是
```json
{"a":1,"b":2,"c":3,"d":4,"e":5}
```
```py
fp = open('json.txt')
d = json.load(fp)
print(d) # {'a': 1, 'b': 2, 'c': 3, 'd': 4, 'e': 5}
print(type(d)) # <class 'dict'>
```

## 生成JSON字符串
```py
s = json.dumps(d) # {"a": 1, "b": 2, "c": 3, "d": 4, "e": 5}
```

## 生成JSON字符串，直接保存为文本文件
```py
with open('json.txt', 'w') as fp3:
   json.dump(d, fp3)
```

## 生成有缩进排版的字符串
```py
json.dumps(d, indent=2)
```
输出：
```json
{
  "a": 1,
  "b": 2,
  "c": 3,
  "d": 4,
  "e": 5
}
```

---
## JSONDecodeError: invalid control character

一般是字符串里有 `\n` 这两个字符导致的，以下两种处理方式：
### 忽略，直接设置 `strict` 参数为 `False` 即可
```py
json.loads(jsonString, strict=False)
```
没太弄明白怎么复现这个。。。