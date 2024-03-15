---
counter: True  
---

# chapter 1 C++语言特性

!!! Abstract
    - 课上例程
    - 语法记录

## The First C++ Program

```cpp
#include<iostream>
using namespace std;
int main()
{
    cout << "hello" << "world" << endl;
    return;
}
```

## Read Input

```cpp
#include<iostream>
using namespace std;
int main()
{
    int number;
    cin >> number;
    cout << "hello" << "world" << number << endl;
    return;
}
```

## 与C语言对比

- **Strengths:**
  - Efficient programs
  - Direct access to machine
  - Flexible

- **Weakness:**
  - Insufficient type checking
  - Poor support for programming-in-the-large
  - TBD
  
## String

!!! Info

    * `string` is a class in C++. (需要 `#include <string>`)  
    * 可以像定义其他类型一样定义变量。 ***e.g.*** `string str;`
    * 可以对字符串初始化，用 `cin, cout` 输入输出。

### Assignment for string

- 与`char`数组对比

```cpp
char charr1[20];
char charr2[20] = "ABC";

string str1;
string str2 = "ABC";

str1 = str2; //legal
charr1 = charr2; //illegal
```

### Concatenation for string 

- `str3 = str1 + str2;`
- `str1 += str2;`
- `str1 += "ABC";`

!!! Warning
    ``` C++
    string name;
    name = name + "Johnson";
    ```

    这里 `name` 已经有确定值了，因为这里是一个 class 态，为空字符串。

### Ctors

- string(const char*cp, int len);
- string(const string& s2, int pos);
- string(const string& s2, int pos, int len);

```cpp
#include<string>
#include<iostream>
using namespace std;
int main()
{
    string str1, str2("world");
    str1 = "hello";
    cout << str1 << str2 << endl;
    return;
}
```

- 可以使用`str.empty()`来判断是否为空

!!! Warning
    要注意行为调用与函数调用的区别

### alter string(不常考)

- assign();
- insert(const string &, int len);
- insert(int pos, const string &s);
- erase();
- append();
- replace();

### search string

- find()

## File I/O

```cpp
#include<ifstream>
#include<ofstream>
/*现在合并为了<fstream>*/
ofstream File1("C:\\test.txt");
File1 << "Hello" << endl;
ofstream File2("C:\\test.txt");
File2 >> str;
```

## Stream

- `iostream`处理控制台IO
- `fstream`处理命名文件IO
- `stringstream`完成内存`string`的IO

## Vars

- global vars
- static global vars
- local vars
- static local vars

```cpp
int i; //global vars
string str;
static int j; // static global vars

f(){
    int k; // local vars
    static int j; //static local
}
```

## Momery Model

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/b5b7a84b9d41ebbb6a8061aec4f490c.png)

- extern : 说明在外部定义(多文件工程)
- static global var inhibits access from outside the .ccp file(访问控制)
- static local var initialized at its first time(persistence) 

## Pointers to object

!!! Example
    - string s = “hello”;(object itself)
    - string* ps = &s;(a pointer to an object)
    

### Operators with Pointers

- `&` : get address
- `*` : get the object
- `->` :call the function

### Assignment

- string s1, s2;
  - s1 = s2;
- string *ps1, *ps2;
  - ps1 = ps2;

### Dynamic memory allocation

- new(返回一个指针)
  - new int;
  - new Stash;
  - new int[10];
- delete
  - delete p;
  - delete [] p;

!!! Example
    - int *psome = new int [10];
    - delete [] psome;

!!! Example "The new-delete mech"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/6f4e49e874723dcdba9c39d1dddb274.png)
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/57107efe80e3e6a55c9261397051f78.png)

!!! danger "Tips for new and delete"
    - Don't use delete to free memory that new didn't allocate.
    - Don't use delete to free the same block of memory twice  in succession.
    - Use delete [] if you used new [] to allocate an array.
    - Use delete (no brackets) if you used new to allocate a  single entity.
    - It's safe to apply delete to the null pointer (nothing  happens).
    

## References(引用)

!!! tip "References are a new data type in C++"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/62189c7c9a5de0b1249a8e7232c6dbb.png)

**修改引用值也会导致原值发生改变，效果类似指针**

- int x = 3;
- int &y = x;
- const int &z = x; (z值不能修改)

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/03d8906ba044aaafe6dd301b1b1b545.png)

!!! tip "Rules of references"
    - References must be initialized when defined
    - Initialization establishes a binding
    - Bindings don’t change at run time, unlike pointers(binding可以理解为绑定)
    - No references to references
    
    ??? Example
        ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/8f469cce8a21c5f5ea010148716d810.png)

!!! danger "Pointers vs. References"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/5b0f1e5abeb74d0fd8bb92b78bc05fb.png)
    - ailas ：别名
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/0af3e3d4d166ef0e5d2072ee9c7d915.png)
    - 指针可以拥有引用
    - 引用不能拥有指针

## const

- declares a variable to have a constant value

!!! bug "code"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/d52adada11be84ee51fccc93825e4a0.png)

### constants

- Constants are variables
    - Observe scoping rules
    - Declared with “const” type modifier
- A const in C++ defaults to internal linkage
    - the compiler tries to avoid creating storage for a const
        - holds the value in its symbol table.
    - extern forces storage to be allocated.

!!! abstract
    **编译期常量**指的就是程序在编译时就能确定这个常量的具体值
    **非编译期常量**就是程序在**运行时才能确定**常量的值,因此也称为运行时常量

#### Compile time constants

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/6f32e2069b97b2f3ca6f60a8f5c37e5.png)

#### Run-time constants

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/7d26f5995a9e2ba5fe82bb837e2f65f.png)

### Aggregates

!!! Note ""
    可以将const用于aggregates，但会分配存储空间。在这些情况下，const的意思是“不能更改的存储空间”。但是，该值不能在编译时使用，因为编译器在编译时不需要知道存储的内容。

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/6deda12669f4dfb291b45a72e064738.png)

### Pointers and const 

**const跟谁近就修饰谁**

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/16d435eb21f6ca1c2a96b3944fae658.png)

!!! Question 
    - string p1("fred");
    - const string *p = &p1;
    - string const *p = &p1;
    - string *const p = &p1;

    ??? note "Answer"
        3与4意义一致 

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/1f628a9efbc82b68cc6c425691242df.png)

- Can always treat a non-const value as const
- You cannot treat a constant object as non-constant without an explicit cast  (const_cast)可以将const改成varible


!!! tip "Passing and returning addresses"
    - Passing a whole object may cost you a lot.	It is  better to pass by a pointer.	But it’s possible for the  programmer to take it and modify the original value.
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/b02841d98c43e7de9a0bb623fbc4d2a.png)
    - In fact, whenever you’re passing an address into a  function, you should make it a const if at all  possible.
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/7bb3092bbf102447094ffcf275118ca.png)
    
    !!! danger "xww的编程建议"
        - 建议传入指针或者引用，并加上const，可以使得代码运行的更快
     
