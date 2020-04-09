## MainActivity 打开另一个 SecondActivity

```java
// 打开 App
MainActivity.onCreate()
MainActivity.onStart()
MainActivity.onResume()
// 点击按钮
MainActivity.onPause()
SecondActivity.onCreate()
SecondActivity.onStart()
SecondActivity.onResume()
MainActivity.onSaveInstanceState()
MainActivity.onStop()
// 按 back 键
SecondActivity.onPause()
MainActivity.onRestart()
MainActivity.onStart()
MainActivity.onResume()
SecondActivity.onSaveInstanceState()
SecondActivity.onStop()
SecondActivity.onDestroy()  // 注意会 onDestroy
```

总结：
- 切换 Activity 总是会先调原 Activity 的onPause 方法将其变成不可见。
- 然后再调用另一个 Activity 的唤醒流程：如果是新的就从 onCreate 开始，如果是之前的就从 onRestart 开始
- onSaveInstanceState: begin to stop: onStop() 之前调用
- onRestoreInstanceState: 没有被系统杀死不会调用


## 什么情况下只调用 onPause 不调用 onStop

- 弹出对话框不行，什么触发 Activity 的任何生命周期方法，只有 Activity 覆盖在上面才行。
- Activity 要继承 `android.app.Activity`
- 给 Activity 使用 Dialog style 的主题，例如 `@android:style/Theme.Dialog`

## onSaveInstanceState, onRestoreInstanceState

- 系统杀死 Activity 的时候，给 Activity 一个保存内部状态的机会。
- 调用时机：
  - `onSaveInstanceState` 在 `onStop()` 之前
  - `onRestoreInstanceState` 在 `onStart()` 之后，只有真的是被系统杀掉的才会调用。比如横竖屏切换重建 Activity 的时候。
- 系统默认实现版本，恢复内建的各种 View 的状态，例如 EditView 用户输入的文字。
  - 每个系统的 View 都有 `onSaveInstanceState` 和 `onRestoreInstanceState` 方法来保存和恢复自己的状态。

## 直接内嵌 Fragment 的 Activity，两者生命周期的先后关系和顺序

```java
// 点击按钮进入 Activity
Fragment.onAttach()
Fragment.onCreate()
Fragment.onCreateView()
Fragment.onViewCreated()
Activity.onCreate()
Fragment.onActivityCreated()
Fragment.onStart() // 这两个很奇怪，应该是 Activity 在前面
Activity.onStart() // 但在华为荣耀8C上是 Fragment 在前面
Activity.onResume()
Fragment.onResume()
// 按 back 返回上一个 Activity
Fragment.onPause()
Activity.onPause()
Fragment.onStop()
Activity.onStop()
Activity.onSaveInstanceState()
Fragment.onDestroyView()
Fragment.onDestroy()
Fragment.onDetach()
Activity.onDestroy()
```

## menu 的创建时机
```java
Activity.onCreate()
Activity.onStart()
Activity.onResume()
Activity.onCreateOptionsMenu()
Activity.onPrepareOptionsMenu()
```
- `onCreateOptionsMenu()` 在整个生命周期中只调用一次，除非调用了 `invalidateOptionsMenu()`，才会再次触发 `onCreateOptionsMenu()`
- `onPrepareOptionsMenu()` 一定会跟在 `onCreateOptionsMenu()` 后面调用一次
- `onPrepareOptionsMenu()` 每次点击“…”都会调用

```java
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    getMenuInflater().inflate(R.menu.main, menu);
    return super.onCreateOptionsMenu(menu);
}
```