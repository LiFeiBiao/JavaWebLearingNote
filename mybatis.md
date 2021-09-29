### mybatis

1. #### 环境

   jdk1.8          mysql5.7     maven3.6.1   IDEA	

2. #### 回顾

   JDBC        Mysql           Java基础             maven          junit

3. #### ssm框架：配置文件的，最好方式：看官方文档

   

## 1、简介

### 1.1、什么是mybatis

MyBatis 是一款优秀的**持久层框架**，

它支持自定义 SQL、存储过程以及高级映射。

MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。

MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。



### 如何获得mybatis

1. maven仓库

   ```xml
   <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
   <dependency>
       <groupId>org.mybatis</groupId>
       <artifactId>mybatis</artifactId>
       <version>3.5.6</version>
   </dependency>
   ```

2. githubhttps://github.com/mybatis/mybatis-3

3. 中文文档https://mybatis.org/mybatis-3/zh/index.html

### 1.2持久化

数据持久化

- 持久化就是将程序的数据在持久状态和瞬时状态转化的过程
- 内存:断电即失
- 数据库(dbc), io文件持久化。
- 生活:冷藏、罐头。

#### 为什么需要持久化？

- 有一些对象不能让它丢掉
- 内存太贵了

### 1.3持久层

Dao层    service层    controller层

- 完成持久化工作的代码块
- 层界限什么明显

#### 1.4为什么需要mybatis?

- 方便
- 帮助程序员将数据存入到数据库中
- 传统jdbc太复杂，简化，框架，自动化
- 不用mybatis也可以。更容易上手
- 优点：
  - 简单易学：
  - 灵活：
  - sql和代码的分离，提高了可维护性。
  - 提供映射标签，支持对象与数据库的orm字段关系映射
  - 提供对象关系映射标签，支持对象关系组建维护
  - 提供xml标签，支持编写动态sql
  
  spring     springmvc         springboot

## 第一个mybatis程序

思路：搭建环境        导入mybatis          编写代码              测试

### 1.1、搭建环境

- 创建数据库

  ```sql
  create table userone(
  		id int(20) not null primary key,
  		name1 varchar(30)  default null,
  		pwd varchar(30) default null
  )engine=innodb default CHARSET=utf8;
  
  
  insert into userone(id,name1,pwd) values 
  (1,'李飞彪','123456'),
  (2,'李飞彪1','123456'),
  (3,'李飞彪2','123456');
  ```

- 新建项目

  1. 新建一个maven项目

  2. 删除src目录

  3. 导入maven依赖

     ```xml
     <dependency>
         <groupId>mysql</groupId>
         <artifactId>mysql-connector-java</artifactId>
         <version>8.0.13</version>
     </dependency>
     <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
     <dependency>
         <groupId>org.mybatis</groupId>
         <artifactId>mybatis</artifactId>
         <version>3.5.6</version>
     </dependency>
     <!-- https://mvnrepository.com/artifact/junit/junit -->
     <dependency>
         <groupId>junit</groupId>
         <artifactId>junit</artifactId>
         <version>4.13.2</version>
         <scope>test</scope>
     </dependency>
     ```


### 1.2、创建一个模块

- 编写mybatis核心配置文件

- ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <configuration>
      <environments default="development">
          <environment id="development">
              <transactionManager type="JDBC"/>
              <dataSource type="POOLED">
                  <property name="driver" value="com.mysql.jdbc.Driver"/>
                  <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                  <property name="username" value="root"/>
                  <property name="password" value="root"/>
              </dataSource>
          </environment>
      </environments>
  
  </configuration>
  ```

- 编写mybatis工具类

- ```java
  package com.lfb.utils;
  
  import org.apache.ibatis.io.Resources;
  import org.apache.ibatis.session.SqlSession;
  import org.apache.ibatis.session.SqlSessionFactory;
  import org.apache.ibatis.session.SqlSessionFactoryBuilder;
  
  import java.io.IOException;
  import java.io.InputStream;
  
  //SqlSessionFactory      sqlsession
  public class MybatisUtils {
      private static SqlSessionFactory sqlSessionFactory;
      static{
          try {
              //使用mybatis第一步 获取SqlSessionFactory 对象
              String resource = "mybatis-config.xml";
              InputStream inputStream = Resources.getResourceAsStream(resource);
              SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
          } catch (IOException e) {
              e.printStackTrace();
          }
  
      }
  
      //既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。
      // SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句。
      public static SqlSession getSqlSession(){
          return sqlSessionFactory.openSession();
      }
  }
  ```

### 1.3、编写代码

- 实体类

  ```java
  package com.lfb.pojo;
  
  //实体类
  public class User {
      private int id;
      private String name1;
      private String pwd;
  
      public User(int id, String name1, String pwd) {
          this.id = id;
          this.name1 = name1;
          this.pwd = pwd;
      }
      public User(){
  
      }
  
      public int getId() {
          return id;
      }
  
      public void setId(int id) {
          this.id = id;
      }
  
      public String getName1() {
          return name1;
      }
  
      public void setName1(String name1) {
          this.name1 = name1;
      }
  
      public String getPwd() {
          return pwd;
      }
  
      public void setPwd(String pwd) {
          this.pwd = pwd;
      }
  
      @Override
      public String toString() {
          return "User{" +
                  "id=" + id +
                  ", name1='" + name1 + '\'' +
                  ", pwd='" + pwd + '\'' +
                  '}';
      }
  }
  ```

- Dao接口

  ```java
  public interface UserDao {
      List<User> getUserList();
  }
  ```

- 接口实现类由原来的UserDaoImpl转变为一个Mapper配置文件

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  
  <!--namespace=绑定一个对应的Dao/Mapper接口-->
  <mapper namespace="com.lfb.dao.UserDao">
      <select id="getUserList" resultType="com.lfb.pojo.User">
          select * from mybatis.userone
      </select>
  </mapper>
  ```

### 1.4、测试

MapperRegistry是什么？

核心配置文件中注册mappers

- junit测试

  ```java
  public class UserDaoTest {
      @Test
      public void test(){
          //第一步获得sqlsession对象
          SqlSession sqlSession = MybatisUtils.getSqlSession();
  
          //getmapper方式
          UserDao userDao = sqlSession.getMapper(UserDao.class);
          List<User> userList = userDao.getUserList();
  
          for (User user : userList){
              System.out.println(user);
          }
  
          //关闭sqlSession
          sqlSession.close();
      }
  }
  ```

- 你们可以能会遇到的问题：
  - 1.配置文件没有注册
  - 2.绑定接口错误。
  - 3.方法名不对
  - 4．返回类型不对
  - 5. Maven导出资源问题 

## 3、CRUD

### 1、namespace

namespace中的包名要求和Dao/mapper接口的包名一致

### 2、select

