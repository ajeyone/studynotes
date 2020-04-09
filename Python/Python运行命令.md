## 使用 subprocess 执行命令

```python
import subprocess
cmd = ['ls', '-al'] # 这个必须是数组
p = subprocess.Popen(cmd, stdout=subprocess.PIPE, stderr=subprocess.STDOUT)
print(p.stdout.read())
```

`subprocess.PIPE` 可以赋值给这几个参数 
- stdin
- stdout
- stderr

将输入输出流转移到代码中处理