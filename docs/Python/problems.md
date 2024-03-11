# 作业记录

## Week 1

!!! question
    字母A的ASCII编码值和Unicode编码值相同。
    - Answer : T (编码不同，但是编码值相同)

!!! question
    高级语言程序要被机器执行,只有用解释器来解释执行
    - Answer : F (还有编译型语言)

!!! question
    Python是脚本语言
    - Answer : T

!!! question
    Python是静态语言
    - Answer : F

## Week 2

!!! question
    当输入是：45,8 时，下面程序的输出结果是37。
    ```py
    a,b = input().split(',')
    b=int(b)
    c=int('a',b)
    print(c)
    ```
    - a加引号会报错，不加引号为str

!!! question
    print(bin(12.5))的输出是0b1100.1
    - bin()不能处理float
