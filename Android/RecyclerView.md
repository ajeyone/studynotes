# Adapter 中获取 RecyclerView 的引用

适用于 adapter 在外部或者是内部静态类的情况，onAttachedToRecyclerView 回调可以拿到引用。由于 adapter 可能被多个 RecyclerView 使用。如果是这样要小心使用，不过一般都不会这么用。
```java
public class MyAdapter extends RecyclerView.Adapter<MyAdapter.ViewHolder> {
    private RecyclerView mRecyclerView;
    @Override
    public void onAttachedToRecyclerView(@NonNull RecyclerView recyclerView) {
        mRecyclerView = recyclerView;
    }
}
```