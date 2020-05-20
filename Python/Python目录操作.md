## 拼接路径
```python
os.path.join('path/to/a/dir', '*')
```

## 获得绝对路径
```python
os.path.abspath('.')
```

## 文件或目录是否存在
```python
if os.path.exists(dir):
    print('file exists')
```

## 是文件还是目录
```python
os.path.isdir(path)
os.path.isfile(path)
```

## 通配符列出目录中的文件，没有递归
```python
import glob
dir = 'path/to/a/dir/*'
# 发生错误不返回错误值，只是遍历为空
filenames = glob.iglob(dir)
for filename in filenames:
    print('filename:', filename)
```

## 通配符列出目录中的文件，递归，`**` 表示任意路径
```python
import glob
h_files = glob.glob('Rememtext/**/*.h', recursive=True)
for h_file in h_files:
    print(h_file)
```

## 展开用户目录
如果路径第一个字符是"~"，则按用户目录规则展开，如果不是则原样返回，可以用来先处理一下路径，再传给其他路径方法
```python
path = os.path.expanduser(path)
```