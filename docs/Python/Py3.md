# 基本语句

!!! Abstract
    语句通常是一行一条语句。如一行中有多条语句,则用分号(;)分开,如语句太长要跨行时,可以用续行符(\)跨行表示一个语句。
    
    - 输入输出语句（已讲）
    - 赋值语句
    - 分支语句
    - 循环语句

## 赋值语句

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/8355ab133cc72def883f194620bebcf.png)

## 分支语句

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/696b44d0bbfc08c139bc9ba81ea9c3b.png)

!!! Example
    ```py
    x = float(input())
    if !x :
        y = 0
    else :
        y = 1/x

    print(f"f({x:.1f}) = {y:.1f}")
    ```

## 循环语句

### for语句

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/33964d4d2f77cbb95996a2c3891d7af.png)

for **varible** in 列表：
    语句块

```py
for i in [1,2,3,4]:
    print(i)
```

!!! tip  "range函数"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/d3411bf55fd9e11f31f3d02c3c16b9a.png)

### 列表


- 列表推导式是将某种操作应用到序列，从一个或者多个列表快速简洁地创建新列表的一种方法，又被称为列表解析。
- 它还可以将**循环和条件**判断结合,从而避免语法冗长的代码,同时提高程序性能。

!!! Note "列表推导式的几种形式"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/b8b5f8bdedb1b5644fc8078490cddc2.png)

    !!! example "求和"
        ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/831d0b16e0720627e16a85aec6cb014.png)