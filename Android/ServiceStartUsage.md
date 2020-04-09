## Service Start/Stop 使用方式

- 用启动/停止方式来使用服务，启动者与服务没有直接关系，启动者的生命周期与服务也没有直接关系。
- 服务第一次 Start 会进行初始化：构造方法、onCreate()
- 服务在运行期间可以被再次 Start，不会执行初始化代码
- 只要 Start 服务，就会执行服务的 onStartCommand() 方法
- 无论 Start 多少次，通过 intent 的方式停止服务就会彻底停止服务
- 无参数版本的 `stopSelf()` 方法与 intent 方式相同，会彻底停止服务。
- 有参数版本的 `stopSelf(int startId)` 不一定会彻底停止服务，startId 如果是最后一个则会停止，详细分析见下文。

## 代码套路
### manifest 注册
```xml
<service
    android:name=".services.MyService"
    android:enabled="true"
    android:exported="false" />
```

### 定义
```java
import android.app.Service;
public class MyService extends Service {
    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        return super.onStartCommand(intent, flags, startId);
    }
}
```

### 启动
```java
Intent intent = new Intent(this, MyService.class);
startService(intent);
```

### 停止
```java
Intent intent = new Intent(this, MyService.class);
stopService(intent);
```

### 生命周期
```java
// 启动服务
MyService()
MyService.onCreate()
MyService.onStartCommand()
MyService.onStart() // 该方法已废弃，这里打印是因为默认还是调用了它
// 再次启动
MyService.onStartCommand()
MyService.onStart()
// 停止服务
MyService.onDestroy()
```

## startId

是 `onStartCommand(Intent intent, int flags, int startId)` 方法的一个参数，表示这次 Start 请求的一个 token，这个 token 用来传给 `stopSelfResult(int startId)`。

`stopSelfResult(int startId)` 的官方文档很模糊：
> This is the same as calling Context.stopService(Intent) for this particular service but allows you to safely avoid stopping if there is a start request from a client that you haven't yet seen in onStart(Intent, int).

经过试验，发现规则如下：
1. startId 一个生命周期内，每次 onStartCommand 调用，参数 startId 加 1。
2. 只有 stop 最后一个 startId，也就是 onStartCommand 最后一次调用中的 startId，服务才会停止，大于小于都不行。

主要是第2点，理解了 stop 最后一个 startId，service 就会停止，就会理解官方文档下面这句话：
> Be careful about ordering of your calls to this function.. If you call this function with the most-recently received ID before you have called it for previously received IDs, the service will be immediately stopped anyway. If you may end up processing IDs out of order (such as by dispatching them on separate threads), then you are responsible for stopping them in the same order you received them.

最后一句话：
> you are responsible for stopping them in the same order you received them.

是结论：Service 设计者要保证停止 startId 的顺序和启动 startId 的顺序一致。

可以参考一个现成的例子：`IntentService`

IntentService 接收 Intent 封装的请求，每一次请求就是一次 Service 的启动，就是一次 onStartCommand 调用。因此每个 Intent 会关联一个 startId，IntentService 将这个 startId 保存在了 Message 里面，同样保存在 Message 中的还有 Intent。startId + Intent 构成了一次请求。在 onHandleIntent 执行完成后， IntentService 会调用 stopSelf(startId) 来停止这个请求。

- 如果执行 stopSelf(startId) 的时候，这个 startId 不是最大的，不是最后一个，意味着在服务启动期间，又来了一个启动请求。这时上面总结的规则 2 就保证了前面的请求处理完毕后，不会停止服务，而是继续处理下一个请求。
- 如果执行 stopSelf(startId) 的时候，这个 startId 是最大的，说明没有新的请求了，那么处理完本次请求后，根据规则 2 就会停止服务，使 IntentService 不会浪费资源。

这样将 startId 和请求的处理绑定在一起，就可以在请求处理完成后，直接 stop 这个 startId，保证不会影响后面的请求处理，保证自己处理完成后如果是最后一个会自动停止服务。

## 实验的方法和代码

创建一个 Activity 添加两个按钮，一个启动服务，一个停止服务。停止服务的代码不用 Intent 方式，而是自定义了一个停止随机 startId 的方法。通过多次试验，可以观察到上文描述的规则。

```java
public class MyService extends Service {
    public static MyService sInstance = null; // 试验用，实际代码不要这么写。
    @Override
    public void onCreate() {
        super.onCreate();
        sInstance = this; // 试验用，实际代码不要这么写。
    }

    private int mLastId = 0;
    private Random random = new Random();

    public void stopOnce() {
        // 随机一个 id，范围是 [1, mLastId + 1]，可以验证大于等于小于三种情况。
        int id = random.nextInt(mLastId + 1) + 1;
        Log.d(TAG, "stopOnce: random, id=" + id);
        stopSelfResult(id);
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        Log.d(TAG, "onStartCommand: startId=" + startId);
        mLastId = startId;
        return super.onStartCommand(intent, flags, startId);
    }
}

public class MainActivity ... {
    @Override
    public void onClick(View v) {
        if (id == R.id.startService) {
            Intent intent = new Intent(this, MyService.class);
            startService(intent);
        } else if (id == R.id.stopService) {
            MyService.sInstance.stopOnce();
        }
    }
}
```