## 基本用法

```py
try:
    fp = open('json.txt')
    text = fp.read()
    print(text)
except:
    print('something wrong')
else:
    print('no exception')
```

其他见简单教程：

http://www.runoob.com/python/python-exceptions.html