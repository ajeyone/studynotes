## 修改 apk 生成目录和文件名

```groovy
applicationVariants.all { variant ->
    // 过滤，只处理 release 版本
    if (variant.buildType.name.equals('release')) {
        variant.outputs.all { output ->
            def buildName = "Downloader"
            def type = variant.buildType.name
            def releaseApkName = buildName + '_' + type + "_" + versionName + '_' + getTime() + '.apk'
            outputFileName = releaseApkName
            variant.packageApplication.outputDirectory = new File("./apk")
        }
    }
}

applicationVariants // variant 集合
variant.outputs // 输出文件集合

```
