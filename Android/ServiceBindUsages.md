## Service Bind/Unbind 使用方式

- 使用者 Activity 与 Service 关系紧密，绑定在一起，通过 Binder 对象进行通信。
- 绑定时如果服务没启动，则会执行初始化，但不会走 onStatCommand()。
- 一个 Service 可以被多个使用者绑定。
- 绑定 Service 和启动 Service 可以同时使用。
  - 举例：音乐播放器，可以通过Service在后台使用，开始播放 Start Service。在播放过程中要暂停、上一首、下一首等操作，使用 Bind Service 与其通信。
  - 同时使用兼具两者的特点，互相影响的地方是创建和销毁：没初始化时，只要有 Bind 或者 Create 就会初始化。Stop 不会导致 Binding 状态的服务销毁，同样 unbind 也不会导致已启动的服务销毁。
- 通常用在需要复杂的接口调用的 Service 上，可以用 AIDL 定义跨进程的 Service 调用。

## 代码套路

### 生命周期
```java
// 绑定服务
MyBindService()
MyBindService.onCreate()
MyBindService.onBind()
ServiceConnection.onServiceConnected()
// 解绑服务
MyBindService.onUnbind()
MyBindService.onDestroy()
```

### manifest 注册
```xml
<service
    android:name=".services.MyService"
    android:enabled="true"
    android:exported="false" />
```

### 代码
1. 定义
```java
import android.app.Service;
public class MyService extends Service {
}
```
2. 声明一个接口
```java
public interface MyIBinder {
    int calculate(int a, int b);
}
```
3. 创建一个Service的内部类继承 Binder 并实现这个接口
```java
public class MyBinder extends Binder implements MyIBinder {
    @Override
    public int calculate(int a, int b) {
        return MyService.this.calculate(a, b);
    }
}
```
4. onBind() 方法返回这个内部类的对象
```java
@Override
public IBinder onBind(Intent intent) {
    Log.d(TAG, "onBind: ");
    return new MyBinder();
}
```
5. 在 Activity 中创建一个第 2 步的接口变量。
```java
private MyService.MyIBinder mServiceBinder;
```
6. 在 Activity 中创建一个ServiceConnection 对象，在 onServiceConnected 方法中，将 IBinder 类型的参数转换为 MyIBinder 类型。
```java
private ServiceConnection mConnection = new ServiceConnection() {
    @Override
    public void onServiceConnected(ComponentName name, IBinder service) {
        Log.d("MyService", "onServiceConnected: ");
        mServiceBinder = (MyService.MyIBinder) service;
    }

    @Override
    public void onServiceDisconnected(ComponentName name) {
        Log.d("MyService", "onServiceDisconnected: ");
        mServiceBinder = null;
    }
};
```
7. 绑定 Bind
```java
private void doBind() {
    Intent intent = new Intent(this, MyService.class);
    if (bindService(intent, mConnection, Context.BIND_AUTO_CREATE)) {
        shouldUnbind = true;
    } else {
        Log.e(TAG, "doBind: bind failed");
    }
}
```
8. 与 Service 通信
```java
@Override
public void onClick(View v) {
    if (id == R.id.useService) {
        int result = mServiceBinder.calculate(10, 4);
    }
}
```
9. 解绑 Unbind
```java
private void doUnbind() {
    if (shouldUnbind) {
        unbindService(mConnection);
        shouldUnbind = false;
    }
}
```
10. 在 onDestroy 解绑
```java
@Override
protected void onDestroy() {
    doUnbind();
    super.onDestroy();
}
```

其中第 5 步在 Activity 中创建一个 MyIBinder 接口对象，也可以直接将 Service 本身传递给 Activity。官方文档上的例子就是这么写的，更直接一点。

## 异常和问题
- 未绑定时不能调用 unbind 方法，会崩溃
  - `java.lang.IllegalArgumentException: Service not registered`
- 内存泄漏，在 `onDestroy()` 中解绑
  - `Activity MainActivity has leaked ServiceConnection MainActivity$1@4eb8a66 that was originally bound here`
