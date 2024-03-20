---
counter: True  
---

# Chapter 4 Intermediate SQL

!!! Abstract
    - Joined Relation
    - SQL Data Types and Schemas
    - Integrity Constraints
    - Views
    - Indexes
    - Transactions
    - Authorization

!!! warning 
    本周部分内容在[关系模型](./DB2.md)中已经更加地详细记录过，因此本周的课程强度相对较低。~~孙老师甚至给了十五分钟做小测~~

## Joined Relations

- Join operations take two relations and return as a result another relation.
- Join operations are typically used as subquery expressions in the from clause
- Join condition – defines which tuples in the two relations match, and what attributes are present in the result of the join.
- Join type – defines how tuples in each relation that do not match any tuple in the other relation (based on the join condition) are treated.

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/c1bfd608eda2447cb96e94d2cb38944.png)

??? Example "Join operations – Example"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/69504ffc677ddafa3924a8afa4337d1.png)

??? danger "natural join的特性"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/b3e6ad6358c8e2f06dcf44b1553b0cf.png)
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/4399fa2172649245b53e070590ce759.png)
    - 孙老师的quzi2的第四题的参考答案应该有误，多加了natural,natural会自动去除重复的attributes


### Outer Join

- An extension of the join operation that avoids loss of information.
- Computes the join and then adds tuples form one relation that does not match tuples in the other relation to the result of the join. 
- Uses *null* values

??? Example "Left Outer Join"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/00185d83ac2059e0c031d030107638b.png)

??? Example "Right Outer Join"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/f5f84433c1bde29ff15bcc895dcd0a8.png)

??? Example "Full Outer Join"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/b0365126c2b7ec61f2e64191f8e5385.png)

??? Example "Joined Relations – Examples"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/6452e02f3c5680d36acf41788ad0861.png)
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/f7ce748284f25f9f7623598a1b97bc8.png)

## SQL Data Types and Schemas

### Built-in Data Types in SQL 

- **date** Dates, containing a (4 digit) year, month and date
    - Example: **date** ‘2005-7-27’
- **time*** Time of day, in hours, minutes and seconds.
    - Example: **time** ‘09:00:30’     **time** ‘09:00:30.75’
- **timestamp**: date plus time of day
    - Example: **timestamp** ‘2005-7-27 09:00:30.75’
- **interval** period of time
    - Example:  interval ‘1’ day
    - Subtracting a date/time/timestamp value from another gives an interval value
    - Interval values can be added to date/time/timestamp values
    - **date, time functions:** 
        - `current_date(), current_time()`
        - `year(x), month(x), day(x), hour(x), minute(x), second(x)`

### User-Defined Types

`create type` construct in SQL creates user-defined type.  

!!! Example
    `create type Dollars as numeric (12,2) final` 定义了 `Dollars` 这个类型后，我们就可以把它当作元类型使用。  
    ``` SQL
    create table department
        (dept_name varchar (20),
        building varchar (15),
        budget Dollars);
    ```
这样可以支持强类型检查，可以防止如 200 美元 + 300 RMB 得到 500 元的错误。

### Domains

`create domain` construct in SQL-92 creates user-defined domain types.  

**Domains can have constraints**, such as `not null`, specified on them.

!!! Example
    `create domain person_name char(20) not null` 
    ``` SQL
    create domain degree_level varchar(10)
        constraint degree_level_test
        check (value in (’Bachelors’, ’Masters’, ’Doctorate’));
    ```
    这里的 constraint 可以对 domain 的取值进行限制。

不同 type 的变量，即使定义相同，也不能进行运算。不同 domain 的变量（基础类型相同）可以运算。

### Large-Object Types

Large objects (photos, videos, CAD files, etc.) are stored as a large object:  

* **blob**: binary large object -- object is a large collection of uninterpreted binary data (whose interpretation is left to an application outside of the database system)  
存储大对象数据类型，实际上只是存放指针。

    ??? Example "BLOB in MySQL"
        * TinyBlob ：  0 ~ 255 bytes.
        * Blob： 0 ~ 64K bytes.
        * MediumBlob ： 0 ~ 16M bytes.
        * LargeBlob : 0 ~ 4G  bytes.

* **clob**: character large object -- object is a large collection of character data  
文本大对象  

<!-- ### User-Defined Types

create type construct in SQL creates user-defined type

`create type Dollars as numeric (12,2) final`

```sql
create table department(
    dept_name varchar (20),
    building varchar (15),
    budget Dollars);
``` -->

## Integrity Constraints on a Single Relation 

- not null
- primary key
- unique
- check (P), where P is a predicate
- foreign key

### assertion(断言)

- 表达了我们希望数据库总能满足的一个条件

!!! Example
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/4d374b2d29f5c42ccbb11da9d687c2b.png)


## Transaction

### ACID Properties

- **Atomicity**.  Either all operations of the transaction are properly reflected in the database or none are.
- **Consistency**.  Execution of a transaction in isolation preserves the consistency of the database.
- **Isolation**.  Although multiple transactions may execute concurrently, each transaction must be unaware of other concurrently executing transactions.  Intermediate transaction results must be hidden from other concurrently executed transactions.  
    - That is, for every pair of transactions $T_i$ and $T_j$, it appears to $T_i$ that either $T_j$, finished execution before $T_i$ started, or $T_j$ started execution after $T_i$ finished.
- **Durability**.  After a transaction completes successfully, the changes it has made to the database persist, even if there are system failures. 

