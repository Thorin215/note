---
counter: True  
---

# Chapter 1 Introduction

??? Abstract 
    * Database Systems
    * Database Applications
    * Purpose of Database Systems
    * View of Data
    * Data Models
    * Database Languages
    * Database Design
    * Database Engine
    * Database Users and Administrators
    * History of Database Systems

## Database Systems

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/3381734a9f68b21a3d0e663604d0aed.png)


??? Info DBMS 

    - Database is a collection of interrelated data about a enterprise, which is managed by a DBMS(Database Management System).
    - The primary goal of a DBMS is to provide a way to store and retrieve database information that is both convenient and efficient. 
    - Management of data involves both deﬁning structures for storage of information and providing mechanisms for the manipulation of information. 
    - The database system must ensure the safety of the information stored, despite system crashes or attempts at unauthorized access. 
    - If data are to be shared among several users, the system must provide concurrency control mechanisms to avoid possible anomalous results. 

??? Question Difference Between File processor and DBMS

    -  A file-processing system can only coordinate physical access of data, while a DBMS can coordinate both phisical and logical access.
    - A file-processing system only allow accessing data through the way determined in advance, while a DBMS allow doing this flexibly.
    - A file-processing system allows multiple programs access diffrent data at the same time, or 2 programs access the same file at the same time if they both have read-only access to it. On the contrary, a DBMS allows multiple users accessing the same data at the same time.
    - In a file-processing system, a file written by one program may be unreadable for another program. But in a DBMS, data duplications are reduced by make the data physically available 
    to all the programs possessing access to it.




## Purpose of Database Systems

*In the early days, database applications were built directly on top of **file systems**，which leads to:*

- Data redundancy（**数据冗余**）and inconsistency（**不一致**）
  - Multiple file formats, duplication of information in different files

- Data isolation（**数据孤立，数据孤岛**） — multiple files and formats

- Difficulty in accessing data (**存取数据困难**）
  - Need to write a new program to carry out each new task

- Integrity Problem(**完整性问题**）

!!! Info
    - Integrity constraints become “buried” in program code rather than being stated explicitly(显式的),数据与约束条件存储在此程序中，不是显式的
    - Example:  “account balance >=1”
    - Hard to add new constraints or change existing ones
    
- Atomicity problems(原子性问题)

!!! Example
    - Transfer of funds from one to another(**转账问题**)
    - Failures may leave database in an **inconsistent state** with partial updates carried out
    
- Concurrent access anomalies(并发访问异常)

!!! Example
    - Concurrent access needed for performance
    - Uncontrolled concurrent accesses can lead to inconsistencies
    - Example: Two people reading a balance (say 100) and updating it by saving money (say 50 each) at the same time
    

- Security Problems(安全性问题)

!!! Example
    - 银行审计(Audit)
    - 权限(Privilege)
  
## Characteristics of DataBases

??? Abstract
    - data persistence(数据持久性)
    - convenience in accessing data(数据访问便利性)
    - data integrity （数据完整性）
    - concurrency control for multiple users(多用户并发控制)
    - failure recovery（故障恢复） 
    - security  control（安全控制）
 
## Data Models

- A collection of tools for describing 
  - Data (数据)
  - Data relationships(联系)
  - Data semantics(语义)
  - Data constraints(约束)


- **Relational Model(关系模型)**
- Object-based data models 
  - Object-oriented  (面向对象数据模型)
  - Object-relational(对象-关系模型模型)
- Semistructured data model  (XML)(半结构化数据模型)
- Other older models:
  - Network model  (网状模型)
  - Hierarchical model(层次模型)
- Entity-Relationship Model(实体联系模型)

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/20240226143439.png)

这里ID可以确定一行，称之为key
   
## View of data

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/b19a02f33dee02df92f59884ac23b2b.png)

!!! Info View of data

    - 物理模式:描述数据如何存储
    - 逻辑模式:描述了存储的数据以及之间关系
    - 视图模式:应用程序出于安全等目的隐藏了大量细节，且是多样的

!!! Note 目的

    - Hide the complexities(隐藏复杂性)
    - Enhance the adaptation to changes(增强适应性)

## Schema and Instance

- Similar to types and variables in programming languages
- Logical Schema（**逻辑模式**）– the logical structure of the database 
  - Example: The database consists of information about a set of customers and accounts and the relationship between them
  - Analogous to type information of a variable in a program
- Physical schema （**物理模式**） – the overall physical  structure of the database 
- Instance（**实例**） – the actual content of the database at a particular point in time Analogous to the value of a variable


## Data Independence

