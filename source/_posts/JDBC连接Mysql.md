---
title: JDBC连接Mysql
date: 2020-11-24 16:07:43
tags:
---

### 一：JDBC连接

在IJ中使用maven构建一个项目，在pom.xml中加入依赖：

```xml
    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
            <scope>runtime</scope>
        </dependency>
    </dependencies>
```

<!--more-->

**Connection**代表一个JDBC连接，在使用JDBC时，它相当于Java程序到数据库的连接（通常是TCP连接）。打开一个Connection时，需要准备URL、用户名和口令，才能成功连接到数据库。

URL是由数据库厂商指定的格式，Mysql的URL是：

```java
jdbc:mysql://<hostname>:<port>/<db>?key1=value1&key2=value2
```

我的Mysql运行在本地`localhost`，通过`SHOW DATABASES`和`STATUS`分别确认了**数据库的名称**和**端口号(port)**，故URL为：

```java
jdbc:mysql://localhost:3306/crashcourse?useSSL=false&characterEncoding=utf8
```

其中`useSSL=false`表示不使用SSL加密，`characterEncoding=utf8`表示使用UTF-8作为字符编码。

连接数据库代码：

```java
// JDBC连接的URL, 不同数据库有不同的格式:
String JDBC_URL = "jdbc:mysql://localhost:3306/test";
String JDBC_USER = "root";
String JDBC_PASSWORD = "password";
// 获取连接:
Connection conn = DriverManager.getConnection(JDBC_URL, JDBC_USER, JDBC_PASSWORD);
// TODO: 访问数据库...
// 关闭连接:
conn.close();
```



### 二：JDBC查询

获取到JDBC连接后，下一步我们就可以查询数据库了。查询数据库分以下几步：

第一步，通过`Connection`提供的`prepareStatement()`方法创建一个`PreparedStatement`对象，用于执行一个查询；

第二步，执行`PreparedStatement`对象提供的`executeQuery("SELECT * FROM <db>")`并传入SQL语句，执行查询并获得返回的结果集，使用`ResultSet`来引用这个结果集；

第三步，反复调用`ResultSet`的`next()`方法并读取每一行结果。



**为什么使用PreparedStatement而不使用`Statement`：**

使用Statement拼字符串非常容易引发SQL注入的问题，使用PreparedStatement可以*完全避免SQL注入*的问题。

**使用Java对数据库进行操作时，必须使用PreparedStatement，严禁任何通过参数拼字符串的代码！PreparedStatement始终使用`?`作为占位符，然后通过`setObject(索引，值)`设置每个占位符`?`的值(索引从1开始)。(当然还可以用setString,setBoolean)**



**为什么要用 List list = new ArrayList() ,而不用 ArrayList alist = new ArrayList()呢：**

问题就在于List接口有多个实现类，现在你用的是ArrayList，也许哪一天你需要换成其它的实现类，如 LinkedList或者Vector等等，这时你只要改变这一行就行了。



**JDBC查询的返回值总是`ResultSet`：**

**结果集(ResultSet)**是数据中查询结果返回的一种对象，可以说结果集是一个存储查询结果的对象，但是结果集并不仅仅具有存储的功能，他同时还具有操纵数据的功能，可能完成对数据的更新等. 。**结果集读取数据的方法主要是getXXX()，他的参数可以是整型表示第几列（是从1开始的，而不是0），还可以是列名，使用`String`类型的列名比索引要易读，而且不易出错。返回的是对应的XXX类型的值。**如果对应那列是空值，XXX是对象的话返回XXX型的空值，如果XXX是数字类型，如Float等则返回0,boolean返回false.使用getString()可以返回所有的列的值，不过返回的都是字符串类型的。XXX可以代表的类型有: 基本的数据类型如整型(int)，布尔型(Boolean)，浮点型(Float,Double)等，比特型(byte)，还包括一些特殊的类型，如：日 期类型（java.sql.Date），时间类型(java.sql.Time)，时间戳类型(java.sql.Timestamp)，大数型 (BigDecimal和BigInteger等)等。还可以使用getArray(intcolindex/String columnname)，通过这个方法获得当前行中，colindex所在列的元素组成的对象的数组。使用getAsciiStream(intcolindex/String colname)可以获得该列对应的当前行的ascii流。也就是说所有的getXXX方法都是对当前行进行操作。  结果集从其使用的特点上 可以分为四类，这四类的结果集的所具备的特点都是和Statement语句的创建有关，因为**结果集是通过Statement语句执行后产生的**，所以可以 说，结果集具备何种特点，完全决定于Statement。



```java
//示例：查询了运行在我电脑本地的Mysql中的数据库(<db>)crashcourse中的表(TABLE)中的所有行和列
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

public class Main {

    public static void  main(String[] args){
        try {
            List<customers> customers = queryCustomers();  //查询
            customers.forEach(System.out::println);
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }
    }


    static List<customers> querycustomers() throws SQLException{
        List<customers> customers = new ArrayList<customers>();
        try(Connection conn = DriverManager.getConnection(jdbcUrl,jdbcUsername,jdbcPassword)){  //连接数据库
            try(PreparedStatement ps = conn.prepareStatement("SELECT * FROM customers")){   //传入SQL语句
                try(ResultSet rs = ps.executeQuery()){    //执行查询命令获得返回的结果集，并使用ResultSet来引用这个                                                           //结果集
                    while (rs.next()){
                        customers.add(extractRow(rs));   //将所有结果存入customers中
                    }
                }
            }
        }
        return customers;
    }

    static customers extractRow(ResultSet rs) throws SQLException{
        customers std = new customers();
        std.setCust_id(rs.getInt("cust_id"));
        std.setCust_name(rs.getString("cust_name"));
        std.setCust_address(rs.getString("cust_address"));
        std.setCust_city(rs.getString("cust_city"));
        std.setCust_state(rs.getString("cust_state"));
        std.setCust_zip(rs.getInt("cust_zip"));
        std.setCust_country(rs.getString("cust_country"));
        std.setCust_contact(rs.getString("cust_contact"));
        std.setCust_email(rs.getString("cust_email"));
        return std;
    }


    static final String jdbcUrl = "jdbc:mysql://localhost:3306/crashcourse?useSSL=false&characterEncoding=utf8";
    static final String jdbcUsername = "root";
    static final String jdbcPassword = "***";
}
```

