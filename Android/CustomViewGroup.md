本文以一个 `#并没什么卵用` `#写着玩` 的自定义 ViewGroup 为例，记录一下实现自定义 ViewGroup 的要点。

`CircleGroupView` 提供一个极坐标布局的容器，可以指定 Child View 的角度和距离原点的距离。

## 构造方法 和 自定义属性
比较简单与上一篇内容重复：[Android - 简单自定义View的Checklist](https://www.jianshu.com/writer#/notebooks/30602864/notes/44142074/preview)

## 自定义 LayoutParams
首先，先理解一下 LayoutParams 的概念：LayoutParams 是 ViewGroup 定义的，但在 xml 中是写在 Child View 的标签内的，用来指定 Child View 在这个 ViewGroup 中的布局属性。一般以 `layout_` 作为前缀。
```xml
<com.example.ajeyone.views.CircleGroupView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content">
    <View
        android:layout_width="1dp"
        android:layout_height="1dp"
        app:layout_distance="0dp"
        app:layout_angle="0" /> 
</com.example.ajeyone.views.CircleGroupView>
```
在这个极坐标的例子里，就是距离 `layout_distance` 和角度 `layout_angle` 了。定义如下：
```xml
<declare-styleable name="CircleGroupView_Layout">
    <attr name="layout_distance" format="dimension" />
    <attr name="layout_angle" format="float" />
</declare-styleable>
```
Android Studio 提示自定义的 LayoutParams 参数的名称最好使用 `<ViewGroupName>_Layout` 这种格式，说是各种工具会使用这种约定。

### 定义 LayoutParams 子类

在自定义 ViewGroup 中写一个内部静态类继承 `ViewGroup.LayoutParams` 或者它的子类 `ViewGroup.MarginLayoutParams`，`MarginLayoutParams` 自带 margin 相关属性，大部分的原生 Layout 的 LayoutParams 都是继承自 `MarginLayoutParams`。

这里简化一下直接使用 `ViewGroup.LayoutParams`。

```java
public static class LayoutParams extends ViewGroup.LayoutParams {
    // 自定义属性，用于自定义的布局行为
    public int distance;
    public float angle; // 这里存储的是弧度，xml 中用角度。
    // 通过 xml 中定义的属性创建，负责解析 xml 中的属性。
    public LayoutParams(Context c, AttributeSet attrs) {
        super(c, attrs);
        TypedArray a = c.obtainStyledAttributes(attrs, R.styleable.CircleGroupView_Layout);
        distance = a.getDimensionPixelSize(R.styleable.CircleGroupView_Layout_layout_distance, DEFAULT_DISTANCE);
        angle = degree2radian(a.getFloat(R.styleable.CircleGroupView_Layout_layout_angle, DEFAULT_ANGLE));
        a.recycle();
    }
    // 通过宽高来创建，传入的数据最少，自定义的属性需要设置为默认值。
    public LayoutParams(int width, int height) {
        super(width, height);
        setDefault(); // 设置自定义属性为默认值
    }
    // 通过任意 LayoutParams 类型创建，可以当做一个转换，注意类型相同时的处理。
    // 可以看下父类 ViewGroup.LayoutParams 的这个方法，只是简单的设置了宽高。
    public LayoutParams(ViewGroup.LayoutParams source) {
        super(source);
        if (source instanceof LayoutParams) {
            LayoutParams other = (LayoutParams)source;
            angle = other.angle; // 复制自定义属性
            distance = other.distance; // 复制自定义属性
        } else {
            setDefault(); // 设置自定义属性为默认值
        }
    }
    private void setDefault() {
        distance = DEFAULT_DISTANCE;
        angle = degree2radian(DEFAULT_ANGLE);
    }
    public int getX() { // 极坐标转换为笛卡尔坐标系的公式
        return (int) (distance * Math.cos(angle));
    }
    public int getY() { // 极坐标转换为笛卡尔坐标系的公式
        return (int) (distance * Math.sin(angle));
    }
}
```

### 实现 ViewGroup 中 LayoutParams 相关的方法

先列出来需要自定义实现的方法：
```java
// 检查布局合法性，一般检查是否是自己的 LayoutParams 就行了 ①
@Override
protected boolean checkLayoutParams(ViewGroup.LayoutParams p) {
    return p instanceof LayoutParams;
}
// 将其他布局方式转换为合法布局 ②
@Override
protected ViewGroup.LayoutParams generateLayoutParams(ViewGroup.LayoutParams p) {
    return new LayoutParams(p); // 自定义 LayoutParams 的这个构造方法就有了用武之地。
}
// 通过 xml 属性生成 LayoutParams ③
@Override
public ViewGroup.LayoutParams generateLayoutParams(AttributeSet attrs) {
    return new LayoutParams(getContext(), attrs); // 直接使用构造方法来解析。
}
// 在没有指定布局参数的情况下，创建默认布局 ④
@Override
protected ViewGroup.LayoutParams generateDefaultLayoutParams() {
    return new LayoutParams(LayoutParams.WRAP_CONTENT, LayoutParams.WRAP_CONTENT); 
}
```

再看是哪些过程需要调用这些方法（注意注释中的 ①②③④）：

**在添加 Child View 的阶段**

```
public void addView(View child, int index) {
    LayoutParams params = child.getLayoutParams();
    if (params == null) {
        params = generateDefaultLayoutParams(); // 创建默认布局 ④
    }
    addView(child, index, params);
}
public void addView(View child, int width, int height) {
    final LayoutParams params = generateDefaultLayoutParams();  // 创建默认布局 ④
    params.width = width;
    params.height = height;
    addView(child, -1, params);
}
private void addViewInner(child) { // public addView 方法最后都会走到这里
    ...
    if (!checkLayoutParams(params)) { // 检查布局合法性 ①
        params = generateLayoutParams(params); // 不合法使用转换法生成合法布局 ②
    }
    ...
}
```

**在解析布局 xml 文件的阶段**
```java
public View inflate(XmlPullParser parser, @Nullable ViewGroup root, boolean attachToRoot) {
    ...
    final View temp = createViewFromTag(root, name, inflaterContext, attrs);
    ViewGroup.LayoutParams params = null;
    if (root != null) {
        // Create layout params that match root, if supplied
        params = root.generateLayoutParams(attrs); // 自定义View是根布局的时候 ③
    }
    ...
}
void rInflate(XmlPullParser parser, View parent, Context context,
            AttributeSet attrs, boolean finishInflate) throws XmlPullParserException, IOException {
    ...
    while (((type = parser.next()) != XmlPullParser.END_TAG ||
                parser.getDepth() > depth) && type != XmlPullParser.END_DOCUMENT) {
        ...
        if (TAG_REQUEST_FOCUS.equals(name)) {
            ...
        } else if (TAG_TAG.equals(name)) {
            ...
        } else if (TAG_INCLUDE.equals(name)) {
            ...
        } else if (TAG_MERGE.equals(name)) {
            ...
        } else {
            final View view = createViewFromTag(parent, name, context, attrs);
            final ViewGroup viewGroup = (ViewGroup) parent;
            // 递归解析过程中 ③
            final ViewGroup.LayoutParams params = viewGroup.generateLayoutParams(attrs);
            rInflateChildren(parser, view, attrs, true);
            viewGroup.addView(view, params);
        }
        ...
    }
}
```
其他原生 View 或者其他自定义 View 中也有可能使用这几个方法，上面并没有全部列出，也不用找到所有的调用地方，只要按照这些方法代表的含义来实现就行了。

## onMeasure

与 `View` 的 `onMeasure()` 不同的是，`ViewGroup` 的 `onMeasure()` 负责调用 Child View 的 `measure()` 方法，并根据 Child View 的 measured width/height 计算出自己的大小，最终调用 `setMeasuredDimension()` 完成 `onMeasure` 的任务。

调用 Child View 的 `void measure(int widthMeasureSpec, int heightMeasureSpec)` 方法，关键是两个参数，这两个参数就是传递给 Child View 的 `void onMeasure(int widthMeasureSpec, int heightMeasureSpec)` 方法的两个参数。ViewGroup 的 `onMeasure()` 方法内最终会调用 Child View 的 `onMeasure()` 方法，就形成了 measure 过程的树形递归调用。

Child View 的 Measure Spec 应该怎么计算？可以直接使用 ViewGroup 中的一个静态方法：
`public static int getChildMeasureSpec(int spec, int padding, int childDimension)`

这个算法根据 
- ViewGroup 的 Measure Spec `int spec`
- 需要刨去的空间 `int padding`
- Child View 自己定义的尺寸 `int childDimension`

来计算出 Child View Measure Spec。算法细节可以参考这篇文章：[Android UI绘制 onMeasure](https://www.jianshu.com/p/736c83a16c1e)

有了这个方法，需要计算的东西就变成了 `int padding` 这个参数。它的含义是：ViewGroup 根据自己的定义和当前的属性，预留了一些空间，剩下的才能分配给这个 Child View。

比如，LinearLayout 在这个过程中，这个 padding 值就是当前 Child View 的前面的兄弟节点所占用的空间大小。因为 LinearLayout 是按顺序排列 Child View，所以前面的兄弟节点已经占据的空间，在计算当前 Child View 时要刨去。

回到极坐标布局上，极坐标必须转换为 View 体系中的坐标系，也就是原点在左上角的笛卡尔坐标系，再通过 Child View 的 measured width/height，来计算出 Child View 的边界。考虑所有的 Child View 的边界，计算出一个能将所有 Child View 包裹进去的矩形范围，就得到了最终结果：ViewGroup 的 measured width/height。

```java
@Override
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
    int specWidthMode = MeasureSpec.getMode(widthMeasureSpec);
    int specWidthSize = MeasureSpec.getSize(widthMeasureSpec);
    int specHeightMode = MeasureSpec.getMode(heightMeasureSpec);
    int specHeightSize = MeasureSpec.getSize(heightMeasureSpec);

    int maxAbsX = 0, maxAbsY = 0;
    int n = getChildCount();
    for (int i = 0; i < n; i++) {
        View child = getChildAt(i);
        LayoutParams lp = (LayoutParams) child.getLayoutParams();
        // 这个（x，y）的坐标系并不是 View 的坐标系，而是以 View 的中心为原点的笛卡尔坐标系。
        // 注意 onMeasure 最终要计算的是尺寸，不是位置，因此不必转换坐标系，
        // 在这个虚拟的坐标系上计算出大小即可。
        int polarX = lp.getX();
        int polarY = lp.getY();
        // 计算不能用的空间，这是一个数学问题。。。见下文的图
        int spaceXCannotUse = Math.abs(polarX) * 2;
        int spaceYCannotUse = Math.abs(polarY) * 2;
        // 执行 ChildView 的 measure 过程，这个方法的定义在下面。
        measureChild(child, widthMeasureSpec, spaceXCannotUse, heightMeasureSpec, spaceYCannotUse);

        int childMeasuredWidth = child.getMeasuredWidth();
        int childMeasuredHeight = child.getMeasuredHeight();
        int x0 = polarX - childMeasuredWidth / 2;
        int x1 = polarX + childMeasuredWidth / 2;
        maxAbsX = Math.max(maxAbsX, Math.max(Math.abs(x0), Math.abs(x1)));
        int y0 = polarY - childMeasuredHeight / 2;
        int y1 = polarY + childMeasuredHeight / 2;
        maxAbsY = Math.max(maxAbsY, Math.max(Math.abs(y0), Math.abs(y1)));
    }

    int width = getSize(specWidthMode, specWidthSize, maxAbsX * 2);
    int height = getSize(specHeightMode, specHeightSize, maxAbsY * 2);
    setMeasuredDimension(width, height);
}
// 这个方法内调用 getChildMeasureSpec() 方法计算 Child View 的 MeasureSpec。
private void measureChild(View child, int parentWidthMeasureSpec, int widthUsed, int parentHeightMeasureSpec, int heightUsed) {
    final ViewGroup.LayoutParams lp = child.getLayoutParams();
    final int childWidthMeasureSpec = getChildMeasureSpec(parentWidthMeasureSpec,
            widthUsed + getPaddingLeft() + getPaddingRight(),
            lp.width);
    final int childHeightMeasureSpec = getChildMeasureSpec(parentHeightMeasureSpec,
            heightUsed + getPaddingTop() + getPaddingBottom(),
            lp.height);
    child.measure(childWidthMeasureSpec, childHeightMeasureSpec);
}
```
![如何计算 spaceCannotUse ](https://upload-images.jianshu.io/upload_images/689792-c90403248c430333.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## onLayout

与 `onMeasure()` 类似，ViewGroup 的 `onLayout()` 负责调用 Child View 的 `layout()` 方法，`layout()` 方法又会调用 `onLayout()` 方法。最终形成了 layout 过程的树形递归调用。

`void onLayout(boolean changed, int l, int t, int r, int b)` 方法的后面四个参数表示 ViewGroup 在它上一级 ViewGroup 中的四个边界。同理，调用 Child View 的 `void layout(int l, int t, int r, int b)` 方法应该传入的后四个参数，应该是 Child View 在 ViewGroup 中的位置。

代码如下：

```java
@Override
protected void onLayout(boolean changed, int l, int t, int r, int b) {
    int viewGroupWidth = r - l;
    int viewGroupHeight = b - t;

    int n = getChildCount();
    for (int i = 0; i < n; i++) {
        View child = getChildAt(i);
        LayoutParams lp = (LayoutParams) child.getLayoutParams();
        int childMeasuredWidth = child.getMeasuredWidth();
        int childMeasuredHeight = child.getMeasuredHeight();

        int x = lp.getX();
        int y = lp.getY();

        int childLeft = viewGroupWidth / 2 + x - childMeasuredWidth / 2;
        int childRight = childLeft + childMeasuredWidth;
        int childTop = viewGroupHeight / 2 + y - childMeasuredHeight / 2;
        int childBottom = childTop + childMeasuredHeight;
        child.layout(childLeft, childTop, childRight, childBottom);
    }
}
```
(ole)