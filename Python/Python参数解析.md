## 原生方法
```python
import sys
# 至少有 1 个参数就是执行的 py 文件
print('参数个数为:', len(sys.argv))
print('参数列表:', sys.argv)
```

## 复杂参数解析 argparse
```python
import argparse
parser = argparse.ArgumentParser(description='this is a good tool.')
parser.add_argument('--posdir', help='positive images', default='posdir')
parser.add_argument('--negdir', help='negative images', default='negdir')
parser.add_argument('--testdir', help='test images', default='testdir')
args = parser.parse_args()
args.posdir # 直接使用 '--posdir' 中写的字符串就能引用
args.negdir
```
