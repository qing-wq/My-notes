# JDBC

**什么是JDBC？**

SUN公司为了简化开发人员对数据库的统一操作，提供的一个Java操作数据库的规范，称为JDBC。

规范的具体实现由数据库厂商去做。作为开发人员，我们只需要通过JDBC接口操作数据库。

比较重要的两个包：`java.sql`，`javax.sql`

对应关系：

<img src="JDBC.assets/image-20220221001624203.png" alt="image-20220221001624203" style="zoom:67%;" />

## JDBC程序

假设数据库中存在一张用户表：

<img src="JDBC.assets/image-20220221003705944.png" alt="image-20220221003705944" style="zoom:67%;" />

现在我们要在代码层获取用户的信息：

**添加依赖**

```xml
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.26</version>
    <scope>runtime</scope>
</dependency>
```

注意：MySQL对应哪个版本就导入哪个版本的jar包。  [仓库地址](https://mvnrepository.com/artifact/mysql/mysql-connector-java)

> 注意到这里添加依赖的`scope`是`runtime`，因为编译Java程序并不需要MySQL的这个jar包，只有在**运行期才需要使用**。如果把`runtime`改成`compile`，虽然也能正常编译，但是在IDE里写程序的时候，会多出来一大堆类似`com.mysql.jdbc.Connection`这样的类，非常容易与Java标准库的JDBC接口混淆，所以坚决不要设置为`compile`。

或者手动导入jar包，添加Lib目录并放入驱动jar包：

<img src="JDBC.assets/image-20220221204622480.png" alt="image-20220221204622480" style="zoom: 50%;" />

**JDBC程序**

```java
public class FirstJdbc {
    public static void main(String[] args) throws SQLException, ClassNotFoundException {
        //1.加载驱动
        Class.forName("com.mysql.cj.jdbc.Driver");   // 固定写法
        //2.用户信息和url
        String url = "jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEncoding=utf8&&useSSL=true&serverTimezone=GMT%2B8";
        String username = "root";
        String password = "123456";
        //3.连接成功，创建数据库对象  Connection代表数据库
        Connection connection = DriverManager.getConnection(url, username, password);
        //4.获取执行SQL的对象：Statement
        Statement statement = connection.createStatement();
        //5.执行SQL的对象 用于执行SQL 可能存在返回结果
        String sql = "SELECT * FROM `user`";
        ResultSet resultSet = statement.executeQuery(sql);  // executeQuery()查询。返回查询的结果集
            // 遍历
        while(resultSet.next()){
            System.out.println("id="+resultSet.getObject("id"));
            System.out.println("name="+resultSet.getObject("name"));
            System.out.println("password="+resultSet.getObject("password"));
            System.out.println("email="+resultSet.getObject("email"));
            System.out.println("birth="+resultSet.getObject("birthday"));
        }
        //6.释放资源:十分消耗资源，用完一定要关掉
        resultSet.close();
        statement.close();
        connection.close();
    }
```

执行结果：

<img src="JDBC.assets/image-20220221173927341.png" alt="image-20220221173927341" style="zoom:67%;" />

编写步骤：

1. 加载驱动    `DriverManager`
2. 连接数据库   `Connection`
3. 获得执行sql的对象  `Statement`
4. 获得返回的结果集   `ResultSet`
5. 释放连接

注意：

- MySQL新版的驱动为`com.mysql.cj.jdbc.Driver`，旧版的是`com.mysql.jdbc.Driver`
- MySQL版本高于JDBC时设置`sueSSL=false`，否则会报错

- 显示时区问题：设置url参数`&serverTimezone=GMT%2B8`

## JDBC中的对象

> **`DriverManager `  驱动**

`DriverManager.registerDriver(new Driver())`可以用来注册驱动类。

但是`Class.forName("com.mysql.cj.jdbc.Driver")`获取对象时就已经将类加载到类模板中了，而`Driver`中的静态方法，载入`Driver`时就会自动执行注册驱动的静态方法。因此使用这句话我们就不再需要加一行`DriverManager.registerDriver(new Driver())`来注册驱动类了。

`Class.forName("com.mysql.cj.jdbc.Driver")`不需要返回Class对象，因为没有作用

注意导入的包是`com.mysql.cj.jdbc.Driver`，不要导错了。

> **`connection`   数据库对象**

数据库有关的操作都可以利用它实现

```java
connection.setAutoCommit();   // 数据库设置自动提交
connection.commit();   // 事务提交
connection.rollback();   // 事务回滚
```

> **URL参数**

```java
String url = "jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEncoding=utf8&useSSL=true&serverTimezone=GMT%2B8";

// 格式：协议://主机地址：端口号/数据库名?参数1&参数2&...

// 拓展：oracle格式
String url = jdbc:oracle:thin:@localhost:1521:sid
```

> **`statement,PreparedStatement`  执行SQL的对象**

```java
statement.execute();   // 查询操作使用。会返回结果集ResultSet
statement.executeQuery();   // 执行任何SQL
statement.executeUpdate();    // update、insert、delete用这个。
```

- `statement.executeUpdate()`返回受影响的行数。

> **`ResultSet`  查询的结果集**

封装了所有查询的结果。

**获取结果集中的信息**

```java
// 括号中的参数是列名
resultSet.getObject();   // 不知道返回值是什么类型的情况下使用
resultSet.getString();   // 知道返回值类型的情况下，使用指定类型的方法更精确
resultSet.getInt();
resultSet.getFloat();
resultSet.getDate();
```

**遍历结果集中的指针移动**

```java
resultSet.beforeFirst(); // 移动到最前面
resultSet.afterLast();  // 移动到最后面
resultSet.next();  // 移动到下一个数据
resultSet.previous();  // 移动到前一行
resultSet.absolute(row);  // 移动到指定行
```

## JDBC代码优化

`db.properties`

```properties
driver=com.mysql.cj.jdbc.Driver
url = jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEncoding=utf8&useSSL=true
username=root
password=123456
```

`jdbcUtils.java`

```java
package com.qing.utils;

import java.io.IOException;
import java.io.InputStream;
import java.sql.*;
import java.util.Properties;
// 提取工具类
public class jdbcUtils {
    public static String driver = null;
    public static String url = null;
    public static String username = null;
    public static String password = null;

    static {
        try {
            InputStream in = jdbcUtils.class.getClassLoader().getResourceAsStream("db.Properties");
            Properties properties = new Properties();
            properties.load(in);    // 载入Properties文件
            driver=properties.getProperty("driver");
            url=properties.getProperty("url");
            username=properties.getProperty("username");
            password=properties.getProperty("password");
            // 1.加载驱动
            Class.forName(driver);
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
	// 2.获取连接
    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(url,username,password);
    }
	// 3.释放z
    public static void release(Connection con, Statement st, ResultSet res) throws SQLException {
        if(res!=null){
            res.close();
        }
        if(res!=null){
            st.close();
        }
        if(res!=null){
            con.close();
        }
    }
}
```

`TestUpdate.java`

```java
package com.qing;

import com.qing.utils.jdbcUtils;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TestUpdate {
    public static void main(String[] args) throws SQLException {

        Connection connection = null;
        Statement statement = null;
        ResultSet resultSet = null;

        try{
            connection = jdbcUtils.getConnection();
             statement = connection.createStatement();

            String sql = "UPDATE `user` SET `name`='小菁' WHERE id=2";

            int i = statement.executeUpdate(sql);    // 返回受影响的行数
            if(i>0){
                System.out.println("修改成功");
            }
        }catch (SQLException e){
            e.printStackTrace();
        }finally {
            jdbcUtils.release(connection,statement, resultSet);
        }
    }
}
```

注意：`.properties`文件要放在`src`目录下

​	<img src="JDBC.assets/image-20220222001314223.png" alt="image-20220222001314223" style="zoom:67%;" />

## SQL注入

SQL注入是指web应用程序对用户输入数据的合法性没有判断或过滤不严，攻击者可以在web应用程序中事先定义好的查询语句的结尾上添加额外的SQL语句，在管理员不知情的情况下实现非法操作，以此来实现欺骗数据库服务器执行非授权的任意查询，从而进一步得到相应的数据信息。

以用户登录为例：

```java
import com.qing.utils.jdbcUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class SQL注入 {
    public static void main(String[] args) throws SQLException {
//  正常输入：      login("zhangsan","123456");
        login("' or '1=1","123456' or '1=1");   // 恶意数据
    }
    public static void login(String username,String password) throws SQLException {

        Connection connection = null;
        Statement statement = null;
        ResultSet resultSet = null;
        try {
            connection = jdbcUtils.getConnection();
            statement = connection.createStatement();
            // SELECT * FROM USER WHERE NAME = '' OR '1=1'  AND `password` = '' OR '1=1'
            String sql = "select * from user where `name` = '"+ username + "' AND `password` = '" + password + "'";
            resultSet = statement.executeQuery(sql);

            while(resultSet.next()){
                String name = resultSet.getString("name");
                String pwd = resultSet.getString("password");
                System.out.println("用户："+name+",密码："+pwd);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            jdbcUtils.release(connection,statement,resultSet);
        }
    }
}
```

执行结果：用户信息全部暴露，数据不安全。

<img src="JDBC.assets/image-20220222114822362.png" alt="image-20220222114822362" style="zoom:67%;" />

解决办法：使用`PreparedStatement`对象。

## PreparedStatement

`PreparedStatement`是`Statement`子类，也是能够执行SQL的对象。但是能够防止SQL注入问题。

```java
package com.qing;

import com.qing.utils.jdbcUtils;

import java.sql.Connection;
import java.sql.Date;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class TestPrepareStatement {
    public static void main(String[] args) throws SQLException {

        Connection connection = null;
        PreparedStatement preparedStatement = null;
        try {
            connection = jdbcUtils.getConnection();
            String sql = "INSERT `user`(`name`,`password`,`email`,`birthday`) VALUE(?,?,?,?)";

            preparedStatement = connection.prepareStatement(sql);  // 预编译SQL，即先写sql，但不执行
            // 手动传参
            preparedStatement.setString(1,"lisi");
            preparedStatement.setString(2,"aaaaaaa");
            preparedStatement.setString(3,"jing@qq.com");
            // new java.util.Date().getTime()  获取时间戳
            preparedStatement.setDate(4,new Date(new java.util.Date().getTime()));  
            // 执行sql
            int i = preparedStatement.executeUpdate();
            if(i!=0){
                System.out.println("插入成功!");
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            jdbcUtils.release(connection,preparedStatement,null);
        }
    }
}
```

- `PreparedStatement`可以用`?`作为占位符，并通过`preparedStatement.setXXX(索引,参数)`来设置参数。
- `setXXX()`的索引从1开始
- `setDate()`中的两个`Date`并不相同，一个是`java.util.Date`，一个是`java.sql.Date`，两个类是父子关系。
- `PreparedStatement`的`executeXXX`方法不需要传入`sql`语句，`sql`已经被预编译。

**`PreparedStatement`防止SQL注入的本质**

将传进来的参数当作字符。假设其中存在特殊字符，如 $'$ 会被直接转义。

还是[上面](#SQL注入)的例子：

```java
import com.qing.utils.jdbcUtils;
import java.sql.*;

public class SQL注入 {
    public static void main(String[] args) throws SQLException {
        login("' or '1=1","' or '1=1");
    }

    public static void login(String username,String password) throws SQLException {

        Connection connection = null;
        PreparedStatement preparedStatement = null;
        ResultSet resultSet = null;
        try {
            connection = jdbcUtils.getConnection();

            String sql = "select * from user where `name` = ? AND `password` =?";
            preparedStatement = connection.prepareStatement(sql);
            preparedStatement.setString(1,username);
            preparedStatement.setString(2,password);
            resultSet = preparedStatement.executeQuery();

            while(resultSet.next()){
                String name = resultSet.getString("name");
                String pwd = resultSet.getString("password");
                System.out.println("用户："+name+",密码："+pwd);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            jdbcUtils.release(connection,preparedStatement,resultSet);
        }
    }
}
```

执行结果：

<img src="JDBC.assets/image-20220222164644001.png" alt="image-20220222164644001" style="zoom:67%;" />

## IDEA连接数据库

连接数据库

<img src="JDBC.assets/image-20220222201337223.png" alt="image-20220222201337223" style="zoom:67%;" />

添加驱动

<img src="JDBC.assets/image-20220226144508626.png" alt="image-20220226144508626" style="zoom: 50%;" />

导入数据

<img src="JDBC.assets/image-20220222194203109.png" alt="image-20220222194203109" style="zoom:50%;" />

修改数据

<img src="JDBC.assets/image-20220222195905396.png" alt="image-20220222195905396" style="zoom:50%;" />

## JDBC操作事务

**事务回顾**

> ACID原则

原子性：要么全部完成，要么什么都不完成

一致性：总数不变

隔离性：多个进程互不干扰

持久性：事务一旦提交不可逆，持久化到数据库



隔离导致的问题：

脏读：一个事务读取了另一个没有提交的事务

不可重复读：在同一个事务内，重复读取表中的数据，表的数据发生了变化

虚度（幻读）：在一个事务内，读取到别人插入的数据，导致前后读出的数据不一致

> 代码实现

```java
import com.qing.utils.jdbcUtils;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class TestTruncation {
    public static void main(String[] args) throws SQLException {
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        ResultSet resultSet = null;
        try {
             connection = jdbcUtils.getConnection();
            // 关闭自动提交，自动开启事务
            connection.setAutoCommit(false);
            String sql1 = "update `account` set money=money+500 where `name`='A'";
            preparedStatement = connection.prepareStatement(sql1);  // 预解析
            preparedStatement.executeUpdate();

            String sql2 = "update `account` set money=money-500 where `name`='B'";
            preparedStatement = connection.prepareStatement(sql2);
            preparedStatement.executeUpdate();
            // 提交事务
            connection.commit();
            System.out.println("执行成功==================");
        } catch (SQLException e) {
            // 失败就回滚。这句可以默认不写
            connection.rollback();
            e.printStackTrace();
        }finally {
            connection.setAutoCommit(true);
            // s
            jdbcUtils.release(connection,preparedStatement,resultSet);
        }
    }
}
```

## 数据库连接池

与之前的多线程相同，我们创建和销毁线程都需要非常昂贵的资源，为了提高效率，我们可以创建线程池。	

类似的，为了避免我们频繁的创建和销毁JDBC连接，我们也可以通过数据库连接池来复用创建好的连接。

池化技术：准备一些预先的资源，过来就连接预先准备好的

编写连接池时，需要实现一个`DataSource`接口

**常用术语**

最小连接数：10   

最大连接数：15

等待超时：100ms  超过等待时间就向正在排队的抛出异常，让其余队伍不再等待。

**常用的开源数据源**

- DBCP
- C3P0
- Druid

这些数据源都实现了`DataSource`接口，可以作为数据库连接池。

使用了这些数据库连接池之后，我们在项目开发中就不需要编写连接数据库的代码了。

### DBCP

需要导入的jar包：`commons-dbcp-1.4`，`commons-pool-1.6`

**导入依赖**

```xml
<!-- https://mvnrepository.com/artifact/commons-dbcp/commons-dbcp -->
<dependency>
    <groupId>commons-dbcp</groupId>
    <artifactId>commons-dbcp</artifactId>
    <version>1.4</version>
</dependency>
<!-- https://mvnrepository.com/artifact/commons-pool/commons-pool -->
<dependency>
    <groupId>commons-pool</groupId>
    <artifactId>commons-pool</artifactId>
    <version>1.6</version>
</dependency>
```

**配置文件**

`dbcpconfig.properties` ==文件名字固定==

```properties
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEncoding=utf8&useSSL=true
username=root
password=123456

#<!-- 初始化连接 -->
initialSize=10

#最大连接数量
maxActive=50

#<!-- 最大空闲连接 -->
maxIdle=20

#<!-- 最小空闲连接 -->
minIdle=5

#<!-- 超时等待时间以毫秒为单位 6000毫秒/1000等于60秒 -->
maxWait=60000
#JDBC驱动建立连接时附带的连接属性属性的格式必须为这样：【属性名=property;】
#注意："user" 与 "password" 两个属性会被明确地传递，因此这里不需要包含他们。
connectionProperties=useUnicode=true;characterEncoding=UTF8

#指定由连接池所创建的连接的自动提交（auto-commit）状态。
defaultAutoCommit=true

#driver default 指定由连接池所创建的连接的只读（read-only）状态。
#如果没有设置该值，则“setReadOnly”方法将不被调用。（某些驱动并不支持只读模式，如：Informix）
defaultReadOnly=

#driver default 指定由连接池所创建的连接的事务级别（TransactionIsolation）。
#可用值为下列之一：（详情可见javadoc。）NONE,READ_UNCOMMITTED, READ_COMMITTED, REPEATABLE_READ, SERIALIZABLE
defaultTransactionIsolation=READ_UNCOMMITTED
```

**代码实现**

**`JdbcUtils_DBCP.java`**

```java
import org.apache.commons.dbcp.BasicDataSourceFactory;

import javax.sql.DataSource;
import java.io.IOException;
import java.io.InputStream;
import java.sql.*;
import java.util.Properties;
// 工具类
public class JdbcUtils_DBCP {
    public static String driver = null;
    public static String url = null;
    public static String username = null;
    public static String password = null;

    private static DataSource dataSource = null;
    static {
        
        try {
            InputStream in = JdbcUtils_DBCP.class.getClassLoader().getResourceAsStream("dbcpconfig.properties");
            Properties properties = new Properties();
            properties.load(in);
            
            // 创建数据源  工厂模式:创建
            dataSource = BasicDataSourceFactory.createDataSource(properties);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static Connection getConnection() throws SQLException {
        return dataSource.getConnection();     // 从数据源获取连接
    }

    public static void release(Connection con, Statement st, ResultSet res) throws SQLException {
        if(res!=null){
            res.close();
        }
        if(res!=null){
            st.close();
        }
        if(res!=null){
            con.close();
        }
    }
}
```

**`TestDBCP.java`**

```java
package com.qing;

import com.qing.utils.JdbcUtils_DBCP;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TestDBCP {
    public static void main(String[] args) throws SQLException {

        Connection connection = null;
        Statement statement = null;
        ResultSet resultSet = null;

        try{
            connection = JdbcUtils_DBCP.getConnection();
            statement = connection.createStatement();

            String sql = "UPDATE `user` SET `name`='小菁' WHERE id=2";

            int i = statement.executeUpdate(sql);
            if(i>0){
                System.out.println("修改成功");
            }
        }catch (SQLException e){
            e.printStackTrace();
        }finally {
            JdbcUtils_DBCP.release(connection,statement, resultSet);
        }
    }
}
```



### C3P0

需要导入的jar包：`csp0-0.9.55`，`mchange-commons-java-0.2.19`

- 如果出现`Could not initialize class`，那么还需要导入一个`commons-logging-1.2.jar`的依赖

**导入依赖**

```xml
<!-- https://mvnrepository.com/artifact/org.apache.tomcat/tomcat-jasper -->
<dependency>
    <groupId>org.apache.tomcat</groupId>
    <artifactId>tomcat-jasper</artifactId>
    <version>9.0.55</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.mchange/mchange-commons-java -->
<dependency>
    <groupId>com.mchange</groupId>
    <artifactId>mchange-commons-java</artifactId>
    <version>0.2.19</version>
</dependency>
```

**配置文件**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<c3p0-config>
    <!--
c3p0的缺省（默认）配置
如果在代码中"ComboPooledDataSource ds=new ComboPooledDataSource();"这样写就表示使用的是c3p0的缺省（默认）-->
    <default-config>
        <property name="driverClass">com.mysql.cj.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&amp;characterEncoding=utf8&amp;uesSSL=true&amp;serverTimezone=UTC</property>
        <property name="user">root</property>
        <property name="password">123456</property>

        <property name="acquireIncrement">5</property>
        <property name="initialPoolSize">10</property>
        <property name="minPoolSize">5</property>
        <property name="maxPoolSize">20</property>
    </default-config>
    <!--
c3p0的命名配置
如果在代码中"ComboPooledDataSource ds=new ComboPooledDataSource("MySQL");"这样写就表示使用的是c3p0的MySQL命名配置-->
    <named-config name="MySQL">
        <property name="driverClass">com.mysql.cj.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&amp;characterEncoding=utf8&amp;uesSSL=true&amp;serverTimezone=UTC</property>
        <property name="user">root</property>
        <property name="password">123456</property>

        <property name="acquireIncrement">5</property>
        <property name="initialPoolSize">10</property>
        <property name="minPoolSize">5</property>
        <property name="maxPoolSize">20</property>
    </named-config>
</c3p0-config>
```

**代码实现**

**`JdbcC3P0.java`**

```java
import com.mchange.v2.c3p0.ComboPooledDataSource;
import com.mysql.cj.jdbc.Driver;
import org.apache.commons.dbcp.BasicDataSourceFactory;

import javax.sql.DataSource;
import java.io.InputStream;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Properties;

public class JdbcUtils_C3P0 {
    public static String driver = null;
    public static String url = null;
    public static String username = null;
    public static String password = null;

    private static DataSource dataSource = null;
    static {

        try {
            // 配置文件方法，获取数据源
            dataSource = new ComboPooledDataSource();  // 无参表示使用的是c3p0的缺省
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static Connection getConnection() throws SQLException {
        return dataSource.getConnection();
    }

    public static void release(Connection con, Statement st, ResultSet res) throws SQLException {
        if(res!=null){
            res.close();
        }
        if(res!=null){
            st.close();
        }
        if(res!=null){
            con.close();
        }
    }
}
```

**`TestC3P0.java`**

```java
import com.qing.utils.JdbcUtils_C3P0;
import com.qing.utils.JdbcUtils_DBCP;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TestC3P0 {
    public static void main(String[] args) throws SQLException {

        Connection connection = null;
        Statement statement = null;
        ResultSet resultSet = null;

        try{
            connection = JdbcUtils_C3P0.getConnection();
            statement = connection.createStatement();

            String sql = "UPDATE `user` SET `name`='xiaojing' WHERE id=2";

            int i = statement.executeUpdate(sql);
            if(i>0){
                System.out.println("修改成功");
            }
        }catch (SQLException e){
            e.printStackTrace();
        }finally {
            JdbcUtils_C3P0.release(connection,statement, resultSet);
        }
    }
}
```

虽然使用数据源对代码量的节省并不大，但却可以实现性能的提高。

