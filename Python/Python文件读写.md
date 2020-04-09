
## 使用 with 简化异常处理
```python
with open('file_name','r') as f:
    r=f.read()
```

## open
| Character | Meaning |
| ------ | ------ |
| 'r' | open for reading (default) |
| 'w' | open for writing, truncating the file first |
| 'x' | open for exclusive creation, failing if the file already exists |
| 'a' | open for writing, appending to the end of the file if it exists |
| 'b' | binary mode |
| 't' | text mode (default) |
| '+' | open a disk file for updating (reading and writing) |
```python
open('file path') # 默认mode参数为 'r' 默认模式为文本，'t'
open('file path', 'rt') # 返回文本流对象
open('file path', 'rb') # 返回数据流对象 
```

## read
```python
file = open('xxxx')
file.read() # 返回所有内容，二进制返回bytes对象，文本返回str对象
file.readline() # 返回一行，str
file.readlines() # 返回行数组，[str]
```