```java
//构建的customers类
public class customers {
    private int cust_id;
    private String cust_name;
    private String cust_address;
    private String cust_city;
    private String cust_state;
    private int cust_zip;
    private String cust_country;
    private String cust_contact;
    private String cust_email;

    public int getCust_id() {
        return cust_id;
    }

    public void setCust_id(int cust_id) {
        this.cust_id = cust_id;
    }

    public String getCust_name() {
        return cust_name;
    }

    public void setCust_name(String cust_name) {
        this.cust_name = cust_name;
    }

    public String getCust_address() {
        return cust_address;
    }

    public void setCust_address(String cust_address) {
        this.cust_address = cust_address;
    }

    public String getCust_city() {
        return cust_city;
    }

    public void setCust_city(String cust_city) {
        this.cust_city = cust_city;
    }

    public String getCust_state() {
        return cust_state;
    }

    public void setCust_state(String cust_state) {
        this.cust_state = cust_state;
    }

    public int getCust_zip() {
        return cust_zip;
    }

    public void setCust_zip(int cust_zip) {
        this.cust_zip = cust_zip;
    }

    public String getCust_country() {
        return cust_country;
    }

    public void setCust_country(String cust_country) {
        this.cust_country = cust_country;
    }

    public String getCust_contact() {
        return cust_contact;
    }

    public void setCust_contact(String cust_contact) {
        this.cust_contact = cust_contact;
    }

    public String getCust_email() {
        return cust_email;
    }

    public void setCust_email(String cust_email) {
        this.cust_email = cust_email;
    }

    @Override
    public String toString() {
        return "customers{" +
                "cust_id=" + cust_id +
                ", cust_name='" + cust_name + '\'' +
                ", cust_address='" + cust_address + '\'' +
                ", cust_city='" + cust_city + '\'' +
                ", cust_state='" + cust_state + '\'' +
                ", cust_zip=" + cust_zip +
                ", cust_country='" + cust_country + '\'' +
                ", cust_contact='" + cust_contact + '\'' +
                ", cust_email='" + cust_email + '\'' +
                '}';
    }
}

```



### **三：JDBC增、删、改**

增删改查除了用`PreparedStatement`分别执行对应的SQL语句和最后执行的是`executeUpdate()`而不是`executeQuery`，其他代码与

查询时基本一致。

```java
//插入操作(增)
static void insertCustomers(String cust_name,String cust_address,String cust_city,String cust_state,int cust_zip,String cust_country,String cust_contact,String cust_email) throws SQLException {
    try(Connection conn = DriverManager.getConnection(jdbcUrl,jdbcUsername,jdbcPassword)){  //连接数据库
        try (PreparedStatement ps = conn.prepareStatement("INSERT INTO customers(cust_name,cust_address,cust_city,cust_state, cust_zip,cust_country,cust_contact,cust_email) VALUES (?,?,?,?,?,?,?,?)")){
            ps.setObject(1, cust_name);
            ps.setObject(2, cust_address);
            ps.setObject(3, cust_city);
            ps.setObject(4, cust_state);
            ps.setObject(5, cust_zip);
            ps.setObject(6, cust_country);
            ps.setObject(7, cust_contact);
            ps.setObject(8, cust_email);
            ps.executeUpdate();
        }
    }
}
```

```java
//删
static void deleteCustomers(int cust_id) throws SQLException {
    try(Connection conn = DriverManager.getConnection(jdbcUrl,jdbcUsername,jdbcPassword)){
        try(PreparedStatement ps = conn.prepareStatement("DELETE FROM  customers WHERE cust_id=?")){
            ps.setObject(1,cust_id);
            ps.executeUpdate();
        }
    }
}
```

```java
//改(更新)(此处以更新cust_id=10006的cust_name列为updataTest为例示范)
static void updateCustomers() throws SQLException {
    try (Connection conn = DriverManager.getConnection(jdbcUrl,jdbcUsername,jdbcPassword)){
        try(PreparedStatement ps = conn.prepareStatement("UPDATE customers SET ?=? WHERE cust_id=?")){
            ps.setObject(1,"cust_name");
            ps.setObject(1,"updataTest");
            ps.setObject(2,10006);
            ps.executeUpdate();
        }    }

}
```



### [最后总结](https://www.cnblogs.com/jing-an-feng-shao/p/9220085.html)

### Mysql中其他常用命令

| 命令                                    | 功能                             |
| --------------------------------------- | -------------------------------- |
| show global variables like "%datadir%"; | 查看mysql数据库文件存储位置      |
| status;                                 | 显示当前mysql的version的各种信息 |
|                                         |                                  |

