## pyinstaller

使用 pip 安装

```shell
pip install pyinstaller
```

pyinstaller 是一个命令行工具，执行之前需要先进入 python 项目目录。即使只有一个文件，建议也创建一个目录存放，因为 pyinstaller 会生成几个目录，与其他文件不相关文件在一起容易混淆。

生成 exe 的命令：

```shell
pyinstaller -F -w -i python.ico watermark.py
```

参数详解：

| 参数 | 用法                                                         |
| :--- | :----------------------------------------------------------- |
| -F   | 生成结果是一个 exe 文件，所有的第三方依赖、资源和代码均被打包进该 exe 内 |
| -D   | 生成结果是一个目录，各种第三方依赖、资源和 exe 同时存储在该目录（默认） |
| -a   | 不包含unicode支持                                            |
| -d   | 执行生成的 exe 时，会输出一些log，有助于查错                 |
| -w   | 不显示命令行窗口                                             |
| -c   | 显示命令行窗口（默认）                                       |
| -p   | 指定额外的 import 路径，类似于使用 python path               |
| -i   | 指定图标                                                     |
| -v   | 显示版本号                                                   |
| -n   | 生成的 .exe 的文件名                                         |

执行完毕后生成的 exe 文件在 dist 文件夹中。

