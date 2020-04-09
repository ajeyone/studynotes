# 线程
- onSubscribe 所在线程总是调用 subscribe() 的线程，无法更改。
- observeOn 指定 onNext，onComplete，doOnCompete 运行在哪个线程。每次都会生效，影响后面的运算符。
- doOnSubscribe 运行在哪个线程取决于 subscribeOn 在它前面还是后面：
```java
// doOnSubscribe 在前面反而受到 subscribeOn 的影响，运行在 subscribeOn 指定的线程中。
.doOnSubscribe()
.subscribeOn()
```
```java
// doOnSubscribe 在后面不受到 subscribeOn 的影响，运行在调用 subscribe() 的线程中。
.subscribeOn()
.doOnSubscribe()
```
- subscribeOn 指定的线程谁会跑在上面，需要分不同的 Observable：
  - 如果是 Observable.create 创建的，那么 ObservableOnSubscribe.subscribe 运行在 subscribeOn 指定的线程中
  - 如果是 fromCallable 创建的，callable 代码运行在 subscribeOn 指定的线程中。
  - 如果是 fromFuture 创建的，由于 Future 的启动可以另外控制，因此跟 RxJava 关系不大，看 Future.run 在哪个线程执行就是哪个线程跑 Future 内部的代码。
