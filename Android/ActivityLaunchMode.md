# LaunchMode

有四种取值：
- standard
- singleTop
- singleTask
- singleInstance

## LaunchMode 的取值如何影响是否创建新的 Activity 实例
- standard 一定会创建新的实例
- singleTop 如果要启动的实例所在的task顶部已经有了，才可以复用之前。其他情况，比如：
  - 原来并没有这个 Activity 的实例。
  - 原来有这个 Activity 的实例，但并不在栈顶。
  - 原来有这个 Activity 的实例，但是和要启动的实例不在一个 task 中。

## LaunchMode 的取值如何影响task

以下表格表示各种组合情况下，启动 Main > A > B 的过程中，获取的 task ID 的值。

相同 task ID 的 Activity 在同一个 task 中。

表格内 standard(A) 表示 standard 模式，括号表示设置的 taskAffinity。

| Activity Main | Activity A | Activity B | Task IDs(Main,A,B) | 解析
| --- | --- | --- | --- | --- |
| standard | singleInstance | standard | X,Y,X | singleInstance就是和别人不一样 |
| standard(P) | singleInstance | standard(P) | X,Y,X | 显式设置相同的 taskAffinity，其实跟不设置是一样的 |
| standard(P) | singleInstance | standard(Q) | X,Y,Z | 具有不同taskAffinity的standard中间插一个singleInstance，也放在了不同的task中 |
| standard(P) | singleInstance(P) | standard(P) | X,Y,X | 即使手动设置了singleInstance的taskAffinity，也是没有影响，不会和别的在一个task内|
| singleIntance | standard(P) | standard(Q) | X,Y,Y | standard启动standard会忽略taskAffinity |
| standard | singleTask | standard | X,X,X | Main和A的taskAffinity相同，singleTask也不会创建新的task |
| standard(P) | singleTask(P) | standard(Q) | X,Y,Y | 同上 |
| standard | singleTask(P) | standard(Q) | X,Y,Y | Main和A的taskAffinity不相同了，就启动了新的task |
| standard | singleTask(P) | standard | X,Y,Y | 即使B与Main的taskAffinity相同，但是standard自己决定不了task要跟启动者相同 |
| singleTask(P) | singleTask(Q) | standard(P) | X,Y,Y | - | 
| singleTask(P) | singleTask(Q) | standard(P,NT) | X,Y,X | - | 

