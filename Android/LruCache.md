# Android - LruCache 内存缓存

## 什么是 LruCache
是一种内存缓存对象，使用 LRU（Least Recent Used）算法管理缓存。

缓存是空间换时间的一种策略，将低速设备上的数据保存一部分到高速设备上，从而提高整个系统长期运行时的平均读取速度。通常高速设备的空间较小，低速设备的空间较大，也就是说缓存的空间总是不可能覆盖数据全集，需要有缓存数据交换的策略。而缓存算法实现的就是缓存如何交换的细节。

LRU 算法，Least Recent Used，翻译过来是最近最少使用的意思，该算法在缓存空间不够的时候，将近期最少使用的数据替换为新数据。该算法源自这样一种访问资源的需求：近期访问的数据，可能在不久的将来会再次访问。于是最少访问的数据就是价值最小的，是最应该踢出缓存空间的数据。

> 读取数据就像吃自助餐，吃的动作就是读，你需要一个盘子作为缓存，将好吃的先填满一个盘子，这样就可以坐下来好好吃一波。如果没有缓存，可以想像成一次盘子里只拿一份食物，坐下来吃两口，没了，还得再拿去，这样就降低了吃的效率。去食物摆放处拿，是从低速设备读入，直接在盘子里吃，是从高速设备读入。

## 用法
1. 继承 `LruCache` 新建一个类，这是为了
   1. 重写 `int sizeOf(K, V)` 方法，精确衡量缓存大小。
   2. 重写 `V create(K)`、`entryRemoved()` 方法，不常用。
2. 创建一个实例，指定缓存大小：`new LruCache(maxSize)`
   1. 注意这个缓存大小是个逻辑值，需要自己定义单位
   2. 要保证 `int sizeOf(K, V)` 返回值与 `maxSize` 单位相同
3. 写入和读取
   1. 写入使用 `V put(K, V)` 方法，返回原来的 V
   2. 读取使用 `V get(K)` 方法，返回 null 表示缓存未命中

## 线程安全
`LruCache` 常用操作 `get`、`put` 都是线程安全的。但不常用的 `create`、`entryRemoved` 不是线程安全的。

## 套路
1. 创建和配置
```java
private void initCache() {
    int maxMemoryKB = Runtime.getRuntime().maxMemory() / 1024;
    int cacheMaxSizeKB = maxMemoryKB / 8;
    mLruCache = new ImageCache(cacheMaxSizeKB);
}
private static class ImageCache extends LruCache<String, Bitmap> {
    public ImageCache(int maxSizeKB) {
        super(maxSizeKB);
    }
    @Override
    protected int sizeOf(String key, Bitmap bitmap) {
        return bitmap.getByteCount() / 1024;
    }
}
```
2. 读取资源：先从缓存中查找，找到后使用资源，如果找不到，启动低速载入方法
```java
private void loadImageToView(String url, ImageView imageView) {
    Bitmap bitmap = mLruCache.get(url);
    if (bitmap != null) {
        Log.d(TAG, "loadImageToView: memory cache hits for " + url);
        imageView.setImageBitmap(bitmap);
    } else {
        Log.d(TAG, "loadImageToView: NO memory cache hits for " + url);
        imageView.setImageResource(R.drawable.placeholder);
        ImageWorkTask task = new ImageWorkTask(url, imageView);
        task.executeOnExecutor(sThreadPool);
        mTasks.add(task);
    }
}
```
3. 写入资源：从低速设备载入后，直接存入缓存中
```java
private Bitmap download() {
    HttpURLConnection connection = null;
    InputStream input = null;
    try {
        connection = newConnection();
        input = connection.getInputStream();
        Bitmap bitmap = getBitmapFromInputStream(input);
        if (bitmap != null) {
            mLruCache.put(url, bitmap);
            return bitmap;
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
    	// close IO
    }
    return null;
}
```

## 实现原理
`LruCache` 内部使用了 `LinkedHashMap` 来持有数据，利用 `LinkedHashMap` 的有序性和 `accessOrder` 特性来实现 LRU 算法。

数据是以强引用形式保存在 `LinkedHashMap` 中的，强引用保证不会意外被 GC 掉，因此需要手动管理缓存对象。注意 `LruCache` 对象的生命周期，不使用时可以考虑释放掉。OOM 发生时清空也许有点用。

`LruCache` 通过判断当前缓存总量有没有超出最大值来决定是否要删除 `LinkedHashMap` 中的某些数据，来控制缓存大小，防止 OOM。

`LinkedHashMap` 的 `accessOrder` 属性可以在调用构造方法时设置，设置为 true 的时候，`public V get(Object key)` 方法获取到的数据，会被提到 `LinkedHashMap` 有序序列的最前面。这样，最近没有使用的就会排列在有序序列的后面，而这种数据，正是 LRU 算法需要踢出缓存的数据。也可以说，`LinkedHashMap` 本就有 LRU 的特征，`LruCache` 只是定义了一些缓存语义。而且 `LruCache` 的接口与 Map 的接口也很相似。