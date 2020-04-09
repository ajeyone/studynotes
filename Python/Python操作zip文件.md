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