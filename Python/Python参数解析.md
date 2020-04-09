## argparse
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
