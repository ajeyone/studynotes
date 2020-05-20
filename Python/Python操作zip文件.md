## 读取 jar 文件中的 class 文件
```python
import glob
import zipfile
import os

path = '/Users/junjiewang/work/Kiwifruit/**/*.jar'
h_files = glob.glob(path, recursive=True)
for h_file in h_files:
    z = zipfile.ZipFile(h_file)
    list1 = z.namelist()
    classFile = ''
    for name in list1:
        if name.endswith('.class'):
            classFile = name
            break
    if classFile != '':
        print(h_file)
        print('class:', classFile)
        className = classFile[0:len(classFile)-6]
        cmd = 'javap -v -cp %s %s' % (h_file, className)
        print('cmd:', cmd)
        print('run result: ', os.system(cmd))
```

## 解压 apk 文件中的特定文件
```python
def extractAssetFile(apkPath):
    with zipfile.ZipFile(apkPath, 'r') as apkzip:
        # 第一个路径是 zip 文件内部的路径，第二个路径是文件系统路径，注意第二个路径+第一个路径才是最终解压后的文件路径
        # 解压后文件路径为：app/src/main/assets/app.config
        apkzip.extract('assets/app.config', 'app/src/main/')
```