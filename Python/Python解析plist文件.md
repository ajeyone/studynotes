使用 plistlib 库解析 Xcode 使用的 plist 文件

下面代码输出Rememtext项目的版本号 

```python
#!/usr/local/bin/python3
import plistlib as plist
with open('Rememtext/Info.plist', 'rb') as fp:
  pl = plist.load(fp)
print(pl['CFBundleShortVersionString'] + '_' + pl['CFBundleVersion'])
# 0.2.1_111
```