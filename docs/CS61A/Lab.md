# Lab 记录

[这里放一个中文翻译版题目的blog](https://blog.csdn.net/Epoch101/article/details/130468459?spm=1001.2014.3001.5501)

## Lab 0

- 代码填充

```py
def twenty_twenty():
    """Come up with the most creative expression that evaluates to 2020,
    using only numbers and the +, *, and - operators.

    >>> twenty_twenty()
    2020
    """
    return 2020
```

- 运行过程

```shell
root@localhost:/mnt/d/86134/Desktop/CS61A/lab00# python3
Python 3.10.12 (main, Nov 20 2023, 15:14:05) [GCC 11.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> from lab00 import *
>>> twenty_twenty()
2020
>>>
```

- 前面几个运算的`lab`都是基础的配置环境，这里就不一一罗列了

## Project 1:  Hog

```py
    # BEGIN PROBLEM 1
    IsOne = 0
    sum = 0
    for i in range(num_rolls):
        now = dice()
        if now == 1:
            IsOne = 1
        sum += now
    return sum if IsOne != 1 else 1
    # END PROBLEM 1
```

若此轮本人不出牌，则取对手的得分对应的`digit`+`3`作为得分

```py
    # BEGIN PROBLEM 2
    pi = pi // pow(10, 100 - score)
    # END PROBLEM 2
    return pi % 10 + 3
```

这里利用`if - else`来判断分支

```py
    # BEGIN PROBLEM 3
    if num_rolls == 0:
        return free_bacon(opponent_score)
    else:
        return roll_dice(num_rolls, dice)
    # END PROBLEM 3
```

求最大公因数并依据题意判断

```py
    # BEGIN PROBLEM 4a
    a, b = player_score, opponent_score
    while b > 0:
        a, b = b, a % b
    return player_score > 0 and opponent_score > 0 and a >= 10
    # END PROBLEM 4a
```

- `Python`可以实现函数的嵌套定义

```py
    def say(score0, score1, last_score = last_score, running_high = running_high):
        new = last_score - running_high
        if who == 0:
            if score0 - last_score > running_high:
                running_high = score0 - last_score
                print(running_high, 'point(s)! The most yet for Player 0')
            last_score_tmp = score0
        else:
            if score1 - last_score > running_high:
                running_high_tmp = score1 - last_score
                print(running_high, 'point(s)! The most yet for Player 1')
            last_score = score1
        return announce_highest(who, last_score, running_high)
    return say
```

- `*args`表示传入的这个函数的所有参数，后面使用这个`*arg`可以用这些参数调用函数

```py
>>> def printed(f):
...     def print_and_return(*args):
...         result = f(*args)
...         print('Result:', result)
...         return result
...     return print_and_return
>>> printed_pow = printed(pow)
>>> printed_pow(2, 8)
Result: 256
256
>>> printed_abs = printed(abs)
>>> printed_abs(-10)
Result: 10
10
```

