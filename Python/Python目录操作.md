## 路径

### 拼接路径

```python
os.path.join('path/to/a/dir', '*')
```

### 获得绝对路径
```python
os.path.abspath('.')
```

### 展开用户目录

如果路径第一个字符是"~"，则按用户目录规则展开，如果不是则原样返回，可以用来先处理一下路径，再传给其他路径方法

```python
path = os.path.expanduser(path)
```

### 获得文件(夹)名

```python
baseName = os.path.basename(path)
```



## 属性

### 文件或目录是否存在

```python
if os.path.exists(dir):
    print('file exists')
```

### 是文件还是目录
```python
os.path.isdir(path)
os.path.isfile(path)
```



## 遍历

### 遍历目录，无递归

```python
fileList = os.listdir(dir)
for f in fileList:
    print("file name:", f)
```

### 通配符列出目录中的文件，无递归

```python
import glob
dir = 'path/to/a/dir/*'
# 发生错误不返回错误值，只是遍历为空
filenames = glob.glob(dir)
for filename in filenames:
    print('filename:', filename)
```

### 通配符列出目录中的文件，递归，`**` 表示任意路径
```python
import glob
h_files = glob.glob('Rememtext/**/*.h', recursive=True)
for h_file in h_files:
    print(h_file)
# 输出示例
# Rememtext/AppDelegate.h
# Rememtext/Views/BlockManager.h
```

pattern 参数中的 `**/*.h` 表示 0 ~ n 个任意目录，可以匹配当前目录下的 "a.h"，也可以匹配很多层目录的 "a/b/c/d.h"。

返回的 list 中的路径字符串与传入的参数相关，如果传入参数是相对路径，返回的也都是相对路径；如果传入参数是绝对路径，返回的也都是相对路径。无论是哪种路径，都是可以直接传给 `open()` 方法的正确的路径。

### glob.glob() 与 glob.iglob() 的区别

`glob`：返回的是 list，可以用 len() 方法判断数量

`iglob`：返回的是 iterator，表示流数据，没法在不读取所有数据的前提下获得数量

