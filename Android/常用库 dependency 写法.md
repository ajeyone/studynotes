# 常用库 dependency 写法

官网查询androidx各个库最新版本：https://developer.android.com/jetpack/androidx/versions#version-table

## AppCompact - AndroidX

```groovy
implementation 'androidx.appcompat:appcompat:1.1.0'
```

## ConstraintLayout - AndroidX
```groovy
implementation 'androidx.constraintlayout:constraintlayout:1.1.3'
```

## RecyclerView - AndroidX
```groovy
implementation 'androidx.recyclerview:recyclerview:1.1.0'
```

## 最后一个 support 库：28.0.0
```groovy
implementation 'com.android.support:support-compat:28.0.0'
implementation 'com.android.support:appcompat-v7:28.0.0'
implementation 'com.android.support:support-fragment:28.0.0'
implementation 'com.android.support:recyclerview-v7:28.0.0'
implementation 'com.android.support:design:28.0.0'
```

## ButterKnife
### app module
```groovy
android {
  ...
  // Butterknife requires Java 8.
  compileOptions {
    sourceCompatibility JavaVersion.VERSION_1_8
    targetCompatibility JavaVersion.VERSION_1_8
  }
}
dependencies {
  implementation 'com.jakewharton:butterknife:10.1.0'
  annotationProcessor 'com.jakewharton:butterknife-compiler:10.1.0'
}
```
### lib module
```groovy
buildscript {
  repositories {
    google()
    jcenter()
   }
  dependencies {
    classpath 'com.android.tools.build:gradle:3.4.0'
    classpath 'com.jakewharton:butterknife-gradle-plugin:10.1.0'
  }
}
apply plugin: 'com.jakewharton.butterknife'
```

## RxAndroid RxJava
```groovy
implementation 'io.reactivex.rxjava2:rxandroid:2.1.1'
implementation 'io.reactivex.rxjava2:rxjava:2.2.8'
```

## Retrofit + Gson + RxJava
```groovy
implementation "com.squareup.retrofit2:retrofit:2.5.0"
implementation 'com.google.code.gson:gson:2.8.5'
implementation "com.squareup.retrofit2:converter-gson:2.5.0"
implementation 'io.reactivex.rxjava2:rxandroid:2.1.1'
implementation 'io.reactivex.rxjava2:rxjava:2.2.8'
implementation "com.squareup.retrofit2:adapter-rxjava:2.5.0"
```

## Glide
```groovy
implementation 'com.github.bumptech.glide:glide:4.9.0'
annotationProcessor 'com.github.bumptech.glide:compiler:4.9.0'
```