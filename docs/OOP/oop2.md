---
counter: True  
---

# Chapter 2 类的基本性质

!!! Abstract
    - 抽象
    - 封装和数据隐藏
    - 多态
    - 继承
    - 代码的可重用性
    - 容器

## Class(类)

类是一种将抽象转化为用户定义类型的C++工具，他将数据类型和操纵数据的方法组合成一个整洁的包。

这里如下是`CS61A`的`Account`以及对应的`C++`版本

??? bug "code-python"
    ```py
    class Account:
        def __init__(self, account_holder):
            self.balance = 0
            self.holder = account_holder
        def deposit(self, amount):
            self.balance = self.balance + amount
            return self.balance
        def withdraw(self, amount):
            if amount > self.balance:
                return 'Insufficient funds'
            self.balance = self.balance - amount
            return self.balance
    ```

??? bug "code-C++"
    ```cpp
    #include<iostream>
    #include<string>
    using namespace std;

    class Account
    {
    private:
        /* data */
        int accountNumber;
        string accountHolderName;
        double balance;
    public:
        Account(int accountNumber = 0, string accountHolderName = "No name", double balance = 0);
        /*
        Account();//default constructor(默认构造函数)
        已经提供显示构造函数后，编译器不会再提供默认构造函数，所以这里会报错，只能手动提供默认构造函数
        */
        void withdraw(double amount);
        void deposit(double amount);
        void display();
    }; 

    void Account::display()
    {
        cout << "Account Number: " << accountNumber << endl;
        cout << "Account Holder Name: " << accountHolderName << endl;
        cout << "Balance: " << balance << endl;
    }


    Account::Account(int accountNumber, string accountHolderName, double balance)
    {
        this->accountNumber = accountNumber;
        this->accountHolderName = accountHolderName;
        this->balance = balance;
    }

    void Account::withdraw(double amount)
    {
        if (balance < amount)
        {
            cout << "Insufficient balance" << endl;
        }
        else
        {
            balance -= amount;
        }
    }

    void Account::deposit(double amount)
    {
        balance += amount;
    }

    int main()
    {
        Account a1(101, "John", 5000); //隐式调用
        //Account a1 = Account(101, "John", 5000); 显式调用
        a1.display();
        a1.deposit(2000);
        a1.display();
        return 0;
    }
    ```

### Example:Point

```c
typedef struct point {
    float x;
    float y;
} Point;

void print(const Point *p)
{
    printf("%d %d\n", p->x, p->y);
}

void move(Point* p,int dx, int dy)
{
    p->x += dx;
    p->y += dy;
}

int main()
{
    Point a;
    a.x = 1;
    a.y = 2;
    print(&a);
    move(&a, 10, 20);
    print(&a);
}
```

以上是 C 语言能做的，在 C++ 里我们可以把函数也放到结构里面。  

```cpp
typedef struct point {
    int x;
    int y;
    void print();       
}Point;
```

这里只是声明，并不会产生实际代码（包括结构里的变量也只是声明）。我们往结构里放了函数原型（即函数声明）  

结构外面的 `print()` 称为自由函数，和任何类都没有关系，左为函数自身是独立的。 
声明在结构内的函数不是独立的，从属于 `Point` 结构。还需要一个 body.  

```C++
struct Point {
    int x;
    int y;
    void print();       
}Point;

void Point::print()
{
    printf("%d %d\n", x, y);
}
```

C++ 中不需要 `typedef` 来声明结构体。  
`a.print()` 即可调用结构体内成员函数。但是这个成员函数如何知道我们要输出的就是 `a.x a.y` ?  

**我们在成员函数内 `cout << this << endl;` 发现 `this` 和 `&a` 相同。**  

```C++
void Point::init(int x, int y)
{
    this->x = x;
    this->y = y;
}
```

这里必须加 `this`, 否则类似于局部变量会屏蔽全局变量，编译器会认为 `x = x` 什么也没做。

### `::`resolver(预解析器)

- `<Class Name>::<function name>`
- `::<function name>`


```cpp
void S::f(){
::f(); // Would be recursive otherwise!
::a++; // Select the global a  
a--; // The a at class scope
}
```

??? Info
    `Point` 是姓, `init` 是名，因此 `Point::init` 共同构成了成员函数的名字，因此 `void` 需要放在函数名前面，即 `void Point::init`. 

#### this: the hidden parameter

- **this** is a hidden parameter for all member functions, with the type of the struct

```cpp
void Stash::initialize(int sz) 
```

$➔$ (can be regarded as) 

```cpp
void Stash::initialize(Stash* this, int sz)
```

- To call the function, you must specify a variable

```cpp
Stash a; 
a.initialize(10);
```

$➔$ (can be regarded as)

```cpp
Stash::initialize(&a,10);
```

