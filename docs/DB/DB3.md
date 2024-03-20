---
counter: True  
---

# Chapter 3 SQL Introduction

!!! Abstract
    - Overview of the SQL Query Language
    - Data Definition
    - Basic Query Structure
    - Additional Basic Operations
    - Set Operations
    - Null Values
    - Aggregate Functions
    - Nested Subqueries
    - Modification of the Database 

!!! Warning "SQL与关系代数的关系"
    - SQL与关系代数并不是一一对应的
    - 但SQL语句都可以写成关系代数表达式

!!! Quote
    本次课孙老师语速感人，部分笔记后期对照[HobbitQia的笔记本](http://note.hobbitqia.cc)进行补全。

## History

- IBM Sequel language developed as part of System R project at the IBM San Jose Research Laboratory (Sequel: A structured English query language, 1974)
- Renamed SQL(Structured Query Language )
- ANSI and ISO standard SQL:
    - SQL-86, SQL-89, SQL-92 
    - SQL:1999, SQL:2003, SQL:2006, SQL:2008, SQL:2011, SQL:2016，SQL:2019 
- The benefits of the standards:
    - **Points out which language extensions are important and useful.**
    - **Guides the development of SQL implementations in databases.** 
    - **Provides the common syntax and semantics that most databases will implement.**
- Commercial systems offer most, if not all, SQL-92 features, plus varying feature sets from later standards and special proprietary features.  

## Data Definition Language

The **SQL data-definition language (DDL)** allows the specification of information about relations, including:

The schema for each relation.

- The domain of values associated with each attribute.
- Integrity constraints
- And as we will see later, also other information such as 
    - The set of indices to be maintained for each relations.
    - Security and authorization information for each relation.
    - The physical storage structure of each relation on disk.

## Domain Types in SQL

* `char(n).` Fixed length character string, with user-specified length n.   
定长字符串. C 语言里字符串结尾有 `\0`, 但数据库里没有，长度由定义而得。  
* `varchar(n).` Variable length character strings, with user-specified maximum length n.  
不定长字符串。不同的数据类型比较可能有问题（比如定长和不定长的字符串）
* `int.` Integer (a finite subset of the integers that is machine-dependent).
* `smallint.` Small integer (a machine-dependent subset of the integer domain type).
* `numeric(p,d).` Fixed point number, with user-specified precision of p digits, with d digits to the right of decimal point.     
p 表示有效数字位数, d 表示小数点后多少位。
***e.g.*** `number(3,1)` allows 44.5 to be store exactly,  but neither 444.5 or 0.32
* real, double precision.  Floating point and double-precision floating point numbers, with machine-dependent precision.
* `float(n).` Floating point number, with user-specified precision of at least n digits.

!!! Tip "difference between char and varchar"
    - 对于char(10),输入avi,会自动在后面补上空格
    - 对于varchar(10),输入avi，不会追加空格

## Built-in Data Types in SQL 

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

## Create Table Construct

An SQL relation is defined using the create table command:

```sql
create table r (A1 D1, A2 D2, ..., An Dn,
(integrity-constraint1),
...,
(integrity-constraintk))
```

* $r$ is the name of the relation
* each $A_i$ is an *attribute name* in the schema of relation $r$
* $D_i$ is the *data type* of values in the domain of attribute $Ai$

!!! Example
    ```sql
    create table instructor (
    ID        char(5),
    name    varchar(20) not null,
    dept_name varchar(20),
    salary     numeric(8,2))
    ```

**Integrity Constraints in Create Table**

* `not null`
* primary key $(A_1,\ldots,A_n)$  
不能为空; 表内不能有相同的 keys. 否则这样的数据是插入不进去的。
* foreign key $(A_m,\ldots,A_n)$ references r  
隐含：引用对应表的主键。

如果不符合完整性约束条件，插入可能会失败。

??? Example 
    ```sql
    create table instructor (
        ID             char(5),
        name           varchar(20) not null,
        dept_name      varchar(20),
        salary         numeric(8,2),
        primary key (ID),
        foreign key (dept_name) references department)
    ```

可以给一个缺省值**e.g.**　`default 0`

??? Example
    ```sql
    create table takes (
            ID                  varchar(5),
            course_id       varchar(8),
            sec_id            varchar(8),
            semester        varchar(6),
            year                numeric(4,0),
            grade              varchar(2),
            primary key (ID, course_id, sec_id, semester, year),
            foreign key (ID) references  student,
            foreign key (course_id, sec_id, semester, year) references section );
    ```
    - `sec_id` can be dropped from primary key above, to ensure a student cannot be registered for two sections of the same course in the same semester

如果引用的表中有条目被删除，可能会破坏完整性约束条件。有下面的方法：  

* *restrict*: 如果有条目是被引用的，那么不允许删除。
* *cascade*: 引用的条目被删了之后，引用者也一并删除
* *set null*: 引用者的指针设为 `null`. 
* *set default* : 引用者的对应`Attribute`值设置为缺省值 

如果引用的表中有更新，也有类似上面的四种方法.

!!! Example  
    ```sql
    create table course (
        course_id        varchar(8) primary key,
        title            varchar(50),
        dept_name        varchar(20),
        credits          numeric(2,0),
        foreign key (dept_name) references department (dept_name));
    ```
    - 可以直接将primary key写在后面

在 `create table` 中定义  
* `on delete cascade  |set null |restrict |set default`
* `on update cascade  |set null |restrict |set default`

**insert into** *instructor* **values** (‘10211’, ’Smith’, ’Biology’, 66000);
**insert into** *instructor* **values** (‘10211’, null, ’Biology’, 66000);

## Drop and Alter Table Constructs

- drop table student
    - Deletes the table and its contents
- delete from student
    - Deletes all contents of table, but retains table
- alter table
    - alter table r add A D
        - where A is the name of the attribute to be added to relation r  and D is the domain of A.
        - All tuples in the relation are assigned null as the value for the new attribute.  
        - alter table student add resume varchar(256);
    - alter table r drop A
        - where A is the name of an attribute of relation r
        - Dropping of attributes not supported by many databases

!!! Abstract
    - drop比delete更强，后者保留关系，前者不保留
    - alter可以给已有关系增加或减少属性

## SQL and Relational Algebra

```sql
select A1, A2, .. An
from   r1, r2, …, rm
where P
```

is equivalent to the following expression in multiset relational algebra

$\Pi_{A_1, .., A_n}(\sigma_P(r_1 \times r_2 \times .. \times r_m))$

```sql
select A1, A2, sum(A3)
from   r1, r2, …, rm
where P
group by A1, A2
```

is equivalent to the following expression in multiset relational algebra

$A_1, A_2G_{sum(A3)} ( \sigma_P (r_1 \times r_2 \times .. \times r_m)))$

