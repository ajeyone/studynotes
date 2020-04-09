## ndarray 数据类型
```python
np.ndarray((3, 5), dtype=np.float32) # 参数dtype指定
```

## ndarray 设置单一值
```python
shape = (2, 3)
a = np.ndarray(shape) # a is filled in random values
a.fill(100) # set all the values in ndarray to 100
```

## ndarray.shape
```python
# shape 的类型是 python 中的 tuple
a = np.array([1,2,3])
a.shape # (3,)
a = np.array([[1,2,3],[4,5,6]])
a.shape # (2, 3)
# 查看 shape 是几维的，也就是 tuple 的长度，可以用 len()
a.shape, len(a.shape) # (2, 3) 2
```

## ndarray 小数组填充大数组
```python
# gradient_list 中保存了 30 个 shape (100, 1) 的 ndarray
# 最后合成一个 shape (100, 30) 的 ndarray
def convertTrainSamples(gradient_list):
    rows = gradient_list[0].shape[0]
    cols = len(gradient_list)
    samples = np.ndarray((rows, cols), dtype=np.float32)
    for i in range(0, cols):
        samples[:,i:i+1] = gradient_list[i]
    return samples

# 给数组直接用 ":" 写法引用一块空间，直接赋值，需要源数组和目标数组形状相同
# 如果不用 ":"，数组会降维，也可以，但注意形状要相同
samples[:,i:i+1] = gradient_list[i]
```

## ndarray 数组合并
```python
# 将两个数组拼接起来，对数组的形状有要求
# 下面例子是将两个二维数组横向拼接，也就是拼接第2维
label_pos = np.ndarray((1, 33), dtype=np.int32)
label_pos.fill(1)
label_neg = np.ndarray((1, 55), dtype=np.int32)
label_neg.fill(-1)
# 注意除了 axis 指定的维度不需要相同，其他维度必须相同才能拼接上
labels = np.concatenate((label_pos, label_neg), axis=1) # 第二维，axis从0开始，axis=1
```

## ndarray 矩阵相乘
```python
# m1, m2 是二维数组，满足矩阵相乘条件
# 例如两个数组的 shape 为 (1, 6) x (6, 576)，结果 shape 是 (1, 576)
result = np.matmul(m1, m2)
```