- Inside member functions, you can use this as the pointer to the variable that calls the function.
- **this** is a natural local variable of all structs member functions that you can not define, but can use it directly.

### Container

!!! Abstract
    - Container is an object that holds other objects.
    - For most kinds of containers, the common interface is put() and get().
    - Stash is a container that stores objects and can be expanded during running.

!!! example "Stash"
    - Typeless container.
    - Stores objects of the same type.
        - Initialized w/ the size of the type 
        - Doesn’t care the type but the size
    - add() and fetch() 
    - Expanded when needed

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/150b266039a4abc7d72f87d70046def.png)

### Objects

!!! Abstract "Objects = Attributes + Services"
    - Data: the properties or status 
    - Operations: the functions
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/20240314151609.png)

??? Example "Ticket Machine"
    - We make a program simulate the  procedure of buying tickets.  It works.  But there is no such machine. There's  nothing left for the further development.
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/0f72f241a55270245a7d05304b4613d.png)

- Objects(cat)
    - Represent things, events, or concepts
    - Respond to messages at run-time
- Classes(cat class)
    - Define properties of instances
    - Act like types in C++

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/07eb00d636c08a9c3e340743fe513e5.png)

### 访问控制

!!! Info
    `private`和`public`描述了对类成员的访问控制。使用类对象的程序都可以直接访问公有部分，但是私有对象只能通过公有成员函数(或友元函数)来访问。

- 无论类成员是数据成员还是成员函数，都可以在类的公有部分或私有部分中声明它。但由于隐藏数据是OOP主要目标之一，因此通常数据项放在**私有部分**而组成类接口放在**公有部分**
- 通常，使用私有函数来处理不属于公有接口的部分

### 封装

- 类设计尽可能将公有接口与实现细节分开。公有接口表示设计的抽象组件，将实现细节放在一起并将他们与抽象分开称之为**封装**
- 数据隐藏(将数据放在类的私有部分中)也是一种**封装**，将实现细节隐藏在私有部分(私有接口)也是**封装**

### 实现成员函数

!!! Abstract
    类的描述的第二部分：为那些类声明的原型表示的成员函数提供代码
    - 定义成员函数时，使用作用域解析运算符`(::)`来标识函数所属的类
    - 类的方法可以访问类的`private`组件

#### Inline Function

!!! Info
    在C语言中，如果一些函数被频繁调用，不断地有函数入栈，即函数栈，会造成栈空间或栈内存的大量消耗。为了解决这个问题，特别的引入了`inline`修饰符，表示为内联函数。(栈空间就是指放置程式的局部数据也就是函数内数据的内存空间，在系统下，栈空间是有限的，假如频繁大量的使用就会造成因栈空间不足所造成的程式出错的问题，函数的死循环递归调用的最终结果就是导致栈内存空间枯竭。)

??? Example

    ```cpp
    class Account
    {
    private:
        int number;
        void set_num(){number += 1;}//Inline Function
    }；
    /*或者使用inline修饰符*/
    class Account
    {
    private:
        int number;
        void set_num();//Inline Function
    }；
    inline void set_num()
    {
        number += 1;
    }
    ```
    
可以类比为`#define`,函数会在调用它的地方进行替换。(一般内联函数不建议超过十行)，还要确保内联函数对多文件程序中的所有文件都是可用的，最简便的方法是：将内联定义放在定义类的头文件里。

#### Constructor(构造函数)

- 构造函数虽然没有返回值，但是没有被声明为`void`，实际上，构造函数没有声明类型。
- 隐式调用构造函数
  - `Stock garment("Furry Mason", 50, 2.5);`
- 显式调用构造函数
  - `Stock gaement = Stock("Furry Mason", 50, 2.5);`
- 使用`new`动态分配内存
  - `Stock *pstock = new Stock("Furry Mason", 50, 2.5);`

!!! Info "default constructor(隐式构造函数)"
    - 如果没有提供任何构造函数，则`C++`将自动提供默认构造函数，它是默认构造函数的隐式版本，不做任何工作
    - 默认构造函数没有任何参数，因为声明中不包含
    - 如果为类提供了非默认构造函数，而没有提供默认构造函数，则类似下面的声明将出错(禁止创建未初始化的对象)
        - `Stock stock1;`
    - 定义默认构造函数有两种方法
      - 给已有构造函数提供所有参数默认值
      - 函数重载来定义一个没有参数的构造函数 

- `C++11`列表初始化(提供一个构造函数参数列表匹配的内容)：
  - `Stock stock1 = {"name", 2, 2.0};`  
  - `Stock stock1 {"name", 2, 2.0};`

#### Destructor(析构函数)

- 如果构造函数使用`new`来分配内存,则使用`delete`来释放空间
- 如果没有使用`new`,则会让编译器生成一个隐式析构函数

