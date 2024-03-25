---
counter: True  
---

# Chapter 5 Advanced SQL

!!! Abstract
    - Accessing SQL From a Programming Language 
    - Functions and Procedures
    - *Triggers
    - **Recursive Queries
    - **Advanced Aggregation Features
    
## Accessing SQL from a Programming Language

### 两个主要原因

* Not all queries can be expressed in SQL, since SQL does not provide the full expressive power of a general-purpose language.  
不是所有的查询都能用 SQL 语言表达。
* Non-declarative actions -- such as printing a report, interacting with a user, or sending the results of a query to a graphical user interface -- cannot be done from within SQL.  
用户交互是图形界面，语音、图像，数据库不具备这方面的功能。

### 两个主要方式

从高级语言（如 C）访问数据库，主要是下面两种方式：

动态SQL dynamic SQL

* **API**(Application Program Interface) -- A general-purpose program can connect to and communicate with a database server using a collection of functions.  
函数库
* **Embedded SQL(嵌入式SQL)** -- provides a means by which a program can interact with a database server.   
把 SQL 语句嵌入到语言内
    * The SQL statements are translated at *compile time* into function calls.   
    * At runtime,  these function calls connect to the database  using an API that provides dynamic SQL facilities.  

### JDBC and ODBC

**API** (application-program interface) for a program to interact with a database server  
Application makes calls to

* Connect with the database server  
建立连接
* Send SQL commands to the database server  
指令发送到服务器
* Fetch tuples of result one-by-one into program variables  
拿回结果

SQL 与 C 语言存在鸿沟（如 select 得到的是集合，但是 C 语言没有这种类型）会返回指针/迭代器

* **ODBC** (Open Database Connectivity) works with C, C++, C#
* **JDBC** (Java Database Connectivity) works with Java  
通过类定义，将数据库操作封装到 Java 内
* Embedded SQL in C
* **SQLJ** - embedded SQL in Java
* **JPA**(Java Persistence API)  - OR mapping of Java  

#### JDBC

**JDBC** is a Java API for communicating with database systems supporting SQL.  
1.调用驱动程序管理器来创建对象
2.抛出异常并进行检查

``` Java
public static void JDBCexample(String dbid, String userid, String passwd) 
        { 
     try { 
   Connection conn = DriverManager.getConnection("jdbc:oracle:thin:@db.yale.edu:2000:univdb", userid, passwd); 
          Statement stmt = conn.createStatement(); 
              ... Do Actual Work ...
          stmt.close();	
          conn.close();	
     }		
     catch (SQLException sqle) { 		
          System.out.println("SQLException : " + sqle);		
     }		
        }
```

* Open a connection
* Create a “statement” object
* Execute queries using the Statement object to send queries and fetch results
* Exception mechanism to handle errors

??? Example
    * Update to database
    ``` Java
    Update to database 
    try {
        stmt.executeUpdate(
            "insert into instructor values(’77987’, ’Kim’, ’Physics’, 98000)");
            } catch (SQLException sqle)
            {
                System.out.println("Could not insert tuple. " + sqle);
            }
    ```
    * Execute query and fetch and print results
    ``` Java
    ResultSet rset = stmt.executeQuery(
            "select dept_name, avg (salary)
            from instructor
            group by dept_name");
            while (rset.next()) {
                System.out.println(rset.getString("dept_name") + " " + rset.getFloat(2));
            }
    ```
    * Getting result fields:
    ``` Java
    rset.getString(“dept_name”) and rset.getString(1) equivalent if dept_name is the first argument of select result.
    ```
    * Dealing with Null values
    ``` Java
    int a = rset.getInt(“a”);
        if (rset.wasNull()) Systems.out.println(“Got null value”);
    ```

**Prepared Statement**  

??? Example "Prepared Statement"
    ``` Java
    PreparedStatement pStmt = conn.prepareStatement(
            "insert into instructor values(?,?,?,?)");
    pStmt.setString(1, "88877");      
    pStmt.setString(2, "Perry");
    pStmt.setString(3, "Finance");   
    pStmt.setInt(4, 125000);
    pStmt.executeUpdate();    
    pStmt.setString(1, "88878");
    pStmt.executeUpdate();
    ```
    这里空格是占位符，表示执行时需要提供四个参数。  
    `setString, setInt` 就是把第几个占位符设置为参数，并 `executeUpdate` 进行插入。

!!! Warning "SQL Injection"
    Always use prepared statements when taking an input from the user and adding it to a query. **NEVER** create a query by concatenating strings which you get as inputs.  
    SQL 注入攻击。

    Suppose query is constructed using `select * from instructor where name = ’" + name + “ ’`  
    Suppose the user, instead of entering a name, enters: `X’ or ’Y’ = ’Y`  
    then the resulting string of the statement becomes: `select * from instructor where name = ’" + "X’ or ’Y’ = ’Y" + “’`  
    which is: `select * from instructor where name = ’X’ or ’Y’ = ’Y’`   
    User could have even used  
    `X’; update instructor set salary = salary + 10000; `  
    then `select * from instructor where name = ’X’; update instructor set salary = salary + 10000;`  
    Always use prepared statements, with user inputs as parameters