More generally, the non-aggregated attributes in the select clause may be a subset of the group by attributes, in which case the equivalence is as follows:

```sql
select A1, sum(A3)
from   r1, r2, …, rm
where P
group by A1, A2
```

is equivalent to the following expression in multiset relational algebra
$\Pi_{A1,sumA3}( A1,A2 G_{sum(A3) as sumA3}(\sigma_P (r_1 \times r_2  \times .. \times r_m)))$


## Test for Absence of Duplicate Tuples

The unique construct tests whether a subquery has any duplicate tuples in its result.
**(Evaluates to “true” on an empty set)**
Find all courses that were offered at most once in 2009

```sql
  select T.course_id
  from course as T
  where unique (select R.course_id
                          from section as R
                          where T.course_id= R.course_id and R.year = 2009) ;
```

## Basic Query Structure

!!! Abstract
    - select用于列出查询结果中所需要的属性
    - from用于列出在查询求值中应该访问的关系列表
        - from后面一个表是单关系查询，多个表是多关系查询
    - where是作用在from子句中关系的属性上的谓词

### The `select` Clause 

- The **`select`** clause list the attributes desired in the result of a query.  
- corresponds to the **projection operation** of the relational algebra
- <u>SQL names are case insensitive</u>  大小写不敏感。（属性名字、表的名字等）