- `C++`中对象`new`出来和直接声明的区别(类似malloc)
  - 首先，最直观的，new出来的对象需要使用指针接收，而直接声明的不用。例如 A* a=new A() 与A a（）。
  - `new`出来的对象是直接使用堆空间，而局部声明一个对象是放在栈中。
  - `new`出来的对象类似于申请空间，因此需要delete销毁，而直接声明的对象则在使用完直接销毁。
  - `new`出来的对象的生命周期是具有全局性，譬如在一个函数块里new一个对象，可以将该对象的指针返回回去，该对象依旧存在。而声明的对象的生命周期只存在于声明了该对象的函数块中，如果返回该声明的对象，将会返回一个已经被销毁的对象。
  - `new`对象指针用途广泛，比如作为函数返回值、函数参数等

#### This Pointer(this 指针)

`this`指针指向用来调用成员函数的对象(this被作为隐藏参数传递给方法)
如果要防止`this`被调用修改所指向的对象的值，则可以在函数声明后面加上`const`

??? Info
    最初的UNIX实现使用C++前端cfront将C++程序转化为C程序。会将限定符`ClassName::`转换为函数参数(指向对象的指针)

## 对象数组

- 在没有显式构造函数的情况下(或显式构造函数有默认值)的情况下，可以直接如下定义
  - `Stock mystuff[4];`或者可以寄希望于自动补足未初始化的对象
- 否则要为每个元素调用构造函数
  
```cpp
const int num = 4;
Stock stocks[num] = {
    Stock("A", 2, 2.0),
    Stock("B", 3, 3.0),
    Stock("C", 4, 4.0),
    Stock("D", 5, 5.0)
};
```

## 类作用域

### 作用域为类的常量

如果希望所有对象共享一个常量，这样做是错误的例子：

```cpp
class Bakery:
{
private:
    const int months = 12; 
    double costs[months];   
}
```

!!! Warning 
    声明类只是描述了对象的形式，并没有创建对象。因此，在创建对象前，将没有用于存储值的空间。

#### 1.声明一个枚举

在类声明中声明的枚举的作用域为整个类，因此可以用枚举为整型变量提供作用域为整个类的符号名称。

!!! Example
    同为这个`Bakrey`类
    ```cpp
    class Bakery:
    {
    private:
        enum {enum = 12};
        double costs[Months];
    }
    ```

**所有对象中都不包含枚举(枚举不会创建类数据成员)**，作用域为整个域的代码遇到它时，会进行替换。

#### 2.使用关键字`static`

!!! Example
    在`const`加上`static`
    ```cpp
    class Bakery:
    {
    private:
        static const int months = 12; 
        double costs[months];   
    }
    ```

这个`static`类型的变量会被与其他静态变量一起存储，而不是存储在对象中。

### 作用域内枚举

传统的枚举存在一些问题，其中之一是两个枚举定义中的枚举量可能发生冲突。

!!! Example
    如下，`egg Small`和`t_shirt Small`位于相同的作用域中，他们将发生冲突。
    ```cpp
        enum egg {Small, Medium, Large, Jumbo};
        enum t_shirt {Small, Medium, Large, Jumbo};
    ```

- `C++`提供了一种新枚举，作用域为类。

```cpp
enum class egg {Small, Medium, Large, Jumbo};
```

- `class`也可以替换为`struct`

但是作用域内枚举不能进行隐式类型转换，只能进行显式类型转换。


## Using Class

!!! Abstract
    - Overload(重载)
    - Friends(友元)
    - 自动转换和强制类型转换

### Overload(重载)

!!! Info Overload(函数重载)
    默认参数让您能够使用不同数目的参数调用同一个函数，而函数多态(函数重载)让您能够使用多个同名的函数。函数重载的关键是**函数特征标(function signature)**:函数的参数列表。

### 运算符重载限制

1.重载后的运算符必须有一个操作数是用户定义的类型。(防止用户为标准类型重载运算符)
2.不能违反原来的句法规则。
3.不能创建新的运算符
4.不能重载如下的运算符

|operator|function|
|:---:|:---:|
|sizeof|sizeof运算符|
|.|成员运算符|
|.*|成员指针运算符|
|::|作用域解析运算符|
|?:|条件运算符|
|typeid|一个RTTI运算符|
|const_cast|强制类型转换运算符|
|dynamic_cast|强制类型转换运算符|
|reinterpret_cast|强制类型转换运算符|
|static_cast|强制类型转换运算符|

5.大部分运算符都可以通过成员或非成员函数来进行重载，但下面的运算符只能通过成员函数进行重载。

- `=` : 赋值运算符
- `()` : 函数调用运算符
- `[]` : 下标运算符
- `->` : 通过指针访问类成员的运算符