**Metadata Features**  

* **ResultSet metadata**  

    ??? Example
        after executing query to get a ResultSet rs:
        ``` Java
        ResultSetMetaData rsmd = rs.getMetaData();
        for(int i = 1; i <= rsmd.getColumnCount(); i++) {
            System.out.println(rsmd.getColumnName(i));
                    System.out.println(rsmd.getColumnTypeName(i));
            }
        ```

* **Database metadata**

    ??? Example
        ``` Java
        DatabaseMetaData dbmd = conn.getMetaData();
        ResultSet rs = dbmd.getColumns(null, "univdb", "department", "%");
        // Arguments to getColumns: Catalog, Schema-pattern, Table-pattern,
        // and Column-Pattern
        // Returns: One row for each column; row has a number of attributes
        // such as COLUMN_NAME, TYPE_NAME
        while( rs.next()) {
            System.out.println(rs.getString("COLUMN_NAME"),
                                                            rs.getString("TYPE_NAME");
        ```

**Transaction Control in JDBC**  

* Can turn off automatic commit on a connection `conn.setAutoCommit(false);`
* Transactions must then be committed or rolled back *explicitly* `conn.commit();` or `conn.rollback();`
* `conn.setAutoCommit(true)` turns on automatic commit.

所有的数据库功能都是通过 Java 封装好的类来实现的。

#### ODBC

(相较于JDBC更加复杂)

<div align=center> <img src="http://cdn.hobbitqia.cc/202303271036474.png" width = 60%/> </div>

Each database system supporting ODBC provides a "driver" library that must be linked with the client program.

??? Example
    ``` C
    int ODBCexample()
	{
        RETCODE error;
        HENV    env;     /* environment */ 
        HDBC    conn;  /* database connection */ 
        SQLAllocEnv(&env);
        SQLAllocConnect(env, &conn);
        SQLConnect(conn, “db.yale.edu", SQL_NTS, "avi", SQL_NTS, "avipasswd", SQL_NTS); 
        { ... Do actual work ... }

        SQLDisconnect(conn); 
        SQLFreeConnect(conn); 
        SQLFreeEnv(env); 
    }
    ```

同一个数据库可能服务于多个用户，而且使用的编程语言可能不同，如字符串的结束标志可能也不同，因此需要用 `SQL_NTS` 标识。(表示空值结束)

* Program sends SQL commands to database by using `SQLExecDirect`
* Result tuples are fetched using `SQLFetch()`
* `SQLBindCol()` binds C language variables to attributes of the query result 
    * When a tuple is fetched, its attribute values are automatically stored in corresponding C variables.  
    * Arguments to SQLBindCol()
        * ODBC stmt variable, attribute position in query result
        * The type conversion from SQL to C.  
        * The address of the variable. 
        * For variable-length types like character arrays,   
            * The maximum length of the variable  
            * Location to store actual length when a tuple is fetched.
            * Note: A negative value returned for the length field indicates null value

??? Example
    ``` C
    Main body of program
    char deptname[80];
    float salary;
    int lenOut1, lenOut2;
    HSTMT stmt;
    char * sqlquery = "select dept_name, sum (salary) from instructor group by dept_name";
    SQLAllocStmt(conn, &stmt);
    error = SQLExecDirect(stmt, sqlquery, SQL_NTS);
    if (error == SQL SUCCESS) {
        SQLBindCol(stmt, 1, SQL_C_CHAR, deptname , 80, &lenOut1);
        SQLBindCol(stmt, 2, SQL_C_FLOAT, &salary, 0 , &lenOut2);
        while (SQLFetch(stmt) == SQL_SUCCESS) {
            printf (" %s %g\n", deptname, salary);
        }
    }
    SQLFreeStmt(stmt, SQL_DROP);
    ```
    定义数组需要多一个，否则会有截断。如 `char deptname[11];` 才能定义十个元组。  
    如果结果为空，则 `lenOut` 为 -1. 

**ODBC Prepared Statements**

* SQL statement prepared: compiled at the database
    * To prepare a statement `SQLPrepare(stmt, <SQL String>);`  
    * To bind parameters `SQLBindParameter(stmt, <parameter#>,  ... type information and value omitted for simplicity..)`
    * To execute the statement `retcode = SQLExecute(stmt); `
* Can have placeholders: ***e.g.*** `insert into account values(?,?,?)`
* Repeatedly executed with actual values for the placeholders

**More ODBC Features**  

* Metadata features  
    * finding all the relations in the database and
    * finding the names and types of columns of a query result or a relation in the database.
* By default, each SQL statement is treated as a separate transaction that is committed automatically.
    * Can turn off automatic commit on a connection `SQLSetConnectOption(conn, SQL_AUTOCOMMIT, 0)} `
    * Transactions must then be committed or rolled back explicitly by `SQLTransact(conn, SQL_COMMIT)` or `SQLTransact(conn, SQL_ROLLBACK)`