!!! Example
    Name $≡$ NAME $≡$ name

* To force the elimination of duplicates, insert the keyword **`distinct`** after select.
***e.g.*** `select distinct dept_name from instructor`  
可以加 **all** 显式指明不去重
* An asterisk in the select clause denotes “all attributes”  
***e.g.*** `select * from instructor`
* The select clause can contain arithmetic expressions involving the operation, +, –, $\div$, and /, and operating on constants or attributes of tuples.  
可以有加减乘除运算 ***e.g.*** `select ID, name, salary/12 from instructor`

!!! Example "find the names of all instructors"
    - select name
    - from instructor

### The `where` Clause

- The **`where`** clause specifies conditions that the result must satisfy.
- Corresponds to the selection predicate of the relational algebra.  
- <u>where允许我们选出那些在from里满足特定关系的元组</u>

* SQL includes a **`between`** comparison operator
***e.g.*** ` select name from instructor where salary between 90000 and 100000`
* Tuple comparison  
元组相等等价于各个元素都相等。
***e.g.*** `select name, course_id from instructor, teaches where (instructor.ID, dept_name) = (teaches.ID, ’Biology’);`  

??? Example "To find all instructors in Comp. Sci. dept with salary > 80000"		
    ```sql
    select name	
    from instructor	
    where dept_name = ‘Comp. Sci.'  and salary > 80000
    ```

- Comparison results can be combined using the logical connectives and, or, and not.(逻辑连词) 
- Comparisons can be applied to results of arithmetic expressions.(算术表达式，比较运算符)

### The `from` clause

The **`from`** clause lists the relations involved in the query.  
Corresponds to the **Cartesian product** operation of the relational algebra.  

### Natural Join

***e.g.*** `select * from instructor natural join teaches;`

***e.g.*** `select name, course_id from instructor, teaches where instructor.ID = teaches.ID;`  
`select name, course_id from instructor natural join teaches;`   
上面两条语句是等价的。

<u>**Unrelated attributes with same name** which get equated incorrectly!</u>  

!!! Example 
    `course(course_id,title, dept_name,credits）`, `teaches(ID, course_id,sec_id,semester, year)`, `instructor(ID, name, dept_name,salary）` 这里的 department 含义各有不同，不能直接自然连接。  
    可以写成 `select name, title from (instructor natural join teaches）join course using(course_id);` 即规定连接的属性，对应于 $\sigma_\theta$   

    Find students who takes courses across his/her department.  
    可写作 

    ``` SQL
    select distinct student.id
        from (student natural join takes) 
               join course using (course_id） 
        where student.dept_name <> course.dept_name
    ```

!!! bug "要注意自然连接的语法规范"
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/f489c67d7ce908116750664f233e81d.png)

### The Rename Operation

The SQL allows renaming relations and attributes using the **`as`** clause.  
`old-name as new-name`

***e.g.*** 
`select distinct T. name from instructor as T, instructor as S where T.salary > S.salary and S.dept_name = ‘Comp. Sci.’`

- S,T一般被称为表别名/相关名称/相关变量/元组变量

* Keyword **`as`** is optional and may be omitted.  

### String Operations

SQL includes a **string-matching operator** for comparisons on character strings.  The operator **`like`** uses patterns that are described using two special characters. 

注意单引号表示字符串。

* **percent (%)**.  The % character matches any substring.  
***e.g.*** `select name from instructor where name like '%dar%'; `
找名字里面含有 `dar` 的字符串。 
* **underscore (_)**.  The _ character matches any character.  

