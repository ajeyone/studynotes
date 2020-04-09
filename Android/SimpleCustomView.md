## 构造方法
需要实现 3 个方法，提供统一的一个自定义初始化方法
```java
public ClockView(Context context) {
    super(context);
    init(null);
}
public ClockView(Context context, @Nullable AttributeSet attrs) {
    super(context, attrs);
    init(attrs);
}
public ClockView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
    super(context, attrs, defStyleAttr);
    init(attrs);
}
```

## 自定义属性
1. 在 values 目录下创建 attrs.xml
2. 使用 declare-styleable 定义属性。每个属性包括名字和数据类型
```xml
<resources>
    <declare-styleable name="ClockView"> ①
        <attr name="lineWidth" format="dimension" />
    </declare-styleable>
</resources>
```
3. 在自定义初始化方法中解析自定义属性
```java
TypedArray a = getContext().obtainStyledAttributes(attrs, R.styleable.ClockView); // 对应 ① 处定义的 ClockView
a.recycle(); // 一定要recycle。
```

为什么 TypedArray 要 recycle？

为了缓存的目的，TypedArray 保存的都是小数组。解析属性又是频率非常高的操作。大量小数组对象如果在解析 XML 期间创建和销毁，造成 GC 影响 UI 流畅性

## onMeasure
1. 根据 parent view 给的参数，测量自己的大小。
2. 在 onMeausre 内必须调用 `setMeasuredDimension(int measuredWidth, int measuredHeight)`。
3. 注意在这个方法里不要创建对象，因为 draw/layout 相关方法会被频繁地调用，大量创建对象会引起 GC，影响 UI 流畅性。
> Avoid object allocations during draw/layout operations (preallocate and reuse instead) less... (⌘F1) 
Inspection info:You should avoid allocating objects during a drawing or layout operation. These are called frequently, so a smooth UI can be interrupted by garbage collection pauses caused by the object allocations.

例：设置一个正方形的大小，宽和高不一样时，选最小边作为正方形边长。
```
int width = getDefaultSize(getSuggestedMinimumWidth(), widthMeasureSpec);
int height = getDefaultSize(getSuggestedMinimumHeight(), heightMeasureSpec);
int size = Math.min(width, height);
setMeasuredDimension(size, size);
```

## onDraw
1. 调用 canvas 的各种方法进行绘制
2. 同样注意不要创建新的对象

## 一种动画的方法，钟表控件
1. 在 onMeasure 里面绘制完成后，调用 invalidate，就会触发下一次的 onMeasure
2. 绘制时使用当前时间作为属性，不停变化的时间，绘制不停变化的外观

