# Input and Output

!!! Abstract
    - 输入函数
    - 输出函数
    - 输出格式控制
    - 数据类型
    - 标识符
    - 保留字
  
## 输入函数 input

- input()函数实现了从控制台读入输入

```py
a = input()
type(a)

<class 'str'>
```

- 看似整数类型，但实则是字符串类型，若想获得整数类型，可用`int()`函数进行转换：`a=int(input())`
- 可以调用split()函数分割，在一行中获得多个值
- 可以调用map将一行中多个输入转化为其他类型
- m,n = map(int, input().split('/'))

## 输出函数 print

- 如何设置多个值之间的分隔 ： `print(a, b, c, sep = ',')`
- 如何设置结束符: `print(a, end = "")`

## 输出格式控制

- 方法一 ：%
- 方法二 ：format
- 方法三 ：f-string

```py
# 1 %
print("my name is %s, my age is %d, my height is %.lf " % (name, age, height))
# 2 format
print("my name is {:s}, my age is {:d}, my height is {:.lf} ".format(name, age, height))
# 3 f-string
print(f"my name is {name}, my age is {age}, my height is {height:.lf} ".format(name, age, height))
```

## 字符串

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/3bf34231dbb8f6baea4912b2a67143e.png)

!!! Abstract
    在python语言中,一般对于片段，引号后面的值都是取不到的。    

## 转义字符

|字符|含义|
|:--:|:--:|
|`\'` |单引号|
|`\"` |双引号|
|\a |响铃|
|\b |退格(Backspace)|
|\n |换行|
|\v |纵向制表符|
|\t |横向制表符|
|\r |回车|
|\ooo |最多三位 八进制，例如：\12代表换行|
|\xyy |十六进制数，yy代表的字符，例如：\x0a代表换|

## 运算符优先级

|优先级 |运算符 |描述| 结合性|
|:---:|:---:|:---:|:---:|
|1| x**y| 幂 |从右向左|
|2| +x,-x |正，负||
|3| x*y, x/y,x//y, x%y| 乘，除，取模 |从左向右|
|4 |x+y, x-y |加，减| 从左向右|
|5 |x<y, x<=y, x==y, x!=y, x>=y, x>y| 比较| 从左向右
|6 |not x |逻辑否 |从左向右|
|7 |x and y |逻辑与| 从左向右|
|8 |x or y |逻辑或 |从左向右|

## 内置转换函数

|函数名 |含义|
|:---:|:---:|
|bool|根据传入的参数的逻辑值创建一个新的布尔值|
|int |根据传入的参数创建一个新的整数|
|float| 根据传入的参数创建一个新的浮点数|
|complex| 根据传入参数创建一个新的复数|
|str |创建一个字符串|
|ord| 返回Unicode字符对应的整数|
|chr| 返回整数所对应的Unicode字符|
|bin| 将整数转换成2进制字符串|
|oct| 将整数转化成8进制数字符串|
|hex| 将整数转换成16进制字符串|
|list| 根据传入的参数创建一个新的列表|