- Physical Data Independence（物理数据独立性）– the ability to modify the physical schema without changing the logical schema
  - Applications depend on the logical schema
  - In general, the interfaces between the various levels and components should be well defined so that changes in some parts do not seriously influence others.

- Logical Data Independence(逻辑数据独立性) – the ability to modify the logical schema without changing the user view schema

## Database Languages

??? Abstract
    
    - Data Definition Language (DDL) 
    - Data Manipulation Language (DML) 
    - SQL Query Language
    - Application Program Interface （API）
    
### Data Definition Language (DDL)（数据定义语言）

Specification notation for defining the database schema
Example:	

```java
create table instructor (
    ID         char(5),                           
    name       varchar(20),
    dept_name  varchar(20),
    salary     numeric(8,2)
);
```

- DDL compiler generates a set of table templates stored in a data dictionary（数据字典）
- Data dictionary contains metadata (元数据， i.e., data about data)
  - Database schema 
  - Integrity constraints（完整性约束）
    - Primary key (ID uniquely identifies instructors)（主健）
    - Referential integrity (references constraint in SQL)（参照完整性）
      - **e.g.** dept_name value in any instructor tuple must appear in department relation
  - Authorization（权限）
- 常用的语句关键字有 CREATE、DROP、ALTER 等

!!! Example

    ```java
    CREATE TABLE 表名(
    列名1 数据类型,
    列名2 数据类型,
    列名3 数据类型,
    ...
    )

    ALTER TABLE 表名;
    eg：ALTER TABLE 表名 ADD 列名 数据类型;（添加一个列）
        ALTER TABLE 表名 CHANGE 列名 新列名 新数据类型;（修改列名）
        ALTER TABLE 表名 DROP 列名;

    DROP TABLE 表名;
    DROP DATABASE 数据库名;
    ```

### Data Manipulation Language (DML)（数据操作语言）

- Language for accessing and manipulating the data organized by the appropriate data model
- DML also known as **query** language
- Two classes of languages 
  - Procedural（过程式） – user specifies what data is required and how to get those data 
  - Declarative (nonprocedural， 陈述式，非过程式) – user specifies what data is required without specifying how to get those data
- **SQL** is the most widely used query language

!!! Example

    ```java
    INSERT INTO 表名 (字段1,字段2,...) values (某值,某值,...),(某值,某值,...);

    UPDATE 表名 SET 列名=新值 WHERE 限定条件;

    DELETE FROM 表名 WHERE 限定条件;
    ``` 

### SQL Query Language 

SQL: widely used non-procedural language

??? Example
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/ff065e32c9b48fac901e0a856fd02ef.png)


## DataBase Design

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/2d3a0e7711d91cff9381188ebd56973.png)

## DataBase Engine
 
??? Abstract 
    - The storage manager
    - The query processor  
    - The transaction management component.
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/20240229214018.png)

### Storage Manager

- A program module that provides the interface between the low-level data stored in the database and the application programs and queries submitted to the system.
- The storage manager is responsible to the following tasks: 
  - Interaction with the OS file manager 
  - Efficient storing, retrieving and updating of data
- The storage manager components include:
  - File manager
  - Buffer manager
  - Authorization and integrity manager
  - Transaction manager
- Covered in Part five - Chapters 12 , 13, 14 
- The storage manager implements several data structures as part of the physical system implementation:
  - **Data files** -- store the database itself
  - **Data dictionary** --  stores metadata about the structure of the database, in particular the schema of the database.
  - **Indices** --  can provide fast access to data items.  A database index provides pointers to those data items that hold a particular value.  
  - **Statistical data**

### Query Processor

- The query processor components include:
  - **DDL interpreter** --  interprets DDL statements and records the definitions in the data dictionary.
  - **DML compiler** -- translates DML statements in a query language into an evaluation plan consisting of low-level instructions that the query evaluation engine understands.
    - **The DML compiler** performs query optimization; that is, it picks the lowest cost evaluation plan from among the various alternatives.
  - **Query evaluation engine** -- executes low-level instructions generated by the DML compiler.

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/20240229214329.png)

### Transaction Management(事务管理)

- **A transaction** （事务）is a collection of operations that performs a single logical function in a database application.
- **Recover Manager** (恢复管理器)ensures that the database remains in a consistent (correct) state despite system failures (e.g., power failures and operating system crashes) and transaction failures.
- **Concurrency-control manager**（并发控制管理器） controls the interaction among the concurrent transactions, to ensure the consistency of the database.

## Database Users

![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/b4f3b75b041f663149f542652a287bb.png)

## Turing Award

Charles W. Bachman(1924-2017)
Edgar F. Codd (1923-2003)
Jim Gray(1944-2007)
Michael Stonebraker (1943~ )