!!! Example "Match the string"
    匹配字符串 `'100 %'` 但是 `%` 符号被我们作为了通配符，这里我们需要用到转义符 `\`. `\%` 即将 `%` 作为正常字符匹配。  
    `\` 也可以是一个基本符号，我们需要在后面写出 `escape` 表示其在这里作为转义符。类似地我们还可以将转义符定义为 `#`. 
    ``` SQL
    like ‘100 \%'  escape  '\' 
    like ‘100 \%'  
    like ‘100  #%'  escape  ‘#' 
    ```

SQL supports a variety of string operations such as

* concatenation (using `||`)
* converting from upper to lower case (and vice versa)
* finding string length, extracting substrings, etc.  

### Ordering the Display of Tuples

- 关系是无序的，但我们可以规定显示出来的顺序。

* We may specify desc for *descending* order or asc for *ascending* order, for each attribute; ascending order is the default.  
***e.g.*** `order by name desc`  
可以排序的类型，如字符串、数字。
* Can sort on multiple attributes  
***e.g.*** `order by  dept_name, name` 先按第一个排，如果第一个元素相同再按第二个排。

!!! Example "List in alphabetic order the names of all instructors" 
    ```sql
    select distinct name
    from   instructor
    order by name
    ```

### The `limit` Clause

- The **`limit`** clause can be used to constrain the number of rows returned by the select statement.（前几名）
- limit clause takes one or two numeric arguments, which must both be nonnegative integer constants: 

* `limit offset, row_count`
* `limit row_count == limit 0, row_count`

***e.g.***  `select  name from instructor order by salary desc limit 3；   //  limit 0,3 `

### Set Operations

* `union, intersect, except` 是严格的集合操作，会对结果去重.
* `union all`, `intersect all` and `except all`. 保持多重集。

??? Example
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/191c8625d2e347e8f4a565eeca9a26d.png)

### Null Values

**null** signifies *an unknown value* or that *a value does not exist*.

* The result of any arithmetic expression involving null is null.  
***e.g.*** `5 + null` returns null
* The predicate is null can be used to check for null values.  
***e.g.*** Find all instructors whose salary is null.   
`select name from instructor where salary is null`
* Comparisons with null values return the special truth value: **unknown**.  
![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/427aa4c0c3683b28486c59020d5964c.png) 

* Result of select predicate is treated as false if it evaluates to unknown

### Aggregate Functions

??? Example
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/febd5a1720e50fd7ac88c46962f27d8.png)

注意在 `select` 里出现的属性，除了统计函数以外，一定要是分组属性里出现过的。

!!! tip
    - 星号"*"可以用在select子句中表示“所有的属性，如`select *`或`select instructor.*`

#### Having Clause

对分组后的组进行筛选。  

***e.g.*** 

```sql
select dept_name, count (*) as cnt
from instructor
where  salary >=100000
group by dept_name
having  count (*) > 10
order by cnt;
```

predicates in the **`having`** clause are applied *after the formation of groups* whereas predicates in the **`where`** clause are applied *before forming groups*.  

#### Null Values and Aggregates

`select sum (salary) from instructor`  

* Above statement ignores null amounts
* Result is null if there is no non-null amount
* All aggregate operations except `count(*)` ignore tuples with null values on the aggregated attributes

??? Example "Arithmetric expression with Aggregate functions"
    <div align=center> <img src="http://cdn.hobbitqia.cc/202303131130602.png" width = 60%/> </div>    

    第二个表示重名率小于千分之一的系。

### Nested Subqueries

A **subquery** is a **select-from-where** expression that is nested within another query.

#### Set Membership

`in, not in`

??? Example
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/9a6c042a59e7ee7a232f8c18b977367.png)  

除了单个元素外，元组也可以使用 `in, not in` 

Find the total number of (distinct) students who have taken course sections taught by the instructor with ID 10101

```sql
select count (distinct ID)
from takes
where (course_id, sec_id, semester, year) in 
        (select course_id, sec_id, semester, year
                                 from teaches
                                 where teaches.ID= ‘10101’);
```

