# Chapter 3 Object Interaction

## 多文件工程

### Definition of a class

- In C++, separated .h and .cpp files are used to define one class.
- Class declaration and prototypes in that class are inthe header file (.h).
- All the bodies of these functions are in the source file (.cpp).

### Compile unit

- The compiler sees only one .cpp file, and generates `.obj` file
- The linker links all `.obj` into one executable file
- To provide information about functions in other `.cpp` files, use `.h`
- *只有.cpp才会生成obj*

### Header = interface

- The header is a contract between you and the user of your code.
- The compile enforces the contract by requiring you to declare all structures and functions before they are used.

<img src="https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/342c3783ef6657ef02f289f59241786.png"/>

### Declarations vs. Definitions

- A .cpp file is a compile unit
- Only declarations are allowed to be in .h 
    - extern variables
    - function prototypes 
    - class/struct declaration

## The Makefile utility

### Motivation

- Small programs single file
- “Not so small” programs :
    – Many lines of code
    – Multiple components
    – More than one programmer

## C'tor and D'tor

### Point::init()

```cpp
class Point {
public: 
    void init(int x, int y); 
    void print() const; 
    void move(int dx, int dy);
private: 
    int x; 
    int y;
};

Point a;
a.init(1,2);
a.move(2,2);
a.print();
```

## Guaranteed initialization with the constructor

- If a class has a constructor, the compiler automatically calls that constructor at the point an object is created, before client programmers can get their hands on the  object.
- The  name of the constructor is the same as the name of the class.

