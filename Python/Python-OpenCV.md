## 读取图片
```python
import cv2 as cv
src = cv.imread(filename) # return `None` if cannot read image
if src is None:
    print("cannot load %s as image" % filename)
```

## HOGDescriptor 初始化

指定参数初始化，注意这些参数初始化后无法修改
```python
HOGDescriptor(winSize, blockSize, blockStride, cellSize, nbins)
hog = cv.HOGDescriptor((image_shape[1], image_shape[0]), (16,16), (8,8), (8,8), 9)
```

都使用默认值的版本
```python
HOGDescriptor()
```

等价于
```python
HOGDescriptor(Size(64,128), Size(16,16), Size(8,8), Size(8,8), 9)
```