#### Set Comparison

* `some` 某些成员   
* `all` 所有成员

!!! Example
    工资大于生物系中的某些老师的老师.  
    ``` SQL
    select name
    from instructor
    where salary > some (select salary
                                        from instructor
                                        where dept_name = ’Biology’);
    ```

#### Scalar Subquery

**Scalar (标量) subquery** is one which is used where a single value is expected.  

!!! Example
    ``` SQL 
    select name
    from instructor
    where  salary * 10 > 
        (select budget from department 
        where department.dept_name = instructor.dept_name)
    ```  

    这里 `dept_name` 是这个表的主键，只返回一个元组，这种情况下是可以不用 `some, all` 的。

Runtime error if subquery returns more than one result tuple.  

#### Test for Empty Relations

The exists construct returns the value true if the argument subquery is nonempty.

* `exists r` $\Leftrightarrow r \neq \emptyset$
* `not exists r` $\Leftrightarrow r = \emptyset$

!!! Example 
    09FALL 开的课，而且 10SPRING 也开了课。
    ``` SQL
    select course_id
    from section as S
    where semester = ’Fall’ and year= 2009 and                exists (select *                            
    from section as T                      
        where semester = ’Spring’ and year= 2010 and S.course_id= T.course_id);
    ```

!!! Example 
    Find all students who have taken all courses offered in the Biology department.  
    SQL 语句往往需要逆向考虑，即找到这样的学生，不存在他没选过的生物系的课。  
    ``` SQL
    select distinct S.ID, S.name
    from student as S
    where not exists ( (select course_id
                            from course
                            where dept_name = ’Biology’)
                    except
                        (select T.course_id
                            from takes as T
                            where S.ID = T.ID));
    ```

#### Test for Absence of Duplicate Tuples

The **`unique`** construct tests whether a subquery has any duplicate tuples in its result.  
验证是否是一个集合，而非多重集。  

* Evaluates to “true” on an empty set.   
可以将 unique 理解为 at most once. 

!!! Example
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/cf63c652731db66c5ceedb97177e37d.png)  
    
    - 如果不加 exist, 可能有没开过的课也被算进去。我们这里求得是恰好只开过一次的。

??? Example
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/427b5ec341b25c23098cbbf110afe55.png)
    - 也可以用 `group by count(*) > 1` 实现。

### With Clause

The **`with`** clause provides a way of defining a temporary view whose definition is available only to the query in which the with clause occurs.  
构造一个临时表(子查询找最值之类的) 

!!! Example
    Find all departments with the maximum budget.
    ``` SQL
    with max_budget (value) as 
        (select max(budget)
        from department)
    select dept_name
    from department, max_budget
    where department.budget = max_budget.value;
    ```

!!! Example "两个临时表"
    - Complex Queries using With Clause
    ```sql
    with dept _total (dept_name, value) as //两个临时表 
            (select dept_name, sum(salary) 
             from instructor
             group by dept_name),
            dept_total_avg(value) as
            (select avg(value)
             from dept_total)
    select dept_name
    from dept_total, dept_total_avg
    where dept_total.value >= dept_total_avg.value;
    ```

## Modification of the Database(数据库的修改)

### Deletion

??? Example 
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/5c936c3136433d47871045f2b03558e.png) 

### Insertion

??? Example 
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/6d23055f190db59ad0cdb68377b14ae.png) 

    第二种写法，可以不用严格按照定义的元素顺序，只要和自己写的对应即可。

除了基本写法，我们还可以在 `insert` 后跟查询语句，把查询结果插入到表里去。

??? Example 
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/0222d06914c1f458922bd1c59aae612.png)  

### Updates

`update ... set ...`

The order is important. So it can be done better using the **`case`** statement.  

??? Example
    ![](https://blog-pic-thorin.oss-cn-hangzhou.aliyuncs.com/de35aa4dff8ddefe3af350c6ddaf1c0.png)