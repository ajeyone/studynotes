# ADB 命令

## 常用命令

通过网络连接设备，先要保证在同一个局域网内。

```shell
adb connect 127.0.0.1:60025
# 指定 IP 和端口号，如果端口号是 5555 可以省略
```

检查当前连接的设备

```shell
adb devices
```

断开单个设备连接

```shell
adb disconnect 127.0.0.1:60025
# IP 和端口号同 adb connect 命令，或者可以直接复制 adb devices 命令输出的条目
```

断开所有设备

```shell
adb disconnect
```

安装apk

```shell
adb install -r app.apk
# -r 表示覆盖安装，如果没安装过也可以；如果已安装，不加 -r 会提示失败。
```

抓 Log 保存到文件

```sh
set OUT=%1
if "%OUT%" == "" set OUT=log.txt
adb logcat -v threadtime > %OUT% 2>&1
```



## 针对应用的命令

卸载apk，传入的是**包名**

```shell
adb uninstall <package>
```

清理缓存

```
adb shell pm clear <package>
```

强制关闭应用

```shell
adb shell am force-stop <package>
```

打开 Activity，需要传入包名和类名，可以省略类名中与包名相同的前缀

```shell
adb shell am start <package>/<activity_class_name>
```



## 查询命令

查询已安装应用的版本号

```shell
# windows
adb shell pm dump <package> | findstr version
# mac/linux
adb shell pm dump <package> | grep version
```

查询设备 API Level

```shell
adb shell getprop ro.build.version.sdk
```

查询已安装的所有 package 信息，该命令输出内容太多，建议重定向到文件里查看，或直接过滤内容

```shell
adb shell dumpsys package > dump.txt
adb shell dumpsys package | grep <anything>
```

查询所有系统属性（ROM版本号、厂家、品牌等）

```sh
adb shell cat /system/build.prop
```

查询最顶层 Activity

```sh
# windows
adb shell dumpsys activity | findstr "mFocusedActivity"
# mac/linux
adb shell dumpsys activity | grep "mFocusedActivity"
```



## ROM 相关

root 设备，只有在模拟器或者开发版 ROM 有效

```sh
adb root
```

开启系统目录写入权限

```sh
adb remount
```

