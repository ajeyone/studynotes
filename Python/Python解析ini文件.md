## ini 文件格式

ini 文件内容由多个 section 组成。每个 section 以 `[SectionKey]` 开头，可以定义若干 option，就是一个键值对。示例如下：

```ini
[mysql]
host = localhost
user = user7
passwd = s$cret
db = ydb

[postgresql]
host = localhost
user = user8
passwd = mypwd$7
db = testdb
```

上面的 ini 文件定义了两个 section：`mysql` 和 `postgresql`，每个 section 又定义了 4 个 option。

## ConfigParser

### 基本使用

import

```python
import configparser
```

打开 ini 文件

```python
config = configparser.ConfigParser()
config.read('db.ini', 'utf-8')
```

这个库有个问题，`ConfigParser.read` 方法居然不检查文件是否存在，最好在使用前自己检查一下

```python
if not os.path.exists('db.ini'):
    raise Exception("ini file not exists!")
```

读取具体的值直接用索引的方式

```python
host = config['mysql']['host']
user = config['mysql']['user']
passwd = config['mysql']['passwd']
db = config['mysql']['db']
```

### 异常处理

可用 `has_section()` 方法或者 `in` 操作符判断 section 是否存在

```python
if config.has_section('mysql'):
    print(config['mysql']['host'])
if 'mysql' in config:
  	print(config['mysql']['host'])
```

option 是否存在也可以使用 `in` 操作符

```python
if 'mysql' in config and 'host' in config['mysql']:
		print(config['mysql']['host'])
```

如果 section 使用索引方式访问，但实际不存在时，会抛异常

```python
host = config['sqlserver']['host']
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/local/Cellar/python@3.9/3.9.10/Frameworks/Python.framework/Versions/3.9/lib/python3.9/configparser.py", line 963, in __getitem__
    raise KeyError(key)
KeyError: 'x'
```

只要没有找到 section 或 option，基本都抛异常，如果不想处理异常只能事先判断。

### 官方文档

https://docs.python.org/3/library/configparser.html